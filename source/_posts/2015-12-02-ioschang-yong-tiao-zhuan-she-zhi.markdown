---
layout: post
title: "iOS常用跳转设置"
date: 2015-12-02 14:50:56 +0800
comments: true
categories: iOS
---
> 经常会在应用中应用中遇到这样的问题，在app内部运行到某个场景的时候，需要打开系统的某项设置，但由于用户的不小心操作，或者当时的一些小想法而在系统弹框的时候选择了拒绝，之后就不能收到某项服务或者在app运行的环节中造成断片，这个时候我们就需要提醒用户手动去去打开系统设置，然后打开相应的服务，这给用户的体验是不好的，最好的用户体验是，如果用户拒绝了某项服务，但当他进入这个应用场景的时候，弹框提示用户的同时，还应该跳转到响应的系统设置页面，进行设置。

<!--more-->
</br>
###跳转本App设置界面以及相应的系统版本
需要请求一下位置权限或者通知权限，才可以跳进自己的app设置里面，如果没有任何权限请求，就只能跳到系统的设置界面  
**iOS8及以上**

    		NSURL * url = [NSURL URLWithString:UIApplicationOpenSettingsURLString];
    		if([[UIApplication sharedApplication] canOpenURL:url]) {
    			NSURL*url =[NSURL URLWithString:UIApplicationOpenSettingsURLString];
        		[[UIApplication sharedApplication] openURL:url];
    		}
	
**iOS8以下**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;打开应用的相册、推送、位置等等信息的时候会跳转到相应的页面

</br>
###跳转到系统设置
**通用模版**  

1. 设置Url Schemes(Target > info > URL Type)  
		![urlSchemes设置](http://7xopon.com1.z0.glb.clouddn.com/8.png)
		 
2. 项目相应位置相应的代码  
＃define kCommonMacro  系统设置root

		 NSURL *url = [NSURL URLWithString:@"kCommonMacro"];
    	if ([[UIApplication sharedApplication] canOpenURL:url]) {
        	[[UIApplication sharedApplication] openURL:url];
   		 }

		例子:
		**WIFI**
		 NSURL *url = [NSURL URLWithString:@"prefs:root=WIFI"];
    	if ([[UIApplication sharedApplication] canOpenURL:url]) {
        	[[UIApplication sharedApplication] openURL:url];
   		 }
   		 
**常用的系统设置prefs**  
 
* Notification <pre/>prefs:root=NOTIFICATIONS_ID

* About <pre/>prefs:root=General&path=About  

* Accessibility <pre/>prefs:root=General&path=ACCESSIBILITY  

* AirplaneModeOn <pre/>prefs:root=AIRPLANE_MODE  

* Auto-Lock <pre/>prefs:root=General&path=AUTOLOCK  

* Brightness <pre/>prefs:root=Brightness  

* Bluetooth <pre/>prefs:root=General&path=Bluetooth

* Date& Time <pre/>prefs:root=General&path=DATE_AND_TIME  

* FaceTime <pre/>prefs:root=FACETIME

* General <pre/>prefs:root=General

* Keyboard <pre/>prefs:root=General&path=Keyboard  

* iCloud <pre/>prefs:root=CASTLE  iCloud 

* Storage & Backup <pre/>prefs:root=CASTLE&path=STORAGE_AND_BACKUP  

* International <pre/>prefs:root=General&path=INTERNATIONAL  

* Location Services <pre/>prefs:root=LOCATION_SERVICES  

* Music <pre/>prefs:root=MUSIC  

* Music Equalizer <pre/>prefs:root=MUSIC&path=EQ  

* Music VolumeLimit <pre/>prefs:root=MUSIC&path=VolumeLimit  

* Network <pre/>prefs:root=General&path=Network  

* Nike + iPod <pre/>prefs:root=NIKE_PLUS_IPOD  

* Notes <pre/>prefs:root=NOTES   

* Phone <pre/>prefs:root=Phone  

* Photos <pre/>prefs:root=Photos  

* Profile <pre/>prefs:root=General&path=ManagedConfigurationList  

* Reset <pre/>prefs:root=General&path=Reset  

* Safari <pre/>prefs:root=Safari 

* Siri <pre/>prefs:root=General&path=Assistant  

* Sounds <pre/>prefs:root=Sounds  

* SoftwareUpdate <pre/>prefs:root=General&path=SOFTWARE_UPDATE_LINK  

* Store <pre/>prefs:root=STORE  

* Twitter <pre/>prefs:root=TWITTER  

* Usage <pre/>prefs:root=General&path=USAGE  

* VPN <pre/>prefs:root=General&path=Network/VPN  

* Wallpaper <pre/>prefs:root=Wallpaper  

* Wi-Fi <pre/>prefs:root=WIFI

* Setting  <pre/>prefs:root=INTERNET_TETHERING
