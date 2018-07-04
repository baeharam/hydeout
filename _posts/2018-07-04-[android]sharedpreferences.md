---
layout: post
category: Android
title: "[안드로이드]SharedPreferences"
---

데이터 영속화를 위해 보통 DBMS를 사용하지만 사소한 설정값을 true/false로 저장하는 등의 간단하게 이용할 데이터의 경우는 SharedPreferences 클래스를 사용해서 key-value 형태로 저장한다.

# 객체 얻기

객체를 생성하는 방법은 3가지가 있는데 각각 접근할 수 있는 범위가 다르다.

* **하나의 액티비티만 사용 가능**

```java
SharedPreferences pref = getPreferences(Context.MODE_PRIVATE);
```

* **다른 액티비티나 컴포넌트들이 데이터를 공유해서 사용 가능**

```java
SharedPreferences pref = getSharedPreferences("prefs", Context.MODE_PRIVATE);
```

* **파일명이 패키지명이 되어 다른 컴포넌트에서 데이터 사용 가능**

```java
SharedPreferences pref = PreferenceManager.getDefaultSharedPreferences(this);
```

객체를 획득할 때 외부 앱에 대한 접근 권한에 따라 mode 값이 3가지 있었지만 2가지는 API level 17에서 deprecated 되었기 때문에 보통 MODE_PRIVATE을 사용한다. ([참고](https://developer.android.com/reference/android/content/Context#MODE_WORLD_READABLE))



# 데이터 저장하기

데이터를 저장할 때는 [SharedPreferences.Editor](https://developer.android.com/reference/android/content/SharedPreferences.Editor) 클래스를 사용해야 하며 역시 key-value 형태로 저장한다.

```java
SharedPreferences.Editor editor = pref.edit();
editor.putString("key1", "value1");
editor.putInt("key2", 2);
editor.commit();
```



# 데이터 획득하기

데이터를 획득할 때는 SharedPreferences 객체를 이용한다.

```java
String value1 = pref.getString("key1", ""); // 0은 해당 객체가 존재하지 않을 때 리턴값
int value2 = pref.getInt("key2", 0);
```

