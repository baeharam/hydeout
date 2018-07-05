---
layout: post
category: flutter
title: "[Flutter]Container"
---

컨테이너는 single-child layout 위젯으로 Padding, Center나 Align 위젯과 동일한 성격을 지닌다. 특정 위젯을 어떤 위치에 위치시키거나 배경색을 칠하고 padding/margin 값을 줄 때 보통 사용된다. 각 속성들을 보면서 어떻게 동작하는지 보자.

[Container](https://docs.flutter.io/flutter/widgets/Container-class.html)

# 색깔 칠하기

색깔을 칠하는데는 2가지 방법이 있고 2가지를 동시에 사용할 수는 없다.

```dart
new Container(
	// BoxDecoration 클래스 이용
    color: new BoxDecoration(
    	color: Colors.yellow
    ),
);
```

```dart
new Container(
	// 단순 속성 이용
    color: Colors.yellow
);
```



# Child 위젯 포함시키기

물론 이름 대로 다른 위젯도 포함시킬 수 있다.

```dart
new Container(
	child: new Container(
            child: new Center(
                    child: new Text(
                        	"다른 위젯 포함 가능!!"
                    )
            )
    )
);
```

child 위젯과의 관계를 잘 이해해야 하는데, 만약 컨테이너에게 child가 없다면 색깔이 적용된 경우 전체 컨네이너에 색깔이 칠해지지만 child가 있는 경우 해당 child의 크기와 색깔이 적용된다. 따라서 다음과 같은 경우는 노란색으로 배경색이 나온다.

```dart
new Container(
	color: new BoxDecoration(color: Colors.blue),
    child: new Container(
    	color: Colors.yellow
    )
);
```



# Margin

child에 margin 값이 적용된 경우는 그만큼 컨테이너의 색깔이 보이게 된다. 

> ...The container is then surrounded by additional empty space described from the margin.

```dart
void main() {
  runApp(new Container(
    decoration: new BoxDecoration(color: Colors.yellow),
    child: new Container(
      color: Colors.red,
      margin: const EdgeInsets.all(50.0),
      child: new Center(
          child: new Text(
        "haram",
        textDirection: TextDirection.ltr,
        style: new TextStyle(fontSize: 50.0, fontWeight: FontWeight.w900),
      )),
    ),
  ));
}
```

<img src="https://user-images.githubusercontent.com/35518072/42257266-c7acea38-7f90-11e8-94c0-9c78c2ea3063.png" height="400px">



# Padding

Padding이 상위 컨테이너에게 적용된 경우 그 만큼 하위 컨테이너의 크기가 줄어들게 된다.

```dart
void main() {
  runApp(new Container(
    decoration: new BoxDecoration(color: Colors.yellow),
    padding: EdgeInsets.all(30.0),
    child: new Container(
      color: Colors.red,
      margin: const EdgeInsets.all(50.0),
      child: new Center(
          child: new Text(
        "haram",
        textDirection: TextDirection.ltr,
        style: new TextStyle(fontSize: 50.0, fontWeight: FontWeight.w900),
      )),
    ),
  ));
}
```

<img src="https://user-images.githubusercontent.com/35518072/42257571-0f2f926a-7f92-11e8-8cea-fc845152a9c2.png" height="400px">



# Alignment

정렬을 적용할 수 있는데 이 부분이 굉장히 헷갈리기 때문에 잘 보아야 한다. 우선 레퍼런스에서 어떻게 설명하고 있는지 보자.

> If the widget has an alignment, and the parent provides unbounded constraints, then the Container tries to size itself around the child.
>
> If the widget has an alignment, and the parent provides bounded constaints, then the Container tries to expand to fit the parent, and then positions the child within itself as per the alignment.

1. 위젯이 정렬속성을 가지고, parent에 제약조건이 없으면 child 주위를 감싼다.
2. 위젯이 정렬속성을 가지고, parent에 제약조건이 있으면 parent의 크기로 확장되며 child를 정렬 속성에 맞게 위치시킨다. 

아래 경우는 첫번째 경우로 위젯이 정렬속성이 있고 parent에 제약조건이 없기 때문에 노란색의 컨테이너가 빨간색의 컨테이너를 감싸고 있다.

```dart
void main() {
  runApp(new Container(
    decoration: new BoxDecoration(color: Colors.yellow),
    alignment: FractionalOffset.center,
    child: new Container(
      color: Colors.red,
      child: const Text(
        "haram",
        textDirection: TextDirection.ltr,
      )),
    ),
  );
}
```

<img src="https://user-images.githubusercontent.com/35518072/42257654-767d048e-7f92-11e8-826c-12cffabf4488.png" height="400px">

이제 2번째 경우로, 위젯에 정렬 속성이 있고 parent에 제약조건이 있는 경우에 parent로 확장되며 정렬 속성이 적용된다.

```dart
void main() {
  runApp(
    new Container(
      decoration: new BoxDecoration(color: Colors.yellow),
      alignment: FractionalOffset.center,
      child: new Container(
        // ....
        alignment: FractionalOffset.center,
      ),
    ),
  );
}
```

<img src="https://user-images.githubusercontent.com/35518072/42258252-c45d09c6-7f95-11e8-914b-384bb746db94.png" height="400px">

만약 parent로 확장시키고 싶지 않다면 width와 height 속성을 이용해 명시적으로 지정해주면 된다.

```dart
void main() {
  runApp(
    new Container(
      decoration: new BoxDecoration(color: Colors.yellow),
      alignment: FractionalOffset.center,
      child: new Container(
        // ...
        width: 200.0,
        height: 200.0,
        //...,
        alignment: FractionalOffset.center,
      ),
    ),
  );
}
```

<img src="https://user-images.githubusercontent.com/35518072/42258299-00b8718a-7f96-11e8-9a03-ffb2d11dd6b6.png" height="400px">



# Transform & foregroundDecoration

컨테이너를 방향에 따라 변형시킬 수도 있고 앞쪽을 꾸밀수도 있으며 변형시킬 때는 [Matrix4](https://docs.flutter.io/flutter/vector_math_64/Matrix4-class.html) 클래스를 사용한다. 다음 예시는 변형과 앞쪽 데코를 같이 적용한 것이다.

```dart
void main() {
  runApp(
    new Container(
      decoration: new BoxDecoration(color: Colors.yellow),
      alignment: FractionalOffset.center,
      child: new Container(
        // ...
        transform: new Matrix4.rotationZ(0.2),
        foregroundDecoration: new BoxDecoration(
          color: Colors.black26
        ),
      ),
    ),
  );
}
```

<img src="https://user-images.githubusercontent.com/35518072/42258637-ba817cfa-7f97-11e8-94fe-8b1a15d5f70d.png" height="400px">



# Constraints

제약조건도 줄 수 있는데 대표적으로 최대 너비/높이, 최소 너비/높이가 있다. 너비/높이가 정해져 있어도 제약조건 기준으로 정해진다.

```dart
void main() {
  runApp(
    new Container(
      // ...
      child: new Container(
        constraints: new BoxConstraints(
          maxHeight: 100.0,
          maxWidth: 100.0,
          minHeight: 50.0,
          minWidth: 50.0
        ),
        // ...
      ),
    ),
  );
}
```

<img src="https://user-images.githubusercontent.com/35518072/42258824-ae087270-7f98-11e8-9aed-ad99e654debd.png" height="400px">