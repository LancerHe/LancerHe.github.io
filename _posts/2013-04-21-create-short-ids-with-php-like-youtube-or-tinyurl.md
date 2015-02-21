---
title: '创建一个类似Youtube的Id &#8211; 使用PHP/Python/Javascript/Java/SQL'
author: 谇雨
layout: post
permalink: /create-short-ids-with-php-like-youtube-or-tinyurl.html
views:
  - 126
categories:
  - PHP
tags:
  - youtube
  - 加密id
  - 短地址
---
> 本文译自：Create Youtube-like IDs with PHP/Python/Javascript/Java/SQL  
> <a href="http://kvz.io/blog/2009/06/10/create-short-ids-with-php-like-youtube-or-tinyurl/" target="">http://kvz.io/blog/2009/06/10/create-short-ids-with-php-like-youtube-or-tinyurl/</a>

id通常都是用数字，不巧的是只有10个数字来使用，所以如果你有很多的记录，id往往变得非常冗长。当然对于计算机来说无所谓，但我们更希望id尽可能短。所以我们如何能使id变短？我们可以利用字母让它们附加上数字。字母可以解决!

所以这里就有两个问题：

*   如何使用PHP和MySQL创建独特的短字符串id？
*   或如何创建id类似YouTube的方式，如yzNjIBEdyww？

我很久以前创建了这个函数。是时候和大家分享一下了。

#### 多就是少 &#8211; “这就是数学”

字母有26个字母。这是一个已经超过10字数。如果我们还区分大写和小写字母,并添加数字,我们已经有(26 x 2 + 10)=62个选项我们可以使用，让它出现在我们的id中。

当然我们现在也可以添加其他特殊字符如：/ * & #，但是那些可能导致的URL上的一些问题。

因为大约有60+字符，让id变的更短，所以我们有更多的选择可以使数据在每个位置。

这就是因为网址必须做的更加短，就像youtube上看到的：http://www.youtube.com/watch?v=yzNjIBEdyww

#### 转化你的id

不同于数据库服务器，网络服务容易收录这样的字符，这样你就可以让做一个转换id和字符串,同时保持你的数据库的快速与数字(MySQL真正喜欢它们纯数字);

由此产生的字符串不是很难破译, 但它可以是一个非常不错的功能使url或目录结构更紧凑的和更重要。

所以基本上:

*   当有人请求rLHWfKd
*   alphaID()将其转换为999999999999
*   你查找的记录id 999999999999在你的数据库

<pre class="lang:php decode:true " ><?php
/**
 * Translates a number to a short alhanumeric version
 *
 * Translated any number up to 9007199254740992
 * to a shorter version in letters e.g.:
 * 9007199254740989 --> PpQXn7COf
 *
 * specifiying the second argument true, it will
 * translate back e.g.:
 * PpQXn7COf --> 9007199254740989
 *
 * this function is based on any2dec && dec2any by
 * fragmer[at]mail[dot]ru
 * see: http://nl3.php.net/manual/en/function.base-convert.php#52450
 *
 * If you want the alphaID to be at least 3 letter long, use the
 * $pad_up = 3 argument
 *
 * In most cases this is better than totally random ID generators
 * because this can easily avoid duplicate ID's.
 * For example if you correlate the alpha ID to an auto incrementing ID
 * in your database, you're done.
 *
 * The reverse is done because it makes it slightly more cryptic,
 * but it also makes it easier to spread lots of IDs in different
 * directories on your filesystem. Example:
 * $part1 = substr($alpha_id,0,1);
 * $part2 = substr($alpha_id,1,1);
 * $part3 = substr($alpha_id,2,strlen($alpha_id));
 * $destindir = "/".$part1."/".$part2."/".$part3;
 * // by reversing, directories are more evenly spread out. The
 * // first 26 directories already occupy 26 main levels
 *
 * more info on limitation:
 * - http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-talk/165372
 *
 * if you really need this for bigger numbers you probably have to look
 * at things like: http://theserverpages.com/php/manual/en/ref.bc.php
 * or: http://theserverpages.com/php/manual/en/ref.gmp.php
 * but I haven't really dugg into this. If you have more info on those
 * matters feel free to leave a comment.
 *
 * The following code block can be utilized by PEAR's Testing_DocTest
 * <code>
 * // Input //
 * $number_in = 2188847690240;
 * $alpha_in  = "SpQXn7Cb";
 *
 * // Execute //
 * $alpha_out  = alphaID($number_in, false, 8);
 * $number_out = alphaID($alpha_in, true, 8);
 *
 * if ($number_in != $number_out) {
 *    echo "Conversion failure, ".$alpha_in." returns ".$number_out." instead of the ";
 *    echo "desired: ".$number_in."\n";
 * }
 * if ($alpha_in != $alpha_out) {
 *    echo "Conversion failure, ".$number_in." returns ".$alpha_out." instead of the ";
 *    echo "desired: ".$alpha_in."\n";
 * }
 *
 * // Show //
 * echo $number_out." => ".$alpha_out."\n";
 * echo $alpha_in." => ".$number_out."\n";
 * echo alphaID(238328, false)." => ".alphaID(alphaID(238328, false), true)."\n";
 *
 * // expects:
 * // 2188847690240 => SpQXn7Cb
 * // SpQXn7Cb => 2188847690240
 * // aaab => 238328
 *
 * </code>
 *
 * @author   Kevin van Zonneveld <kevin@vanzonneveld.net>
 * @author   Simon Franz
 * @author   Deadfish
 * @copyright 2008 Kevin van Zonneveld (http://kevin.vanzonneveld.net)
 * @license   http://www.opensource.org/licenses/bsd-license.php New BSD Licence
 * @version   SVN: Release: $Id: alphaID.inc.php 344 2009-06-10 17:43:59Z kevin $
 * @link   http://kevin.vanzonneveld.net/
 *
 * @param mixed   $in      String or long input to translate
 * @param boolean $to_num  Reverses translation when true
 * @param mixed   $pad_up  Number or boolean padds the result up to a specified length
 * @param string  $passKey Supplying a password makes it harder to calculate the original ID
 *
 * @return mixed string or long
 */
function alphaID($in, $to_num = false, $pad_up = false, $passKey = null)
{
  $index = "abcdefghijklmnopqrstuvwxyz0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ";
  if ($passKey !== null) {
      // Although this function's purpose is to just make the
      // ID short - and not so much secure,
      // with this patch by Simon Franz (http://blog.snaky.org/)
      // you can optionally supply a password to make it harder
      // to calculate the corresponding numeric ID

      for ($n = 0; $n<strlen($index); $n++) {
          $i[] = substr( $index,$n ,1);
      }

      $passhash = hash('sha256',$passKey);
      $passhash = (strlen($passhash) < strlen($index))
          ? hash('sha512',$passKey)
          : $passhash;

      for ($n=0; $n < strlen($index); $n++) {
          $p[] =  substr($passhash, $n ,1);
      }

      array_multisort($p,  SORT_DESC, $i);
      $index = implode($i);
  }

  $base  = strlen($index);

  if ($to_num) {
      // Digital number  <<--  alphabet letter code
      $in  = strrev($in);
      $out = 0;
      $len = strlen($in) - 1;
      for ($t = 0; $t <= $len; $t++) {
          $bcpow = bcpow($base, $len - $t);
          $out   = $out + strpos($index, substr($in, $t, 1)) * $bcpow;
      }

      if (is_numeric($pad_up)) {
          $pad_up--;
          if ($pad_up > 0) {
              $out -= pow($base, $pad_up);
          }
      }
      $out = sprintf('%F', $out);
      $out = substr($out, 0, strpos($out, '.'));
  } else {
      // Digital number  -->>  alphabet letter code
      if (is_numeric($pad_up)) {
          $pad_up--;
          if ($pad_up > 0) {
              $in += pow($base, $pad_up);
          }
      }

      $out = "";
      for ($t = floor(log($in, $base)); $t >= 0; $t--) {
          $bcp = bcpow($base, $t);
          $a   = floor($in / $bcp) % $base;
          $out = $out . substr($index, $a, 1);
          $in  = $in - ($a * $bcp);
      }
      $out = strrev($out); // reverse
  }

  return $out;
}</pre>

还有一个可选的第三个参数:$pad_up。这能使生成的alphaId至少X字符长。  
你可以支持更多字符(使生成的alphaID甚至更小)通过添加和修改函数顶部的$index变量。

#### Javascript方式

<pre class="lang:javascript decode:true">/**
 *  Javascript AlphabeticID class
 *  (based on a script by Kevin van Zonneveld <kevin@vanzonneveld.net>)
 *
 *  Author: Even Simon <even.simon@gmail.com>
 *
 *  Description: Translates a numeric identifier into a short string and backwords.
 *
 *  Usage:
 *    var str = AlphabeticID.encode(9007199254740989); // str = 'fE2XnNGpF'
 *    var id = AlphabeticID.decode('fE2XnNGpF'); // id = 9007199254740989;
 **/

var AlphabeticID = {
  index:'abcdefghijklmnopqrstuvwxyz0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ',

  /**
   *  [@function](http://twitter.com/function) AlphabeticID.encode
   *  [@description](http://twitter.com/description) Encode a number into short string
   *  [@param](http://twitter.com/param) integer
   *  [@return](http://twitter.com/return) string
   **/
  encode:function(_number){
    if('undefined' == typeof _number){
      return null;
    }
    else if('number' != typeof(_number)){
      throw new Error('Wrong parameter type');
    }

    var ret = '';

    for(var i=Math.floor(Math.log(parseInt(_number))/Math.log(AlphabeticID.index.length));i>=0;i--){
      ret = ret + AlphabeticID.index.substr((Math.floor(parseInt(_number) / AlphabeticID.bcpow(AlphabeticID.index.length, i)) % AlphabeticID.index.length),1);
    }

    return ret.reverse();
  },

  /**
   *  [@function](http://twitter.com/function) AlphabeticID.decode
   *  [@description](http://twitter.com/description) Decode a short string and return number
   *  [@param](http://twitter.com/param) string
   *  [@return](http://twitter.com/return) integer
   **/
  decode:function(_string){
    if('undefined' == typeof _string){
      return null;
    }
    else if('string' != typeof _string){
      throw new Error('Wrong parameter type');
    }

    var str = _string.reverse();
    var ret = 0;

    for(var i=0;i<=(str.length - 1);i++){
      ret = ret + AlphabeticID.index.indexOf(str.substr(i,1)) * (AlphabeticID.bcpow(AlphabeticID.index.length, (str.length - 1) - i));
    }

    return ret;
  },

  /**
   *  [@function](http://twitter.com/function) AlphabeticID.bcpow
   *  [@description](http://twitter.com/description) Raise _a to the power _b
   *  [@param](http://twitter.com/param) float _a
   *  [@param](http://twitter.com/param) integer _b
   *  [@return](http://twitter.com/return) string
   **/
  bcpow:function(_a, _b){
    return Math.floor(Math.pow(parseFloat(_a), parseInt(_b)));
  }
};

/**
 *  [@function](http://twitter.com/function) String.reverse
 *  [@description](http://twitter.com/description) Reverse a string
 *  [@return](http://twitter.com/return) string
 **/
String.prototype.reverse = function(){
  return this.split('').reverse().join('');
};
</pre>

其他语言请参照原文：  
<a href="http://kvz.io/blog/2009/06/10/create-short-ids-with-php-like-youtube-or-tinyurl/" target="">http://kvz.io/blog/2009/06/10/create-short-ids-with-php-like-youtube-or-tinyurl/</a>