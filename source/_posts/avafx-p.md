title: javafx
author: gsal
tags:
  - Java
  - javafx
  - gui
categories:
  - java
date: 2018-05-28 21:19:00
---
## TreeView
树形列表，树的每一个节点都是TreeItem,总的这棵树是TreeView
``` java

import javafx.application.Application;
import javafx.geometry.Insets;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.TreeItem;
import javafx.scene.control.TreeView;
import javafx.scene.layout.VBox;
import javafx.stage.Stage;

public class Main extends Application {


	Stage window;
	
	public static void main(String[] args) {
		launch(args);
	}
	
	@Override
	public void start(Stage primaryStage) throws Exception {
		primaryStage.setTitle("What's for dinner?");
		window = primaryStage;

		
		//control
		TreeItem<String> root = new TreeItem<>();
		root.setExpanded(true);
		
		TreeView<String> tree = new TreeView<>(root);
		tree.setShowRoot(false);
	
		TreeItem<String> vegetables, meat, onion, banana, tomato, beef, chicken;
		vegetables = makeBranch("vegetables",root);
		meat = makeBranch("meat",root);
		
		makeBranch("onion",vegetables);
		makeBranch("banana",vegetables);
		makeBranch("tomato",vegetables);
		makeBranch("beef",meat);
		makeBranch("chicken",meat);
		
		
		tree.getSelectionModel().selectedItemProperty().addListener(
				(e, oldValue, newValue) -> {
					System.out.println(newValue.getValue());
				}
				);
		
		
		
		Button b = new Button("choose");
		b.setOnAction(e -> {
			
			TreeItem<String> item = tree.getSelectionModel().getSelectedItem();
			System.out.println(item.getValue());
			
		});
		//layout 
		VBox layout = new VBox(10);
		layout.setPadding(new Insets(20,20,20,20));
		layout.getChildren().addAll(tree,b);
		Scene scene = new Scene(layout,300,300);
		
		window.setScene(scene);
		
		
		window.show();		
	}
	//make branch: a leaf is also a branch(a TreeItem object)
	private TreeItem<String> makeBranch(String title, TreeItem<String> parent) {
		TreeItem<String> item = new TreeItem<>(title);
		parent.getChildren().add(item);
		parent.setExpanded(true);
		return item;
	}
}

```

## TableView
tableview还是比较简单的，主要是有几个column需要添加进去。
```

import javafx.application.Application;
import javafx.collections.FXCollections;
import javafx.collections.ObservableList;
import javafx.geometry.Insets;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.SelectionMode;
import javafx.scene.control.TableColumn;
import javafx.scene.control.TableView;
import javafx.scene.control.TextField;
import javafx.scene.control.cell.PropertyValueFactory;
import javafx.scene.layout.HBox;
import javafx.scene.layout.VBox;
import javafx.stage.Stage;

public class Main extends Application {


	Stage window;
	TextField nameInput;
	TextField quantityInput;
	TextField priceInput;
	TableView<Product> table;
	
	public static void main(String[] args) {
		launch(args);
	}
	
	@Override
	public void start(Stage primaryStage) throws Exception {
		primaryStage.setTitle("What's for dinner?");
		window = primaryStage;

		
		//control
		
		
		//table columns: column name, data source, column data type
		//name column:
		TableColumn<Product, String> nameColumn = new TableColumn<>("商品名称");
		nameColumn.setMinWidth(200);
		nameColumn.setCellValueFactory(new PropertyValueFactory<>("name"));
		

		TableColumn<Product, Integer> quantityColumn = new TableColumn<>("商品数量");
		quantityColumn.setMinWidth(100);
		quantityColumn.setCellValueFactory(new PropertyValueFactory<>("quantity"));
		

		TableColumn<Product, Double> priceColumn = new TableColumn<>("商品价格");
		priceColumn.setMinWidth(150);
		priceColumn.setCellValueFactory(new PropertyValueFactory<>("price"));
		
		//add columns to tableview:add items(data source),add columns
		table = new TableView<>();
		table.setItems(getProducts());
		table.getColumns().addAll(nameColumn,quantityColumn,priceColumn);
		table.getSelectionModel().setSelectionMode(SelectionMode.MULTIPLE);
		
		
		//add edit area in hbox
		HBox hbox = new HBox(10);
		hbox.setPadding(new Insets(10,10,10,10));
		hbox.setSpacing(10);
		//nameInput, quantityInput, priceInput, addButton, deleteButton
		nameInput = new TextField();
		nameInput.setPromptText("商品名称");
		nameInput.setMinWidth(150);
		quantityInput = new TextField();
		quantityInput.setPromptText("商品数量");
		quantityInput.setMinWidth(150);
		priceInput = new TextField();
		priceInput.setPromptText("价格");
		quantityInput.setMinWidth(150);
		
		//button
		Button addButton = new Button("添加");
		Button deleteButton = new Button("删除");
		addButton.setOnAction(e -> {
			addButtonClicked();
		});
		deleteButton.setOnAction(e -> {
			deleteButtonClicked();
		});
		
		
		hbox.getChildren().addAll(nameInput,quantityInput,priceInput,addButton,deleteButton);
		
		
		//layout 
		VBox layout = new VBox();
		//layout.setPadding(new Insets(20,20,20,20));
		layout.getChildren().addAll(table,hbox);
		layout.setMaxHeight(500);
		Scene scene = new Scene(layout);
	
		
		window.setScene(scene);
		
		
		window.show();
		
		
		
	}

	
	private void addButtonClicked() {
		Product p = new Product();
	
		p.setName(nameInput.getText());
		p.setQuantity(Integer.parseInt(quantityInput.getText()));
		p.setPrice(Double.parseDouble(priceInput.getText()));
		
		table.getItems().add(p);
		nameInput.clear();
		quantityInput.clear();
		priceInput.clear();
		
	}
	
	
	private void deleteButtonClicked() {
		ObservableList<Product> selected,allProducts;
		allProducts = FXCollections.observableArrayList();
		selected = table.getSelectionModel().getSelectedItems();
		
		for (Product p: table.getItems()) {
			allProducts.add(p);
		}
		
		for (Product p: selected) {
			System.out.print("this is selected:");
			System.out.println(p.getName());
		}
		
		for (Product p: selected) {
			System.out.println("going to delete: " + p.getName());
			allProducts.remove(p);
		}

		System.out.println("deleted:");
		for (Product p: allProducts) {
			System.out.println(p.getName());
		}
		System.out.println("---------");
		
		table.setItems(allProducts);
		
	
		
		
	
	}

	//prepare data for tableview
	public ObservableList<Product> getProducts() {
		ObservableList<Product> products = FXCollections.observableArrayList();
 		products.add(new Product("laptop",20,2000d));
 		products.add(new Product("soap",10,3d));
 		products.add(new Product("watermelon",2,40d));
 		products.add(new Product("bread",5,20d));
 		products.add(new Product("glasses",8,180d));
 		return products;
	}
}



```