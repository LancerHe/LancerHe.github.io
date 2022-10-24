---
title: 关于ExtJS在提交表单出现SYNTAX ERROR解决方案
author: 谇雨
layout: post
permalink: /extjs-form-submit-syntax-error.html
views:
  - 2037
categories:
  - 前端设计
tags:
  - ExtJS
  - SYNTAX ERROR
  - 表单提交
---
<p style="text-indent: 2em;">
  今天搞了个登陆框，但是在登陆的时候一直出现等待窗体，又找不出什么原因。用FireBug检验的时候发现数据已经成功POST，但是貌似一直获取不到返回值。卡在waitMsg数据处，死循环。FireBug检查的结果是SYNTAX ERROR ：<!--DOCTYPE html PUBLI&#8230;&#8230;&#8230;&#8230;
</p>

<p style="text-align: center;">
  <a href="http://crackedzone.com/blog/wp-content/uploads/2011/01/ExtJs-login.gif"><img class="size-full wp-image-366 aligncenter" title="ExtJs-login" src="http://crackedzone.com/blog/wp-content/uploads/2011/01/ExtJs-login.gif" alt="" width="452" height="266" border=0 /></a>
</p>

<p style="text-indent: 2em;">
  网上找了很多终于发现问题的根源。返回值的模式必须是这样的模式：<span style="color: #ff0000;">{success:true,&#8230;&#8230;}</span>,并且必须指定success返回true，才说明ExtJS调用成功,否则ExtJS卡住无法执行;如果没有这行代码即为报&#8221;SYNTAX ERROR:)&#8221;错.这其实应该算ExtJS的一个BUG吧，个人是这么认为的。同时返回值中还能有其他参数，如 <span style="color: #ff0000;">{success:true,msg:&#8217;OK&#8217;}</span>
</p>

<!--more-->

  
ExtJS制作Form

<pre class="lang:js decode:true " title="Extjs" >Ext.onReady(function() {
  Ext.QuickTips.init();
  var Login = new Ext.form.FormPanel({
    labelWidth : 70,
    labelAlign : 'right',
    frame      : true,
    title      : 'Site Manage System',
    bodyStyle  : 'padding:5px 5px 0',
    width      : 350,
    defaults   : {width: 150, msgTarget: 'side'},
    defaultType: 'textfield',
    
    items: [{
      fieldLabel: 'Username',
      name      : 'username',
      allowBlank: false,
      blankText : 'Please input your username'
    }, {
      fieldLabel: 'Password',
      inputType : 'password',
      name      : 'password',
      allowBlank: false,
      blankText : 'Please input your password'
    }],
    
    buttons: [{
      text: 'Login',
      formBind: true,
      type: 'submit',
      handler: function (){
        if (Login.getForm().isValid()) {
  				Login.getForm().submit({
            clientValidation: true,
            waitTitle: 'Waiting',
            waitMsg: 'Check Login Information...',
            method:'POST',
            url:'login.php?action=login',
            success:function(form,action){
              if (action.result.msg == 'OK') {
                document.location = 'admincp.php';
              } else {
                Ext.Msg.alert('Status',action.result.msg);
              }
            },
            failure:function(){
            	Ext.Msg.alert('Status','System Error!');
            }
          });
        }
      }
    },{
      text: 'Cancel',
      handler: function() {Login.form.reset();}
    }]
  });
  
  Login.render(Ext.get('loginForm'));
});


PHP文件

<pre class="lang:php decode:true " title="Extjs" >if ($_GET['action'] == 'login') {
	$username = $_POST['username'];
	$password = $_POST['password'];

	if ($db->GetLogin($username, $password)){
		echo "{success:true,msg:'OK'}";
		exit();
	} else {
		echo "{success:true,msg:'Wrong user name or password.'}";exit();
	}
}