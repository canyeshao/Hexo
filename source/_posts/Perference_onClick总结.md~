---
title: Perference_onClick总结
date: 2016-03-20 13:12:43
tags: [Android,study]
categories: "Android学习笔记"
---


   对于NEW_MTK6580m，Android6.0代码的Settings.Audioprofile模块已经看了一段时间，但之前的查看主要是围绕模块结构以及组织关系。
这两天主要针对Audioprofile模块的铃声选择进行分析，现对其进行总结。

首先，从铃声选择的父界面(即选择进入铃声选择的界面，Settings->Sound&notfication->右上角设置按钮)开始，
父界面代码位置/alps/packages/apps/Settings/src/com/mediatek/audioprofile/Editprofile.java
根据其onCreate()方法中
```java 
addPreferencesFromResource(R.xml.edit_profile_prefs);
```
这一句addPreferencesFromResource()方法找到其对应的Preference界面/alps/packages/apps/Settings/res_ext/xml/edit_profile_prefs.xml
查看这个Preference文件，我们可以发现PreferenceScreen标签包裹整个界面，使用PreferenceCategory标签将整个界面
分为general及System（feedback）两个模块，通过具体的Preference来实现界面列。（对于Preference，重点关注其key属性，它类似于一般界面的id属性）
通过在手机界面查找，发现点击后Phone ringtone会进入一个铃声选择的界面，ok，目标已经确定，我们现在来分析这个铃声选择功能的具体过程。
点击Phone ringtone跳转至铃声选择界面
目光自然聚集在这个”点击“上，查看Editprofile.java，在变量定义的地方发现 
```java   
 public static final String KEY_RINGTONE = "phone_ringtone";
```
 可以看出这个字串与我们要找的功能相关。
 对KEY_RINGTONE在Editprofile.java文件中进行搜索，发现它出现在以下两个位置
```java 
  private void initRingtone(PreferenceScreen parent) {
        mVoiceRingtone = (DefaultRingtonePreference) parent.findPreference(KEY_RINGTONE);
        ...
 } //可以看出此处在初始化mVoiceRingtone变量
```
     
```java      
  public boolean onPreferenceTreeClick(PreferenceScreen preferenceScreen,
            Preference preference) {
        Log.d("@M_" + TAG, "Key :" + preference.getKey());
        if ((preference.getKey()).equals(KEY_RINGTONE)) {  //在这里对preference.getKey()与KEY_RINGTONE进行判断
            setRingtongTypeAndStartSIMSelector(RINGTONE_INDEX);
        } else if ((preference.getKey()).equals(KEY_VIDEO_RINGTONE)) {
            setRingtongTypeAndStartSIMSelector(VIDEO_RINGTONE_INDEX);
        }

        return super.onPreferenceTreeClick(preferenceScreen, preference);
    }
```

在第二个位置我们发现了它所在的方法是`onPreferenceTreeClick()`，通过之前对于Preference方面的学习知道，
对Preference的点击一般会触发该方法。但是它到底怎么被触发的呢，点击事件一般不是触发onClick()方法吗？
带着这个问题我们继续分析。
查看edit_profile_prefs.xml文件发现，phone_ringtone使用自定义的DefaultRingtonePreference
 <com.mediatek.audioprofile.DefaultRingtonePreference
            android:key="phone_ringtone" 
            ...
 />
查看DefaultRingtonePreference.java文件。
```java
public class DefaultRingtonePreference extends RingtonePreference {
```
发现其继承自RingtonePreference，查看RingtonePreference文件
```java
public class RingtonePreference extends Preference implements
        PreferenceManager.OnActivityResultListener {
发现RingtonePreference继承自Preference
```
通过查看DefaultRingtonePreference，RingtonePreference，Preference文件，发现
DefaultRingtonePreference有onClick()方法,RingtonePreference中有onClick()方法，Preference中有performClick(),onClick()方法。
查看到这里仿佛进入了瓶颈，这么多点击触发的方法好像都有用，但具体哪一个被调用，哪一个先被触发，哪一个点击过后进行了界面跳转进入了铃声选择界面呢？
对于这个问题，先对以上各个方法进行逐一查看，随后在其中逐一加入 
Log.w("@M_" + TAG, "Preference performClick");
等log输出语句，打算通过查看log，来确定方法的先后调用问题。

![log图片：](http://i4.piimg.com/9757e7ff169c115a.png)

通过图片中部的log发现Preference.performClick()方法最先被调用，
接下来是DefaultRingtonePreference.onClick()
再接下来是RingtonePreference.onClick(),
再到Editprofile.onPreferenceTreeClick(),
最后在PreferenceFragment.onPreferenceTreeClick()


所以我们先去查看Preference的performClick()
```java
public void performClick(PreferenceScreen preferenceScreen) {

        Log.w("@M_" + TAG, "Preference performClick");
        if (!isEnabled()) {
            return;
        }
        
        onClick();
        
        if (mOnClickListener != null && mOnClickListener.onPreferenceClick(this)) {
            return;
        }
        
        PreferenceManager preferenceManager = getPreferenceManager();
        if (preferenceManager != null) {
            PreferenceManager.OnPreferenceTreeClickListener listener = preferenceManager
                    .getOnPreferenceTreeClickListener();
            if (preferenceScreen != null && listener != null
                    && listener.onPreferenceTreeClick(preferenceScreen, this)) {
                return;
            }
        }
        
        if (mIntent != null) {
            Context context = getContext();
            context.startActivity(mIntent);
        }
    }
```
可以看到performClick()方法先调用了Preference的onClick();
那onClick()呢？
```java
protected void onClick() {
}
```
一个被保护的空方法！！意味着它将被它的子类所复写。
回顾一下之前查看的Preference的继承关系：
DefaultRingtonePreference->RingtonePreference->Preference
根据java面向对象方面的知识，Preference的onClick()将被DefaultRingtonePreference的onClick()所复写。
查看DefaultRingtonePreference的onClick()
```java
protected void onClick() {
        // M: Set different SIM ringtone
        // modified by mtk54031
        final TelephonyManager mTeleManager = (TelephonyManager) getContext()
                .getSystemService(Context.TELEPHONY_SERVICE);
        //int simNum = mTeleManager.getSimCount();
        int simNum = SubscriptionManager.from(getContext()).getActiveSubscriptionInfoCount();
        Log.d("@M_" + TAG, "onClick  : isNoNeedSIMSelector = " + isNoNeedSIMSelector()
                + "simNum <= SINGLE_SIMCARD: simNum = " + simNum);

        if (FeatureOption.MTK_MULTISIM_RINGTONE_SUPPORT && simNum == SINGLE_SIMCARD) {
           int subId = SubscriptionManager.from(getContext()).getActiveSubscriptionIdList()[0];
           setSimId(subId);
           //setSimId(1);
        }
        if (isNoNeedSIMSelector() || simNum <= SINGLE_SIMCARD) {
            //关注这里，在这里调用了DefaultRingtonePreference的父类，也就是RingtonePreference的onclick方法
            super.onClick();   
        }
    }
``` 
此时可以发现我们对于从Preference.performClick()开始的关于onClick方法的推理与log实际打印出的顺序是一致的！
Preference.performClick()方法最先被调用，
Preference.performClick()方法调Preference.onClick();
Preference.onClick()被DefaultRingtonePreference.onClick()复写
DefaultRingtonePreference.onClick()执行super.onClick()调RingtonePreference.onClick(),

同时，在RingtonePreference.onClick()中看到了startActivityForResult(intent, mRequestCode); Activity返回值的跳转
意味着饶了一大圈实际进行选择铃音跳转的位置在这里，RingtonePreference.onClick()。
RingtonePreference的onClick()
```java
protected void onClick() {
        Log.w("@M_" + TAG, "RingtonePreference  onClick()");
        // Launch the ringtone picker
        Intent intent = new Intent(RingtoneManager.ACTION_RINGTONE_PICKER);
        onPrepareRingtonePickerIntent(intent);
        PreferenceFragment owningFragment = getPreferenceManager().getFragment();
        if (owningFragment != null) {
            owningFragment.startActivityForResult(intent, mRequestCode);//Activity返回值的跳转
        } else {
            getPreferenceManager().getActivity().startActivityForResult(intent, mRequestCode);
        }
    }
```

铃声跳转intent的action为ACTION_RINGTONE_PICKER，
public static final String ACTION_RINGTONE_PICKER = "android.intent.action.RINGTONE_PICKER";
对其全局查找，找到了以下信息
 @see RingtoneManager#ACTION_RINGTONE_PICKER

public final class RingtonePickerActivity extends AlertActivity implements

查看RingtonePickerActivity，发现它即是我们要找的铃声选择的界面Activity。

现在我们关于onClick()方法的调用过程清楚了及其功能大体清楚。但是最开始的Editprofile.onPreferenceTreeClick()方法呢？
回到Preference的performClick()
```java
public void performClick(PreferenceScreen preferenceScreen) {

        Log.w("@M_" + TAG, "Preference performClick");
        if (!isEnabled()) {
            return;
        }
        
        onClick();
        
        if (mOnClickListener != null && mOnClickListener.onPreferenceClick(this)) {
            return;
        }
        
        PreferenceManager preferenceManager = getPreferenceManager();
        if (preferenceManager != null) {
            PreferenceManager.OnPreferenceTreeClickListener listener = preferenceManager
                    .getOnPreferenceTreeClickListener();
            if (preferenceScreen != null && listener != null
                    && listener.onPreferenceTreeClick(preferenceScreen, this)) {
                return;
            }
        }
        
        if (mIntent != null) {
            Context context = getContext();
            context.startActivity(mIntent);
        }
    }
```
可以看到performClick()中调用完onClick()后会调用listener.onPreferenceTreeClick(preferenceScreen, this)
从preference中如何调用Editprofile中的方法呢？它们之间又没有继承关系。
回到Editprofile，发现
public class Editprofile extends SettingsPreferenceFragment {
而SettingsPreferenceFragment：
public abstract class SettingsPreferenceFragment extends InstrumentedPreferenceFragment
InstrumentedPreferenceFragment：
public abstract class InstrumentedPreferenceFragment extends PreferenceFragment {
PreferenceFragment：
public abstract class PreferenceFragment extends Fragment implements
        PreferenceManager.OnPreferenceTreeClickListener {
终于，在多次的继承关系后我们发现PreferenceFragment实现PreferenceManager.OnPreferenceTreeClickListener
接口。在PreferenceFragment.onStart()
```java
public void onStart() {
        super.onStart();

        if (DBG) {
            Log.d(TAG, "onStart, this = " + this);
        }

        mPreferenceManager.setOnPreferenceTreeClickListener(this);
}
```
在这里设置了OnPreferenceTreeClick的监听器，
PreferenceFragment.onPreferenceTreeClick:
```java
public boolean onPreferenceTreeClick(PreferenceScreen preferenceScreen,
            Preference preference) {
        Log.w("@M_" + TAG, "PreferenceFragment onPreferenceTreeClick");
        if (preference.getFragment() != null &&
                getActivity() instanceof OnPreferenceStartFragmentCallback) {
            return ((OnPreferenceStartFragmentCallback)getActivity()).onPreferenceStartFragment(
                    this, preference);
        }
        return false;
}
```
根据之前的继承关系，Editprofile.onPreferenceTreeClick()将复写PreferenceFragment.onPreferenceTreeClick()
Editprofile.onPreferenceTreeClick()：
```java
public boolean onPreferenceTreeClick(PreferenceScreen preferenceScreen,
            Preference preference) {
        Log.d("@M_" + TAG, "Key :" + preference.getKey());
        if ((preference.getKey()).equals(KEY_RINGTONE)) {
            setRingtongTypeAndStartSIMSelector(RINGTONE_INDEX);//设置铃音类型及sim卡id
        } else if ((preference.getKey()).equals(KEY_VIDEO_RINGTONE)) {
            setRingtongTypeAndStartSIMSelector(VIDEO_RINGTONE_INDEX);
        }
        //调PreferenceFragment的onPreferenceTreeClick()
        return super.onPreferenceTreeClick(preferenceScreen, preference);
}
```
performClick()中调用listener.onPreferenceTreeClick()调用；Editprofile.onPreferenceTreeClick()
Editprofile.onPreferenceTreeClick()调super.onPreferenceTreeClick()。这个顺序与log顺序也一致。

此时，完成了点击铃音选择的跳转过程分析，分析暂时到此。

通过之前的分析，发现在查看此处代码时，往往有很深的继承关系(带来复杂的函数重载及复写)，且类与类之间的相互调用也很复杂。
最开始只能看到一个函数，一个点，需要不断的查看其深层次的关系及方法调用。在查找时常会用到全局查找，会有很多干扰数据，
要学习从中辨别寻找实际需要的。同时，在推理分析时多查看log，log是最准确的结论。
