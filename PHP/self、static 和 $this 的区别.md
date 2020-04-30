原文地址 https://blog.csdn.net/lamp_yang_3533/article/details/79912453  

self、static 和 $this 的区别
-----------------------

为了更好地理解 self、static 和 $this 的区别，先来看一个示例。

``` php
1.  <?php
2.  class A {
3.
4.      protected $name = 'A';
5.      static $alias = 'a';
6.      const HASH = 'md5';
7.
8.      public function dd() {
9.          echo $this->name; echo '--';
10.          echo static::$alias; echo '--';     // 后期静态绑定
11.          echo static::HASH; echo '--';     // 后期静态绑定
12.          echo self::$alias; echo '--';
13.          echo self::HASH; echo '--';
14.
15.          var_dump(new self); echo '--';
16.          var_dump($this); echo '--';
17.          var_dump(new static); echo '<br>';   // 后期静态绑定
18.      }
19.
20.      public static function who() {
21.          echo __CLASS__;
22.          echo ' [ This is A ]'; echo '<br>';
23.      }
24.
25.      public static function test() {
26.          self::who();
27.      }
28.
29.      public static function test2() {
30.          static::who();  // 后期静态绑定
31.      }

33.      public static function getInstance() {
34.          var_dump(new self); echo '--';
35.          var_dump(new static); echo '<br>';  // 后期静态绑定
36.      }
37.  }
38.
39.  class B extends A {
40.      protected $name = 'B';
41.      static $alias = 'b';
42.      const HASH = 'sha1';
43.
44.      public static function who() {
45.          echo __CLASS__;
46.          echo ' [ This is B ]'; echo '<br>';
47.      }
48.  }
49.
50.  class C extends B {
51.      public static function who() {
52.          echo __CLASS__;
53.          echo ' [ This is C]'; echo '<br>';
54.      }
55.  }

58.  (new A)->dd();  // A--a--md5--a--md5--object(A)#2 (1) { ["name":protected]=> string(1) "A" } --object(A)#1 (1) { ["name":protected]=> string(1) "A" } --object(A)#2 (1) { ["name":protected]=> string(1) "A" }
59.  (new B)->dd();  // B--b--sha1--a--md5--object(A)#2 (1) { ["name":protected]=> string(1) "A" } --object(B)#1 (1) { ["name":protected]=> string(1) "B" } --object(B)#2 (1) { ["name":protected]=> string(1) "B" }

61.  A::who();  // A [ This is A ]
62.  B::who();  // B [ This is B ]

64.  A::test();  // A [ This is A ]
65.  B::test();  // A [ This is A ]

67.  A::test2(); // A [ This is A ]
68.  B::test2(); // B [ This is B ]
69.  C::test2(); // C [ This is C]

71.  A::getInstance();   // object(A)#1 (1) { ["name":protected]=> string(1) "A" } --object(A)#1 (1) { ["name":protected]=> string(1) "A" }
72.  B::getInstance();   // object(A)#1 (1) { ["name":protected]=> string(1) "A" } --object(B)#1 (1) { ["name":protected]=> string(1) "B" }
```

总结说明：

*   self 和 __CLASS__，都是对当前类的静态引用，取决于定义当前方法所在的类。也就是说，self 写在哪个类里面， 它引用的就是谁。
*   $this 指向的是实际调用时的对象，也就是说，实际运行过程中，谁调用了类的属性或方法，$this 指向的就是哪个对象。但 $this 不能访问类的静态属性和常量，且 $this 不能存在于静态方法中。
*   static 关键字除了可以声明类的静态成员（属性和方法）外，还有一个非常重要的作用就是后期静态绑定。
*   self 可以用于访问类的静态属性、静态方法和常量，但 self 指向的是当前定义所在的类，这是 self 的限制。
*   $this 指向的对象所属的类和 static 指向的类相同。
*   static 可以用于静态或非静态方法中，也可以访问类的静态属性、静态方法、常量和非静态方法，但不能访问非静态属性。
*   静态调用时，static 指向的是实际调用时的类；非静态调用时，static 指向的是实际调用时的对象所属的类。

后期静态绑定
------

后期静态绑定（也叫延迟静态绑定），可用于在继承范围内引用静态调用的类，也就是代码运行时最初调用的类。

后期静态绑定本想通过引入一个新的关键字来表示，但最终还是沿用了 static 关键字。

### 工作原理

确切地说，static 后期静态绑定的工作原理是存储了上一个非转发调用（non-forwarding call）的类名。

当进行静态方法调用时，该类名（static指向的类名）为明确指定的那个（通常是 :: 运算符的左侧部分），即实际调用时的类。

如上述示例中的：

``` php
1.  A::test2(); 
2.  B::test2();
```

static 和 self 的区别：

*   self 可以用于访问类的静态属性、静态方法和常量，但 self 指向的是当前定义所在的类，这是 self 的限制。
*   static 也可以用于访问类的静态属性、静态方法和常量，static 指向的是实际调用时的类。

当进行非静态方法调用时，该类名（static指向的类名）为该对象所属的类，即实际调用时的对象所属的类。

如上述示例中的：

``` php
1.  (new A)->dd(); 
2.  (new B)->dd();
```

static 和 $this 有点类似，但又有区别：

*   $this 指向的对象所属的类和 static 指向的类相同。
*   $this 不能用于静态方法中，也不能访问类的静态属性和常量。
*   $this 指向的是实际调用的对象。
*   static 可以用于静态或非静态方法中，也可以访问类的静态属性、静态方法、常量和非静态方法，但不能访问非静态属性。
*   static 指向的是实际调用时的对象所属的类。

#### 转发调用（forwarding call）

所谓的转发调用（forwarding call）指的是通过以下几种方式进行的静态调用：self::，parent::，static:: 以及 forward_static_call() 。

可用 get_called_class() 函数来获取被调用的方法所在的类名。

以下四种形式的调用，都是转发调用：

*   self::
*   parent::
*   static::
*   forward_static_call()

除此之外的调用，就是非转发调用。

#### 非转发调用（non-forwarding call）

后期静态绑定的工作原理是存储了上一个非转发调用（non-forwarding call）的类名。

通过具体的类名或具体的对象进行的调用都是非转发调用。

比如：

``` php
1.  A::test2(); 
2.  B::test2(); 

4.  (new A)->dd(); 
5.  (new B)->dd();
```

### 注意事项

#### 非静态环境下的私有方法的查找顺序

在非静态环境下，在类的非静态方法中，使用 $this 和 static 调用类的私有方法时，执行方式有所不同。

*   $this 会优先寻找所在定义范围（父类）中的私有方法，如果存在就调用。
*   static 是先到它指向的类（子类）中寻找私有方法，如果找到了就会报错，因为私有方法只能在它所定义的类内部调用；如果没找到，再去所在定义范围（父类）中寻找该私有方法，如果存在就调用。

具体来说，$this 会先到所在定义范围内寻找私有方法，再到它指向的对象所属的类中寻找私有方法，然后寻找公有方法，最后到所在定义范围内寻找公共方法。只要找到了匹配的方法，就调用，并停止查找。

而 static 则是先到它指向的类中寻找私有方法，再寻找共有方法；然后到所在定义范围内寻找私有方法，再寻找共有方法。只要找到了匹配的方法，就调用，并停止查找。

下面是一个例子：

``` php
1.  <?php
2.   class  A  {
3.      private function  foo () {
4.          var_dump($this); echo '--';
5.          var_dump(new static); echo '--';

7.          echo __CLASS__; echo '--';
8.          echo get_called_class();
9.          echo '<br>';
10.      }

12.      public function  test () {
13.          $this -> foo ();
14.          static:: foo ();
15.          echo '<br>';
16.      }
17.  }

19.  class  B  extends  A  { }

21.  class  C  extends  A  {
22.      private function foo () {
23.          echo 'this is C';
24.      }
25.  }

27.  (new  B())->test();
28.  (new  C())->test();
```

输出结果为：

``` php
1.  object(B)#1 (0) { } --object(B)#2 (0) { } --A--B
2.  object(B)#1 (0) { } --object(B)#2 (0) { } --A--B

4.  object(C)#1 (0) { } --object(C)#2 (0) { } --A--C

6.  Fatal error: Uncaught Error: Call to private method C::foo() from context 'A'
```

#### 关于后期静态绑定的解析

后期静态绑定的解析会一直到取得一个完全解析了的静态调用为止。如果静态调用使用了 parent:: 或者 self:: 等转发调用的形式，将会转发调用信息。

``` php
1.  <?php
2.  class  A  {
3.      public static function  foo () {
4.          static:: who ();
5.      }

7.      public static function  who () {
8.          echo  __CLASS__ . "\n" ;
9.      }
10.  }

12.  class  B  extends  A  {
13.      public static function  test () {
14.          A :: foo ();
15.          parent :: foo ();
16.          self :: foo ();
17.          static::foo();
18.          forward_static_call(['A', 'foo']);
19.          echo '<br>';
20.      }

22.      public static function  who () {
23.          echo  __CLASS__ . "\n" ;
24.      }
25.  }

27.  class  C  extends  B  {
28.      public static function  who () {
29.          echo  __CLASS__ . "\n" ;
30.      }

32.      public static function test2() {
33.          self::test();
34.      }
35.  }

37.  class  D  extends  C  {
38.      public static function  who () {
39.          echo  __CLASS__ . "\n" ;
40.      }
41.  }

43.  B::foo();
44.  B::test();

46.  C::foo();
47.  C::test();

49.  D::foo();
50.  D::test2();
```

以上的输出结果为：

``` php
1.  B A B B B B 
2.  C A C C C C 
3.  D A D D D D
```

static 后期静态绑定的工作原理是存储了上一个非转发调用（non-forwarding call）的类名。请记住这句话。

下面的例子是非转发调用。

``` php
1.  A::foo();  // 输出 A

3.  B::foo();   // 输出 B

5.  C::foo();   // 输出 C
```

后期静态绑定 static ，是定义在了 foo() 方法中，哪个类通过非转发调用的形式调用 foo() 方法， foo() 方法中的 static 指向的就是哪个类。

但是，如果通过转发调用的形式，调用 foo() 方法，如：

``` php
1.  parent :: foo ();
2.  self :: foo ();
3.  static::foo();
4.  forward_static_call(['A', 'foo']);
```

那么，就以转发调用代码所在的方法 test() 为准，哪个类通过非转发调用的形式调用 test() 方法， foo() 方法中的 static 指向的就是哪个类。

假如调用 test() 方法时，也采用了转发调用的形式，如：

``` php
1.  public static function test2() {
2.      self::test();
3.  }
```

那么，就以 test2() 方法为准 ... 依次类推。

也就是说，在使用了后期静态绑定的基类中，后期静态绑定所在的方法如果被转发调用，则 static 的指向，会一直向上追溯，直到遇到非转发调用的形式。
