# Functions review
Functions help you organize code by designing each one to perform a particular task and make it reusable.

```cpp
#include <iostream>

int cube(int number) {
  return number * number * number;
}

int main() {
  std::cout << "The cube of 5 is " << cube(5) << "\n";
  std::cout << "The cube of 3 is " << cube(3) << "\n";
  std::cout << "The cube of 9 is " << cube(9) << "\n";
  return 0;
}
```

# Reusability
The example above shows us an instance where you reuse the function in the same project. What if you need the `cube` function in another project? At the moment, we might think of just copying the code. However, there is a more scalable way of doing this.

## Function declaration
C++ allows the creation of *function declarations* which gives the compiler just enough information to know how the function is used. Specifically, the the *return type*, *function name*, and its *parameters*.

```cpp
// Filename: algebra.h
int cube(int number);
```

Related function declarations are usually organized into what is called a *header file*. Remember how we use `#include <iostream>`? This is an example of a header file that contains function declarations related to the input output stream and allows us to call functions like `cout` and `cin`.

For this tutorial, we can create an `algebra` header file to contain all algebraic functions that we can reuse whenever we need them. Let's save our header into the `algebra.h` file. Header files can have any file name, but we'll use the `.h` extension as our convention. Many projects use this convention, but other variations include `.hh` or `.hpp`.

## Function definition
After creating the function declaration in the header file, we can create its body. We use the `#include` directive to connect our function declaration and it's definition. In this case, we include `algebra.h` because it contains our function declaration for `cube`. Including the header file also ensures that the function implementation has access to all data used by the declaration. The function definition is no different than how we create functions.

```cpp
// Filename: algebra.cc
#include "algebra.h"

int cube(int number) {
  return number * number * number;
}
```

The only difference in using this multi-file structure is that we organize functions for easier management. Usually, there is an implementation file associated with each header file. We can create a file called `algebra.cc` that contains all definitions of the function declarations in `algebra.h`. We want to use the same file name so that they are easy to distinguish especially when you have projects that use multiple header and implementation files.

## Integrating functions to projects
At this point we're already done creating our functions. However, it doesn't really make sense unless you use them. Just like how we include our other header files using `#include` we do the same for our own header file, but use double quotes (" ") instead of angle brackets (< >). The double quotes tell the compiler to find the header file in the current folder where you invoke the compiler, while the angle brackets tells the compiler that the header file is in the compiler's system directory.

```cpp
// Filename: main.cc
#include <iostream>
#include "algebra.h"

int main() {
  std::cout << "The cube of 5 is " << cube(5) << "\n";
  std::cout << "The cube of 3 is " << cube(3) << "\n";
  std::cout << "The cube of 9 is " << cube(9) << "\n";
  return 0;
}
```

If you notice, what changed from the first implementation is that we just included `algebra.h` instead of writing the cube function in the same file. So how will the compiler know about `cube`'s implementation? The magic happens during compilation. Aside from compiling `main.cc`, we also compile the implementation file, `algebra.cc`.

```
clang++ -std=c++17 algebra.cc main.cc -o main
```

When the compiler compiles `main.cc` it will see the `#include "algebra.h"`
and include the `cube` function declaration. This way, when `cube(5)` is called, it knows that `cube` is a function which receives a single integer parameter and returns an integer value.

The compiler also compiles `algebra.cc` that contains `cube`'s definition. During the linking step, it connects the function call in `main` to its corresponding implementation. You should be able to run your program as usual.

```
./main
```

For organization, we name our executable file `main` because the file containing the `main` function is `main.cc`

## Reusing function definitions
If you happen to create another project that requires functions you already created then its as easy as just copying the header and implementation files then adding the appropriate `#include` directive.

In our case, we can reuse our algebraic functions by copying `algebra.h` and `algebra.cc` and add the `#include "algebra.h"` in any of our projects. Don't forget to compile `algebra.cc` together with your other project files.

## Facilitating reuse
Why do we need to create separate header and implementation files? Can't we just put everything in one file and include it? Actually, you can and some projects do that. However, that structure makes the code less readable.

In practice, all function declarations are placed in the header file so that you can easily see the entire list of functions. Having a single function prototype, like `cube`, is easily manageable, but imagine having a project that contains hundreds of functions! Moreover, the header file is also where most developers provide the documentation. You help other developers reuse your headers by providing them the information they need to call your function and also some documentation if necessary. They don't really need to see the function definitions; just what it does and how to use it.

Naturally, the function definitions will be in a different file because we separate them from the function declarations. The advantage of this multi-file structure is that developers who want to know more about a function's definition can do so by reading the implementation file. You can also provide documentation about the inner workings of your function there. It is good coding practice to ensure that all function declarations in the header file have their function definitions in the implementation file and vice versa.

# Additional reading
## Include guards
A common issue with using the `#include` directive is the possibility of including the same header twice. This potentially leads to a *redefinition error* that causes compilation to fail. Developers often use what is called an *include guard* to prevent adding header files twice.

```cpp
// Filename: algebra.h
#ifndef ALGEBRA_H
#define ALGEBRA_H

int cube(int number);

#endif
```

`#ifndef` and `#endif` are a pair of macros the work like `if` statements. Anything between the pair is implemented if a particular identifier is defined; in this case, `ALGEBRA_H`. The name can be any identifier, but is usually the name of the file using upper case and using an underscore (`_`) in place of the period. If it has not been defined yet, the `#define` macro defines the identifier. If the `#include` is called more than once, it fails the condition because the identifier has already been defined previously. In effect, the statements inside the include guard are only performed once during compilation.
