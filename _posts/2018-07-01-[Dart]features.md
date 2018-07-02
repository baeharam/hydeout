---
layout: post
category: dart
title: "[Dart]특이한 특징들"
---

## / 연산자와 ~/ 연산자

보통 대부분의 언어에서 `/` 연산자는 몫을 계산해주는 연산자지만 dart에선 실제 나눗셈을 하여 double 타입의 값을 리턴한다. 따라서 int 타입의 값을 얻어내기 위해선 `~/` 연산자를 써야 한다.

```dart
print(2/32); // 0.0625
print(2~/32); // 0
```

## 타입 검사

```dart
print(2 is num); // true
print("haram" is bool); // false
```

## => (fat arrow syntax)

함수가 한줄일 때 아주 간단하게 사용할 수 있다.

```dart
String func() => "haram";
```

## 선택적인 매개변수

함수를 만들 때 매개변수가 선택적일 수 있다.

```dart
void func(String a, [String b]){
    if(b == null) print(a);
    else print(a+b);
}

void main(){
    func("hello"," haram"); // hello haram
    func("hello"); // hello
}
```

## 익명 함수 (anonymous function)

lambda나 closure라고 불리기도 하는데 이름을 정하지 않고 함수를 정의할 수 있다.

```dart
void main() {
    var list = ['apples', 'bananas', 'oranges'];
    list.forEach((item){
        print('${list.indexOf(item)}: $item');
    });
}
/*
0: apples
1: bananas
2: oranges
*/
```

처음에 괄호를 써서 매개변수를 받고 함수의 내용을 정의하는데 매개변수의 타입은 옵션이다.

## 생성자

* **Syntactic Sugar Constructor**

보통 생성자를 쓸 때 클래스에 있는 인스턴스 변수를 초기화 시키기 위해 매개변수를 전달하는데 이걸 훨씬 간단하게 만들어준다.

```dart
class sugar{
    String a;
    int b;
    bool c;
    sugar(this.a, this.b, this.c);
}

void main(){
    sugar s = sugar("haram",3,true);
}
```

상속한 경우에도 자식클래스가 부모클래스의 syntactic sugar constructor를 사용할 수 있다.

```dart
class honey extends sugar{
    String d;
    honey(this.d) : super("haram",3,true);
}
```

* **Named Constructor**

생성자의 이름을 따로 지정할 수도 있다.

```dart
class name{
    String a;
    name.construct(this.a);
}
```

위 예시는 syntactic sugar까지 적용한 경우이다.

* **Getter / Setter**

가장 특이한 부분이었는데 보통 클래스를 정의할 때 만드는 getter, setter 메서드를 언어 차원에서 지원을 한다. `get`과 `set`이라는 키워드가 있다.

```dart
class getset{
    String a;
    get getter => a;
    set setter(String a) => this.a = a;
}

void main(){
    getset gs = getset();
    gs.setter = "haram";
    print(gs.getter); // haram
}
```

정의할 때는 =>를 써서 나타내며 사용할 때는 <u>메서드가 아닌 변수 형태로 사용한다.</u>