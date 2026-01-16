[English](README.md)  
地址: [GitHub 仓库](https://github.com/chhuang-one/c4), [Gitee 仓库](https://gitee.com/chhuang-one/c4)

# C/C++ 语言语法扩展方案

## 1 基本设计理念

我们将类型、函数、变量等语言元素视为对象实例，在声明时使用点语法为其添加属性。
这种方法可以在不增加过多关键字的前提下扩展语言功能。
用户可以设置类似 UNIX 系统中的 rwx 权限，控制编译器选项，甚至可以使用特殊声明来扩展语言。  
  
事实上，现代编程语言中的许多关键字都描述了访问权限，例如 mutable、const、public、protected 和 private。

## 2 语法扩展示例

**类型声明增强**：

```c++
// 方式一：类型名后直接添加属性
struct MyClass .ownership().permission(rwx).optimization1()
{
    // 类实现
	void  doSomething() .public().permission(rwx);
};

// 方式二：多行属性声明
struct MyClass
  .ownership()
  .permission(rwx)
  .optimization2()
{
    // 类实现
	void  doSomething() .public().permission(rwx);
};

// 方式三：尾部属性声明
struct MyClass
{
    // 类实现
	void  doSomething() .public().permission(rwx);
}
.ownership()
.permission(rwx)
.optimization3();
```

**变量属性支持（MVVM 关键特性）**：

```c++
// 方式一：变量名后直接添加属性
bool visible
  .permission(rw)                          // mutable
  .onGet(notifyPropertyRead)
  .onSet(notifyPropertyChanged) = true;    // 默认可见

// 方式二：
bool visible .permission(rw).property() {  // mutable
  bool operator=(bool isVisible) {
    notifyPropertyChanged();
    return this = isVisible;
  }
  bool operator=() {
    notifyPropertyRead();
    return this;
  }
} = true;                                  // 默认可见


// 设置值时自动调用 notifyPropertyChanged
visible = true;

// 读取值时自动调用 notifyPropertyRead
if (visible)
    showMessage("Hello");
```

**函数属性声明**：

```c++
void doMigration(int version) .async() {
    // 函数实现
}

void doSomething(int action) .permission(rwx) {
    // 函数实现
}
```

## 3. 高级应用场景

### 3.1 模块系统增强

模仿 Rust 的模块系统，减少关键字数量：

```c++
struct MyLib .module() {
    struct MyStruct1;
    struct MyStruct2;
}
```

### 3.2 继承与接口实现

模仿 Java 的继承机制：

```c++
struct Child .extend(Parent).implement(MyInterface) {
    // 类实现
};
```

### 3.3 原生库无缝对接

通过声明式语法实现与 C 库的无缝对接，以 GTK+ 为例：

GTK 命名规范映射：

| 类型     | 命名规范         | 前缀    |
|----------|------------------|---------|
| 结构体   | 大驼峰命名法     | Gtk     |
| 函数     | 小写下划线命名法 | gtk     |
| 常量     | 大写下划线命名法 | GTK     |

声明式映射配置：

```c++
struct Gtk {
    // 类型定义
}
.module()
.mapping("C")
.typeNamePrefix("Gtk").typeNaming(upperCamelCase)
.funcNamePrefix("gtk").funcNaming(lowerSnakeCase)
.constNamePrefix("GTK").constNaming(upperSnakeCase);
```

传统 C 代码：

```c
GtkWindow *window;
window = gtk_window_new(GTK_WINDOW_TOPLEVEL);
gtk_window_set_title(window, "Hello");
```

映射后的 C++ 代码：

```c++
Gtk::Window *window;
window = Gtk::Window::new(Gtk::Window::TOPLEVEL);
window->setTitle("Hello");
```

## 4. 总结与展望

本文提出的语法扩展方案旨在通过声明式语法增强 C/C++ 的表达能力，同时保持语言的性能和兼容性。

该构想于 2025 年 10 月形成，并于 2025 年 11 月 7 日完成本文档的初步编写。
