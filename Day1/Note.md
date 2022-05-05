# 018 方法参数的使用
## 传值参数  
- 在声明时不带修饰符的形参。
- 值参数创建变量的副本
### 值类型

```c#
using System;
using System.Collections.Generic;

namespace ParametersExample
{
    class Program
    {
        static void Main(string[] args)
        {
            Student stu = new Student();
            int y= 100;
            stu.AddOne(y);// 101
            Console.WriteLine(y);// 100
        }
    }

    class Student
    {
        public void AddOne(int x)
        {
            x = x + 1;
            Conclose.WritLine(x);
        }
    }

}
```

### 引用类型

```c#
using System;
using System.Collections.Generic;

namespace ParametersExample
{
    class Program
    {
        static void Main(string[] args)
        {
            Student stu = new Student() {Name = "Tim"};
            SomeMethod(stu);
            Console.WriteLine("{0} {1}", stu.GetHashCode(), stu.Name);
        }

        static void SomeMethod(Student stu)
        {
            stu = new Student() { Name = "Tom" };
            // Console.WriteLine(stu.Name);
            Console.WriteLine("{0} {1}", stu.GetHashCode(), stu.Name); //GetHashCode()用来获取对象的唯一值
        }
    }

    class Student
    {
        public string Name { get; set; }

    }
}
```


```c#
// 通过某个方法修改参数所引用对象的值的操作，叫做某个方法的副作用(side-effect)
using System;
using System.Collections.Generic;

namespace ParametersExample
{
    class Program
    {
        static void Main(string[] args)
        {
            Student stu = new Student() {Name = "Tim"};
            UpdateObject(stu);
            Console.WriteLine("HashCode={0} Name={1}", stu.GetHashCode(), stu.Name);
        }

        static void UpdateObject(Student stu)
        {
            stu.Name = "Tom";
            Console.WriteLine("HashCode={0} Name={1}", stu.GetHashCode(), stu.Name); 
        }

        class Student
        {
            public string Name { get; set; }
        }
    }
}

```


## 引用参数
- 用ref关键字修饰符声明的形参
- 引用形参并不会创建新的存储位置。
- 引用形参表示的存储位置是方法调用中作为实参给出的那个变量所表示的存储位置。
- 改变实际参数的值

### 值类型
```c#
using System;
using System.Collections.Generic;

namespace ParametersExample
{
    class Program
    {
        static void Main(string[] args)
        {
            int y = 1;
            IWantSideEffect(ref y);// 调用后y值发生改变
            Console.WriteLine(y);
        }
        // 引用参数传入值类型的值
        static void IWantSideEffect(ref int x)
        {
            x = x + 100;
        }
    }
}
```

### 引用类型

```c#
using System;
using System.Collections.Generic;

namespace ParametersExample
{
    class Program
    {
        static void Main(string[] args)
        {
            Student outterStu = new Student() {Name = "Tim"};
            Console.WriteLine("HashCode={0} Name={1}", outterStu.GetHashCode(), outterStu.Name);
            Console.WriteLine("--------------------------");
            IWantSideEffect(ref outterStu);
            Console.WriteLine("HashCode={0} Name={1}", outterStu.GetHashCode(), outterStu.Name);
        }
        // 引用参数传入引用类型的值
        static void IWantSideEffect(ref Student stu)
        {
            stu = new Student() {Name = "Tom"};
            Console.WriteLine("HashCode={0} Name={1}", stu.GetHashCode(), stu.Name); 
        }

        class Student
        {
            public string Name { get; set; }
        }
    }
}
```

```c#
using System;
using System.Collections.Generic;

namespace ParametersExample
{
    class Program
    {
        static void Main(string[] args)
        {
            Student outterStu = new Student() {Name = "Tim" };
            Console.WriteLine("HashCode={0}, Name={1}", outterStu.GetHashCode(), outterStu.Name);
            Console.WriteLine("-----------------------------");
            SomeSideEffect(ref outterStu); // 修改对象的属性值，指向同一个对象
            Console.WriteLine("HashCode={0}, Name={1}", outterStu.GetHashCode(), outterStu.Name);
        }
        // 引用参数传入引用类型的值并改变其属性的值
        static void SomeSideEffect(ref Student stu)
        {
            stu.Name = "Tom";
            Console.WriteLine("HashCode={0}, Name={1}", stu.GetHashCode(), stu.Name);
        }
    }

    class Student
    {
        public string Name { get; set; }
    }
}
```

## 输出参数
- 用out修饰符声明的形参，输出形参不创建新的存储位置。
- 变量在可以作为输出形参传递之前不一定需要明确赋值。
- 在方法返回之前，该方法的每个输出参数都必须明确赋值。
  
### 值类型

```C#
using System;
using System.Collections.Generic;
namespace ParametersExample
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Please input first number:");
            string arg1 = Console.ReadLine();
            double x = 0;
            bool b1 = double.TryParse(arg1, out x);// Tryparse()尝试解析字符是否为double类型
            if (b1 == false)
            {
                Console.WriteLine("Input error");
                return;
            }

            Console.WriteLine("Please input second number: ");
            string arg2 = Console.ReadLine();
            double y = 0;
            bool b2 = double.TryParse(arg2, out y);
            if (b2 == false)
            {
                Console.WriteLine("Input error");
                return;
            }

            double z = x+y;
            Console.WriteLine("{0} + {1} + {2}", x, y, z);
        }
    }
}
```

### 引用类型
```c#
//引用类型作为输出参数
using System;
using System.Collections.Generic;
namespace ParametersExample
{
    class Program
    {
        static void Main(string[] args)
        {
            Student stu = null;
            bool b = StudentFactory.Create("Tim", 34, out stu);
            if (b == true)
            {
                Console.WriteLine("Student {0}, age is {1}", stu.Name, stu.Age);
            }
        }
    }

    class Student
    {
        public int Age { get; set; }
        public string Name { get; set; }

    }

    class StudentFactory
    {
        public static bool Create(string stuName, int stuAge, out Student result)
        {
            result = null;
            if (string.IsNullOrEmpty(stuName))//判断名字是否为空
            {
                return false;
            }

            if (stuAge < 20 || stuAge > 80)//年龄是否合适
            {
                return false;
            }

            result = new Student() { Name = stuName, Age = stuAge };
            return true;
        }
    }
}

```
```c#
using System;
using System.Collections.Generic;
namespace ParametersExample
{
    class Program
    {
        static void Main(string[] args)
        {
            double x = 0;
            bool b = DoubleParser.TryParse("789", out x);
            if (b == true)
            {
                Console.WriteLine(x+1);
            }
        }
    }

    class DoubleParser
    {
        public static bool TryParse(string input, out double result)
        {
            try
            {
                result = double.Parse(input);
                return true;
            }
            catch
            {
                result = 0;
                return false;
            }
        }
    }
}
```
## 数组参数

- praram关键字用来声明可变长度的数组参数
一个方法参数列表中只能有一个praram参数，且必须是最后一个参数。
```c#
using System;
using System.Collections.Generic;
namespace ParametersExample
{
    class Program
    {
        static void Main(string[] args)
        {
            // 简化代码，不必创建数组元素，直接将数组元素传入函数
            // int[] array = new int[] {1 ,2 ,3}; 
            // int result = CalculateSum(array);
            int result = CalculateSum(1,2,3);
            Console.WriteLine(result);
        }

        // static int CalculateSum(int[] intArray)
        static int CalculateSum(params int[] intArray)
        {
            int sum = 0;
            foreach (var item in intArray)
            {
                sum += item;
            }
        }
    }
}
```

```c#
using System;
using System.Collections.Generic;
namespace ParametersExample
{
    class Program
    {
        static void Main(string[] args)
        {
            string str = "Tim;Tom,Amy.Lisa";
            // Split()按提供的参数分割字符串
            string[] result = str.Split(';', '.', ',');
            foreach (var name in result)
            {
                Console.WriteLine(name);
            }
        }
    }
}
```

## 具名参数  
- 调用一个方法时，传入的参数是带有名称。
- 提高代码的可读性。
- 参数的位置顺序可以不受限制。

```c#
using System;
using System.Collections.Generic;
namespace ParametersExample
{
    class Program
    {
        static void Main(string[] args)
        {
            //参数位置不受限制 提高代码可读性
            PrintInfo(name: "Tim", age: 40);
            PrintInfo(age: 40, name: "Tim");
            PrintInfo("Tim", 40);
        }

        static void PrintInfo(string name, int age)
        {
            Console.WriteLine("Hello {0}, you are {1}.", name, age);
        }
    }
}
```

## 可选参数  
- 参数因为具有默认值而变得“可选”  

```c#
using System;
using System.Collections.Generic;
namespace ParametersExample
{
    class Program
    {
        static void Main(string[] args)
        {
            PrintInfo("Tim", 40);
            PrintInfo();
        }

        //设置参数的默认值，使其成为可选参数
        static void PrintInfo(string name = "Tim", int age = 40)
        {
            Console.WriteLine("Hello {0}, you are {1}.", name, age);
        }
    }
}
```

## 扩展方法  
- 方法必须是公有的、静态的；
- 必须是形参列表中的第一个，有this修饰；
- 必须是由一个静态类来同意收纳对SomeType类型的扩展方法；
- LINQ(/lingk/)方法 
- 用来为某个方法扩展需要的方法。

```c#
using System;
using System.Collections.Concurrent;
using System.Collections.Generic;
namespace ParametersExample
{
    class Program
    {
        static void Main(string[] args)
        {
            double x = 3.14159;
            //double y = Math.Round(x, 4);
            double y = x.Round(4);// 扩展方法，为double元素添加Round()方法
            Console.WriteLine(y);
        }
    }

    // 静态类
    static class DoubleExtension
    {
        // 公有方法，被this修饰的参数放在参数列表中的第一个
        public static double Round(this double input, int digits)
        {
            double result = Math.Round(input, digits);
            return result;
        }
    }
}
```

```c#

//Linq扩展方法，为目标数据类型追加方法
using System;
using System.Collections.Concurrent;
using System.Collections.Generic;
using System.Linq;
namespace ParametersExample
{
    class Program
    {
        static void Main(string[] args)
        {
            List<int> myList = new List<int>() { 11, 12, 13, 14 };
            bool result = AllGreaterThanTen(myList);
            bool result = myList.All(i => i > 10);//LinQ方法
            Console.WriteLine(result);
        }
        static bool AllGreaterThanTen(List<int> intList)
        {
            foreach (var item in intList)
            {
                if (item <= 10)
                {
                    return false;
                }
            }
            return true;
        }
    }
}

```
