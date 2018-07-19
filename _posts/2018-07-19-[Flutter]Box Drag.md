---
layout: post
category: flutter
title: "[Flutter]박스를 드래그해서 색 변경하기"
---

[Tensor Programming](https://www.youtube.com/watch?v=On2Oew0NdGo&index=9&list=PLJbE2Yu2zumDqr_-hqpAN0nIr6m14TAsd)님의 강의를 바탕으로 정리한 내용입니다.

<img src="https://user-images.githubusercontent.com/35518072/42930220-6213d0c2-8b77-11e8-817d-a49816b246d8.gif" width="300px">

# Positioned

> A [Positioned](https://docs.flutter.io/flutter/widgets/Positioned-class.html) widget must be a descendant of a [Stack](https://docs.flutter.io/flutter/widgets/Stack-class.html), and the path from the [Positioned](https://docs.flutter.io/flutter/widgets/Positioned-class.html) widget to its enclosing [Stack](https://docs.flutter.io/flutter/widgets/Stack-class.html) must contain only [StatelessWidget](https://docs.flutter.io/flutter/widgets/StatelessWidget-class.html)s or [StatefulWidget](https://docs.flutter.io/flutter/widgets/StatefulWidget-class.html)s (not other kinds of widgets, like[RenderObjectWidget](https://docs.flutter.io/flutter/widgets/RenderObjectWidget-class.html)s). 

Stack의 자식이어야 하며 해당 Stack은 StatelessWidget이나 StatefulWidget의 자식이어야 한다는 것이 레퍼런스의 설명이다. 기본적인 역할은 Stack의 children들이 그 안에서 어떤 위치에 있는지를 정해주는 것이라고 보면 된다. 속성은 그에 맞는 것들이 있다.

* width/height : child의 너비/높이를 정한다.
* left/right/top/bottom : Stack으로부터 어느 방향으로 얼마나 떨어져있는지를 정한다.

<br>

# Draggable과 DragTarget

Draggable은 드래그 하는 대상이고 DragTarget은 드래그를 받는 대상이라고 보면 된다. Draggable을 통해서 원하는 데이터를 DragTarget에 전달할 수 있으며 DragTarget은 해당 데이터를 받을지 말지 선택할 수 있다. 여기서 사용하는 속성만 간단히 보자.

* **Draggable**
  * data : 전달하고자 하는 데이터
  * feedback : 드래그를 할 때 나타나는 모양을 정의할 수 있음
  * onDraggableCanceled : DragTarget이 데이터를 받지 않고 취소됬을 경우의 콜백
* **DragTarget**
  * builder : 어떻게 생겼나를 정의
  * onAccept : 받을수 있는 Draggable로부터 전달될 경우의 콜백

<br>

# DragBox

먼저 드래그가 될 박스를 지칭하는 클래스를 정의하자. 해당 클래스는 Stack안에서의 좌표와 라벨, 그리고 색깔을 가지고 있다.

```dart
class DragBox extends StatefulWidget {
	final Offset initPos;
	final String label;
	final Color itemColor;

	DragBox(this.initPos, this.label, this.itemColor);

    @override
    _DragBoxState createState() => _DragBoxState();
}
```

<br>

# _DragBoxState

```dart
class _DragBoxState extends State<DragBox> {

	Offset position = Offset(0.0, 0.0);

	@override
	void initState() {
		super.initState();
		position = widget.initPos;
	}

  @override
  Widget build(BuildContext context) {
    return Positioned(
	    left: position.dx,
	    top: position.dy,
	    child: Draggable(
		    data: widget.itemColor,
		    child: Container(
			    width: 100.0,
			    height: 100.0,
			    color: widget.itemColor,
			    child: Center(
				    child: Text(
					    widget.label,
					    style: TextStyle(
						    color: Colors.white,
						    decoration: TextDecoration.none,
						    fontSize: 20.0
					    ),
				    ),
			    ),
		    ),

		    onDraggableCanceled: (velocity, offset) {
		    	setState(() {
		    	    position = offset;
		    	});
		    },
		    feedback: Container(
			    width: 120.0,
			    height: 120.0,
			    color: widget.itemColor.withOpacity(0.5),
			    child: Center(
				    child: Text(
					    widget.label,
					    style: TextStyle(
						    color: Colors.white,
						    decoration: TextDecoration.none,
						    fontSize: 18.0
					    ),
				    ),
			    ),
		    ),
	    ),
    );
  }
}
```

복잡해보이지만 과정을 살펴보면 간단하다.

* 자신만의 Offset 값인 position을 갖고 initState()를 오버라이딩 해서DragBox의 값으로 초기화된다.
* 결국은 DragBox는 Positioned 위젯이며 Stack 안의 위치는 position으로 정해지고 데이터는 자기의 색깔이다.
* 몸통은 Container로 100x100의 크기를 갖고 라벨 값이 중앙에 위치한다.
* DragTarget이 데이터를 받지 않을 경우엔 해당 offset에 위치하게 된다.
* 드래그를 하게 될 경우 120x120의 크기와 50%의 투명도가 적용된다.

<br>

# 전체적인 구조

```dart
class _AppState extends State<App> {

	Color caughtColor = Colors.grey;

  @override
  Widget build(BuildContext context) {
    return Stack(
	    children: <Widget>[
			DragBox(Offset(0.0, 0.0), "Box One", Colors.lime),
		    DragBox(Offset(200.0, 0.0), "Box Two", Colors.orange),
		    DragBox(Offset(300.0, 0.0), "Box Three", Colors.green),
		    Positioned(
			    left: 100.0,
			    bottom: 0.0,
			    child: DragTarget(
				    onAccept: (Color color){
				    	caughtColor = color;
				    },
				    builder: (_, List<dynamic> accepted, __){
				    	return Container(
							width: 200.0,
							height: 200.0,
						    decoration: BoxDecoration(
							    color: accepted.isEmpty 
                                ? caughtColor : Colors.grey.shade200
						    ),
						    child: Center(
							    child: Text("Drag Here!"),
						    ),
					    );
				    },
			    ),
		    )
	    ],
    );
  }
}
```

* x방향으로 0,200,300만큼 떨어진 곳에 DragBox를 3개 놓는다.
* 왼쪽에서 100 떨어지고 바닥에 붙어있는 Positioned를 하나 만들어서 DragTarget으로 설정한다.
* DragTarget은 200x200의 Container이며 받고 있는 데이터가 없는 경우는 caughtColor(기본은 회색)로 설정하고 있는 경우엔 희미한 회색으로 설정한다.