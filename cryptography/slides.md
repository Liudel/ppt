---
fonts:
  mono: 'LXGWWenKaiGBScreenR'
  serif: 'LXGWWenKaiGBScreenR'
  sans: 'LXGWWenKaiGBScreenR'
  local: 'LXGWWenKaiGBScreenR'
layout: cover


lineNumbers: true

---

# 密码学简史

在不同时代的加密算法

<div class="uppercase text-sm tracking-widest">
增长后端 王若愚
</div>

<div class="abs-bl mx-14 my-12 flex">
  <img src="/public/pilot-bust.svg" class="h-8">
  <div class="ml-3 flex flex-col text-left">
    <div><b>Share</b>Day</div>
    <div class="text-sm opacity-50">Oct. 26th 2023</div>
  </div>
</div>

---
layout: center

---

# 1、什么是密码学

---
clicks: 3
---

## 1.1、密码学的定义
<br/>

##### wiki百科对密码学的定义:

> Cryptography is about constructing and analyzing protocols that prevent third parties or the public from reading private messages.
> 密码学是关于构建和分析防止第三方或公众读取私人消息的协议

<div class="grid grid-cols-2 gap-x-4">
<div class="mt-8 col-span-1" v-click="1">
<img src= "SCR-20231025-ntxi.png" class="w-4/5 ml-5">
</div>
<div class="mt-8 col-span-1" v-click="2">


#### 【智取威虎山有这么段台词】

土匪：天王盖地虎!

<div v-click="3"> 

> 释意：你好大的胆!敢来气你的祖宗? 

</div>


杨子荣：宝塔镇河妖!

<div v-click="3"> 

> 释意：要是那样，叫我从山上摔死，掉河里淹死。


</div>
</div>
</div>

<!--
1、我们平时使用的密码用户口令来说明更确切一些

2、智取威虎山改编自《林海雪原》，讲述侦察英雄杨子荣与威虎山座山雕匪帮斗智斗勇的故事
-->

---
layout: center

---

# 2、古典密码学

---

## 2.1、[换位加密法](https://zh.wikipedia.org/wiki/%E7%BD%AE%E6%8D%A2%E5%BC%8F%E5%AF%86%E7%A0%81)

换位加密法（Transposition Cipher）是指在加密的过程中不改变字或字母本身，只改变它们的排列或阅读顺序，以达到加密的目的。

古代西方人有时会把单词或句子逆序写出，这也属于换位加密。如把I have a book （我有一本书）写成koob a evah I。

中国古代有一种文字形式叫“藏头诗”，是在一首看似普通的诗中，把每句的第一个字连起来读，就会表达出另一种意思。这也可以算是一种换位加密法。如《水浒传》中的智多星吴用在玉麒麟卢俊义家中的墙上写下了这样一首诗：

<div class="text-center">
芦花丛中一扁舟，
<br>
俊杰俄从此地游。
<br>
义士若能知此理，
<br>
反躬难逃可无忧。
</div>

---

## 2.2、[替换加密法](https://zh.wikipedia.org/wiki/%E6%9B%BF%E6%8D%A2%E5%BC%8F%E5%AF%86%E7%A0%81)

替换加密法（Substitution Cipher）就是不改变信文中字或字母的顺序，而是用其他的符号来替换它们，以此达到加密的目的。

由于西方使用拼音文字，所以很适合用替换加密。事实上，直到计算机密码的出现，替换加密法是西方近代以来使用的主要加密方法。

<div class="grid grid-cols-2 gap-x-4">
<div class="col-span-1">
1. 恺撒密码

<img src="kaisa.png" class="mt-2">

Hello world

等价于

Khoor zruog

</div>
<div class="col-span-1" v-click>
2. 单表替换密码

<img src="single_replace.png" class="mt-2">
</div>
</div>

---

## 2.3、破解替换加密法

1、穷举法

a对应着 26种可能，b对应着25种可能，c对应着24种可能，列出所有的可能。所有，最后总共由 26! 种可能。这个时间复杂度算比较高的了，这么破解也很麻烦。

2、[频率分析](https://zh.wikipedia.org/w/index.php?title=%E9%A2%91%E7%8E%87%E5%88%86%E6%9E%90&oldformat=true&variant=zh-cn)
<div class="grid grid-cols-2 gap-x-4">
<div class="col-span-1">
频率分析是在9世纪时，阿拉伯博学家－肯迪在其所著的《手稿上破译加密消息》中提出的。 它对于古兰经的文本研究发现阿拉伯文有一个特别的字母频率。主要说明在一篇文章中，每个字母出现的频率不是均等的。比如，字母E出现的频率很高，而X则出现得较少。类似地，ST、NG、TH，以及QU等双字母组合出现的频率非常高，NZ、QJ组合则极少。
基于此，单表替换密码的破译就会轻松很多。
</div>
<div class="col-span-1">
<img src="SCR-20231025-sdfa.png" class="w-3/4 ml-10">
</div>
</div>

---
layout: center

---

# 3、现代密码学

---

## 3.1、现代密码学解决的问题

<img src="11698242096_.pic.jpg" class="w-5/9 ml-46">

---

## 3.2、对称加密

<div class="grid grid-cols-2 gap-x-4">
<div class="col-span-1">
<br>
<br>

1. 加密算法就是加密和解密过程的密钥是相同的算法。
2. 优点是加解密效率（速度快，空间占用小）和加密强度都很高。
3. 缺点是参与方需要提前持有密钥，一旦有人泄露则系统安全性被破坏。
4. 对称密码从实现原理上可以分为两种：分组加密和序列加密。前者将明文切分为定长数据块作为基本加密单位，应用最为广泛。后者则每次只对一个字节或字符进行加密处理，且密码不断变化，只用在一些特定领域（如数字媒介的加密）。

</div>
<div class="col-span-1">
<img src="20211118173239.png" class="mt-20">
</div>
</div>

---

## 3.3、非对称加密

<div class="grid grid-cols-2 gap-x-4">
<div class="col-span-1">
<br>

1. 非对称加密就是加密密钥和解密不同的算法。分别称为公钥（Public Key）和私钥（Private Key）。私钥一般通过随机数算法生成，公钥可以根据私钥生成。
2. 优点是公私钥分开，无需安全通道来分发密钥。
3. 缺点是处理速度（特别是生成密钥和解密过程）往往比较慢，一般比对称加解密算法慢 2~3 个数量级；同时加密强度也往往不如对称加密。
4. 非对称加密算法的安全性往往基于数学问题，
   1. 整数分解：素数相乘得到的因式很难分解回来
   2. 离散对数：假设在整数范围内，对数$x = \log_a b$已知a,x, 计算b比容易，但是已知a,b，计算x就比较麻烦
</div>
<div class="col-span-1">
<img src="20211118195321.png" class="mt-20">
</div>
</div>


---

## 3.3、非对称加密

<br>

3. 椭圆曲线：椭圆曲线通用方程为$y^3 =x^3 + ax + b$ 任何一条不垂直的直线与曲线的交点不超过3个定义一种运算，曲线上两个点为AB，AB连直线得到C’，C’关于x轴有对称点C，称作
A dot B=C。给定一个初始点A，一个动点B，经过n次dot运算之后得出Z，从A Z求出n比较困难。

<img src="20211118203903.gif" class="w-2/5 ml-52">

---

## 3.4 对称加密和非对称加密
<br>
<br>

|   算法类型     | 优势 | 缺陷 | 代表算法 |
| ---- | ---- | ---- | ---- |
| 对称加密    | 计算效率高，加密强度高 | 需提前共享密钥，易泄露|DES、3DES、AES、IDEA |
| 非对称加密  | 无需提前共享密钥 |计算效率低，存在中间人攻击可能 | RSA、ElGamal、椭圆曲线算法 |

---

## 3.5 TLS

<br>

- 协商密钥原理：

前面说了 A 点和 Z 点，计算出 n 是十分困难的。<br>
换句话说，知道了公钥和椭圆曲线基点等公开信息，要破解出私钥 k 是非常困难的。

<img src="2023-10-19-09-21-59.png" class="w-3/5 mt-10 ml-45">

---

## 3.5 TLS

<p style="margin-bottom:0;"> TLS1.2 握手过程：</p>

<img src="2023-10-16-04-06-23.jpg" class="w-3/7 ml-50">

---
layout: center
class: text-center pb-5

---

# THANKS!
