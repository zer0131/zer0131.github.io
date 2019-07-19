---
layout: post
title: php与java md5和base64加密
category: php
keywords: php,java,签名,md5,base64
tags: php java 签名 md5 base64
---

* content
{:toc}

## 写在前面的话

很久没有更新博客了，感觉很惭愧，真的很惭愧！！！

最近正好在用php对接java的接口，数据在传输过程中涉及到了对数据的签名校验，经过一番折腾，这里整理一下关于php和java各自的签名如何达到一致的方法。

有兴趣的朋友可以贴走源代码~~~

<!--more-->

## 签名方式

1. 拿到传输的数据，当然这个数据肯定是***string*类型的了，数据假如是“你好”
2. 上下游持有一个相同的secret，比如：123456
3. 拼接数据和secret，如：你好123456
4. 对拼接后的字符串做MD5加密
5. MD5后的字符串再做base64加密
6. 最终得到我们的签名字符串

## java实现方式

```java
package zer.sign;

import sun.misc.BASE64Encoder;

import java.security.MessageDigest;

public class Sign {
    public String digest(String toVerifyText, String encode) throws Exception {

        String base64Str = null;

        MessageDigest md5 = null;

        try {

            md5 = MessageDigest.getInstance("MD5");

            md5.update(toVerifyText.getBytes(encode));

            byte[] md = md5.digest();

            base64Str = new BASE64Encoder().encode(md);

        } catch (Exception e) {

            throw new Exception("BASE64与MD5加密异常", e);

        }

        return base64Str;
    }
}
```

## php实现方式

### Bytes类

```php
class Bytes {

    /**
     * 转换一个String字符串为byte数组
     * @param  string $str 需要转换的字符串
     * @return array $bytes 目标byte数组
     */
    public static function getBytes($str) {

        $len   = strlen($str);
        $bytes = [];
        for ($i = 0; $i < $len; $i++) {
            if (ord($str[$i]) >= 128) {
                $byte = ord($str[$i]) - 256;
            } else {
                $byte = ord($str[$i]);
            }
            $bytes[] = $byte;
        }
        return $bytes;
    }

    /**
     * 将字节数组转化为String类型的数据
     * @param array $bytes 字节数组
     * @return  string $str   目标字符串
     */
    public static function toStr($bytes) {
        $str = '';
        foreach ($bytes as $ch) {
            $str .= chr($ch);
        }
        return $str;
    }

    /**
     * 转换一个int为byte数组
     * @param int $val 需要转换的字符串
     * @return array $byt 目标byte数组
     */
    public static function integerToBytes($val) {
        $byt    = [];
        $byt[0] = ($val & 0xff);
        $byt[1] = ($val >> 8 & 0xff);
        $byt[2] = ($val >> 16 & 0xff);
        $byt[3] = ($val >> 24 & 0xff);
        return $byt;
    }

    /**
     * 从字节数组中指定的位置读取一个Integer类型的数据
     * @param array $bytes    字节数组
     * @param int $position 指定的开始位置
     * @return int 一个Integer类型的数据
     */
    public static function bytesToInteger($bytes, $position) {
        $val = 0;
        $val <<= 8;
        $val |= $bytes[$position + 2] & 0xff;
        $val <<= 8;
        $val |= $bytes[$position + 1] & 0xff;
        $val <<= 8;
        $val |= $bytes[$position] & 0xff;
        return $val;
    }

    /**
     * 转换一个shor字符串为byte数组
     * @param int $val 需要转换的字符串
     * @return  array $byt 目标byte数组
     */
    public static function shortToBytes($val) {
        $byt    = [];
        $byt[0] = ($val & 0xff);
        $byt[1] = ($val >> 8 & 0xff);
        return $byt;
    }

    /**
     * 从字节数组中指定的位置读取一个Short类型的数据。
     * @param array $bytes 字节数组
     * @param int $position 指定的开始位置
     * @return int 一个Short类型的数据
     */
    public static function bytesToShort($bytes, $position) {
        $val = $bytes[$position + 1] & 0xFF;
        $val = $val << 8;
        $val |= $bytes[$position] & 0xFF;
        return $val;
    }
}
```

### 加密方法

```php
$data = '你好123456';
$sign = base64_encode(Bytes::toStr(unpack("c*", md5($data, true))));
```

## 关于pach/unpack

* pack：该函数用来将对应的参数($args)打包成二进制字符串
* unpack：unpack的使用相当简单，就是讲pack打包的数据解包，打包的时候用的什么参数，就用什么参数解包

参考链接：<a href="https://blog.csdn.net/qq_34908844/article/details/79696135" target="_blank">https://blog.csdn.net/qq_34908844/article/details/79696135</a>




