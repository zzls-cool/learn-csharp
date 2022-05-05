# 事件详解
## 初步了解事件
- 定义  
    > - Event 能够发生的什么事情 例如：“公司上市” “产品发布”  
    > - 事件是一种类型成员。
    > - 事件参数（Eevent Argument）
    > - 事件处理器（Event Handler）
- 角色 
    > - 使对象或类具备“通知”能力的成员
    > - 对象O拥有一个事件E：当事件E发生的时候，O有能力通知别的对象
- 功能
  > - 功能 = 通知 + 可选择的事件参数
  > - 用于对象或类间的动作协调与信息传递
- 原理 事件模型中的2个5
  > - 发生->响应中的5个部分： “闹钟”+“响了”+“你”+“起床”+（隐藏的订阅关系“我的闹钟”）
  > - 发生->响应中的5个动作:  我有一个事件 -> 一个人或一群人关心我的这个事件 -> 我的事件发生 -> 关心这个事件的人依次被通知到 -> 被通知的人根据拿到的事件信息对事件进行响应`
- MVC MVP MVVM 设计模式
## 事件的应用
- 实例  
  > - 事件不会主动发生，被拥有者的内部逻辑触发之后才发生
  > - 派生（继承）与扩展（extent）
- ⭐事件的五个组成部分
    > - 事件的拥有者（event source, 对象）  
    > - 事件的成员（event，成员）  
    > - 事件的响应者（event subscriber，对象）  
    > - 事件处理器（event handler，成员）--- 本质上是一个回调方法  
    > - ⭐事件订阅 -- 把事件处理器与事件关联在一起，本质上是一种委托类型为基础的“约定”  
    >   - 当事件发生的时候，这个事件都会通知谁  
    >   - 那什么样的方法才能处理这个事件  
            拿一个方法定义一个事件时会进行类型检查  
            事件处理器和事件遵守某个“约定”（委托） 
    >   - 事件的响应者拿哪个方法来处理这个事件  

```c#
using System;
using System.Timers;

namespace EventExample
{
    class Program 
    {
        static void Main(string[] args)
        {
            System.Timers.Timer timer = new();//事件的拥有者
            timer.Interval = 1000;
            Boy boy = new Boy();//事件响应者
            Girl girl = new Girl();
            timer.Elapsed/*事件Elapsed*/  += boy.Action/*事件订阅*/;
            timer.Elapsed += girl.Action;
            timer.Start();
            Console.ReadLine();
        }


    }

    class Boy
    {
        internal void Action(object sender, ElapsedEventArgs s)//事件处理器
        {
            Console.WriteLine("Jump!");
        }
    }

    class Girl
    {
        internal void Action(object sender, ElapsedEventArgs e)
        {
            Console.WriteLine("Singing!");
        }
    }
}
```


```c#
//事件拥有者和事件响应者相互独立
using System;
using System.Windows.Forms;

namespace EventExample
{
    class Program 
    {
        static void Main(string[] args)
        {
            Form form = new Form();//事件的拥有者
            Controller controller/*事件的响应者*/ = new Controller(form);
            form.ShowDialog();
        }
    }

    class Controller
    {
        private Form form;
        public Controller(Form form)
        {
            if(form != null)
            {
                this.form = form;
                this.form.Click/*事件*/ += this.FormClicked;
            }
        }

        //事件处理器
        private void FormClicked(object sender, EventArgs e)
        //EventArgs为事件约定，不能拿Elapsed的事件处理器去相应Click事件
        {
            this.form.Text = DateTime.Now.ToString();
        }
    }
}
```


```c#
// 事件的拥有者是事件本身
// 事件拥有者form
// 事件form.Click
// 事件的响应者form
// 事件的处理器FormClicked
// 时间的订阅 +=
using System;
using System.Windows.Forms;

namespace EventExample
{
    class Program 
    {
        static void Main(string[] args)
        {
            MyForm form = new MyForm();
            form.Click += form.FormClicked;
            form.ShowDialog();
        }
    }


    // 派生类
    class MyForm : Form
    {
        internal void FormClicked(object? sender, EventArgs e)
        {
            this.Text = DateTime.Now.ToString();
        }
    }
}
```

>- 事件拥有者是事件响应者的成员，事件响应者用自己的方法订阅着自己的字段成员的某个事件  
>- 一个事件可以挂接多个事件处理器，一个事件处理器也可以被多个事件所挂接

## 深入了解事件  

- 事件的声明  
    > - 完整声明
    > - 简略声明  

```c#
/*详细声明*/
using System;
using System.Threading;

namespace EventExample
{
    class Program
    {
        static void Main(string[] args)
        {
            Customer customer = new Customer();
            Waiter waiter = new Waiter();
            customer.Order += waiter.Action;
            customer.Action();
            customer.PayTheBill();
        }
    }

    // 传递数据消息
    // 事件信息都需要派生自EventArgs这个类
    public class OrderEventArgs : EventArgs
    {
        public string DishName { get; set; }
        public string Size { get; set; }
    }

    // EventHandler的声明的名称的含义
    // 1.专门用来声明事件
    // 2.约束事件处理器
    // 3.创建的实例是用来专门用于存储事件处理器

    // 注意委托声明的位置 一般与类体平级
    public delegate void OrderEventHandler(Customer customer, OrderEventArgs e);

    public class Customer
    {
        // 用来存储/引用事件处理器
        private OrderEventHandler orderEventHandler;

        public event OrderEventHandler Order
        {
            add
            {
                this.orderEventHandler += value;
            }

            remove
            {
                this.orderEventHandler -= value;
            }

        }
        public double Bill { get; set; }
        public void PayTheBill()
        {
            Console.WriteLine("I will pay ${0}.", this.Bill);
        }
        public void WalkIn()
        {
            Console.WriteLine("Walk into the restaurant.");
        }

        public void SitDown()
        {
            Console.WriteLine("Sit Down.");
        }

        public void Think()
        {
            for (int i = 0; i < 5; i++)
            {
                Console.WriteLine("Let me think ...");
                Thread.Sleep(1000);
            }

            if (this.orderEventHandler != null)
            {
                OrderEventArgs e = new OrderEventArgs();
                e.DishName = "Kongpao Chicken";
                e.Size = "large";
                this.orderEventHandler.Invoke(this, e);
            }
        }

        public void Action()
        {
            Console.WriteLine();
            this.WalkIn();
            this.SitDown();
            this.Think();
        }
    }

    public class Waiter
    {
        public void Action(Customer customer, OrderEventArgs e)
        {
            Console.WriteLine("I will server you the dish - {0}", e.DishName);
            double price = 10;
            switch (e.Size)
            {
                case "small":
                    price = price * 0.5;
                    break;
                case "large":
                    price = price * 1.5;
                    break;
                default:
                    break;
            }
            customer.Bill += price;
        }
    }
}

```  


```c#
/*简略声明*/


```