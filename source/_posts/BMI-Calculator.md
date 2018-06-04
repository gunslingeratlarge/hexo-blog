title: BMI Calculator
author: gsal
date: 2018-05-30 16:33:27
tags:
---
不显示title部分`primaryStage.initStylt(stageStyle.UNDECORATED)`  
String类型保留两位小数`String.format("%.2f",num)`   
好看的ui需要引入jfoenix这个库，真的好看 

我怎样才能拖动一个undecorated的窗口呢？https://stackoverflow.com/questions/11780115/moving-an-undecorated-stage-in-javafx-2
``` java
// allow the clock background to be used to drag the clock around.
final Delta dragDelta = new Delta();
layout.setOnMousePressed(new EventHandler<MouseEvent>() {
  @Override public void handle(MouseEvent mouseEvent) {
    // record a delta distance for the drag and drop operation.
    dragDelta.x = stage.getX() - mouseEvent.getScreenX();
    dragDelta.y = stage.getY() - mouseEvent.getScreenY();
  }
});
layout.setOnMouseDragged(new EventHandler<MouseEvent>() {
  @Override public void handle(MouseEvent mouseEvent) {
    stage.setX(mouseEvent.getScreenX() + dragDelta.x);
    stage.setY(mouseEvent.getScreenY() + dragDelta.y);
  }
});

...

// records relative x and y co-ordinates.
class Delta { double x, y; } 

```

问题：我怎样才能在controller类里面拿到window对象呢？https://stackoverflow.com/questions/13015537/javafx-class-controller-stage-window-reference

``` java
@FXML
private Button closeBtn;
Stage currentStage = (Stage)closeBtn.getScene().getWindow();
currentStage.close();
```