---
layout: 笔记
title: webview不能播放视频和声音的问题分析
date: 2019-12-25 10:07:28
tags: 笔记
---

webview打开网页后不能播放视频和声音, 有以下几种情况.
1. 没打开硬件加速:
	在manifest.xml文件的cocos2dx的activity内加上 
	{% codeblock %}
	android:hardwareAccelerated="true"
	{% endcodeblock %}
	在AppActivity.java文件的onCreate方法内加上 
	{% codeblock %}
	getWindow().setFlags(WindowManager.LayoutParams.FLAG_HARDWARE_ACCELERATED, WindowManager.LayoutParams.FLAG_HARDWARE_ACCELERATED);
	{% endcodeblock %}
	在Cocos2dxWebView.java的public Cocos2dxWebView(xxx,xxx) 方法内添加 
	{% codeblock %}
	this.setLayerType(View.LAYER_TYPE_HARDWARE, null);
	{% endcodeblock %}
2. 没打开js语法应用:
	在Cocos2dxWebView.java的public Cocos2dxWebView(xxx,xxx) 方法内添加 
	{% codeblock %}
	this.getSettings().setJavaScriptEnabled(true);
	{% endcodeblock %}
3. 没打开dom:
	在Cocos2dxWebView.java的public Cocos2dxWebView(xxx,xxx) 方法内添加 
	{% codeblock %}
	this.getSettings().setDomStorageEnabled(true);
	{% endcodeblock %}
4. 没打开视频自动播放控制:
	在Cocos2dxWebView.java的public Cocos2dxWebView(xxx,xxx) 方法内添加 
	{% codeblock %}
	this.getSettings().setMediaPlaybackRequiresUserGesture(false);
	{% endcodeblock %}

如果其上方法都没能解决, 请继续google/baidu/github/leecode/csdn ====