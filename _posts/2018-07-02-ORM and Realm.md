---
layout: post
category: Android
title: "[안드로이드]ORM과 Realm"
---

# ORM(Object-Relational Mappings)

> programming technique for converting data between incompatible type systems using object-oriented programming languages.

객체지향 언어를 이용해서 호환될 수 없는 시스템 사이에서 데이터를 변환하는 테크닉이라고 나와있는데 보통 데이터베이스를 사용할 때 SQL을 쓰지만 SQL을 쓰지 않고 OOP의 객체를 직접 해석해서 데이터를 획득하거나 저장하는 개념이다. Realm은 이 기술을 지원하는 외부 라이브러리이다.



# Realm 사용하기

Relam을 사용하기 위해선 `build.gradle` 파일에 설정을 해주어야 하는데 프로젝트 수준의 dependencies에 `classpath 'io.realm:realm-gradle-plugin:3.5.0'` 이라고 적어주고 (현재 버전) 적용하고 싶은 모듈의 `build.gradle`에 `apply plugin: 'realm-android'`로 적어주면 된다.

Relam은 ORM을 위한 것이기 때문에 데이터를 저장할 객체가 필요하기 때문에 RealmObject를 상속하는 클래스 하나를 구현해주어야 한다. 이런 클래스를 VO(Value-Object) 클래스라고 하는데 간단하게 데이터만을 표현하기 위한 클래스라고 생각하면 된다.

```java
public class MemoVO extends RelamObject{
    public String title;
    public String content;
}
```

Realm을 사용하기 위해선 해당 객체를 획득해야 하며 하기전에 `init()` 으로 초기화를 해야한다.

```java
Realm.init(this);
Realm mRealm = Realm.getDefaultInstance(); // Realm 객체 획득
```



# 데이터 저장하기

데이터를 저장할 때 db에서 배웠듯이 ACID를 준수해야 하기 때문에 트랜잭션을 사용하는데 Realm에서도 트랜잭션을 이용해 데이터를 저장한다.

```java
mRealm.executeTransaction(new Realm.Transaction){
    @Override
    public void execute(Realm realm){
        MemoVO vo = realm.createObject(MemoVO.class);
        vo.title = title;
        vo.content = content;
    }
}
```

* [executeTransaction()](https://www.realm.io/docs/java/latest/api/io/realm/realm#executeTransaction-io.realm.Realm.Transaction-) : Realm.Transaction 객체를 인자로 받아 주어진 트랜잭션을 수행한다.
* [Realm.Transaction](https://www.realm.io/docs/java/latest/api/io/realm/realm.transaction) : 트랜잭션을 수행할 때 사용하는 인터페이스이며 `execute()` 메서드를 오버라이딩 해야 한다.
* [createObject()](https://www.realm.io/docs/java/latest/api/io/realm/realm#createObject-java.lang.Class-) : Realm에 새로운 객체를 하나 생성해서 추가한다.

결국 Realm 객체를 VO 클래스의 객체와 연결시켜 트랜잭션을 수행하는 형태이다.



# 데이터 가져오기

데이터를 가져올 때도 VO 객체를 이용하며 수많은 메서드가 있다.

```java
MemoVO vo = mRealm.where(MemoVO.class).equalTo("title", "hello").findFirst();
```

MemoVO.class 타입이면서 title 값이 hello인 데이터 중 첫 번째 데이터를 가져온다. `where()` 메서드에 의해서 반환되는 객체가 [RealmQuery](https://www.realm.io/docs/java/latest/api/io/realm/realmquery) 인데 여기서 다양한 findXXX 메서드를 제공한다.

```java
RealmResults<MemoVO> results = mRealm.where(MemoVO.class).equalTo("title", "hello").findAll();
```

데이터가 여러개일 경우는 [RealmResults](https://www.realm.io/docs/java/latest/api/io/realm/realmresults) 타입으로 획득한다.



# 데이터 삭제하기

데이터를 삭제하기 위해선 해당 VO 객체를 획득한 다음 삭제하거나 전부 삭제해야 한다.

```java
vo.deleteFromRealm(); // 해당 VO 객체 삭제
mRealm.delete(MemoVO.class); // 해당 타입의 데이터 전부 삭제
```



## 출처

* 깡샘의 안드로이드 프로그래밍
* Realm 공식 문서 및 레퍼런스



