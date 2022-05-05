# 什么是类？

## 类的含义

- 是一种数据结构
- 是一种数据类型
- 代表现实世界的“种类”

## 构造器与析构器

```c#
using System;

namespace Example
{
    class Program
    {
        static void Main(string[] args)
        {
            //Student stu= new Student() { Id = 1, Name = "Timothy"};

            //反射应用
            /*            Type t = typeof(Student);
                        object o = Activator.CreateInstance(t, 1, "Tom");
                        Student stu = o as Student;
                        // Student stu = (Student)o;
                        Console.WriteLine(stu.Name);*/
            // 动态编程
            Type t = typeof(Student);
            dynamic stu = Activator.CreateInstance(t, 1, "Tom");
            Console.WriteLine(stu.Name);


            /*Student stu = new Student(1, "Timothy");
            Console.WriteLine(stu.Id);
            Console.WriteLine(stu.Name);
            stu.Report();*/
        }
    }

    class Student
    {
        // 实例构造器
        public Student(int id, string name)
        {
            this.Id = id;
            this.Name = name;
            Amount++;
        }

        // 静态构造器 只能构造静态成员，创建实例时自带的属性
        public static int Amount { get; set; }
        static Student()
        {
            Amount = 100;
        }

        // 析构器
        ~Student()
        {
            Amount--;
            Console.WriteLine("Bye bye! Release the system resourcecs...");
        }

        public int Id { get; set; }
        public string Name { get; set; }
        public void Report()
        {
            Console.WriteLine($"I'm # {Id} student, my name is {Name}.");
        }
    }
}

```

## 类声明的全貌

- 声明的位置
    - 声明在名称空间
    - 声明在全局名称空间
    - 声明在类体里
- 声明即定义

- 最简单的类声明
    - 类的访问控制
