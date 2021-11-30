## 下载网址

点击这里下载 [下载网址](https://download.qt.io/archive/qt/)

## 信号与槽

#### 1、信号与槽的机制

```c++
// 1) 自己书写connect函数
connect(ui->QPushButton, &QPushButton, [=](){
    // 执行代码块
});

// 2) ui界面上右键转到槽函数

// 3)
/* 自定义信号与自定义槽 */
class{
singals:  
    void quit(); // 自定义信号
};
emit quit(); // 发射信号
```

## 资源样式

> 需要将资源保存在 .pro 文件同级目录或者同级子目录下
>
> ```qss```引用的时候，需要使用 ```url(:/source/b.jpg);```注意这里的冒号
>
> 引用外部资源，不使用绝对路径，使用相对路径（相对于```cpp```文件的路径）
>
> ```css
> url(../b.jpg);
> ```
>
> 