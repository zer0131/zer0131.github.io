---
layout: post
title: php开发微信公众账号事例
category: php
keywords: 微信开发,php,wechat
description: 一个由php编写的微信公众账号的类
tags: 微信开发 PHP
---

* content
{:toc}

![wechat](http://blog.zhangenrui.cn/wechat_1.png)

## 写在前面的话

总是想写点儿对大家有用的东西，微信这个东西最近火的很，到处都是公众账号，微营销等等。这段时间专门研究了一些关于微信公众平台的东西，也做过一些小的项目，所以分享一些关于微信公众平台的东西。

<!--more-->

## 开发者模式

微信公众平台有两个模式：编辑模式和开发者模式，做为一个程序员，我们不看编辑模式，这是给不懂程序的人用的。我们重点看开发者模式。

![wechat 1](http://blog.zhangenrui.cn/wechat_2.jpg)

开发者模式主要是通过一定的代码程序来链接你已有的网站。那么使用开发者模式的第一步就是验证你的网站了。具体怎么验证可以参考微信中的帮助信息。

网站验证通过了我们就可以使用开发者模式了。微信官方给我们提供了一系列的api，我们通过调用这些api来实现和网站的链接。

## 官方API

> [http://mp.weixin.qq.com/wiki/home/index.html](http://mp.weixin.qq.com/wiki/home/index.html)

## 源码

{% highlight php %}
<?php
class Wechat{
	//签名
    private $token = '';
    //消息类型
    private $msgtype;
    //消息内容
    private $msgobj;
    //事件类型
    private $eventtype;
    //事件key值
    private $eventkey;
	#{服务号才可得到
    //AppId
    private $appid = "";
    //AppSecret
    private $secret = "";
	#}
	private $_isvalid = false;
	public function __construct($token,$isvalid = false){
		$this->token = $token;
		$this->_isvalid = $isvalid;
	}

	/**
	 *	执行程序入口
	 */
	public function index(){
		if($this->_isvalid){
			$this->valid();
		}
		$this->getMsg();
		$this->responseMsg();
	}

	/**
     *  初次校验
     */
	private function valid(){
        $echoStr = $_GET["echostr"];

        if($this->checkSignature()){
        	echo $echoStr;
        	exit();
        }
    }

    /**
     *  创建自定义菜单
     */
    private function createMenu(){
        $url = "https://api.weixin.qq.com/cgi-bin/menu/create?access_token=".$this->getAccessToken();
        $menujson = '{
            "button":[
                {
                    "type":"click",
                    "name":"NAME1",
                    "key":"V1001_NEW"
                },
                {
                    "type":"view",
                    "name":"NAME2",
                    "url":"http://www.zhangenrui.cn"
                },
                {
                    "type":"view",
                    "name":"NAME3",
                    "url":"http://www.zhangenrui.cn"
                }
            ]
        }';

        $ch = curl_init($url);

        curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "POST");
        curl_setopt($ch, CURLOPT_POSTFIELDS,$menujson);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER,1);

        $info = curl_exec($ch);

        if (curl_errno($ch)) {
            echo 'Errno'.curl_error($ch);
        }

        curl_close($ch);

        var_dump($info);
    }

    /**
     *  删除自定义菜单
     */
    private function deleteMenu(){
        $url = "https://api.weixin.qq.com/cgi-bin/menu/delete?access_token=".$this->getAccessToken();

        $ch = curl_init($url);

        curl_setopt($ch, CURLOPT_RETURNTRANSFER,1);

        $info = curl_exec($ch);

        if (curl_errno($ch)) {
            echo 'Errno'.curl_error($ch);
        }

        curl_close($ch);

        var_dump($info);

    }

    /**
     *  获取消息
     */
    private function getMsg(){
        //验证消息的真实性
        if(!$this->checkSignature()){
            exit();
        }

        //接收消息
        $poststr = $GLOBALS["HTTP_RAW_POST_DATA"];
        if(!empty($poststr)){
            $this->msgobj = simplexml_load_string($poststr,'SimpleXMLElement',LIBXML_NOCDATA);
            $this->msgtype = strtolower($this->msgobj->MsgType);
        }
        else{
            $this->msgobj = null;
        }
    }

    /**
     *  回复消息
     */
    private function responseMsg(){
        switch ($this->msgtype) {
            case 'text':
                $data = $this->getData($this->msgobj->Content);
                if(empty($data) || !is_array($data)){
                    $content = "ruiblog";
                	$this->textMsg($content);//查询不到记录返回提示信息
                }
                else{
                	$this->newsMsg($data);
                }
                break;
            case 'event':
                $this->eventOpt();
                break;
            default:
                # code...
                break;
        }
    }

    /**
     *  回复文本消息
     */
    private function textMsg($content=''){
        $textxml = "<xml><ToUserName><![CDATA[{$this->msgobj->FromUserName}]]></ToUserName><FromUserName><![CDATA[{$this->msgobj->ToUserName}]]></FromUserName><CreateTime>".time()."</CreateTime><MsgType><![CDATA[text]]></MsgType><Content><![CDATA[%s]]></Content></xml>";
        
        //做搜索处理
        if(empty($content)){
            $content = "查询功能正在开发中...";
        }
        $resultstr = sprintf($textxml,$content);
        echo $resultstr;
    }

    /**
     *  回复图文消息
     */
    private function newsMsg($data){
        if(!is_array($data)){
        	exit();
        }
        $newscount = (count($data) > 10)?10:count($data);
        $newsxml = "<xml><ToUserName><![CDATA[{$this->msgobj->FromUserName}]]></ToUserName><FromUserName><![CDATA[{$this->msgobj->ToUserName}]]></FromUserName><CreateTime>".time()."</CreateTime><MsgType><![CDATA[news]]></MsgType><ArticleCount>{$newscount}</ArticleCount><Articles>%s</Articles></xml>";
        $itemxml = "";
        foreach ($data as $key => $value) {
        	$itemxml .= "<item>";
        	$itemxml .= "<Title><![CDATA[{$value['title']}]]></Title><Description><![CDATA[{$value['summary']}]]></Description><PicUrl><![CDATA[{$value['picurl']}]]></PicUrl><Url><![CDATA[{$value['url']}]]></Url>";
        	$itemxml .= "</item>";
        }
        $resultstr = sprintf($newsxml,$itemxml);
        echo $resultstr;
    }

    /**
     *  事件处理
     */
    private function eventOpt(){
        $this->eventtype = strtolower($this->msgobj->Event);
        switch ($this->eventtype) {
            case 'subscribe':

                //做用户绑定处理

                $content = "ruiblog";
                $this->textMsg($content);
                break;
            case 'unsubscribe':
                
                //做用户取消绑定的处理

                break;
            case 'click':
                $this->menuClick();
                break;
            default:
                # code...
                break;
        }
    }

    /**
     *  自定义菜单事件处理
     */
    private function menuClick(){
        $this->eventkey = $this->msgobj->EventKey;
        switch ($this->eventkey) {
            case 'V1001_NEW':
            	$data = $this->getData();
                $this->newsMsg($data);
                break;
            default:
                # code...
                break;
        }
    }

    /**
	 *	获取本地数据
     */
    private function getData($key='ruiblog'){
		$data = $key;
		//写你自己相关的程序
    	return $data;
    }
	
    /**
     *  校验签名
     */
	private function checkSignature(){
        $signature = $_GET["signature"];
        $timestamp = $_GET["timestamp"];
        $nonce = $_GET["nonce"];	
        		
		$token = $this->token;
		$tmpArr = array($token, $timestamp, $nonce);
		sort($tmpArr);
		$tmpStr = implode( $tmpArr );
		$tmpStr = sha1( $tmpStr );
		
        return ($tmpStr == $signature)?true:false;
	}

    /**
     *  获取access token
     */
    private function getAccessToken(){
        $url = "https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=$this->appid&secret=$this->secret";
        $atjson=file_get_contents($url);
        $result=json_decode($atjson,true);//json解析成数组
        if(!isset($result['access_token'])){
            exit( '获取access_token失败！' );
        }
        return $result["access_token"];
    }
}
?>
{% endhighlight %}

> 下载地址：[http://pan.baidu.com/s/18GkNk](http://pan.baidu.com/s/18GkNk)
