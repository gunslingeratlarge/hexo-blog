title: 以太坊源码分析02-rlp part2
author: gsal
tags:
  - 以太坊
  - 区块链
categories:
  - 区块链
date: 2018-04-11 19:43:00
---
分析有空再补上。
<!--more-->
``` java
package org.ethereum.util;

import java.math.BigInteger;
import java.nio.ByteBuffer;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Queue;

import static java.util.Arrays.copyOfRange;
import static org.ethereum.util.ByteUtil.byteArrayToInt;
import static org.spongycastle.util.Arrays.concatenate;

import java.util.List;

import org.ethereum.util.RLP;
import org.ethereum.util.RLPItem;
import org.ethereum.util.RLPList;

/**
 * Recursive Length Prefix (RLP) encoding. 
 * 
 * The purpose of RLP is to encode arbitrarily nested arrays of binary data, and
 * RLP is the main encoding method used to serialize objects in Ethereum. The
 * only purpose of RLP is to encode structure; encoding specific atomic data
 * types (eg. strings, ints, floats) is left up to higher-order protocols; in
 * Ethereum the standard is that integers are represented in big endian binary
 * form. If one wishes to use RLP to encode a dictionary, the two suggested
 * canonical forms are to either use [[k1,v1],[k2,v2]...] with keys in
 * lexicographic order or to use the higher-level Patricia Tree encoding as
 * Ethereum does.
 * 
 * The RLP encoding function takes in an item. An item is defined as follows:
 * 
 * - A string (ie. byte array) is an item - A list of items is an item
 * 
 * For example, an empty string is an item, as is the string containing the word
 * "cat", a list containing any number of strings, as well as more complex data
 * structures like ["cat",["puppy","cow"],"horse",[[]],"pig",[""],"sheep"]. Note
 * that in the context of the rest of this article, "string" will be used as a
 * synonym for "a certain number of bytes of binary data"; no special encodings
 * are used and no knowledge about the content of the strings is implied.
 * 
 * See: https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-RLP
 */
public class RLP {

	/** Allow for content up to size of 2^64 bytes **/
	private static double MAX_ITEM_LENGTH = Math.pow(256, 8);

	/**
	 * Reason for threshold according to Vitalik Buterin:
	 * 	- 56 bytes maximizes the benefit of both options
	 * 	- if we went with 60 then we would have only had 4 slots for long strings 
	 * so RLP would not have been able to store objects above 4gb
	 * 	- if we went with 48 then RLP would be fine for 2^128 space, but that's way too much
	 * 	- so 56 and 2^64 space seems like the right place to put the cutoff
	 * 	- also, that's where Bitcoin's varint does the cutoff
	 **/
	private static int SIZE_THRESHOLD = 56;
	
	/** RLP encoding rules are defined as follows: */
	
	/*
	 * For a single byte whose value is in the [0x00, 0x7f] range, that byte is
	 * its own RLP encoding.
	 */

	/*
	 * If a string is 0-55 bytes long, the RLP encoding consists of a single
	 * byte with value 0x80 plus the length of the string followed by the
	 * string. The range of the first byte is thus [0x80, 0xb7].
	 */
	private static int OFFSET_SHORT_ITEM = 0x80;

	/*
	 * If a string is more than 55 bytes long, the RLP encoding consists of a
	 * single byte with value 0xb7 plus the length of the length of the string
	 * in binary form, followed by the length of the string, followed by the
	 * string. For example, a length-1024 string would be encoded as
	 * \xb9\x04\x00 followed by the string. The range of the first byte is thus
	 * [0xb8, 0xbf].
	 */
	private static int OFFSET_LONG_ITEM = 0xb8;

	/*
	 * If the total payload of a list (i.e. the combined length of all its
	 * items) is 0-55 bytes long, the RLP encoding consists of a single byte
	 * with value 0xc0 plus the length of the list followed by the concatenation
	 * of the RLP encodings of the items. The range of the first byte is thus
	 * [0xc0, 0xf7].
	 */
	private static int OFFSET_SHORT_LIST = 0xc0;

	/*
	 * If the total payload of a list is more than 55 bytes long, the RLP
	 * encoding consists of a single byte with value 0xf7 plus the length of the
	 * length of the list in binary form, followed by the length of the list,
	 * followed by the concatenation of the RLP encodings of the items. The
	 * range of the first byte is thus [0xf8, 0xff].
	 */
	private static int OFFSET_LONG_LIST = 0xf8;
	
	
	/* ******************************************************
	 * 						DECODING						*
	 * ******************************************************/
	
	//解析byte型
	private static byte decodeOneByteItem(byte[] data, int index) {
        // null item
        if ((data[index] & 0xFF) == OFFSET_SHORT_ITEM) {
            return (byte) (data[index] - OFFSET_SHORT_ITEM);
        }
        // single byte item
        if ((data[index] & 0xFF) < OFFSET_SHORT_ITEM) {
            return (byte) (data[index]);
        }
        // single byte item
        // 大于等于127的情况
        if ((data[index] & 0xFF) == OFFSET_SHORT_ITEM+1) {
            return (byte) (data[index + 1]);
        }
        return 0;
    }

	//解析int型
	 public static int decodeInt(byte[] data, int index) {

	        int value = 0;
	        //为什么要有一个index，因为有可能从其他位置解析，不一定是开头，比如dog,cat,对dog有可能要单独解析
	        //大于128，小于183，即1-55字节数据（非列表
	        if ((data[index] & 0xFF) > OFFSET_SHORT_ITEM && (data[index] & 0xFF) < 0xB7) {

	        	System.out.println(data[index] - OFFSET_SHORT_ITEM);
	            //byte length = (byte) (data[index] - OFFSET_SHORT_ITEM);
	        	//&0xff就把它转成了无符号数，就从-126转为了130，（256 - 126），再减去130即可
	        	byte length =(byte)( data[index] & 0xff - OFFSET_SHORT_ITEM);
	            //我们考察一个例子1024，这个整数，存为 1000 0010  0000 0100  0000 0000，length为2，
	        	//第一次value加上了0000 0100左移八位的数，即1024，第二次就是最后八位了
	        	byte pow = (byte) (length - 1);
	            for (int i = 1; i <= length; ++i) {
	                value += data[index + i] << (8 * pow);
	                pow--;
	            }
	        //解析一个int，长度不可能达到56字节及以上的，如果达到了就说明有问题
	        } else {
	            throw new RuntimeException("wrong decode attempt");
	        }
	        return value;
	    }
	    
	    

    //解析short型
    private static short decodeShort(byte[] data, int index) {

        short value = 0;

        if ((data[index] & 0xFF) > OFFSET_SHORT_ITEM && (data[index] & 0xFF) < 0xB7) {
            byte length = (byte) (data[index] - OFFSET_SHORT_ITEM);
            value = ByteBuffer.wrap(data, index, length).getShort();
        } else {
            value = data[index];
        }
        return value;
    }

    //跟int型可以说是一模一样
    private static long decodeLong(byte[] data, int index) {

    	long value = 0;

        if ((data[index] & 0xFF) > OFFSET_SHORT_ITEM && (data[index] & 0xFF) < 0xB7) {

            byte length = (byte) (data[index] - OFFSET_SHORT_ITEM);
            byte pow = (byte) (length - 1);
            for (int i = 1; i <= length; ++i) {
                value += data[index + i] << (8 * pow);
                pow--;
            }
        } else {
            throw new RuntimeException("wrong decode attempt");
        }
        return value;
    }
    
    private static String decodeStringItem(byte[] data, int index) {

        String value = null;
        //183-192
        if ((data[index] & 0xFF) >= 0xB7 && (data[index] & 0xFF) < OFFSET_SHORT_LIST) {
        	
        	//其实就是长度的长度，比如我们之前的例子中，183,30，。。。这里30是后面省略号的长度，而长度的长度即
        	//30的长度就是1. 
            byte lengthOfLength = (byte) (data[index] - 0xB7);
            //现在知道了lengthoflength是1，接下来就是去拿出这个长度为1的length然后返回为int
            int length = calcLengthRaw(lengthOfLength, data, index);
            //然后再把后面的数据拿出来作为字符串
            value = new String(data, index + lengthOfLength + 1, length);
        //128-183 如果是数据是1-55字节以内的
        } else if ((data[index] & 0xFF) > OFFSET_SHORT_ITEM && (data[index] & 0xFF) < 0xB7) {
        	//这个长度也可以改为我的写法，也没问题
            byte length = (byte) ((data[index] & 0xFF) - OFFSET_SHORT_ITEM);
            //取除了长度位以外的部分
            value = new String(data, index + 1, length);

        } else {
            throw new RuntimeException("wrong decode attempt");
        }
        return value;
    }


    //类似，先不细看了
    private static byte[] decodeItemBytes(byte[] data, int index) {

        byte[] value = null;
        int length = 0;

        if ((data[index] & 0xFF) >= 0xB7 && (data[index] & 0xFF) < OFFSET_SHORT_LIST) {

            byte lengthOfLength = (byte) (data[index] - 0xB7);
            length = calcLengthRaw(lengthOfLength, data, index);

        } else if ((data[index] & 0xFF) > OFFSET_SHORT_ITEM && (data[index] & 0xFF) < 0xB7) {

            length = (byte) (data[index] - OFFSET_SHORT_ITEM);

        } else {
            throw new RuntimeException("wrong decode attempt");
        }
        byte[] valueBytes = new byte[length];
        System.arraycopy(data, index, valueBytes, 0, length);
        value = valueBytes;
        return value;
    }

    //大整数也类似
    public static BigInteger decodeBigInteger(byte[] data, int index) {

        BigInteger value = null;
        int length = 0;

        if ((data[index] & 0xFF) >= 0xB7 && (data[index] & 0xFF) < OFFSET_SHORT_LIST) {

            byte lengthOfLength = (byte) (data[index] - 0xB7);
            length = calcLengthRaw(lengthOfLength, data, index);

        } else if ((data[index] & 0xFF) > OFFSET_SHORT_ITEM && (data[index] & 0xFF) < 0xB7) {

            length = (byte) (data[index] - OFFSET_SHORT_ITEM);

        } else {
            throw new RuntimeException("wrong decode attempt");
        }
        byte[] valueBytes = new byte[length];
        System.arraycopy(data, index, valueBytes, 0, length);
        value = new BigInteger(valueBytes);
        return value;
    }
    
    //与decodeByteArray类似
    private static byte[] decodeByteArray(byte[] data, int index) {

        byte[] value = null;
        int length = 0;

        if ((data[index] & 0xFF) >= 0xB7 && (data[index] & 0xFF) < OFFSET_SHORT_LIST) {

            byte lengthOfLength = (byte) (data[index] - 0xB7);
            length = calcLengthRaw(lengthOfLength, data, index);

        } else if ((data[index] & 0xFF) > OFFSET_SHORT_ITEM && (data[index] & 0xFF) < 0xB7) {

            length = (byte) (data[index] - OFFSET_SHORT_ITEM);
           
        } else {
            throw new RuntimeException("wrong decode attempt");
        }
        byte[] valueBytes = new byte[length];
        System.arraycopy(data, index, valueBytes, 0, length);
        value = valueBytes;
        return value;
    }
    
    private static int nextItemLength(byte[] data, int index) {

        if (index >= data.length)
            return -1;

        if ((data[index] & 0xFF) >= 0xF7) {
            byte lengthOfLength = (byte) (data[index] - 0xF7);
            
            int length = calcLength(lengthOfLength, data, index);
            return length;
        }
        if ((data[index] & 0xFF) >= OFFSET_SHORT_LIST && (data[index] & 0xFF) < 0xF7) {

            byte length = (byte) ((data[index] & 0xFF) - OFFSET_SHORT_LIST);
            return length;
        }
        if ((data[index] & 0xFF) >= 0xB7 && (data[index] & 0xFF) < OFFSET_SHORT_LIST) {

            byte lengthOfLength = (byte) (data[index] - 0xB7);
            int length = calcLength(lengthOfLength, data, index);
            return length;
        }
        if ((data[index] & 0xFF) > OFFSET_SHORT_ITEM && (data[index] & 0xFF) < 0xB7) {

            byte length = (byte) ((data[index] & 0xFF) - OFFSET_SHORT_ITEM);
            return length;
        }
        if ((data[index] & 0xFF) == OFFSET_SHORT_ITEM) {
            return 1;
        }
        if ((data[index] & 0xFF) < OFFSET_SHORT_ITEM) {
            return 1;
        }
        return -1;
    }

    public static byte[] decodeIP4Bytes(byte[] data, int index) {

        int length = (data[index] & 0xFF) - OFFSET_SHORT_LIST;
        int offset = 1;

        byte aByte = decodeOneByteItem(data, index + offset);

        if ((data[index + offset] & 0xFF) > OFFSET_SHORT_ITEM)
            offset = offset + 2;
        else
            offset = offset + 1;
        byte bByte = decodeOneByteItem(data, index + offset);

        if ((data[index + offset] & 0xFF) > OFFSET_SHORT_ITEM)
            offset = offset + 2;
        else
            offset = offset + 1;
        byte cByte = decodeOneByteItem(data, index + offset);

        if ((data[index + offset] & 0xFF) > OFFSET_SHORT_ITEM)
            offset = offset + 2;
        else
            offset = offset + 1;
        byte dByte = decodeOneByteItem(data, index + offset);

        // return IP address
        return new byte[] { aByte, bByte, cByte, dByte } ;
    }

    //获得有效载荷中的第一个元素
    public static int getFirstListElement(byte[] payload, int pos) {

        if (pos >= payload.length)
            return -1;

        if ((payload[pos] & 0xFF) >= 0xF7) {
            byte lengthOfLength = (byte) (payload[pos] - 0xF7);
            return pos + lengthOfLength + 1;
        }
        if ((payload[pos] & 0xFF) >= OFFSET_SHORT_LIST && (payload[pos] & 0xFF) < 0xF7) {
            return pos + 1;
        }        
        if ((payload[pos] & 0xFF) >= 0xB7 && (payload[pos] & 0xFF) < OFFSET_SHORT_LIST) {
            byte lengthOfLength = (byte) (payload[pos] - 0xB7);
            return pos + lengthOfLength + 1;
        }

        return -1;
    }

    public static int getNextElementIndex(byte[] payload, int pos) {

        if (pos >= payload.length)
            return -1;

        if ((payload[pos] & 0xFF) >= 0xF7) {
            byte lengthOfLength = (byte) (payload[pos] - 0xF7);
            int length = calcLength(lengthOfLength, payload, pos);
            return pos + lengthOfLength + length + 1;
        }
        if ((payload[pos] & 0xFF) >= OFFSET_SHORT_LIST && (payload[pos] & 0xFF) < 0xF7) {

            byte length = (byte) ((payload[pos] & 0xFF) - OFFSET_SHORT_LIST);
            return pos + 1 + length;
        }
        if ((payload[pos] & 0xFF) >= 0xB7 && (payload[pos] & 0xFF) < OFFSET_SHORT_LIST) {

            byte lengthOfLength = (byte) (payload[pos] - 0xB7);
            int length = calcLength(lengthOfLength, payload, pos);
            return pos + lengthOfLength + length + 1;
        }
        if ((payload[pos] & 0xFF) > OFFSET_SHORT_ITEM && (payload[pos] & 0xFF) < 0xB7) {

            byte length = (byte) ((payload[pos] & 0xFF) - OFFSET_SHORT_ITEM);
            return pos + 1 + length;
        }
        if ((payload[pos] & 0xFF) == OFFSET_SHORT_ITEM) {
            return pos + 1;
        }
        if ((payload[pos] & 0xFF) < OFFSET_SHORT_ITEM) {
            return pos + 1;
        }
        return -1;
    }

    /**
     * Get exactly one message payload
     */
	public static void fullTraverse(byte[] msgData, int level, int startPos,
			int endPos, int levelToIndex, Queue<Integer> index) {
		
        try {

        	if (msgData == null || msgData.length == 0)
                return;
            int pos = startPos;

            while (pos < endPos) {

                if (level == levelToIndex)
                    index.add(new Integer(pos));

                // It's a list with a payload more than 55 bytes
                // data[0] - 0xF7 = how many next bytes allocated
                // for the length of the list
                if ((msgData[pos] & 0xFF) >= 0xF7) {

                    byte lengthOfLength = (byte) (msgData[pos] - 0xF7);
                    int length = calcLength(lengthOfLength, msgData, pos);

                    // now we can parse an item for data[1]..data[length]
                    System.out.println("-- level: [" + level
                            + "] Found big list length: " + length);

                    fullTraverse(msgData, level + 1, pos + lengthOfLength + 1,
                            pos + lengthOfLength + length, levelToIndex, index);

                    pos += lengthOfLength + length + 1;
                    continue;
                }
                // It's a list with a payload less than 55 bytes
                if ((msgData[pos] & 0xFF) >= OFFSET_SHORT_LIST
                        && (msgData[pos] & 0xFF) < 0xF7) {

                    byte length = (byte) ((msgData[pos] & 0xFF) - OFFSET_SHORT_LIST);

                    System.out.println("-- level: [" + level
                            + "] Found small list length: " + length);

                    fullTraverse(msgData, level + 1, pos + 1, pos + length + 1,
                            levelToIndex, index);

                    pos += 1 + length;
                    continue;
                }
                // It's an item with a payload more than 55 bytes
                // data[0] - 0xB7 = how much next bytes allocated for
                // the length of the string
                if ((msgData[pos] & 0xFF) >= 0xB7
                        && (msgData[pos] & 0xFF) < OFFSET_SHORT_LIST) {

                    byte lengthOfLength = (byte) (msgData[pos] - 0xB7);
                    int length = calcLength(lengthOfLength, msgData, pos);

                    // now we can parse an item for data[1]..data[length]
                    System.out.println("-- level: [" + level
                            + "] Found big item length: " + length);
                    pos += lengthOfLength + length + 1;

                    continue;
                }
                // It's an item less than 55 bytes long,
                // data[0] - 0x80 == length of the item
                if ((msgData[pos] & 0xFF) > OFFSET_SHORT_ITEM
                        && (msgData[pos] & 0xFF) < 0xB7) {

                    byte length = (byte) ((msgData[pos] & 0xFF) - OFFSET_SHORT_ITEM);

                    System.out.println("-- level: [" + level
                            + "] Found small item length: " + length);
                    pos += 1 + length;
                    continue;
                }
                // null item
                if ((msgData[pos] & 0xFF) == OFFSET_SHORT_ITEM) {
                    System.out.println("-- level: [" + level
                            + "] Found null item: ");
                    pos += 1;
                    continue;
                }
                // single byte item
                if ((msgData[pos] & 0xFF) < OFFSET_SHORT_ITEM) {
                    System.out.println("-- level: [" + level
                            + "] Found single item: ");
                    pos += 1;
                    continue;
                }
            }
        } catch (Throwable th) {
            throw new RuntimeException("wire packet not parsed correctly",
                    th.fillInStackTrace());
        }
    }

	//这两个方法是一模一样的
    private static int calcLength(int lengthOfLength, byte[] msgData, int pos) {
	    byte pow = (byte) (lengthOfLength - 1);
	    int length = 0;
	    for (int i = 1; i <= lengthOfLength; ++i) {
	        length += (msgData[pos + i] & 0xFF) << (8 * pow);
	        pow--;
	    }
	    return length;
    }
 
    private static int calcLengthRaw(int lengthOfLength, byte[] msgData, int index) {
        byte pow = (byte) (lengthOfLength - 1);
        int length = 0;
        //像之前求值一样，先求最前面八位，再求接下来八位，...一直继续下去
        for (int i = 1; i <= lengthOfLength; ++i) {
            length += msgData[index + i] << (8 * pow);
            pow--;
        }
        return length;
    }
      
   //命令码可能是在一个列表中一起传过来的，而命令码应该是一个小于127的数，getFirstListElement是获得list中的第一个元素也就是命令码
    public static byte getCommandCode(byte[] data) {
        byte command = 0;
        int index = getFirstListElement(data, 0);
        command = data[index];
        command = ((int) (command & 0xFF) == OFFSET_SHORT_ITEM) ? 0 : command;
        return command;
    }
    
    /**
     * Parse wire byte[] message into RLP elements
     *
     * @param msgData
     *            - raw RLP data
     * @param rlpList
     *            - outcome of recursive RLP structure
     */
    public static RLPList decode2(byte[] msgData) {
    	RLPList rlpList = new RLPList();
        fullTraverse(msgData, 0, 0, msgData.length, 1, rlpList);
        return rlpList;
    }

    /**
     * Get exactly one message payload
     */
	private static void fullTraverse(byte[] msgData, int level, int startPos,
			int endPos, int levelToIndex, RLPList rlpList) {

        try {
            if (msgData == null || msgData.length == 0)
                return;
            int pos = startPos;

            while (pos < endPos) {
                // It's a list with a payload more than 55 bytes
                // data[0] - 0xF7 = how many next bytes allocated
                // for the length of the list
                if ((msgData[pos] & 0xFF) >= 0xF7) {

                    byte lengthOfLength = (byte) (msgData[pos] - 0xF7);
                    int length = calcLength(lengthOfLength, msgData, pos);

                    byte[] rlpData = new byte[lengthOfLength + length + 1];
                    System.arraycopy(msgData, pos, rlpData, 0, lengthOfLength
                            + length + 1);

                    RLPList newLevelList = new RLPList();
                    newLevelList.setRLPData(rlpData);

                    // todo: this done to get some data for testing should be
                    // delete
                    // byte[] subList = Arrays.copyOfRange(msgData, pos, pos +
                    // lengthOfLength + length + 1);
                    // System.out.println(Utils.toHexString(subList));

                    fullTraverse(msgData, level + 1, pos + lengthOfLength + 1,
                            pos + lengthOfLength + length + 1, levelToIndex,
                            newLevelList);
                    rlpList.add(newLevelList);

                    pos += lengthOfLength + length + 1;
                    continue;
                }
                // It's a list with a payload less than 55 bytes
                if ((msgData[pos] & 0xFF) >= OFFSET_SHORT_LIST
                        && (msgData[pos] & 0xFF) < 0xF7) {

                    byte length = (byte) ((msgData[pos] & 0xFF) - OFFSET_SHORT_LIST);

                    byte[] rlpData = new byte[length + 1];
                    System.arraycopy(msgData, pos, rlpData, 0, length + 1);

                    RLPList newLevelList = new RLPList();
                    newLevelList.setRLPData(rlpData);

                    if (length > 0)
                        fullTraverse(msgData, level + 1, pos + 1, pos + length
                                + 1, levelToIndex, newLevelList);
                    rlpList.add(newLevelList);

                    pos += 1 + length;
                    continue;
                }
                // It's an item with a payload more than 55 bytes
                // data[0] - 0xB7 = how much next bytes allocated for
                // the length of the string
                if ((msgData[pos] & 0xFF) >= 0xB7
                        && (msgData[pos] & 0xFF) < OFFSET_SHORT_LIST) {

                    byte lengthOfLength = (byte) (msgData[pos] - 0xB7);
                    int length = calcLength(lengthOfLength, msgData, pos);

                    // now we can parse an item for data[1]..data[length]
                    byte[] item = new byte[length];
                    System.arraycopy(msgData, pos + lengthOfLength + 1, item,
                            0, length);

                    byte[] rlpPrefix = new byte[lengthOfLength + 1];
                    System.arraycopy(msgData, pos, rlpPrefix, 0,
                            lengthOfLength + 1);

                    RLPItem rlpItem = new RLPItem(item);
                    rlpList.add(rlpItem);
                    pos += lengthOfLength + length + 1;

                    continue;
                }
                // It's an item less than 55 bytes long,
                // data[0] - 0x80 == length of the item
                if ((msgData[pos] & 0xFF) > OFFSET_SHORT_ITEM
                        && (msgData[pos] & 0xFF) < 0xB7) {

                    byte length = (byte) ((msgData[pos] & 0xFF) - OFFSET_SHORT_ITEM);

                    byte[] item = new byte[length];
                    System.arraycopy(msgData, pos + 1, item, 0, length);

                    byte[] rlpPrefix = new byte[2];
                    System.arraycopy(msgData, pos, rlpPrefix, 0, 2);

                    RLPItem rlpItem = new RLPItem(item);
                    rlpList.add(rlpItem);
                    pos += 1 + length;

                    continue;
                }
                // null item
                if ((msgData[pos] & 0xFF) == OFFSET_SHORT_ITEM) {
                    byte[] item = new byte[0];
                    RLPItem rlpItem = new RLPItem(item);
                    rlpList.add(rlpItem);
                    pos += 1;
                    continue;
                }
                // single byte item
                if ((msgData[pos] & 0xFF) < OFFSET_SHORT_ITEM) {

                    byte[] item = { (byte) (msgData[pos] & 0xFF) };

                    RLPItem rlpItem = new RLPItem(item);
                    rlpList.add(rlpItem);
                    pos += 1;
                    continue;
                }
            }
        } catch (Throwable th) {
			throw new RuntimeException("wire packet not parsed correctly",
					th.fillInStackTrace());
        }
    }
	
	/**
	 * Reads any RLP encoded byte-array and returns all objects as byte-array or list of byte-arrays
	 * 
	 * @param data RLP encoded byte-array 
	 * @param pos position in the array to start reading
	 * @return DecodeResult encapsulates the decoded items as a single Object and the final read position
	 */
	public static DecodeResult decode(byte[] data, int pos) {
		if (data == null || data.length < 1) {
			return null;
		}
		
		int prefix = data[pos] & 0xFF;
		if (prefix == OFFSET_SHORT_ITEM) {
			return new DecodeResult(pos+1, new byte[0]); // means no length or 0
		} else if (prefix < OFFSET_SHORT_ITEM) {
			return new DecodeResult(pos+1, new byte[] { data[pos] }); // byte is its own RLP encoding
		} else if (prefix < OFFSET_LONG_ITEM){
			int len = prefix - OFFSET_SHORT_ITEM; // length of the encoded bytes
			return new DecodeResult(pos+1+len, copyOfRange(data, pos+1, pos+1+len));
		} else if (prefix < OFFSET_SHORT_LIST) {
			int lenlen = prefix - OFFSET_LONG_ITEM + 1; // length of length the encoded bytes
			int lenbytes = byteArrayToInt(copyOfRange(data, pos+1, pos+1+lenlen)); // length of encoded bytes
			return new DecodeResult(pos+1+lenlen+lenbytes, copyOfRange(data, pos+1+lenlen, pos+1+lenlen+lenbytes));
		} else if (prefix < OFFSET_LONG_LIST) {
			int len = prefix - OFFSET_SHORT_LIST; // length of the encoded list
			int prevPos = pos; pos++;
			return decodeList(data, pos, prevPos, len);
		} else if (prefix < 0xFF) {
			int lenlen = prefix - OFFSET_LONG_LIST + 1; // length of length the encoded list
			int lenlist = byteArrayToInt(copyOfRange(data, pos+1, pos+1+lenlen)); // length of encoded bytes
		    pos = pos + lenlen + 1;
		    int prevPos = lenlist;
		    return decodeList(data, pos, prevPos, lenlist);
		} else {
			throw new RuntimeException("Only byte values between 0x00 and 0xFF are supported, but got: " + prefix);
		}
	}
		
	private static DecodeResult decodeList(byte[] data, int pos, int prevPos, int len) {
		List<Object> slice = new ArrayList<Object>();
		for (int i = 0; i < len;) {
			// Get the next item in the data list and append it
			DecodeResult result = decode(data, pos);
			slice.add(result.getDecoded());
			// Increment pos by the amount bytes in the previous read
			prevPos = result.getPos();
	        i += (prevPos - pos);
	        pos = prevPos;
		}
		return new DecodeResult(pos, slice.toArray());
	}
			
	/* ******************************************************
	 * 						ENCODING						*
	 * ******************************************************/
	
	/**
	 * Turn Object into its RLP encoded equivalent of a byte-array
	 * Support for String, Integer, BigInteger and Lists of any of these types.
	 * 
	 * @param item as object or List of objects
	 * @return byte[] RLP encoded 
	 */
	public static byte[] encode(Object input) {
		Value val = new Value(input);
		if (val.isList()) {
			List<Object> inputArray = val.asList();
			if (inputArray.size() == 0) {
				return encodeLength(inputArray.size(), OFFSET_SHORT_LIST);
			}
			byte[] output = new byte[0];
			for (Object object : inputArray) {
				output = concatenate(output, encode(object));
			}
			byte[] prefix = encodeLength(output.length, OFFSET_SHORT_LIST);
			return concatenate(prefix, output);
		} else {
			byte[] inputAsBytes = toBytes(input); 
			if(inputAsBytes.length == 1) {
				return inputAsBytes;
			} else {
				byte[] firstByte = encodeLength(inputAsBytes.length, OFFSET_SHORT_ITEM);				
				return concatenate(firstByte, inputAsBytes);
			}
		} 
	}
	
	/** Integer limitation goes up to 2^31-1 so length can never be bigger than MAX_ITEM_LENGTH */
	public static byte[] encodeLength(int length, int offset) {
		if (length < SIZE_THRESHOLD) {
			byte firstByte = (byte) (length + offset);
			return new byte[] { firstByte };
		} else if (length < MAX_ITEM_LENGTH) {
			byte[] binaryLength;
			if(length > 0xFF)
				binaryLength = BigInteger.valueOf(length).toByteArray();
			else 
				binaryLength = new byte[] { (byte) length };
			byte firstByte = (byte) (binaryLength.length + offset + SIZE_THRESHOLD - 1 );
			return concatenate(new byte[] { firstByte }, binaryLength);
		} else {
			throw new RuntimeException("Input too long");
		}
	}
	
    public static byte[] encodeByte(byte singleByte) {
    	//如果是0，那么就编码为0x80
        if ((singleByte & 0xFF) == 0) {
            return new byte[] { (byte) OFFSET_SHORT_ITEM };
        //如果小于127，那么就不动
        } else if ((singleByte & 0xFF) < 0x7F) {
            return new byte[] { singleByte };
        //如果大于等于127
        } else {
            return new byte[] { (byte) (OFFSET_SHORT_ITEM+1), singleByte };
        }
    }

    public static byte[] encodeShort(short singleShort) {
        if (singleShort <= 0xFF)
            return encodeByte((byte) singleShort);
        else {
			return new byte[] { (byte) (OFFSET_SHORT_ITEM + 2),
					(byte) (singleShort >> 8 & 0xFF),
					(byte) (singleShort >> 0 & 0xFF) };
        }
    }

    //把字符串转为字节数组即可
    public static byte[] encodeString(String srcString) {
        return encodeElement(srcString.getBytes());
    }
    
    //对大整数进行encode，如果是0，直接encode0,如果不是0就转成字节数组
    public static byte[] encodeBigInteger(BigInteger srcBigInteger) {
    	if(srcBigInteger == BigInteger.ZERO) 
    		return encodeByte((byte)0);
    	else 
    		return encodeElement(srcBigInteger.toByteArray());
    }

    //对字节数组进行encode
    public static byte[] encodeElement(byte[] srcData) {

        if (srcData == null){
        	return new byte[] { 0x00 };
        //如果小于等于55，那么就只用一个字节来表示长度
        } else if (srcData.length <= 0x37) {
            // length = 8X
        	//长度就是128 + 数据的字节长度，把length放到第一位
            byte length = (byte) (OFFSET_SHORT_ITEM + srcData.length);
            byte[] data = Arrays.copyOf(srcData, srcData.length + 1);
            System.arraycopy(data, 0, data, 1, srcData.length);
            data[0] = length;

            return data;
        //如果大于55，那么就有两个长度，一个数据的长度，一个长度的长度
        } else {
            // length of length = BX
            // prefix = [BX, [length]]
            int tmpLength = srcData.length;
            byte byteNum = 0;
            while (tmpLength != 0) {
                ++byteNum;
                tmpLength = tmpLength >> 8;
            }
            byte[] lenBytes = new byte[byteNum];
            for (int i = 0; i < byteNum; ++i) {
                lenBytes[0] = (byte) ((srcData.length >> (8 * i)) & 0xFF);
            }
            // first byte = F7 + bytes.length
            byte[] data = Arrays.copyOf(srcData, srcData.length + 1 + byteNum);
            System.arraycopy(data, 0, data, 1 + byteNum, srcData.length);
            data[0] = (byte) (0xB7 + byteNum);
            System.arraycopy(lenBytes, 0, data, 1, lenBytes.length);

            return data;
        }
    }

    //对list进行encode，三个点表示可以接受多个实际参数，实际上相当于接受byte[][] elements
    public static byte[] encodeList(byte[]... elements) {

        int totalLength = 0;
        //把list中的所有元素的长度都加起来
        for (int i = 0; i < elements.length; ++i) {
            totalLength += elements[i].length;
        }

        byte[] data;
        int copyPos = 0;
        //小于55
        if (totalLength <= 0x37) {

        	data = new byte[1 + totalLength];
            data[0] = (byte) (OFFSET_SHORT_LIST + totalLength);
            copyPos = 1;
            //长度大于55
        } else {
            // length of length = BX
            // prefix = [BX, [length]]
            int tmpLength = totalLength;
            byte byteNum = 0;
            while (tmpLength != 0) {
                ++byteNum;
                tmpLength = tmpLength >> 8;
            }
            tmpLength = totalLength;
            byte[] lenBytes = new byte[byteNum];
            for (int i = 0; i < byteNum; ++i) {
                lenBytes[i] = (byte) ((tmpLength >> (8 * i)) & 0xFF);
            }
            // first byte = F7 + bytes.length 
            data = new byte[1 + lenBytes.length + totalLength];
            data[0] = (byte) (0xF7 + byteNum);
            System.arraycopy(lenBytes, 0, data, 1, lenBytes.length);

            copyPos = lenBytes.length + 1;
        }
        //这里写得有问题
        for (int i = 0; i < elements.length; ++i) {
            System.arraycopy(elements[i], 0, data, copyPos, elements[i].length);
            copyPos += elements[i].length;
        }
        return data;
    }
    

    
    
    /*
     *	Utility function to convert Objects into byte arrays 
     */
	private static byte[] toBytes(Object input) {
		if (input instanceof byte[]) {
			return (byte[]) input;
		} else if (input instanceof String) {
			String inputString = (String) input;
			return inputString.getBytes();
		} else if(input instanceof Long) {
			Long inputLong = (Long) input;	
			return (inputLong == 0) ? new byte[0] : BigInteger.valueOf(inputLong).toByteArray();
		} else if(input instanceof Integer) {
			Integer inputInt = (Integer) input;	
			return (inputInt == 0) ? new byte[0] : BigInteger.valueOf(inputInt.longValue()).toByteArray();
		} else if(input instanceof BigInteger) {
			BigInteger inputBigInt = (BigInteger) input;
			return (inputBigInt == BigInteger.ZERO) ? new byte[0] : inputBigInt.toByteArray();
		} else if (input instanceof Value) {
			Value val = (Value) input;
			return toBytes(val.asObj());
		}
		throw new RuntimeException("Unsupported type: Only accepting String, Integer and BigInteger for now");
	}
}
```