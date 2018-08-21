---
layout: post
category: java
title: "[JAVA]직렬화와 역직렬화"
---

# 정의

> [컴퓨터 과학](https://ko.wikipedia.org/wiki/%EC%BB%B4%ED%93%A8%ED%84%B0_%EA%B3%BC%ED%95%99)의 데이터 스토리지 문맥에서 [데이터 구조](https://ko.wikipedia.org/wiki/%EB%8D%B0%EC%9D%B4%ED%84%B0_%EA%B5%AC%EC%A1%B0)나 [오브젝트](https://ko.wikipedia.org/wiki/%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8) 상태를 동일하거나 다른 컴퓨터 환경에 저장(이를테면 [파일](https://ko.wikipedia.org/wiki/%EC%BB%B4%ED%93%A8%ED%84%B0_%ED%8C%8C%EC%9D%BC)이나 메모리 [버퍼](https://ko.wikipedia.org/wiki/%EB%8D%B0%EC%9D%B4%ED%84%B0_%EB%B2%84%ED%8D%BC)에서, 또는 [네트워크](https://ko.wikipedia.org/wiki/%EC%BB%B4%ED%93%A8%ED%84%B0_%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC) 연결 링크 간 전송)하고 나중에 재구성할 수 있는 포맷으로 변환하는 과정이다. 

즉, 어떤 플랫폼에서 생성한 자바 코드를 다른 플랫폼에서도 사용할 수 있도록 변환하는 과정이다. 아래 그림을 보면 더 이해가 쉬울 것이다.

<img src="https://www.geeksforgeeks.org/wp-content/uploads/gq/2016/01/serialize-deserialize-java.png">

객체(데이터)가 바이트 스트림을 이용하여 파일이나 메모리, 데이터베이스에 쓰여지고 그걸 다른 플랫폼(물론 같을 수도 있음)에서 다시 바이트 스트림으로 읽기 위해 변환하는 과정인 것이다.

<br>

# 사용법

직렬화 하고 싶은 객체에 [java.io.Serializable](https://docs.oracle.com/javase/9/docs/api/java/io/Serializable.html)이라는 마커인터페이스(구현할 함수가 없는 인터페이스)를 implements함으로서 사용할 수 있다.

```java
class Member implements Serializable {
    private String name;
    private String email;
    private int age;

    Member(String name, String email, int age) {
        this.name = name;
        this.email = email;
        this.age = age;
    }
    
    @Override
    public String toString() {
        return name+" "+email+" "+age;
    }
}
```

위와 같이 직렬화 조건을 만족시켰으면 [ObjectOutputStream](https://docs.oracle.com/javase/9/docs/api/java/io/ObjectOutputStream.html)을 통해서 직렬화를 한 후에 파일에 쓰거나 데이터베이스에 저장하면 된다.

```java
public class Serialization {
    // FileOutputStream이 FileNotFoundException을 던지고
    // ObjectOutputStream이 IOException을 던진다.
    public static void main(String[] args) throws IOException {
        Member member = new Member("배하람", "gkfka@naver.com", 25);
        FileOutputStream fsOut = new FileOutputStream("member.bin");
        ObjectOutputStream osOut= new ObjectOutputStream(fsOut);
        osOut.writeObject(member);
        osOut.close();
    }
}
```

실행하면 `member.bin` 파일이 만들어진다. 이제 역직렬화를 통해 직렬화된 객체를 읽어보자.

```java
public class Serialization {
    // readObject()가 ClassNotFoundException을 던져서 추가됨
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        FileInputStream fsIn = new FileInputStream("member.bin");
        ObjectInputStream osIn = new ObjectInputStream(fsIn);
        Member clone = (Member)osIn.readObject();
        System.out.println(clone);
        osIn.close();
    }
}
```

제대로 읽히는 것을 확인할 수 있었다. 하지만 주의할 점이 있다.

<br>

# SUID

직렬화 자체는 간단하지만 굉장히 주의할 점이 있는데 바로 SUID(serialVersionUID)라는 값이다. 직렬화된 객체의 버전 값으로 해당 클래스에 변경이 일어났는데 SUID가 다르다면 역직렬화에서 `java.io.InvalidClassException`이 발생한다. SUID는 객체 직렬화 명세사항에 따라 자동적으로 생성되지만 레퍼런스는 컴파일러마다 구현이 다를 수 있기 때문에 명시적으로 정의하는 것을 권한다.

> However, it is ***strongly recommended*** that all serializable classes explicitly declare serialVersionUID values, since the default serialVersionUID computation is highly sensitive to class details that may vary depending on compiler implementations, and can thus result in unexpected `InvalidClassException`s during deserialization. 

SUID는 다음과 같이 정의해주면 된다.

```java
private static final long serialVersionUID = 1L;
```

이렇게 하면 변수가 추가되어도 해당 변수를 제외하고는 직렬화가 가능하다. 하지만 타입이 바뀌게 되면 어떻게 될까? **타입의 경우는 SUID가 같다고 해도 예외가 발생한다.**

<br>

# 정리

* 직렬화 할 땐 명시적으로 SUID를 정의하자.
* 역직렬화 할 때 타입변경에 민감하므로 지양하자.
* SUID값이 동일할 경우 멤버변수 삭제/변경은 괜찮지만 데이터가 누락된다.

이외에도 우아한 개발자들 블로그에서 실무적인 내용에 대해 보았지만 아직은 와닿지가 않아서 이 정도만 알아두고 나중에 더 주의할 점이 보인다면 추가하도록 하자.

<br>

## 참고

* [자바 기초 직렬화](https://www.youtube.com/watch?v=variM5qJsQM)
* [GeeksforGeeks : Serialization and Deserialization](https://www.geeksforgeeks.org/serialization-in-java/)
* [자바 직렬화, 그것이 알고싶다. 훑어보기편](http://woowabros.github.io/experience/2017/10/17/java-serialize.html)
* [자바 직렬화, 그것이 알고싶다. 실무편](http://woowabros.github.io/experience/2017/10/17/java-serialize2.html)