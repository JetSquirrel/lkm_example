# 编写简单的Linux内核模块

## 踏上征程

从现在开始，我们就要开始编写代码了。好了，让我们先准备好工作环境：

```shell
mkdir ~/src/lkm_example
cd ~/src/lkm_example
```

 创建文件lkm_example.c，并输入以下代码： 

```c
#include <linux/init.h>
#include <linux/module.h>
#include <linux/kernel.h>
MODULE_LICENSE("GPL");
MODULE_AUTHOR("Deng Tian");
MODULE_DESCRIPTION("A simple example Linux module.");
MODULE_VERSION("0.01");
static int __init lkm_example_init(void) {
 printk(KERN_INFO "Hello, World!\n");
 return 0;
}
static void __exit lkm_example_exit(void) {
 printk(KERN_INFO "Goodbye, World!\n");
}
module_init(lkm_example_init);
module_exit(lkm_example_exit);
```

 我们将init（加载）和exit（卸载）函数定义为static类型，并让它返回一个int型数据。

· 注意，这里要使用printk函数，而不是printf函数。此外，printk与printf使用的参数也各不相同。例如，KERN_INFO（这是一个标志，用以声明相应的消息记录等级）在定义的时候并没有使用逗号。

· 在文件的最后部分，我们调用了module_init和module_exit函数，来告诉内核哪些是加载函数和卸载函数。这样的话，我们就能够给这些函数自由命名了。

当目前为止，我们仍然无法编译这个文件：我们还需要一个Makefile文件。有了它，这个简单的示例模块就算就绪了。请注意，make会严格区分空格和制表符，因此，在应该使用tab的地方千万不要使用空格。

```makefile
obj-m += lkm_example.o
all:
     make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules
clean:
     make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean
```

如果我们运行“make”，正常情况下应该成功编译我们的模块。最后得到的文件是“lkm_example.ko”。如果在此过程中出现了错误消息的话，请检查示例源文件中的引号是否正确，并确保没有意外粘贴为UTF-8字符。

现在，可以将我们的模块插入内核空间进行测试了。为此，我们可以运行如下所示的命令：

```shell
sudo insmod lkm_example.ko
```

如果一切顺利的话，屏幕上面是不会显示任何内容的。这是因为，printk函数不会将运行结果输出到控制台，相反，它会把运行结果输出到内核日志。为了查看内核模块的运行结果，我们需要运行下列命令：

```shell
sudo dmesg
```

正常情况下，这里应该看到带有时间戳前缀的“Hello，World！”行。这意味着我们的内核模块已经加载，并成功向内核日志输出了相关的字符串。我们还可以通过下面的命令，来检查该模块是否仍然处于加载状态：

```shell
lsmod | grep “lkm_example”
```

要删除该模块，请运行下列命令：

```shell
sudo rmmod lkm_example
```

如果您再次运行dmesg，则会在日志中看到字符串“Goodbye, World!”。同时，您也可以再次使用lsmod来确认它是否已被卸载。

**如果你想了解更多，可点击[原文链接](https://zhuanlan.zhihu.com/p/31931538)**




 
