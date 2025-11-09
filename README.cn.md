# C/C++ 语言语法扩展方案

## 1. 背景与目标

在现代软件开发中，开发者通常需要掌握多种编程语言和语法才能完成系统开发。为了降低学习成本，扩展现有语言的适用范围成为一个重要的发展方向。本文提出一种利用 C/C++ 预处理器实现声明式语法的技术方案，旨在增强 C/C++ 语言的表达能力，同时保持其性能优势。

## 2. 声明式语法的实现思路

### 2.1 数据库表定义的声明式实现

传统 SQL 语法定义数据库表：

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR
);
```

基于 C/C++ 宏的声明式语法实现：

```c
TABLE("users", {
    INTEGER("id");  PRIMARY();
    STRING("name");
});
```

相应的宏定义实现：

```c
#define TABLE(tableName, lambda)                     \
    {                                                \
        SqlTable  *table;                            \
        SqlColumn *column;                           \
        table = new SqlTable(tableName);             \
        lambda;                                      \
    }

#define INTEGER(columnName)    column = table->addInteger(columnName)
#define STRING(columnName)     column = table->addString(columnName)
#define PRIMARY()              column->primary()
```

**技术说明**：由于 C/C++ 宏缺乏命名空间支持，建议为宏添加前缀或将其置于独立的头文件中，以避免命名冲突。

### 2.2 当前预处理器的局限性

虽然上述方案能够实现基本的声明式语法，但在实现复杂的声明式 UI 和 MVVM（Model-View-ViewModel）模式时，现有的预处理器功能仍显不足。传统的命令式写法较为繁琐：

```c
// 传统实现方式
message = "Hello";
component->onMessageChange(redrawComponent);

// 或使用封装函数
setMessage(string msg) {
    message = msg;
    component->onMessageChange(redrawComponent);
}

drawText(getMessage());
```

## 3. 其他语言的借鉴与启发

### 3.1 ArkTS 的声明式UI实现

华为 ArkTS 在 TypeScript 基础上扩展了声明式 UI 和 MVVM 支持：

```typescript
@Component
struct Index {
  @State message: string = 'Hello World';

  build() {
    Column() {
      Text(this.message)
        .width(100)
        .height(50)
    }
  }
}
```

编译后展开的实现：

```typescript
class Index {
  private get message() : string { /* 实现细节 */ }
  private set message(newValue: string) { /* 实现细节 */ }

  private render() : void {
    Column.create(...);
    Text.create(...);
    Text.setLabel(this.message);
    Text.width(100);
    Text.height(50);
    Column.pop();
  }
}
```

## 4. C/C++ 语法扩展方案

### 4.1 基本设计理念

我们将类型、函数、变量等语言元素视为对象实例，在声明时使用点语法为其添加属性。这种方法可以在不增加过多关键字的前提下扩展语言功能。

### 4.2 语法扩展示例

**类型声明增强**：

```c++
// 方式一：类型名后直接添加属性
struct MyClass .ownership().optimization1() {
    // 类实现
};

// 方式二：多行属性声明
struct MyClass
  .ownership()
  .optimization2() {
    // 类实现
};

// 方式三：尾部属性声明
struct MyClass {
    // 类实现
}
.ownership()
.optimization3();
```

**变量属性支持（MVVM 关键特性）**：

```c++
int visible
  .onGet(lambdaFunc1)
  .onSet(lambdaFunc2);

// 设置值时自动调用 lambdaFunc2
visible = true;

// 读取值时自动调用 lambdaFunc1
if (visible)
    showMessage("Hello");
```

**函数属性声明**：

```c++
void doMigration(int version) .public().async() {
    // 函数实现
}
```

## 5. 高级应用场景

### 5.1 模块系统增强

模仿 Rust 的模块系统，减少关键字数量：

```c++
struct MyLib .module() {
    struct MyStruct1;
    struct MyStruct2;
}
```

### 5.2 继承与接口实现

模仿 Java 的继承机制：

```c++
struct Child .extend(Parent).implement(MyInterface) {
    // 类实现
};
```

### 5.3 原生库无缝对接

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

## 6. 总结与展望

本文提出的 C3 语法扩展方案（C2 对应于 C++）旨在通过声明式语法增强 C/C++ 的表达能力，同时保持语言的性能和兼容性。该方案具有以下特点：

1. **渐进式增强**：在保持向后兼容的前提下扩展语言功能
2. **声明式编程**：提供更简洁、表达力更强的语法
3. **工具链友好**：便于编译器和开发工具进行处理
4. **生态兼容**：支持与现有 C/C++ 生态系统的无缝集成

该构想于 2025 年 10 月形成，并于 2025 年 11 月 7 日完成本文档的初步编写。我们相信，这种语法扩展方向将为 C/C++ 生态系统注入新的活力，推动其在现代软件开发中的更广泛应用。


地址: [GitHub](https://github.com/chhuang-one/c3), [Gitee](https://gitee.com/chhuang-one/c3)
