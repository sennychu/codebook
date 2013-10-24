codebook
========

create by sennychu

http://wenku.baidu.com/view/68e6f0d484254b35eefd34e8.html

http://www.rpm.org/wiki/GetSource

http://wenku.baidu.com/view/68e6f0d484254b35eefd34e8.html

http://www.rpm.org/wiki/GetSource

http://wenku.baidu.com/view/68e6f0d484254b35eefd34e8.html

http://www.linuxnix.com/2008/05/how-to-install-yum-server-in-redhatrhel5.html


 js的几个概念
分类： 学习-javascript 2008-11-28 20:11 179人阅读 评论(0) 收藏 举报
prototypefunctionconstructorobjectjavascripttts
================================================================================================================
一. javascript函数的几个相关概念
================================================================================================================
---------------------------------------------------------------
概念1. 一个函数被定义, 实际上就是创建了一个Function类的对象.
---------------------------------------------------------------

在以下4种方法等效的方法定义中,可清晰看到上述结论

方法1(标准)
function showText(text)
{
    alert(test);
}
showText("hi, tomsui!~");

方法2
show = function showText(text)
{
    alert(text);
}
show("hi, tomsui!~");

方法3(匿名方法)
show = function(text)
 {
     alert(text);
 }
 show("hi, tomsui!~");

方法4(显式定义对象)
show = new Function("text","alert(text);");
show("hi, tomsui!~");

注意: 作为一个对象,肯定会有默认的toString(),可以直接打印这个对象,其实就是函数体本身:
      alert(show)        // function anonymous(text) { alert(text); }
      alert((showText))  // function showText(text)  { alert(test); }


---------------------------------------------------------------
概念2.  Function类的对象有一个prototype属性
---------------------------------------------------------------
这个prototype属性是一个对象:

function showText(text)
{
    alert(test);
}
alert(showText.prototype);             // [object, Object]
alert(typeof(showText.prototype));     // object
alert(showText.prototype.constructor); // function showText(text) { alert(test); }

 

构造器的prototype属性作为一个object, 本身存在一个不可for-in的属性"constructor",指向该构造器本身(打印出来的话是函数体定义)

注意: "Function类的对象",不是每一个普通对象(通过new关键字生成)都有prototype属性的!


---------------------------------------------------------------
概念3.  Function类的对象有一个constructor属性
---------------------------------------------------------------

function showText(text)
{
    alert(test);
}

alert(showText.constructor);          // function Function() { [native code] }
alert(typeof(showText.constructor));  // function

关于Function类的对象(showText函数)的constructor属性:
1) 是"一段不可显示的本地代码" (而不像是showText的prototype属性一样是一个对象)
2) 它的类型是"function"


---------------------------------------------------------------
总结.   prototype属性，constructor属性, construct属性 概述
---------------------------------------------------------------

如果把对象( 根据一个对象是否为Function对象 )分为两类:
a. 一个函数对象  (Function类的对象)
b. 一个普通对象  (通过new关键字结合构造函数创造的"非Function类"对象)

那么:
1. 函数对象存在prototype  属性, 而普通对象不存在;
2. 函数对象存在constructor属性, 普通对象不存在; constructor属性的type是"function",toString后是一段“本地代码”(native)，
3. 普通对象存在construct  属性, 函数对象不存在; construct  属性是type是"function" ,toString后是“函数代码本身”，

 

================================================================================================================
二. javascript面向对象的几个结论(new,prototype)
================================================================================================================

---------------------------------------------------------------
2.1 引子
---------------------------------------------------------------

因为javascript最初设计的面向对象存在缺陷，导致如果每一个对象关于"类的方法都进行一次硬拷贝"的问题:
function ShowText(text)
{
    this.word = text;
    this.sing = function singFunction() {alert("HelloWorld!~");};
}

obj1 = new ShowText("sb1");
obj2 = new ShowText("sb2");

这样的话obj1和obj2在内存中分别有一份singFunction的方法代码，显然这很sb,有两种改进方式:

1) 外部定义函数对象，
function ShowText()
{
    this.sing = singFunction;
}

function singFunction(){    alert("HelloWorld!~"); }

new ShowText().sing();


2) 利用函数对象的prototype属性
function ShowText()
{
}
ShowText.prototype.sing = function(){ alert( "HelloWorld!~");}
new ShowText().sing();


---------------------------------------------------------------
2.2  new关键字的作用
---------------------------------------------------------------

请对比如下的例子:
function ShowText(text)
{
    this.word = text;
}

var obj = new ShowText("hi,tomsui!~");
alert(obj.word);                        // "hi,tomsui!~"

var obj2 = new Object();
obj2.constuct = ShowText;
obj2.constuct("hi,tomsui!~");
alert(obj2.word);                      // "hi,tomsui!~"

通过运行结果可以看出存在如下等价关系:
var ball0=new Ball("creating new Ball");
<==>
var ball0=new Object();
ball0.construct=Ball;
ball0.construct("creating new ball");

分析上述等价关系,可得到"new"关键字背后的实际动作如下:
a. 创建一个新的，空的object -- new Object()

b. 调用这个object的constructor;
   obj.construct也是一个对象,对象都具备prototype属性,这是不必说的.

c. [补充] 拷贝这个object的construct的prototype属性到新生成的object中来(软拷贝引用)

说明: 1) "new"关键字让一个普通函数变为了构造器，可以把它理解为一种用于简化创建对象步骤的"约定"
      2) 等价关系只能证明new的前两条作用，关于的prototype的叙述参见下边"2.3  prototype"

---------------------------------------------------------------
2.3  prototype
---------------------------------------------------------------
1. 首先注意:
1) prototype是函数对象才具备的属性,普通对象是没有的．
　 -- 我对一些sb书上说它是一切对象的属性（因为它是Object对象的属性）感到困惑

2) 构造函数prototype属性本身也是一个object，它具有如下特征:

   a. 它存在一个constructor属性指向函数对象的代码 (死规定，记住就ok), 例如：

   　　　function showText(text) { alert(test);}
   　　　// 下面证明了showText的prototype属性是对象
         alert(typeof(showText.prototype));     // object
         // 下面说明了showText.prototype这个对象不能用默认toString()打印
         alert(showText.prototype);             // [object, Object]
         // 下面证明showText.prototype.constructor的存在性
         alert(showText.prototype.constructor); // function showText(text) { alert(test); }
         alert(typeof showText.prototype.constructor); // function

   b. 它像其他对象一样可以添加属性，例如:
         // 添加属性text属性
         function showText() {};
         showText.prototype.text = "HelloWorld!~";

　　　　 // 添加shout()方法
         function showText() {}
         showText.prototype.shout = function(){alert("tomsui wants to smoke!~");};

2. prototype的内幕

　 回想new关键字的幕后机制:
　 先用构造器生成一个对象, 然后"软拷贝构造器prototype属性的内容到新生成的object中来"。
   下面，对引号中强调的内容有必要的两点解释:

   1) "构造器prototype属性的内容"是什么?
       构造器prototype属性是一个对象，那么它的内容包括两部分: a. 属性值　b. 方法

   2) 什么是"软拷贝"?
      就是仅仅拷贝内容的引用到新生成obj,并不是真正的prototype中的内容(软拷贝是tomsui借用linux中"ln -s"的说法，比较简洁吧～)

　 "软拷贝构造器prototype属性的内容到新生成的object中来"恐怕就是prototype的核心内幕了.


3. 引入prototype对javascript的影响

   1) 为创建对象添加附加动作
   　　　为构造器的prototype新添加的任何"属性"和"方法"，都会被new关键字在使用构造器创建对象时，附加到新的对象中去。
      　 例如：

         function ShowText() {};
         // 添加text属性
         ShowText.prototype.text = "HelloWorld!~";
         alert(new ShowText().text);                   // HelloWorld!~

　　　　 // 添加shout()方法
         ShowText.prototype.shout = function(){alert("tomsui wants to smoke!~");};
         new ShowText().shout();                      // tomsui wants to smoke!~

　       上例中,构造器ShowText()本来可是没有text属性和shout()方法的哦～

　　2) prototype 不会影响到构造器的动作
　　　　所以如果两者发生矛盾时，构造器生效， -- 这非常值得注意！

　　　　例如:
        function ShowText()
        {
            this.text = "tomsui is handsome!~";
            this.say = function(){alert("absolutely!")};
        }

        ShowText.prototype.text = "J.J. is handsome!~";
        ShowText.prototype.say = function(){alert("perhaps?")};

        var obj = new ShowText();
        alert(obj.text);　　　　　　　　　// tomsui is handsome!~
        obj.say();                        // absolutely!

        结果是: prototype界定的"J.J. is handsome!~"没有生效，(毕竟是伪科学么);
                构造器讲出了"tomsui is handsome!~","absolutely!", 因为这是真理，不是prototype可以推翻的

　　3) prototype的出现，让js发生了所谓的"极晚绑定"。
　　　 晚绑定("后期绑定")可以参考TIJ或其他面向对象的书吧，不说了。
　　　"极晚绑定"让javascript的伪面向对象更加变态:即便已经new出了一个对象实例，你仍然可以为其添加新的行为和属性，
　　　 例如:
        function Person(name)
        {
            this.name = name;
            this.wordsInHeart = "tell Rora I love her~";
        }
        var tts = new Person("tomsui");　　// tell Rora I love her~
        alert(tts.wordsInHeart);

        Person.prototype.saySomeOtherWords = function(){alert("tell Rora I need her~")};
        tts.saySomeOtherWords();

        看见没有:
        名为tomsui的Person对象出现时本来只会说"tell Rora I love her~"，
        但自从他学会了prototype招数，泡妞技术更上一层楼，
        于是补充他又会多说: "tell Rora I need her~"。

        这个例子可见"极晚绑定"的好处，有得有失，这种动作多了，你的js成本会很高(就是慢贝),所以谨慎使用。


That is not UTF-8, that is percent encoding also known as url encoding.

You can use decodeURIComponent() to convert it back before displaying it

$("#quote1 span").html(decodeURIComponent(text1));
