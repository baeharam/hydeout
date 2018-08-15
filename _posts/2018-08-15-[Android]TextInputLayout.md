---
layout: post
category: Android
title: "[안드로이드]TextInputLayout"
---

# 개요

[TextInputLayout](https://developer.android.com/reference/android/support/design/widget/TextInputLayout)은 support 라이브러리중 design 라이브러리 22.2.0에서 추가된 위젯으로 LinearLayout을 상속하며 구조는 아래와 같다.

![image](https://user-images.githubusercontent.com/35518072/44099822-7275117c-a01e-11e8-8619-c315d26e2155.png)

그렇다면 왜 굳이 EditText를 납두고 이런 위젯을 support 라이브러리로 만들었을까? 레퍼런스를 보면 알 수 있다.

> Layout which wraps an `EditText` (or descendant) to show a floating label when the hint is hidden due to the user inputting text. 

여기서 floating label이라고 하는 것은 텍스트를 입력하기 위해 해당 위젯을 클릭했을 때 위로 올라가는 label을 말한다. 즉, **사용자의 입력 때문에 가려지는 힌트를 위로 올리기 위해 만들어진 레이아웃**이라고 할 수 있다.

<br>

# EditText vs TextInputEditText

문서에선 EditText를 감싸는 레이아웃이라고 나와있지만 실질적으론 TextInputEditText를 사용하는 것이 더 많은 옵션이 있다고 한다. [StackOverflow](https://stackoverflow.com/questions/35775919/edittext-added-is-not-a-textinputedittext-please-switch-to-using-that-class-ins)를 봤지만 정확히 어떤 차이점이 있는지는 모르겠으나 크게 신경쓸 필요는 없는 것 같다.

<br>

# 라이브러리 추가

support 라이브러리를 추가해주어야 한다.

```
implementation 'com.android.support:design:28.0.0-rc01'
```

<br>

# 기본

```xml
<android.support.design.widget.TextInputLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <android.support.design.widget.TextInputEditText
        android:id="@+id/loginActivity_edittext_id"
        android:inputType="textEmailAddress"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"      
        android:hint="이메일"/>

</android.support.design.widget.TextInputLayout>
```

기존의 EditText를 다루는 방식과 동일하다. 단순히 TextInputLayout으로 감싸주고 TextInputEditText로 바꿔 주었을 뿐이다.

<br>

# 여러가지 기능들

[journaldev](https://www.journaldev.com/14748/android-textinputlayout-example)에 아주 친절하게 나와있으니 참조하자.

* `app:hintEnabled` : floating label을 활성화, 비활성화 시키는데 사용된다.
* `app:hintAnimationEnabled` : floating label의 애니메이션을 활성화, 비활성화 시키는데 사용된다.
* `app:hintTextAppearance="@style/HintText"` : styles.xml에 HintText라는 스타일을 하나 만들어 지정하면 floating label의 스타일도 바꿀 수 있다.
* `app:counterEnable` : 문자를 몇개 입력했는지 세주는 카운터를 활성화, 비활성화 시키는데 사용된다.
* `app:counterMaxLength` : 방금 전 속성과 함께 사용되며 입력제한 개수를 정한다.
* `app:counterTextAppearance` : 위의 스타일 지정과 동일하게 카운터에 적용된다.
* `app:passwordToggleEnabled` : 패스워드를 일반 텍스트로 할지 가릴지 정할 수 있다.
* `app:passwordToggleTint` : 토글 아이콘의 색깔을 정한다.

나열하는 것은 의미가 없으니 더 궁금하면 위 링크를 참조하거나 API 문서를 보도록 하자.