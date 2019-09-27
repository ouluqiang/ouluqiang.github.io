---
title: 'kotlin基础'
date: 2019-09-27 13:17:32
tags: [kotlin]
published: true
hideInList: false
feature: 
---
### kotlin函数

main函数  

    fun main(args:array<String>){
        
    }

带参数带返回值

    fun sum(a:Int,b:Int):Int{
        return a+b
    }
    
表达式做函数体，返回值类型自动推断的函数

    fun sum(a:Int,b:Int)=a+b
    
函数无返回值
    
    fun sum(a:Int,b:Int){
        println("sun is ${a+b}")
    }
    
变量

val 只读
   
    val a:Int=1 //立即赋值
    val b=2 //自动推断出int类型
    val c:Int //类型
    c=3  //明确赋值
    
var 读写
    
    var a=5
    a+=1
    
注释跟java一样

表达式

    fun sum(a:Int,b:Int):Int{
        if(a>b){
            return a
        }else{
            return b
        }
    }
    
if作为表达式

    fun sum(a:Int,b:Int)=if(a>b) a else b
    
可空值 null检测

变量的值可以为null，必须在声明处的类型后添加?标识该引用可以为空
    
str的值不是数字返回null
    
    fun parseInt(str:String):Int?{
        
    }

使用返回可空值的函数

    fun parseInt(str:String):Int?{
        return str.toIntOrNull()
    }
    
    fun printInt(a:String,b:String){
        val x=parseInt(a)
        val y=parseInt(b)
        
        
        //直接使用x y 可能会报错，因为可能为空
        if(x!=null&&y!=null){
            println(x*y)
        }else{
            println("error")    
        }
        
        //或者检测空值，x y会自动转换成非空值
        if(x==null){
            println("error")
            return
        }
        if(y==null){
            println("error")
            return
        }
        println(x*y)
    }
    
    
使用类型检测及自动类型转换

is运算符检测一个表达式是否某类型的一个实例，如果一个不可变的局部变量或属性已经判断出为某类型，检测后的分支中可以直接当作该类型使用，不需显示转换：

    fun getStringLength(obj:Any):Int?{
        if(obj is String){
            return obj.length
        }
        return null
    }
    
或者

    fun getStringLength(obj:Any):Int?{
            if(obj !is String){
                return null
            }
            //自动转换为 string
            return obj.length
        }
        
    fun getStringLength(obj:Any):Int?{
        if(obj is String&&obj.length>0){
            return obj.length
        }
        return null
    }
    

for循环.


    val list= listOf<String>("a","b","c")
    
    //直接循环
    for (l in list){
        println(l)
    }
    
    //根据下标找数据  
    for (index in list.indices){
        println("index ${list[index]}")
    }

    //while循环  
    var i=0
    while (i<list.size){
        println("while ${list[i]}")
        i++
    }
    

when表达式

类似java的  

        switch (){
            case 0:
                break;
        }

    fun getWhen(s:String):String?{
        when(s){
            "a"-> return "a1"
            "b"-> return "b1"
            "c"-> return "c1"
        }
        return null
    }

range 使用区间  

    val x=1
    val y=10
    if (x in 1..y+1){
        println("range")
    }
    
检测某个数字是否在区间内  

    val list= arrayListOf<String>("a","b","c")
    val i=-1
    if (i !in 0..list.lastIndex){
        println("${i}")
    }
    
    //list.indices 指list 0..list.size-1的长度
    if (list.size !in list.indices){
        println("${list.indices}---${list.size}")
    }
    
    //打印 0-10的数
    //包含10
    for (x in 0..10){
        println(x)
    }
    //不包含10
     for (x in  0 until 10){
        println(x)
    }

    //0-10中,顺序隔3个打印
    for (x in 0..11 step 3){
        println(x)
    }

    //倒叙隔4个排序
    for (x in 11 downTo 0 step 4){
        println(x)
    }
    
循环迭代  

    for (x in list){
        println(x)
    }
    
用in判断集合是否包含实例

    //如果包含set就进判断，不走后续判断，类似java switch ()
    val set= setOf<String>("aa","bb","cc")
    when{
        "a" in set -> println("这是 a")
        "bb" in set -> println("这是 b")
        "cc" in set -> println("这是 c")
    }
    
用lambada过滤（filter）,映射（map）
  
    list.filter { it.startsWith("a") }
            .sortedBy { it }
            .map { it.toUpperCase() }
            .forEach { println(it) }


创建数据类

    data class UserBean(val uid:Int=0,var username:String?=null,var password:String?=null)

包含  get ,  set ,   toString , equals , hashCode , copy

    
函数参数默认值
    
    fun getWhen(s:String="",i:Int=0){}
    
过滤list

    val list= arrayListOf<Int>(1,2,3,41,5,16,5,3,21,1)
    //val s=list.filter { x-> x > 9 }
    val s=list.filter { it>3}
    for (l in s){
        println(l)
    }
    
String内插

    println("${list.indices}---${list.size}")
    
类型判断
    
    when(s){
       is UserBean -> return "a1"
        "b" -> return "b1"
       is BaseBean<*> -> return "c1"
    }

map 遍历map

    val map= mapOf("a" to "aa","b" to "bb","c" to "cc")
    for ((k,v) in map){
        println("${k}-----${v}")
    }

    //打印map的value
    println(map["a"])
    //给map赋值
    var map= HashMap<String,String>()
    map["a"]="asdfas"
    println(map["a"])
    
    
lateinit 和 lazy 是 Kotlin 中的两种不同的延迟初始化的实现

    lateinit 只用于变量 var，而 lazy 只用于常量 val

创建单例模式

    //object修改类，为单例模式
    object Bean{}
    
用 ? 修饰代表可以为null

    val file=File("T").listFiles()
    println(file?.size)
    
kotlin 类型

    Double
    Float
    Long
    Int
    Short
    Byte
    
    Long类型在后面加L：  123L
    Float类型在后面加F： 123F
    
kotlin 1.1起
使用下划线使常量易读

    val a=1_000_000
    val b=1_0000_0000L
    val c=1_000.000_001
    
显式转换

    //a为int,b为long类型,编译不通过需要显式转换
    if (a==b)
    
    if (a==b.toInt()){}
    
    
运算

    位运算符只用 Int Long:
    
    shl(bits) -有符号左移（java的<<）
    shr(bits) -有符号右移（java的>>）
    ushr(bits) -无符号右移（java的>>>）
    and(bits) -位与
    or(bits) -位或
    xor(bits) -位异或
    inv() -位非
    
字符 char
    
    //char显示转换 int
    val s='1'
    s.toInt()

Boolean

    ||    或
    &&    与
    !     非

数组
    
    Array
    
字符串
    
    //字符串添加转义字符
    val s="hello\nworld"

字符串模板

    // $
     val s="hello\nworld"
    val sa="x= ${s}"
    println(sa)

if 表达式

    if(true){
        
    }elese{
        
    }

when 表达式

    val s=1
    //when取代了switch操作符
    when(s){
        1 -> println("aa")
        //多个分支放一起，用,分隔
        2,3 -> println("bb")
        //用表达式做条件
        getInt() -> println("cc")
        //检测是否在区间
        in 5..10 -> println("dd")
        //其他分支不满足进else
        else ->{
            println("else")
        }
    }

for循环

while , do while

    while(s>0){

    }

    do{
        val y=1
    }while(y!=null)
    
返回和跳转

    return: 从最直接包围他的函数或匿名函数返回
    break:终止直接包围他的循环
    continue:继续下一次直接包围他的循环

类
    
    class修饰
    
构造函数
    
    //constructor主构造函数，一个或多个次构造函数  
    //主构造函数有注解或可见性修饰符，不可以省略
    //主构造函数可以省略，主构造函数不能包含任何代码
    class Test constructor(string: String){

        //初始化块代码，主构造参数可以在init中使用
        init {

        }
    
    
    class Test(val string: String,age:Int) {

        //初始化块代码，主构造参数可以在init中使用
        init {
    
        }
    
        //次级构造函数,次级构造函数委托给主构造函数需要添加this
        constructor(s: String):this(s,0)
        }
    }

创建类的实例

    //调用构造函数，kotlin没有new
    val text=Test("a",0)
    
继承
    
    覆盖函数
    覆盖属性
    覆盖规则
    下次再学    
    
    
抽象类
    
    abstract修饰
    
伴生对象


属性和字段

    属性用var是可变，val是只读
    
    class Test(var string: String?=null,var age:Int?=0) 
    
    //用其中某个属性，用名称引用他
    val text=Test()
    text.age
    
get set

幕后字段， 幕后属性，编译器常量，惰性初始化属性，覆盖属性，委托属性


接口

    用interface修饰
    一个类或对象可以实现一个或多个接口

接口中的属性
解决覆盖冲突

    interface A{
        fun getA(){
            println("a")
        }
    }
    
    interface B{
        fun getA(){
            println("ba")
        }
        fun getB(){
            println("bb")
        }
    }
    
    class C:A,B{
        override fun getA() {
            //TODO("not implemented") //To change body of created functions use File | Settings | File Templates.
            //指定实现哪个父类的接口
            super<A>.getA()
            super<B>.getA()
        }
    
        override fun getB() {
            super.getB()
        }
    
    }

可见性修饰符 
    
    默认是public
    prublic 声明随处可见
    private 声明的文件内可见，类内部可见
    protected 不适用于顶层声明，和private一样+在子类中可见
    internal 相同模块随处可见
    
构造函数

    //指定类的主构造函数的可见性，必须添加显示的 constrector
    
    class Test private constructor(var string: String?=null,var age:Int?=0) 

默认所有构造函数是public

局部声明

    局部变量，函数和类不能有可见性修饰符

模块

    可见性修饰符 internal 只在相同模块内可见，一个模块是编译在一起的一套kotlin文件
    一个idea模块
    一个maven或者gradle项目
    一次<kotlinc>Ant人物执行所编译的一套文件

扩展

扩展函数

扩展是静态解析  
    
    （有空在学吧）

可空接收者

扩展属性

伴生对象的扩展

扩展的作用

扩展声明为成员

动机

    下次学
    
数据类

生成的类含有一个无参的构造函数，所有的属性必须指定默认值

    数据类标记为 data
    data class Test (var string: String?=null,var age:Int?=0) 
    
复制    

复制一个对象改变一些属性，保持其他属性不变
    
    var text=Test()
    text.string="as"
    text.age=11
    text=text.copy(age = 14)
    println(text.toString())
    



































