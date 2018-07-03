---
layout: post
category: dart
title: "[Dart]Const Constructor"
---

# const와 final

Dart에는 const constructor라는 생성자가 있다. 먼저 cosnt 키워드에 대해 알아야 하는데 dart에서 const는 compile-time에 값을 정해주는 역할을 한다. final과 굉장히 헷갈렸는데, 둘 다 값이 정해지면 변경할 수 없지만 다른 점이 있다.

* **const** : const 변수 혹은 상수를 이용해 초기화해야 한다.
* **final** : 함수의 리턴값이나 일반 변수로 초기화해도 상관 없다.

다음 코드를 보면 명확히 차이점을 알 수 있다.

```dart
void main(){
    const a = c(); // error
    final b = c(); // 3
}

int c() => 3;
```



# Const constructor

이제 const는 어느 정도 알았는데 생성자에 적용되면 어떤 것을 의미할까? 여기선 canonicalization에 대해 알아야 하는데, 위키백과에 다음과 같이 정의되어 있다.

> A process for converting data that has more than one possible representation into a "standard" canonical representation.

다양한 표현방식을 가진 데이터를 표준의 단일한 표현방식으로 바꾸는 과정으로 const constructor가 하는 일은 "canonicalized" 인스턴스를 만드는 역할을 해서 똑같은 형태의 객체를 생성하면 lookup 테이블에서 만든 객체가 있는지 보고 없으면 생성하고, 있으면 사용하는 형태이다. [Stackoverflow](https://stackoverflow.com/a/21745617/9437175) 에서 정말 잘 설명해주었다. 

다음과 같이 사용할 수 있다.

```dart
class Point { 
  static final Point ORIGIN = const Point(0, 0); 
  final int x; 
  final int y; 
  const Point(this.x, this.y);
  Point.clone(Point other): x = other.x, y = other.y; //[2] 
}

main() { 
  // Assign compile-time constant to p0. 
  Point p0 = Point.ORIGIN; 
  // Create new point using const constructor. 
  Point p1 = new Point(0, 0); 
  // Create new point using non-const constructor.
  Point p2 = new Point.clone(p0); 
  // Assign (the same) compile-time constant to p3. 
  Point p3 = const Point(0, 0); 
  print(identical(p0, p1)); // false 
  print(identical(p0, p2)); // false 
  print(identical(p0, p3)); // true! 
}
```

마지막 `identical(p0,p3)`에서 true 값이 나온 것을 보면 해당 객체가 canonicalize 된 것을 알 수 있다. 자세한 설명은 [여기](https://stackoverflow.com/a/21746692/9437175) 를 참고하자.