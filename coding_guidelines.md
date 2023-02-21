# Coding guidelines

This section contains a few general rules on how code should be witten.
Those guidelines aim to make written code more readable and to have
an equal style across all files.

<br><br>

## Naming Conventions
### Repositories
Repository names should be **snake_case** (all lowercase connected with "`_`")

<br>

### Files
Just like Repositories, file names should be **snake_case**.

<br>

### Functions
Function names require to be written in **camelCase**, where the **first letter** is **lowercase**.


### Classes
Class name should be written in **PascalCase**. (*camelCase* with **first letter capitalized**)

### Types
Types should be written in **snake_case**, with an added **_t** at the end.

Example:
```cpp
using my_number_t = double;
```

This also includes structures whose primary use is a simple data container:
```cpp
struct coord_t
{
    int x, y, z;
}

```
### Namespaces
If used like a class (only static functions), make it look like a **class**.
Otherwise use a **snake_case** style, though it is preferred to use short namespace names with only one word.

### Variables
**Every** variable name (**not including defines**) should be written in **snake_case**.

### Defines
Defines should be written **ALL_CAPITAL** with a "`_`" as seperator.

<br><br>

## Code Documentation
All code should be documented in a way that someone who never saw it can
at least make some sense of what the code is doing.

For documenting functions, classes and files, the extension `Doxygen` will be used.

To create a description with `Doxygen`, simple write
```cpp
/**
```

and then press enter.

<br>

### Credits
On the top of each file, a header containing the file owner and a brief description of the files content.

Example:

```cpp
/**
 * @file main.cpp
 * @author Nilusink
 * @brief main file for the project
 * @version 0.1
 * @date 2023-02-01
 * 
 * @copyright Copyright FrenchBakery (c) 2023
 * 
 */
```

For **header** and **implementation** files, the corresponding file should each have the same header. The **implementation** file does **not** require the `@brief` keyword.

<br>

### Functions and Classes
Similarly as for file documentation, each function should also have a header.

Example (Function):
```cpp
/**
 * @brief turns on motors until the end switch is triggered
 * 
 * @param speed how fast the motors should be on
 */
void grab_until_end(int speed = 100);
```

Example (Class):
```cpp
/**
 * @brief driver for Tiramisu's gripper
 * 
 */
class Gripper
```

<br><br>

## Style Guidlines
### Headers / Implementations
If possible every class should be seperated into a header (`.hpp`) and a implementation file (`.cpp`). **Defenitions** and **Documentation** for the function should be made in the header files. **Implementations** and **Documentation** for code implementations should be made in the `.cpp` files.

<br>

### Includes
For every include of files that have been written for **this** project, `""` should be used, even if the path is relative to the src folder and not relative to the file.

Example:
```cpp
#include "utils.hpp"
#include "hw/drivers/gripper.hpp"
```

<br>

For every non-poject include, `<>` should be used.

Example:
```cpp
#include <iostream>
```

<br>

### Curly Brackets
Curly brackets, as in **function or class implementations** should be placed at the next line.

Example:
```cpp
float my_test_func(float a, float b)
{
    return a * b;
}
```

<br>

### Classes and Structures

When the primary purpose of an object is to compute complex operations or implement advanced functionality in the form of member functions, **class** should be used:

```cpp
class MyClass
{
    int myfunc1(...);
    int myfunc2(...);
    int myfunc3(...);
    int myfunc4(...);
    .
    .
};
```

When the primary purpose of an object is to store **simple** data grouped as one "type", **struct** should be used:

```cpp
struct vector_t
{
    double x, y, z;
    int len();
};
```