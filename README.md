# Using value types in managed C++

A quick introduction to using .NET value types in managed C++

<!-- Article Starts -->



## Introduction

[Managed reference types](managed_types.asp) in .NET are objects that contains methods and properties
and which live their lives on the .NET managed heap. There are situations, however, where you do not want the
overhead associated with creating and destroying objects on the heap. When dealing with simple data structures such as 
an integer or an (x,y) coordinate it is far more convenient - and efficient - to allocate these values directly
on the stack and work with them using the standard value type semantics. No one wants to have to write

```cpp
int* n = new int();
```

when declaring an integer (or array of 10,000 integers!). Instead, primitive types such as numeric types,
boolean, char and date, structures and enumerations are all declared as value types, meaning they are allocated
on the stack and declared and accessed as you would a structure or stack-based variable in standard C++.

## Declaring and Creating Value Types

To declare a value type in managed C++ you use the new `__value` keyword. For instance, if we
wished to create a data type to represent complex numbers we could declare the following:

```cpp
__value struct Complex
{
    double real;
    double imaginary;
};
```

Value types are created on the stack and are accessed directly. Value types are declared 

```cpp
Complex z;
```

and their members accessed using the `.` syntax 

```cpp
z.real = 1.0;
z.imaginary = -3.1415;
```

Once the memory containing a value type is freed, the value type instance is destroyed. Hence, references 
to value types are not allowed. If it were allowed it would be possible to have a reference point to an 
invalid memory location. A value type will always point to a variable of that type, and cannot be null. 
Assigning a value type to another variable results in a copy of the value being made.

Because value types are managed types, they are initialised to 0 when they are created. Value
types can contain reference types, though these reference types will be created on the managed heap
and the reference to them stored on the stack.

Value types implicitly inherit from `System.ValueType`, and their use is encouraged
for types that act like primitive data types, types that are small, types that do not inherit from,
or are inherited by, other data types, and types that are not frequently passes as parameters (this
would cause a lot of memory allocation and copying). Value types can inherit from managed interfaces,
and can override virtual methods defined therein.

One important point with all value types is that they do can contain methods and fields, and you
can override virtual methods from base interfaces. For instance, we may wish to override the `ToString`
method for our complex type so that we can present it in a formatted manner

```cpp
__value struct Complex
{
    double real;
    double imaginary;

    virtual String *ToString()
    {
        return String::Format("{0} + {1}i", real.ToString("N2"), imaginary.ToString("N2"));
    }
};
```

## Boxing and Unboxing

Boxing is a feature that allows a value type to be passed around as a reference type. For instance,
one of the the standard `Format` method overrides of the .NET `String` class takes
a format string and an object.

```cpp
public: static String* Format(String*, Object*);
```

The format string specifies the format, and the object is accessed within that format string using
the `{0}` syntax. An `int` is not derived from `Object` and hence cannot
normally be passed to this function. However, by boxing an integer value we are creating an object that
will contain the value of the integer and can be used as an object. The syntax is

```cpp
object obj = __box(5);
```

This creates a managed reference on the managed heap containing the value '5'. This object can now
be used wherever an `Object*` object is required.

We can also declare boxed value types like

```cpp
__box Complex* pZ = __box(z);
```

This gives us an object we can use in whatever way we deem fit, but in doing so we retain the
ability to access the underlying type's data fields

```cpp
pZ->real = 4;
```

Once we are done using the value in its boxed form we can unbox the value using the C++
`dynamic_cast` operator

```cpp
Complex w = * dynamic_cast<Complex*>(pZ);
```

## History

26 Apr 2001 - added sample app

16 Oct 2001 - updated source for VS.NET beta 2
