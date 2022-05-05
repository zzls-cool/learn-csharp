# ※019 委托类型的使用  
## 什么是委托
- 委托(delegate)是函数指针的“升级版”
- 一切皆地址  
  -  变量（数据）是以某个地址为起点的一段内存中所存储的值（寻找变量的地址）
  -  函数（算法）是以某个地址为起点的一段内存中所存储的机器语言指令（寻找算法的地址）

- 直接调用与间接调用
  - 直接调用：通过函数名来调用函数，通过函数名直接获得函数所在的地址开始执行
  - 间接调用：通过函数指针来调用函数，读取函数指针所在的地址获得函数所在的地址并开始执行

- 委托的简单使用
  - Action委托
  - Func委托
```c#
  // Action委托
using System;
using System.Collections.Generic;

namespace ConsoleTest
{
    class Program
    {
        static void Main(string[] args)
        {
            Calculator calculator = new Calculator();
             
            // Action委托 无返回值
            Action action1 = new Action(calculator.Report);
            Action action2 = () =>calculator.Report();//Lambda表达式
            calculator.Report();//直接调用
            action1.Invoke();//间接调用
            action2.Invoke();//Lambda匿名函数调用
            action1();//委托的简单调用
            action2();

            // Func委托 有返回值
            Func<int, int, int> func1 = (a, b) => calculator.Add(a, b);
            Func<int, int, int> func2 = (a, b) => calculator.Add(a, b);
            Func<int, int, int> Func1 = new Func<int, int, int>(calculator.Add);
            Func<int, int, int> Func2 = new Func<int, int, int>(calculator.Add);

            int x = 100;
            int y = 200;
            int z = 0;

            z = func1.Invoke(x, y);
            Console.WriteLine(z);
            z = func2.Invoke(x, y);
            Console.WriteLine(z);

            z = Func1.Invoke(x, y);
            Console.WriteLine(z);
            z = Func2.Invoke(x, y);
            Console.WriteLine(z);
        }
    }

    class Calculator
    {
        public void Report()
        {
            Console.WriteLine("I have 3 methods.");
        }

        public int Add(int a, int b)
        {
            int result = a + b;
            return result;
        }

        public int Sub(int a, int b)
        {
            int result = a - b;
            return result;
        }
    }
}  
```


## 委托的声明（自定义委托）

- 委托是一种类（class），类是数据类型，委托也是一种数据类型；
- 它的声明与一般的类不同；
- 声明委托的位置，一般声明在名称空间下；
- 委托与所封装的方法必须“类型兼容” 
  - 返回值类型一致；
  - 参数列表在个数和数据类型上一致。

```c#

// 自定义委托的使用
using System;
using System.Collections.Generic;

namespace ConsoleTest
{
    public delegate double Calc(double x, double y);
    class Program
    {
        static void Main(string[] args)
        { 
            Calculator calculator = new Calculator();
            Calc calc1 = new Calc(calculator.Add);
            Calc calc2 = new Calc(calculator.Sub);
            Calc calc3 = new Calc(calculator.Mul);
            Calc calc4 = new Calc(calculator.Div);

            double a = 100;
            double b = 200;
            double c;

            //c = calc1(a, b);
            c = calc1.Invoke(a, b);
            Console.WriteLine(c);
            
            //c = calc2(a, b);
            c = calc2.Invoke(a, b);
            Console.WriteLine(c);
            
            //c = calc3(a, b);
            c = calc3.Invoke(a, b);
            Console.WriteLine(c);
            
            //c = calc4(a, b);
            c = calc4.Invoke(a, b);
            Console.WriteLine(c);
        }
    }

    class Calculator
    {
        public double Add(double x, double y)
        {
            return x + y;
        }

        public double Sub(double x, double y)
        {
            return x - y;
        }

        public double Mul(double x, double y)
        {
            return x * y;
        }

        public double Div(double x, double y)
        {
            return x / y;
        }
    }
}
```

## 委托的使用

- 实例：把方法当作参数传给另一个方法
  - 模板方法：“借用”指定的外部方法,有一处不确定，需要填补；
    - 相当于“填空”；位于代码中部；有返回值
```c#
// 自定义委托的使用
using System;
using System.Collections.Generic;

namespace ConsoleTest
{
    class Program
    {
        static void Main(string[] args)
        {
            ProductFactory productFactory = new ProductFactory();
            WrapFactory wrapFactory = new WrapFactory();

            //准备委托类型的变量
            Func<Product> func1 = new Func<Product>(productFactory.MakePizza);
            Func<Product> func2 = new Func<Product>(productFactory.MakeToyCar);
            //调用模板方法
            Box box1 = wrapFactory.WrapPoduct(func1);
            Box box2 = wrapFactory.WrapPoduct(func2);
            Console.WriteLine(box1.Product.Name);
            Console.WriteLine(box2.Product.Name);
             
        }
    }

    class Product
    {
        public string Name { get; set; }
    }

    class Box
    {
        public Product Product { get; set; }
    }

    class WrapFactory
    {
        public Box WrapPoduct(Func<Product> getProduct)
        {
            Box box = new Box();
            Product product = getProduct.Invoke();//模板方法得到Product对象,可以修改委托调用函数，实现代码的复用
            box.Product = product;
            return box;
        }
    }

    class ProductFactory
    {
        public Product MakePizza()
        {
            Product product = new Product();
            product.Name = "Pizza";
            return product;
        }

        public Product MakeToyCar()
        {
            Product product = new Product();
            product.Name = "ToyCar";
            return product;
        }
    }
}
```
  - 回调方法：调用指定的外部方法。
    - 相当于”流水线“
    - 位于代码尾部
    - 动态选择后续需要调用的方法
    - 委托无返回值
```c#
// 自定义委托的使用
using System;
using System.Collections.Generic;

namespace ConsoleTest
{
    class Program
    {
        static void Main(string[] args)
        {
            ProductFactory productFactory = new ProductFactory();
            WrapFactory wrapFactory = new WrapFactory();

            //准备委托类型的变量
            Func<Product> func1 = new Func<Product>(productFactory.MakePizza);
            Func<Product> func2 = new Func<Product>(productFactory.MakeToyCar);
            
            // 使用委托的回调方法
            Logger logger = new Logger();
            Action<Product> log = new Action<Product>(logger.Log);
          
            //调用模板方法
            Box box1 = wrapFactory.WrapPoduct(func1, log);
            Box box2 = wrapFactory.WrapPoduct(func2, log);
            Console.WriteLine(box1.Product.Name);
            Console.WriteLine(box2.Product.Name);             
        }
    }

    //记录程序运行的日志
    class Logger
    {
        //记录程序运行状态
        public void Log(Product product)
        {
            Console.WriteLine("Product '{0}' createed at {1}. Price is {2}.", product.Name, DateTime.UtcNow, product.Price);
        }
    }

    class Product
    {
        public string Name { get; set; }
        public double Price { get; set; }
    }

    class Box
    {
        public Product Product { get; set; }
        public double Price { get; set; }

    }

    class WrapFactory
    {
        public Box WrapPoduct(Func<Product> getProduct, Action<Product> logCallback)
        {
            Box box = new Box();
            Product product = getProduct.Invoke();//模板方法得到Product对象,可以修改委托调用函数，实现代码的复用
            if(product.Price >= 50)
            {
                logCallback(product);
            }
            
            box.Product = product;
            return box;
        }
    }

    class ProductFactory
    {
        public Product MakePizza()
        {
            Product product = new Product();
            product.Name = "Pizza";
            product.Price = 12;
            return product;
        }

        public Product MakeToyCar()
        {
            Product product = new Product();
            product.Name = "ToyCar";
            product.Price = 100;
            return product;
        }
    }
}

```

## 注意事项：难精通+易使用+功能强大=>一旦滥用后果严重
- 缺点1:方法级别的耦合，使用时要谨慎
- 缺点2：可读性下降，debug的难度下降
- 缺点3：把委托回调、异步调用和多线程纠缠在一起，使得代码难以维护和阅读
- 缺点4：使用不当造成内存泄漏和程序性能下降

## 委托的高级使用
- 多播委托
  一个委托内部封装了不止一个方法
```c#
// 多播委托的使用
using System;
using System.Collections.Generic;
using System.Threading;

namespace ConsoleTest
{
    class Program
    {
        static void Main(string[] args)
        {
            Student stu1 = new Student() { Id = 1, PenColor = ConsoleColor.Red};
            Student stu2 = new Student() { Id = 2, PenColor = ConsoleColor.Green};
            Student stu3 = new Student() { Id = 3, PenColor = ConsoleColor.Yellow};
            Action action1 = new Action(stu1.DoHomeWork);
            Action action2 = new Action(stu2.DoHomeWork);
            Action action3 = new Action(stu3.DoHomeWork);
            //单播委托
            action1.Invoke();
            action2.Invoke();
            action3.Invoke();

            //多播委托 用户一个委托封装一个方法
            action1 += action2;
            action1 += action3;

            action1.Invoke();

        }
    }

    class Student
    {
        public int Id { get; set; }
        public ConsoleColor PenColor { get; set; }

        public void DoHomeWork()
        {
            for (int i = 0; i < 5; i++)
            {
                Console.ForegroundColor = this.PenColor;
                Console.WriteLine("Student {0} doing homework {1} hour(s)", this.Id, i);
                Thread.Sleep(1000);
            }
        }
    }
}
```
- 隐式异步调用
    - 同步和异步
      - 同步（串行）：两个人做事情，一个人先做，另一个人在前一个人做完的基础上继续做
      - 异步（并行 ）：两个人同时做事情，互不影响
    - 同步调用和异步调用

```c#
// 隐式异步调用
using System;
using System.Collections.Generic;
using System.Threading;

namespace ConsoleTest
{
    class Program
    {
        static void Main(string[] args)
        {
            Student stu1 = new Student() { Id = 1, PenColor = ConsoleColor.Red};
            Student stu2 = new Student() { Id = 2, PenColor = ConsoleColor.Green};
            Student stu3 = new Student() { Id = 3, PenColor = ConsoleColor.Yellow};

            #region 间接同步调用
            //直接同步调用
            /*            stu1.DoHomeWork();
                        stu2.DoHomeWork();
                        stu3.DoHomeWork();
            */

            // 单播委托的间接同步调用
            /*            Action action1 = new Action(stu1.DoHomeWork);
                        Action action2 = new Action(stu2.DoHomeWork);
                        Action action3 = new Action(stu3.DoHomeWork);

                        action1.Invoke();
                        action2.Invoke();
                        action3.Invoke();*/

            //多播委托的间接同步调用
            /*            action1 += action2;
                        action1 += action3;

                        action1.Invoke();*/
            #endregion

            /*            // 隐式异步调用
                        Action action1 = new Action(stu1.DoHomeWork);
                        Action action2 = new Action(stu2.DoHomeWork);
                        Action action3 = new Action(stu3.DoHomeWork);

            *//*            action1.BeginInvoke(null, null);
                        action2.BeginInvoke(null, null);
                        action3.BeginInvoke(null, null);*//*
                        Task.Run(action1);
                        Task.Run(action2);
                        Task.Run(action3);
            */

            #region 异步调用
            // 显示异步调用
            Thread thread1 = new Thread(new ThreadStart(stu1.DoHomeWork));
            Thread thread2 = new Thread(new ThreadStart(stu2.DoHomeWork));
            Thread thread3 = new Thread(new ThreadStart(stu3.DoHomeWork));
            thread1.Start();
            thread2.Start();
            thread3.Start();


            Task task1 = new Task(new Action(stu1.DoHomeWork));
            Task task2 = new Task(new Action(stu2.DoHomeWork));
            Task task3 = new Task(new Action(stu3.DoHomeWork));
            task1.Start();
            task2.Start();
            task3.Start();
            #endregion
            for (int i = 0; i < 10; i++)
            {
                Console.ForegroundColor = ConsoleColor.Cyan;
                Console.WriteLine("Main thread {0}.", i);
                Thread.Sleep(1000);
            }
        }
    }

    class Student
    {
        public int Id { get; set; }
        public ConsoleColor PenColor { get; set; }

        public void DoHomeWork()
        {
            for (int i = 0; i < 5; i++)
            {
                Console.ForegroundColor = this.PenColor;
                Console.WriteLine("Student {0} doing homework {1} hour(s)", this.Id, i);
                Thread.Sleep(1000);
            }
        }
    }
}
```
- 使用接口取代委托的使用
```c#
// 使用接口代替委托
using System;
using System.Collections.Generic;
using System.Threading;

namespace ConsoleTest
{
    class Program 
    {
        static void Main()
        {
            IProductFactory pizzaFactory = new PizzaFactory();
            IProductFactory toycarFactory = new ToyCarFactory();
            WrapFactory wrapFactory = new WrapFactory();

            Box box1 = wrapFactory.WraProduct(pizzaFactory);
            Box box2 = wrapFactory.WraProduct(toycarFactory);

            Console.WriteLine(box1.Product.Name);
            Console.WriteLine(box2.Product.Name);
        }
    }

    class Product
    {
        public string Name { get; set; }
    }

    class Box
    {
        public Product Product { get; set; }
    }
    //接口实现
    interface IProductFactory
    {
        Product Make();
    }

    class PizzaFactory : IProductFactory
    {
        public Product Make()
        {
            Product product = new Product();
            product.Name = "Pizza";
            return product;
        }
    }

    class ToyCarFactory : IProductFactory
    {
        public Product Make()
        {
            Product product = new Product();
            product.Name = "Toy Car";
            return product;
        }
    }

    class WrapFactory
    {
        public Box WraProduct(IProductFactory productFactor)
        {
            Box box = new Box();
            Product product = productFactor.Make();
            box.Product = product;
            return box;
        }
    }
}
```
