---
title: CSS3测试
date: 2021-03-11 21:12:40
categories: 前端
---

![](https://upload-images.jianshu.io/upload_images/10024246-e54779fc756f5400.gif?imageMogr2/auto-orient/strip)
上边的“图像”并非图片，而是纯CSS 。转载外部网站[点这里](https://purecss3.net/doraemon/doraemon_css3.html)由于被墙，源代码copy如下：
## html
```
<!DOCTYPE html>
<!-- saved from url=(0048)https://purecss3.net/doraemon/doraemon_css3.html -->
<html lang="ja">

<head>
	<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">

	<title>CSS3 ドラえもん - Pure CSS Design</title>
	<meta name="description" content="国民的キャラクターである「ドラえもん」を画像を使わずCSS3だけで書いてみた！">
	<meta name="keywords" content="CSS3,ドラえもん,CSS,Doraemon,ドラ">
	<link rel="stylesheet" type="text/css" href="./CSS3 ドラえもん - Pure CSS Design_files/dora3.css">
</head>

<body>
	<div id="doraemon">
		<div id="face">
			<div id="head_light"></div>
			<div id="eyes">
				<div class="eye eye_left"></div>
				<div class="black_eye black_left"></div>

				<div class="eye eye_right"></div>
				<div class="black_eye black_right"></div>
			</div>
			<div id="base">
				<div id="base_white"></div>
				<div id="nose">
					<div id="nose_light"></div>
				</div>
				<div id="nose_line"></div>
				<div id="mouth"></div>
				<div id="mouth_rewrite"></div>
				<div id="firefox_mouth"></div>

				<div class="whiskers whi_right_top rotate160"></div>
				<div class="whiskers whi_right"></div>
				<div class="whiskers whi_right_bottom rotate20"></div>

				<div class="whiskers whi_left_top rotate20"></div>
				<div class="whiskers whi_left"></div>
				<div class="whiskers whi_left_bottom rotate160"></div>
			</div>
		</div>
		<div id="choker">
			<div id="belt"></div>
			<div id="bell">
				<div id="bell_line"></div>
				<div id="bell_circle"></div>
				<div id="bell_under"></div>
				<div id="bell_light"></div>
			</div>
		</div>
		<div id="body">
			<div id="doutai"></div>
			<div class="base_white2 doutai_center"></div>
			<div id="pocket">
				<div id="circle"></div>
				<div id="circle_rewrite"></div>
			</div>
		</div>
		<div id="hand_right">
			<div id="arm_right"></div>
			<div class="hand_circle hand_right"></div>
			<div class="arm_rewrite_right"></div>
		</div>
		<div id="hand_left">
			<div id="arm_left"></div>
			<div class="hand_circle hand_left"></div>
			<div class="arm_rewrite_left"></div>
		</div>
		<div id="foot">
			<div id="foot_left"></div>
			<div id="foot_right"></div>
			<div id="foot_rewrite"></div>
		</div>
		<div id="shadow_doutai_arm"></div>
		<div id="shadow_doutai_left"></div>
		<div id="shadow_doutai_right"></div>
		<div id="shadow_belt"></div>
	</div>

	<div id="footer">
		<p>These are Samples drawn with CSS3 only, with no images. by <a href="http://twitter.com/shop_dd"
				title="Twitter shopdd">@shop_dd</a><br>Return to <a href="http://purecss3.net/">Pure CSS
				Design</a>.&nbsp;&nbsp;Read about <a href="http://shopdd.jp/blog-entry-932.html" target="_blank">the
				CSS3 Doraemon on shop DD</a> . <br></p>
	</div>


	<audio controls="controls" style="display: none;"></audio>
</body>
<style type="text/css">
	#yddContainer {
		display: block;
		font-family: Microsoft YaHei;
		position: relative;
		width: 100%;
		height: 100%;
		top: -4px;
		left: -4px;
		font-size: 12px;
		border: 1px solid
	}

	#yddTop {
		display: block;
		height: 22px
	}

	#yddTopBorderlr {
		display: block;
		position: static;
		height: 17px;
		padding: 2px 28px;
		line-height: 17px;
		font-size: 12px;
		color: #5079bb;
		font-weight: bold;
		border-style: none solid;
		border-width: 1px
	}

	#yddTopBorderlr .ydd-sp {
		position: absolute;
		top: 2px;
		height: 0;
		overflow: hidden
	}

	.ydd-icon {
		left: 5px;
		width: 17px;
		padding: 0px 0px 0px 0px;
		padding-top: 17px;
		background-position: -16px -44px
	}

	.ydd-close {
		right: 5px;
		width: 16px;
		padding-top: 16px;
		background-position: left -44px
	}

	#yddKeyTitle {
		float: left;
		text-decoration: none
	}

	#yddMiddle {
		display: block;
		margin-bottom: 10px
	}

	.ydd-tabs {
		display: block;
		margin: 5px 0;
		padding: 0 5px;
		height: 18px;
		border-bottom: 1px solid
	}

	.ydd-tab {
		display: block;
		float: left;
		height: 18px;
		margin: 0 5px -1px 0;
		padding: 0 4px;
		line-height: 18px;
		border: 1px solid;
		border-bottom: none
	}

	.ydd-trans-container {
		display: block;
		line-height: 160%
	}

	.ydd-trans-container a {
		text-decoration: none;
	}

	#yddBottom {
		position: absolute;
		bottom: 0;
		left: 0;
		width: 100%;
		height: 22px;
		line-height: 22px;
		overflow: hidden;
		background-position: left -22px
	}

	.ydd-padding010 {
		padding: 0 10px
	}

	#yddWrapper {
		color: #252525;
		z-index: 10001;
		background: url(chrome-extension://eopjamdnofihpioajgfdikhhbobonhbb/ab20.png);
	}

	#yddContainer {
		background: #fff;
		border-color: #4b7598
	}

	#yddTopBorderlr {
		border-color: #f0f8fc
	}

	#yddWrapper .ydd-sp {
		background-image: url(chrome-extension://eopjamdnofihpioajgfdikhhbobonhbb/ydd-sprite.png)
	}

	#yddWrapper a,
	#yddWrapper a:hover,
	#yddWrapper a:visited {
		color: #50799b
	}

	#yddWrapper .ydd-tabs {
		color: #959595
	}

	.ydd-tabs,
	.ydd-tab {
		background: #fff;
		border-color: #d5e7f3
	}

	#yddBottom {
		color: #363636
	}

	#yddWrapper {
		min-width: 250px;
		max-width: 400px;
	}
</style>

</html>
```
## css
```
body{
	background:#fff;
}

#doraemon{
	position:relative;
	margin:50px;	
}

#head_light{
	width:50px;
	height:50px;
	transform: rotate(20deg);
		-webkit-transform: rotate(20deg);
		-moz-transform: rotate(20deg);
		-o-transform: rotate(20deg);
		-ms-transform: rotate(20deg);
	box-shadow:80px 20px 50px #fff;
		-webkit-box-shadow:80px 20px 55px #fff;
		-moz-box-shadow:80px 20px 50px #fff;
	border-radius:45px;
		-webkit-border-radius:45px;
		-moz-border-radius:60px;
	position:absolute;
	top:-20px;
	left:170px;
	opacity:0.5
}

#face{
	position:relative;
	width:310px;
	height:300px;
	border-radius:146px;
		-webkit-border-radius:146px;
		-moz-border-radius:146px;
	background:#07beea;	
	background: linear-gradient(right top, #fff,#07beea 20%, #10a6ce 73% ,#000 95% ,#000 155%); 
		background: -webkit-linear-gradient(right top, #fff,#07beea 20%, #10a6ce 73% ,#000 95% ,#000 155%); 
		background: -moz-linear-gradient(right top, #fff,#07beea 20%, #10a6ce 73% ,#000 95% ,#000 155%); 
		background: -o-linear-gradient(right top, #fff,#07beea 20%, #10a6ce 73% ,#000 95% ,#000 155%); 
		background: -ms-linear-gradient(right top, #fff,#07beea 20%, #10a6ce 73% ,#000 95% ,#000 155%);  
	border:#333 2px solid;
	top:-15px;
	box-shadow:-5px 10px 15px rgba(0,0,0,0.45);
		-webkit-box-shadow:-5px 10px 15px rgba(0,0,0,0.45);
		-moz-box-shadow:-5px 10px 15px rgba(0,0,0,0.45);
	
}

#base{
	position:relative;
	top:-5px;
}

#base_white{
	position:absolute;
	border:#000 2px solid;
	width:264px;
	height:196px;
	border-radius: 150px 150px;
		-webkit-border-radius: 150px 150px;
		-moz-border-radius: 150px 150px;
	background:#FFF;
	background: linear-gradient(right top, #fff,#fff 75%, #eee 83%,#999 90%,#444 95%, #000); 
		background: -webkit-linear-gradient(right top, #fff,#fff 75%, #eee 83%,#999 90%,#444 95%, #000); 
		background: -moz-linear-gradient(right top, #fff,#fff 75%, #eee 83%,#999 90%,#444 95%, #000); 
		background: -o-linear-gradient(right top, #fff,#fff 75%, #eee 83%,#999 90%,#444 95%, #000); 
		background: -ms-linear-gradient(right top, #fff,#fff 75%, #eee 83%,#999 90%,#444 95%, #000); 
	z-index:1;
	top:85px;
	left:22px;	
}


#eyes{
	position:relative;
	top:-5px;
}

div.eye{
	position:absolute;
	border-radius: 35px 35px;
		-webkit-border-radius: 35px 35px;
		-moz-border-radius: 35px 35px;
	border:2px solid #000;
	width:72px;
	height:83px;
	z-index:20;
	background:#fff;
}

div.black_eye{
	position:absolute;
	width:15px;
	height:15px;
	border-radius:10px;
		-webkit-border-radius:10px;
		-moz-border-radius:10px;
	background:#333;
	z-index:21;
		-webkit-animation-name: cate;
		-webkit-animation-duration: 10s;
		-webkit-animation-timing-function: linear;
		-webkit-animation-iteration-count: 200;
}

@-webkit-keyframes cate{
	0%{
		margin:0 0 0 0;
	}
	80%	{
		margin:0px 0 0 0;
	}
	85%	{
		margin:-20px 0 0 0;
	}
	90%{
		margin:0 0 0 0;
	}
	93%{
		margin:0 0 0 7px;
	}
	96%{
		margin:0 0 0 0;
	}
	100%{
		margin:0 0 0 0;
	}
}

div.black_left{
	top:100px;
	left:130px;
}

div.black_right{
	top:100px;
	left:170px;
}

div.eye_left{
	top:45px;
	left:82px;	
}

div.eye_right{
	top:45px;
	left:156px;	
}

#nose{
	width:32px;
	height:32px;
	border:2px solid #000;
	border-radius:50px;
		-webkit-border-radius:50px;
		-moz-border-radius:50px;
	background:#c93e00;
	position:absolute;
	top:117px;
	left:139px;
	z-index:30;
}

#nose_light{
	width:10px;
	height:10px;
	border-radius:5px;
		-webkit-border-radius:5px;
		-moz-border-radius:5px;
	box-shadow:19px 8px 5px #fff;
		-webkit-box-shadow:19px 8px 5px #fff;
		-moz-box-shadow:19px 8px 5px #fff;
	position:relative;
	top:0px;
	left:0px;
}


#nose_line{
	background:#333;
	width:3px;
	height:100px;
	top:140px;
	left:155px;
	position:absolute;
		z-index:20;
}


#mouth{
	width:240px;
	height:240px;
	border-bottom:3px solid #333;
	border-radius:120px;
		-webkit-border-radius:120px;
		-moz-border-radius:120px;
	position:absolute;
	top:0px;
	left:35px;
	z-index:50;
	clip:rect(205px auto auto auto);
}


.whiskers{
	background:#333;
	height:2px;
	width:60px;
	position:absolute;
	z-index:20;
}
.whi_right{
	top:165px;
	left:210px;	
}	

.whi_right_top{
	top:145px;
	left:210px;
}	

.whi_right_bottom{
	top:185px;
	left:210px;
}	

.whi_left{
	top:165px;
	left:50px;	
}	
.whi_left_top{
	top:145px;
	left:50px;
}	

.whi_left_bottom{
	top:185px;
	left:50px;
}

.rotate20{
	transform: rotate(20deg);
		-webkit-transform: rotate(20deg);
		-moz-transform: rotate(20deg);
		-o-transform: rotate(20deg);
		-ms-transform: rotate(20deg);
}

.rotate160{
	transform: rotate(160deg);
		-webkit-transform: rotate(160deg);
		-moz-transform: rotate(160deg);
		-o-transform: rotate(160deg);
		-ms-transform: rotate(160deg);
}

#choker{
	position:relative;
	top:-55px;
	left:35px;
	z-index:100;
}

#belt{
	width:230px;
	height:20px;
	border:#000 solid 2px;
	background:#ca4100;
	background: linear-gradient(top, #ca4100, #800400); 
		background: -webkit-linear-gradient(top, #ca4100, #800400); 
		background: -moz-linear-gradient(top, #ca4100, #800400); 
		background: -o-linear-gradient(top, #ca4100, #800400); 
		background: -ms-linear-gradient(top, #ca4100, #800400); 
	border-radius:10px;	
		-webkit-border-radius:10px;	
		-moz-border-radius:10px;
		position:relative;
	left:5px;
}

#bell{
	width:40px;
	height:40px;
	border-radius:50px;
		-webkit-border-radius:50px;
		-moz-border-radius:50px;
	border:2px solid #000;
	background:#f9f12a;
	background: linear-gradient(top, #f9f12a, #e9e11a 75%,#a9a100); 
		background: -webkit-linear-gradient(top, #f9f12a, #e9e11a 75%,#a9a100); 
		background: -moz-linear-gradient(top, #f9f12a, #e9e11a 75%,#a9a100); 
		background: -o-linear-gradient(top, #f9f12a, #e9e11a 75%,#a9a100); 
		background: -ms-linear-gradient(top, #f9f12a, #e9e11a 75%,#a9a100); 
	box-shadow:-5px 5px 10px rgba(0,0,0,0.25);
		-webkit-box-shadow:-5px 3px 5px rgba(0,0,0,0.25);
		-moz-box-shadow:-5px 5px 10px rgba(0,0,0,0.25);
	position:relative;
	top:-15px;
	left:100px;
}

#bell_line{
	width:36px;
	height:2px;
	background:#f9f12a;
	border:#333 solid 2px;
	position:relative;
	top:10px;
}

#bell_circle{
	width:12px;
	height:10px;
	border-radius:5px;
		-webkit-border-radius:5px;
		-moz-border-radius:5px;
	background:#000;
	position:relative;
	top:14px;
	left:14px;
}

#bell_under{
	width:3px;
	height:15px;
	background:#000;
	position:relative;
	top:10px;
	left:18px;
}

#bell_light{
	width:10px;
	height:10px;
	border-radius:10px;
		-webkit-border-radius:10px;
		-moz-border-radius:10px;
	box-shadow:19px 8px 5px #fff;
		-webkit-box-shadow:19px 8px 5px #fff;
		-moz-box-shadow:19px 8px 5px #fff;
	position:relative;
	opacity:0.7;
	top:-35px;
	left:5px;
}

#doutai{
	position:absolute;
	width:220px;
	height:165px;
	background:#07beea;
	background: linear-gradient(right, #07beea, #0073b3 50%,#0096be 75%,#00b0e0 ,#0096be 100% ,#333 114%); 
		background: -webkit-linear-gradient(right, #07beea, #0073b3 50%,#0096be 75%,#00b0e0 ,#0096be 100% ,#333 114%); 
		background: -moz-linear-gradient(right, #07beea, #0073b3 50%,#0096be 75%,#00b0e0 ,#0096be 100% ,#333 114%); 
		background: -o-linear-gradient(right, #07beea, #0073b3 50%,#0096be 75%,#00b0e0 ,#0096be 100% ,#333 114%); 
		background: -ms-linear-gradient(right, #07beea, #0073b3 50%,#0096be 75%,#00b0e0 ,#0096be 100% ,#333 114%); 
	border:#333 2px solid;
	top:262px;
	left:46px;
}

div.base_white2{
	position:absolute;
	width:170px;
	height:170px;
	border-radius:85px;
		-webkit-border-radius:85px;
		-moz-border-radius:85px;
	border:2px solid #000;
	background:#FFF;
	background: linear-gradient(right top, #fff,#fff 75%, #eee 83%,#999 90%,#444 95%, #000); 
		background: -webkit-linear-gradient(right top, #fff,#fff 75%, #eee 83%,#999 90%,#444 95%, #000); 
		background: -moz-linear-gradient(right top, #fff,#fff 75%, #eee 83%,#999 90%,#444 95%, #000); 
		background: -o-linear-gradient(right top, #fff,#fff 75%, #eee 83%,#999 90%,#444 95%, #000); 
		background: -ms-linear-gradient(right top, #fff,#fff 75%, #eee 83%,#999 90%,#444 95%, #000); 
}

.doutai_center{
	top:230px;
	left:72px;
}

#circle{
	position:relative;
	width:130px;
	height:130px;
	border-radius:65px;
		-webkit-border-radius:65px;
		-moz-border-radius:65px;
	background:#fff;
	background: linear-gradient(right top, #fff, #fff 70%,#f8f8f8 75%,#eee 80%,#ddd 88% , #fff); 
		background: -webkit-linear-gradient(right top, #fff, #fff 70%,#f8f8f8 75%,#eee 80%,#ddd 88% , #fff); 
		background: -moz-linear-gradient(right top, #fff, #fff 70%,#f8f8f8 75%,#eee 80%,#ddd 88% , #fff); 
		background: -o-linear-gradient(right top, #fff, #fff 70%,#f8f8f8 75%,#eee 80%,#ddd 88% , #fff); 
		background: -ms-linear-gradient(right top, #fff, #fff 70%,#f8f8f8 75%,#eee 80%,#ddd 88% , #fff);  
	border:2px solid #000;
	top:-120px;
	left:92px;
}
#circle_rewrite{
	position:relative;
	width:134px;
	height:60px;
	background:#fff;	
	border-bottom:2px solid #000;
	top:-250px;
	left:92px;
}

#hand_right{
	position:absolute;
	top:272px;
	left:248px;
	width:100px;
	height:100px;
}

#arm_right{
	position:relative;
	width:80px;
	height:50px;
	background:#07beea;
	background: linear-gradient(top, #07beea, #07beea 85%, #555); 
		background: -webkit-linear-gradient(top, #07beea, #07beea 85%, #555); 
		background: -moz-linear-gradient(top, #07beea, #07beea 85%, #555); 
		background: -o-linear-gradient(top, #07beea, #07beea 85%, #555); 
		background: -ms-linear-gradient(top, #07beea, #07beea 85%, #555); 
	border:solid 1px #000;
	z-index:-1;
	top:17px;
	transform: rotate(35deg);
		-webkit-transform: rotate(35deg);
		-moz-transform: rotate(35deg);
		-o-transform: rotate(35deg);
		-ms-transform: rotate(35deg);
	box-shadow:-10px 7px 10px rgba(0,0,0,0.35);
		-webkit-box-shadow:-10px 7px 10px rgba(0,0,0,0.35);
		-moz-box-shadow:-10px 7px 10px rgba(0,0,0,0.35);
}

#hand_left{
	position:absolute;
	top:272px;
	left:-46px;
	width:100px;
	height:100px;
}

#arm_left{
	position:relative;
	width:80px;
	height:50px;
	background:#07beea;
	background: linear-gradient(top left, #0096be, #0096be 55%, #07beea); 
		background: -webkit-linear-gradient(top left, #0096be, #0096be 55%, #07beea); 
		background: -moz-linear-gradient(top left, #0096be, #0096be 55%, #07beea); 
		background: -o-linear-gradient(top left, #0096be, #0096be 55%, #07beea); 
		background: -ms-linear-gradient(top left, #0096be, #0096be 55%, #07beea); 
	border:solid 1px #000;
	z-index:-1;
	top:17px;
	left:36px;
	transform: rotate(145deg);
		-webkit-transform: rotate(145deg);
		-moz-transform: rotate(145deg);
		-o-transform: rotate(145deg);
		-ms-transform: rotate(145deg);
	box-shadow:5px -7px 10px rgba(0,0,0,0.25);
		-webkit-box-shadow:5px -7px 10px rgba(0,0,0,0.25);
		-moz-box-shadow:5px -7px 10px rgba(0,0,0,0.25);
}

div.hand_circle{
	position:absolute;
	width:60px;
	height:60px;
	border-radius:30px;
		-webkit-border-radius:30px;
		-moz-border-radius:30px;
	border:2px solid #000;
	background:#fff;
	background: linear-gradient(right top, #fff, #fff 50%, #eee 70%, #ddd 80%,#999); 
		background: -webkit-linear-gradient(right top, #fff, #fff 50%, #eee 70%, #ddd 80%,#999); 
		background: -moz-linear-gradient(right top, #fff, #fff 50%, #eee 70%, #ddd 80%,#999); 
		background: -o-linear-gradient(right top, #fff, #fff 50%, #eee 70%, #ddd 80%,#999); 
		background: -ms-linear-gradient(right top, #fff, #fff 50%, #eee 70%, #ddd 80%,#999); 
}

.hand_right{
	top:32px;
	left:40px;	
}

.arm_rewrite_right{
	position:relative;
	width:4px;
	height:45px;
	background:#07beea;
	top:-51px;
	left:18px;
}

.hand_left{
	top:34px;
	left:10px;	
}

.arm_rewrite_left{
	position:relative;
	width:4px;
	height:50px;
	background:#07beea;
	background: linear-gradient(top left, #0096be, #0096be); 
		background: -webkit-linear-gradient(top left, #0096be, #0096be); 
		background: -moz-linear-gradient(top left, #0096be, #0096be); 
		background: -o-linear-gradient(top left, #0096be, #0096be); 
		background: -ms-linear-gradient(top left, #0096be, #0096be); 
	top:-52px;
	left:92px;
}


#foot{
	position:relative;
	width:280px;
	height:40px;
	top:-141px;
	left:20px;
}

#foot_left{
	width:125px;
	height:30px;
	background:#fff;
	background: linear-gradient(right top, #fff,#fff 75%, #eee 85%, #999); 
		background: -webkit-linear-gradient(right top, #fff,#fff 75%, #eee 85%, #999); 
		background: -moz-linear-gradient(right top, #fff,#fff 75%, #eee 85%, #999); 
		background: -o-linear-gradient(right top, #fff,#fff 75%, #eee 85%, #999); 
		background: -ms-linear-gradient(right top, #fff,#fff 75%, #eee 85%, #999); 
	border:solid 2px #333;
	border-top-left-radius:80px;
	border-bottom-left-radius:40px;
	border-top-right-radius:60px;
	border-bottom-right-radius:60px;
		-webkit-border-top-left-radius:80px;
		-webkit-border-bottom-left-radius:40px;
		-webkit-border-top-right-radius:60px;
		-webkit-border-bottom-right-radius:60px;
		-moz-border-radius-topleft:80px;
		-moz-border-radius-bottomleft:40px;
		-moz-border-radius-topright:60px;
		-moz-border-radius-bottomright:60px;
	position:relative;
	left:8px;
	top:2px;
	box-shadow:-6px 0px 10px rgba(0,0,0,0.35);
		-webkit-box-shadow:-6px 0px 10px rgba(0,0,0,0.35);
		-moz-box-shadow:-6px 0px 10px rgba(0,0,0,0.35);
	z-index:-1;
}

#foot_right{
	position:relative;
	width:125px;
	height:30px;
	background:#fff;
	background: linear-gradient(right top, #fff,#fff 75%, #eee 85%, #999); 
		background: -webkit-linear-gradient(right top, #fff,#fff 75%, #eee 85%, #999); 
		background: -moz-linear-gradient(right top, #fff,#fff 75%, #eee 85%, #999); 
		background: -o-linear-gradient(right top, #fff,#fff 75%, #eee 85%, #999); 
		background: -ms-linear-gradient(right top, #fff,#fff 75%, #eee 85%, #999); 
	border:solid 2px #333;
	border-top-left-radius:60px;
	border-bottom-left-radius:60px;
	border-top-right-radius:80px;
	border-bottom-right-radius:40px;
		-webkit-border-top-left-radius:60px;
		-webkit-border-bottom-left-radius:60px;
		-webkit-border-top-right-radius:80px;
		-webkit-border-bottom-right-radius:40px;
		-moz-border-radius-topleft:60px;
		-moz-border-radius-bottomleft:60px;
		-moz-border-radius-topright:80px;
		-moz-border-radius-bottomright:40px;
	top:-32px;
	left:141px;
	box-shadow:-6px 0px 10px rgba(0,0,0,0.35);
		-webkit-box-shadow:-6px 0px 10px rgba(0,0,0,0.35);
		-moz-box-shadow:-6px 0px 10px rgba(0,0,0,0.35);
	z-index:-1;
}

#foot_rewrite{
	position:relative;
	width:20px;
	height:10px;
	background:#fff;
	background: linear-gradient(right top, #666, #fff 83%, #fff); 
		background: -webkit-linear-gradient(right top, #666, #fff 83%, #fff); 
		background: -moz-linear-gradient(right top, #666, #fff 83%, #fff); 
		background: -o-linear-gradient(right top, #666, #fff 83%, #fff); 
		background: -ms-linear-gradient(right top, #666, #fff 83%, #fff); 
	top:-76px;
	left:127px;
	border-top:2px solid #000;
	border-right:2px solid #000;
	border-left:2px solid #000;
	border-top-right-radius:40px;
	border-top-left-radius:40px;
		-webkit-border-top-right-radius:40px;
		-webkit-border-top-left-radius:40px;
		-moz-border-radius-topleft:40px;
		-moz-border-radius-topright:40px;
}

#shadow_doutai_left{
	width:30px;
	height:200px;
	box-shadow:-10px 10px 15px rgba(0,0,0,0.45);
		-webkit-box-shadow:-10px 10px 15px rgba(0,0,0,0.45);
		-moz-box-shadow:-10px 10px 15px rgba(0,0,0,0.45);
	position:absolute;
	top:250px;
	left:46px;
	z-index:-10;
}

#shadow_doutai_right{
	width:30px;
	height:200px;
	box-shadow:10px 10px 15px rgba(0,0,0,0.35);
		-webkit-box-shadow:10px 10px 25px rgba(0,0,0,0.35);
		-moz-box-shadow:10px 10px 15px rgba(0,0,0,0.35);
	position:absolute;
	top:240px;
	left:230px;
	z-index:-10;
}

#shadow_doutai_arm{
	width:85px;
	height:165px;
	box-shadow:-100px 10px 15px rgba(0,0,0,0.0);
		-webkit-box-shadow:-100px 10px 15px rgba(0,0,0,0.25);
		-moz-box-shadow:-100px 10px 15px rgba(0,0,0,0.25);
	position:absolute;
	top:230px;
	left:113px;
	z-index:10;
	opacity:0.5;
	transform: rotate(-20deg);
		-webkit-transform: rotate(-20deg);
		-moz-transform: rotate(-20deg);
		-o-transform: rotate(-20deg);
		ms-transform: rotate(-20deg);
	border-bottom-left-radius:40px;
		-webkit-border-bottom-left-radius:40px;
		-moz-border-radius-bottomleft:40px;
	border-top-left-radius:20px;
		-webkit-border-top-left-radius:20px;
		-moz-border-radius-topleft:20px;
}

#shadow_belt{
	width:40px;
	height:30px;
	box-shadow:-100px 10px 15px rgba(0,0,0,0);
		-webkit-box-shadow:-100px 10px 15px rgba(0,0,0,0.25);
		-moz-box-shadow:-100px 10px 15px rgba(0,0,0,0.25);
	position:absolute;
	top:240px;
	left:130px;
	z-index:10;
	border-bottom-left-radius:40px;
		-webkit-border-bottom-left-radius:40px;
		-moz-border-radius-bottomleft:40px;
	z-index:300;
}

#arm_left:not(\*|*), .arm_rewrite_left:not(\*|*){
	background:#07beea;
}

#arm_left, .arm_rewrite_left{
	background:#07beea\9;
	*background:#07beea;
	_background:#07beea;
}
.arm_rewrite_left{
		background: -ms-linear-gradient(top left, #0096be, #0096be);
}

#arm_left{
		background: -ms-linear-gradient(top left, #0096be, #0096be 55%, #07beea);
}

#kiji{
	position:relative;
	top:-150px;
}

#footer{
	width:97%;
	padding:0 0 0 30px;
	border-bottom:#333 1px solid;
	margin:-150px 0 0;
}

#footer a,a:link{
	color:#09C;
	position:relative;
}
```
