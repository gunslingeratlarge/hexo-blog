title: 一个完整的DAO类的示例代码
author: gsal
tags:
  - Java
categories:
  - Java
date: 2018-05-22 20:01:00
---
一个DAO类，一般包括对对应的POJO类的增删改查操作。有这么几个注意点：
1. 引入的是java.sql.xxx，以mysql开头的一律不引入。
1. 可以用PreparedStatement也可以用Statement，如果要设置参数就用PreparedStatement，不设置参数就用Statement
1. try(){}catch(){}这种结构是1.7之后出现的，可以自动帮你关闭在try之后小括号中实现了Closeble接口的对象。
1. 如何在dao中加入了一条数据之后马上获得它的id？通过ps.getGeneratedKeys()获取到自增长id

<!--more-->

``` java
package mymall.dao;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.List;

import mymall.utils.DBUtil;
import mytmall.bean.Category;

public class CategoryDao {
	//提供对category的CRUD add delete update get list list(start,count), gettotal
	//后面全是查，查一个，列所有，最后两个是为了分页用的
	
	public void add(Category bean) {
		String sql = "insert into category values(null,?)";
		
		try(
			Connection c = DBUtil.getConnection();
			PreparedStatement ps = c.prepareStatement(sql);
		) {
			ps.setString(1, bean.getName());
			ps.execute();
			
			ResultSet rs = ps.getGeneratedKeys();
			if (rs.next()) {
				bean.setId(rs.getInt(1));
			}
			
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	
	public void delete(int id) {
		String sql = "delete from category where id = ?";
		try(
			Connection c = DBUtil.getConnection();
			PreparedStatement ps = c.prepareStatement(sql);
		) {
			ps.setInt(1, id);
			ps.execute();
		} catch(SQLException e) {
			e.printStackTrace();
		}
	};
	
	public void update(Category bean) {
		String sql = "update category set name = ? where id = ?";
		try(		
				Connection c = DBUtil.getConnection();
				PreparedStatement ps = c.prepareStatement(sql);
				) {
			ps.setInt(2, bean.getId());
			ps.setString(1, bean.getName());
			ps.execute();
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}
	
	//get单个，查找list，gettotal
	
	public Category get(int id) {
		String sql = "select * from category where id = " + id;
		
		Category bean = new Category();
		try(Connection c = DBUtil.getConnection();
			Statement s = c.createStatement();) {
			ResultSet rs = s.executeQuery(sql);
			if (rs.next()) {
				bean.setId(rs.getInt(1));
				bean.setName(rs.getString(2));
			}
		} catch(SQLException e) {
			e.printStackTrace();
		}
		return bean;
	}
	
	//查询所有
	public List<Category> list() {
		return list(0,Short.MAX_VALUE);
	}
	
	//分页查询
	public List<Category> list(int start, int count) {
		String sql = "select * from category order by id desc limit ?, ?";
		List<Category> beans = new ArrayList<Category>();
		
		try(Connection c = DBUtil.getConnection();
			PreparedStatement ps = c.prepareStatement(sql);
			) {
			ps.setInt(1,start);
			ps.setInt(2,count);
			ResultSet rs = ps.executeQuery();
			while(rs.next()) {
				Category bean = new Category();
				bean.setId(rs.getInt("id"));
				bean.setName(rs.getString("name"));
				beans.add(bean);
			}
		}catch(SQLException e) {
			e.printStackTrace();
		}
		return beans;
	}
	
	
	public int getTotal() {
		String sql = "select count(*) from category";
		int total = 0;
		try(Connection c = DBUtil.getConnection();
				Statement s = c.createStatement();) {
				ResultSet rs = s.executeQuery(sql);
				if (rs.next()) {
					total = rs.getInt(1);
				}
			} catch(SQLException e) {
				e.printStackTrace();
			}
		return total;
	}
	
	
	public static void main(String[] args) {
		CategoryDao dao = new CategoryDao();
		Category category = dao.get(1);
		System.out.println(category.getName());
		
	}
}

```