[中文](README.cn.md)  
site: [GitHub Repository](https://github.com/chhuang-one/c4), [Gitee Repository](https://gitee.com/chhuang-one/c4)

# C/C++ Language Syntax Extension Proposal

## 1. Core Design Philosophy

We treat language elements such as types, functions, and variables as (compiler's) object instances, using dot (.) or colon (:) syntax to add attributes during declaration.
This approach can extend language functionality without adding excessive keywords.
Users can set permissions similar to rwx permissions in UNIX systems, control the compiler options, and even extend the language using special declarations.  
  
In fact, many keywords in modern programming languages ​​describe access permissions, such as mutable, const, public, protected, and private.

## 2. Syntax Extension Examples

**Enhanced Type Declarations**:

optimization(1) corresponds to the compiler option -O1, 'ownership' imitating Rust's memory management.

```c++
// Method 1: Direct attribute addition after type name. Using colon (:) syntax here.
struct MyClass :ownership:permission(rwx):optimization(1)
{
    // Class implementation
    void  doSomething() :public:permission(rwx);
};

// Method 2: Multi-line attribute declaration. Using dot (.) syntax here.
struct MyClass
  .ownership()
  .permission(rwx)
  .optimization(2)
{
    // Class implementation
    void  doSomething() .public().permission(rwx);
};

// Method 3: Trailing attribute declaration. Using dot (.) syntax here.
struct MyClass
{
    // Class implementation
    void  doSomething() .public().permission(rwx);
}
.ownership()
.permission(rwx)
.optimization(3);
```

**Specific modules require special development guidelines**

The guidelines() is used to specify development guidelines.
The compiler issues a warning when the code does not conform to the development guidelines.  
When no development guidelines are specified, the compiler issues warnings using the default setting.

```c++
// Using dot (.) syntax here.
namespace MyModule .guidelines("MISRA-C:2004")
{
    // implementation
}
```

**Variable Attribute Support (Key MVVM Feature)**:

```c++
// Method 1: Direct attribute addition after variable name. Using dot (.) syntax here.
bool visible
  .permission(rw)                          // mutable
  .property()
  .onGet(notifyPropertyRead)
  .onSet(notifyPropertyChanged) = true;    // default is visible

// Method 2: Using colon (:) syntax here.
bool visible :permission(rw):property {    // mutable
  bool operator=(bool isVisible) {
    notifyPropertyChanged();
    return visible = isVisible;
  }
  bool operator=() {
    notifyPropertyRead();
    return visible;
  }
} = true;                                  // default is visible


// The 'notifyPropertyChanged' function is called when setting the value.
visible = true;

// The 'notifyPropertyRead' function is called when reading the value.
if (visible)
    showMessage("Hello");
```

**Function Attribute Declarations**:

```c++
// Using colon (:) syntax here.
void doMigration(int version) :async:permission(rwx) {
    // Function implementation
}

// Using dot (.) syntax here.
void doSomething(int action)  .async().permission(rwx) {
    // Function implementation
}
```

## 3. Advanced Application Scenarios

### 3.1 Enhanced Module System

Imitating Rust's module system to reduce keyword count:

```c++
// Using dot (.) syntax here.
struct MyLib .module() {
    struct MyStruct1;
    struct MyStruct2;
}
```

### 3.2 Inheritance and Interface Implementation

Imitating Java's inheritance mechanism:

```c++
// Using dot (.) syntax here.
class Child .extend(Parent).implement(MyInterface) {
    // Class implementation
};
```

### 3.3 Seamless Native Library Integration

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

## 4. Summary and Future Outlook

The proposed syntax extension scheme aims to enhance the expressiveness of C/C++ through declarative syntax while maintaining the language's performance and compatibility.

The concept was formed in October 2025, and the initial draft of this document was completed on November 7, 2025.
