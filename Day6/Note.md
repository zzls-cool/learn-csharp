# 017 字段、属性、索引器、常量
## 字段（成员变量）
- 字段(field)是一种表示与对象或类型相关联的值的变量。  
    - 实例字段  与对象相关联的字段
    - 静态字段  与类型相关联的字段  表示类型当前的状态

```c#
using System;

namespace DataMemberExample
{
    class Program
    {
        static void Main(string[] args)
        {
            List<Student> stuList = new List<Student>();
            for (int i = 0; i < 100; i++)
            {
                Student stu =  new Student();
                stu.Age = 24;
                stu.Score = i;
                stuList.Add(stu);
            }

            int totalAge = 0;
            int totalScore = 0;
            foreach(var stu in stuList)
            {
                totalAge += stu.Age;
                totalScore += stu.Score;
            }
            Student.AverageAge = totalAge / Student.Amount;
            Student.AverageScore = totalScore / Student.Amount;

            Student.ReportAmount();
            Student.ReportAverageAge();
            Student.ReportAverageScore();
        }
    }

    class Student
    {
        // 实例字段
        public int Age;
        public int Score;

        // 静态字段
        public static int AverageAge;
        public static int AverageScore;
        public static int Amount;
        
        // 构造函数
        public Student()
        {
            Student.Amount++;
        }

        public static void ReportAmount()
        {
            Console.WriteLine(Student.Amount);
        }

        public static void ReportAverageAge()
        {
            Console.WriteLine(Student.AverageAge);
        } 

        public static void ReportAverageScore()
        {
            Console.WriteLine(Student.AverageScore);
        }

        class Brush
        {
            // 静态只读字段
            public static readonly Color DefaultColor = new Color() { Red =0, Green =0, Blue =0 };
        }
    }
}

```
- 字段的声明
    - 字段的名字应该为名词；
    - 位置应该在类的内部；
    - 字段的声明不是语句；

- 字段的初始值
    - 隐式初始化，字段获得默认值；
    - 实例字段初始化的时机 --对象创建时；
    - 静态字段初始化的时机 --类型被加载时；

- 只读字段
    - 实例只读字段；
    - 静态只读字段；
```c#
class Student
{
    // 只读实例字段
    public readonly int ID;
    // 静态构造器
    // 只读实例字段可以进行初始化，但不能进行赋值
    static Student(int id)
    {
        this.ID = id;
        Amount = 0;
    }
}
```

## 属性（成员属性）

- 属性（property）是用于访问对象或类型的特征的成员，特征反映了状态
    - 属性时字段的自然扩展，属性可以访问字段的值，也可以设置字段的值；
    -  property 更偏向于反应现实世界的特征；
    - 暴露数据，数据可以是存储在字段里的数值，也可以是动态计算出来的；
    - 保护字段不被非法值污染
    - 由get()和set()方法进化而来；
```c#
class Student
{
    private int age;

    public int GetAge()
    {
        return age;
    }

    public void SetAge(int value)
    {
        if (value >=0 && value <= 120)
        {
            this.age = value;
        }
        else
        {
            throw new Exception("Age value has error");
        }
    }
}
```

