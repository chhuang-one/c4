[中文](README.cn.md)  
site: [GitHub Repository](https://github.com/chhuang-one/c3), [Gitee Repository](https://gitee.com/chhuang-one/c3)

# C/C++ Language Syntax Extension Proposal

## 1. Background and Objectives

In modern software development, engineers typically need to master multiple programming languages and syntaxes to complete system development. To reduce learning costs, expanding the application scope of existing languages has become an important development direction. This document proposes a technical solution that utilizes the C/C++ preprocessor to implement declarative syntax, aiming to enhance the expressiveness of C/C++ while maintaining its performance advantages.

## 2. Implementation Approach for Declarative Syntax

### 2.1 Declarative Implementation of Database Table Definitions

Traditional SQL syntax for defining database tables:

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR
);
```

Declarative syntax implementation based on C/C++ macros:

```c
TABLE("users", {
    INTEGER("id");  PRIMARY();
    STRING("name");
});
```

Corresponding macro definition implementation:

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

**Technical Note**: Due to the lack of namespace support in C/C++ macros, it is recommended to add prefixes to macros or place them in separate header files to avoid naming conflicts.

### 2.2 Limitations of Current Preprocessor

Although the above solution can achieve basic declarative syntax, the existing preprocessor functionality remains insufficient when implementing complex declarative UI and MVVM (Model-View-ViewModel) patterns. Traditional imperative approaches are relatively cumbersome:

```c
// Traditional implementation
message = "Hello";
component->onMessageChange(redrawComponent);

// Or using wrapper functions
setMessage(string msg) {
    message = msg;
    component->onMessageChange(redrawComponent);
}

drawText(getMessage());
```

## 3. Lessons and Inspiration from Other Languages

### 3.1 ArkTS Declarative UI Implementation

Huawei's ArkTS extends TypeScript with declarative UI and MVVM support:

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

Expanded implementation after compilation:

```typescript
class Index {
  private get message() : string { /* Implementation details */ }
  private set message(newValue: string) { /* Implementation details */ }

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

## 4. C/C++ Syntax Extension Proposal

### 4.1 Core Design Philosophy

We treat language elements such as types, functions, and variables as object instances, using dot syntax to add attributes during declaration. This approach can extend language functionality without adding excessive keywords.

### 4.2 Syntax Extension Examples

**Enhanced Type Declarations**:

```c++
// Method 1: Direct attribute addition after type name
struct MyClass .ownership().optimization1() {
    // Class implementation
};

// Method 2: Multi-line attribute declaration
struct MyClass
  .ownership()
  .optimization2() {
    // Class implementation
};

// Method 3: Trailing attribute declaration
struct MyClass {
    // Class implementation
}
.ownership()
.optimization3();
```

**Variable Attribute Support (Key MVVM Feature)**:

```c++
int visible
  .onGet(lambdaFunc1)
  .onSet(lambdaFunc2);

// Automatically calls lambdaFunc2 when setting value
visible = true;

// Automatically calls lambdaFunc1 when reading value
if (visible)
    showMessage("Hello");
```

**Function Attribute Declarations**:

```c++
void doMigration(int version) .public().async() {
    // Function implementation
}
```

## 5. Advanced Application Scenarios

### 5.1 Enhanced Module System

Imitating Rust's module system to reduce keyword count:

```c++
struct MyLib .module() {
    struct MyStruct1;
    struct MyStruct2;
}
```

### 5.2 Inheritance and Interface Implementation

Imitating Java's inheritance mechanism:

```c++
struct Child .extend(Parent).implement(MyInterface) {
    // Class implementation
};
```

### 5.3 Seamless Native Library Integration

Achieving seamless integration with C libraries through declarative syntax, using GTK+ as an example:

GTK naming convention mapping:

| Type      | Naming Convention    | Prefix |
|-----------|----------------------|--------|
| Struct    | Upper Camel Case     | Gtk    |
| Function  | Lower Snake Case     | gtk    |
| Constant  | Upper Snake Case     | GTK    |

Declarative mapping configuration:

```c++
struct Gtk {
    // Type definitions
}
.module()
.mapping("C")
.typeNamePrefix("Gtk").typeNaming(upperCamelCase)
.funcNamePrefix("gtk").funcNaming(lowerSnakeCase)
.constNamePrefix("GTK").constNaming(upperSnakeCase);
```

Traditional C code:

```c
GtkWindow *window;
window = gtk_window_new(GTK_WINDOW_TOPLEVEL);
gtk_window_set_title(window, "Hello");
```

Mapped C++ code:

```c++
Gtk::Window *window;
window = Gtk::Window::new(Gtk::Window::TOPLEVEL);
window->setTitle("Hello");
```

## 6. Summary and Future Outlook

The C3 syntax extension proposal presented in this document (where C2 corresponds to C++) aims to enhance C/C++ expressiveness through declarative syntax while maintaining language performance and compatibility. This solution features:

1. **Progressive Enhancement**: Extends language functionality while maintaining backward compatibility
2. **Declarative Programming**: Provides more concise and expressive syntax
3. **Toolchain Friendly**: Facilitates processing by compilers and development tools
4. **Ecosystem Compatibility**: Supports seamless integration with existing C/C++ ecosystems

This concept was formed in October 2025, and the initial version of this document was completed on November 7, 2025. We believe this syntax extension direction will inject new vitality into the C/C++ ecosystem and promote its broader application in modern software development.
