---
layout: post
category: Android
title: "[안드로이드]앱 설정 자동화"
---

예를 들어, 카톡에서 알림을 진동으로 할지 소리로 할지 결정하려면 UI를 구성한 뒤에 해당 이벤트를 처리해주어 설정값을 저장해야 한다. 이런 역할을 지원해주는 것이 앱 설정 자동화 기능이며 정해진 태그로 XML 파일을 구성하고 저장 외에 다른 이벤트 처리를 할 수 있다.

# XML 설정하기

여러가지의 XML 태그들이 존재하며 key 값으로 자바에서 사용할 수 있다.

* [<PreferenceScreen\>](https://developer.android.com/reference/android/preference/PreferenceScreen) : 설정 화면의 단위로 중첩이 가능하다.
* [\<PreferenceCategory>](https://developer.android.com/reference/android/preference/PreferenceCategory) : 설정 화면 여러개의 서브 타이틀이라고 보면 된다.
* [\<SwitchPreference>](https://developer.android.com/reference/android/preference/SwitchPreference) : API LEVEL 14에서 추가된 것으로 설정의 true/false 값을 결정한다.
* [\<ListPreference>](https://developer.android.com/reference/android/preference/ListPreference) : 항목의 다이얼로그를 위한 설정이다.

이외에도 \<CheckboxPreference>, \<EditTextPreference> 등 여러가지 태그들이 있다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">
    <PreferenceCategory android:title="메시지 알림">
        <SwitchPreference
            android:key="message"
            android:title="메시지 알림"/>
        <PreferenceScreen
            android:key="keyword_screen"
            android:title="키워드 알림"
            android:summary="사용">
            <SwitchPreference
                android:defaultValue="false"
                android:key="keyword"
                android:summary="등록된 키워드가 포함된 메시지는 채팅방 알림이 꺼져
있어도 푸시 알림을 받게 되어 키워드는 강조되어 보여 집니다."
                android:title="키워드 알림"/>
            <ListPreference
                android:entries="@array/array_voice"
                android:entryValues="@array/array_voice"
                android:key="keyword_sound_list"
                android:summary="카톡"
                android:title="키워드 전용 알림음"
                android:dependency="keyword"/>
        </PreferenceScreen>

        <SwitchPreference
            android:key="sound"
            android:title="소리"/>
        <ListPreference
            android:entries="@array/array_voice"
            android:entryValues="@array/array_voice"
            android:key="sound_list"
            android:summary="카톡"
            android:title="알림음"/>
    </PreferenceCategory>
</PreferenceScreen>
```

위와 같이 설정하면 다음 화면이 보여진다.

<img src="https://user-images.githubusercontent.com/35518072/42251474-9ee0f90a-7f72-11e8-8add-e1b6582167c6.png" height="400px">

키워드 알림을 누르게 되면 \<PreferenceScreen> 태그로 감싸진 설정 화면이 나온다.

<img src="https://user-images.githubusercontent.com/35518072/42251547-ff02708e-7f72-11e8-8c71-54ec7958110f.png" height="400px">

알림음이나 키워드 전용 알림의 경우 \<ListPreference> 이기 때문에 목록들이 나열되며 해당 목록은 전에 비슷하게 했던 것과 같이 arrays.xml 파일의 \<string-array> 태그를 이용해서 name 값으로 넘겨준다. 여기서 name 값은 "array_voice"이다.

```xml
<resources>
	<string-array name="array_voice">
    	<item>카톡</item>
        <item>카톡왔숑</item>
        <item>카톡카톡</item>
        <item>깨똑</item>
    </string-array>
</resources>
```



# 앱 설정 자동화 적용

XML 파일을 만들었으니 적용하기 위해선 PreferenceFragment 클래스를 상속받는 자식 클래스를 만들어야 한다.

```java
public class SettingPreferenceFragment extends PreferenceFragment{
    @Override
    public void onCreate(final Bundle savedInstanceState){
        super.onCreate(savedInstanceState);
        addPreferencesFromResource(R.xml.settings_preference);
    }
}
```

settings_preference.xml 파일은 위에서 만든 xml 파일이다. 이제 화면에 띄워줘야 하는데 이 클래스는 액티비티 클래스가 아니기 때문에 액티비티의 레이아웃 xml 파일에서 \<fragment> 태그를 이용해 등록해주어야 한다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/settings_fragment"
    android:name="com.haram.part3_9.SettingPreferenceFragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
</fragment>
```

이러면 이제 해당 액티비티에 설정 화면이 띄워지게 된다.



# summary 변경

summary는 어떤 값이 설정되었는지 보여주는 것인데 앱 설정 자동화에서 사용자가 설정 값을 바꿨다면 저장은 되지만 summary 값이 변경되지 않을 경우 이전 값으로 보여지기 때문에 summary 값도 같이 바꿔주어야 한다. 이때 사용하는 것이 SharedPreferences 로 저장할 때도 이 클래스를 사용하기 때문에 데이터를 획득하여 summary를 바꿔줄 때도 이걸 사용해야 한다.

```java
prefs = PreferenceManager.getDefaultSharedPreferences(getActivity());
// "sound"라는 key 값을 가진 value가 존재하면
if(!prefs.getString("sound","").equals("")){
    // 해당 value로 summary를 바꾼다. ("카톡"으로 바꾸는 것이 아님)
    soundPreference.setSummary(prefs.getString("sound","카톡"));
}
```

먼저 패키지 명으로 SharedPreferences 객체를 가져오고 "sound"라는 key 값을 가진 값이 존재한다면 그에 해당되는 preference의 summary를 변경된 값으로 설정하는 코드이다.



# Preference 이벤트 처리

사용자가 환경을 설정하는 순간에 이벤트가 필요할 수 있는데 summary 변경과 비슷하지만 summary 변경은 이벤트 처리가 아닌 단순 조건문이고 이벤트 처리하는 것은 동적으로 사용자의 선택에 따라서 변경시키는 것이다. [registerOnSharedPreferenceListener]("https://developer.android.com/reference/android/content/SharedPreferences#registerOnSharedPreferenceChangeListener(android.content.SharedPreferences.OnSharedPreferenceChangeListener)") 함수를 사용해서 이벤트 핸들러를 등록하며 이벤트 핸들러는 [onSharedPreferenceChanged]("https://developer.android.com/reference/android/content/SharedPreferences.OnSharedPreferenceChangeListener.html#onSharedPreferenceChanged(android.content.SharedPreferences,%20java.lang.String)") 함수로 처리한다.

```java
// 이벤트 핸들러 등록
prefs.registerOnSharedPreferenceListener(prefListener);

// 이벤트 핸들러
SharedPreferences.OnSharedPreferenceChangeListener prefListener = new SharedPreferences.OnSharedPreferenceChangeListener() {
     @Override
     public void onSharedPreferenceChanged(SharedPreferences sharedPreferences, String s) {
            if(s.equals("sound")){
                soundPreference.setSummary(prefs.getString("sound","카톡"));
            }
        }
    };
```

summary 변경 부분과 동일하게 작동한다는 것을 알 수 있다.