# Functions review
Functions help you organize code by designing each one to perform a particular task and make it reusable.

```cpp
#include <iostream>
using namespace std;

int cube(int number) {
  return number * number * number;
}

int main() {
  cout << "The cube of 5 is " << cube(5) << endl;
  cout << "The cube of 3 is " << cube(3) << endl;
  cout << "The cube of 9 is " << cube(9) << endl;
  return 0;
}
```

# Reusability
The example above shows us an instance where you reuse the function in the same project. What if you need the `cube` function in another project? At the moment, we might think of just copying the code. However, there is a more scalable way of doing this.

## Function prototypes
C++ allows the creation of *function prototypes* which gives the compiler just enough information to know how the function is used. Specifically, the function header that is composed of the *return type*, *function name*, and its *parameters*.

```cpp
// Filename: algebra.hpp
int cube(int number);
```

Related function prototypes are usually organized into what is called a *header file*. Remember how we use `#include <iostream>`? This is an example of a header file that contains function prototypes related to the input output stream and allows us to call functions like `cout` and `cin`.

For this tutorial, we can create an `algebra` header to contain all algebraic functions that we can reuse whenever we need them. Let's save our header into the `algebra.hpp` file. Header files can have any file name, but we'll use the `.hpp` extension as our convention. Many projects use this convention, but other variations include `.h` or `.hh`.

## Function prototype implementation
Once we define our function prototype, we can start implementing them. The function implementation is no different than how we create functions.

```cpp
// Filename: algebra.cpp
int cube(int number) {
  return number * number * number;
}
```

The only difference is that we organize them to make it easy to manage. Usually, there is an implementation file associated to each header file. We can create a file called `algebra.cpp` that contains all implementations of the function prototypes defined in `algebra.hpp`. We want to use the same name so that they are easy to distinguish especially when you have projects that use multiple header and implementation files.

## Integrating functions to projects
At this point we're already done creating our functions. However, it doesn't really make sense unless you use them. Just like how we include our other header files using `#include` we do the same for our own header file, but use double quotes (" ") instead of angle brackets (< >). The double quotes tell the compiler to find the header file in the current folder where you invoke the compiler, while the angle brackets tells the compiler that the header file is in the compiler's system directory.

```cpp
// Filename: driver.cpp
#include <iostream>
#include "algebra.hpp"
using namespace std;

int main() {
  cout << "The cube of 5 is " << cube(5) << endl;
  cout << "The cube of 3 is " << cube(3) << endl;
  cout << "The cube of 9 is " << cube(9) << endl;
  return 0;
}
```

If you notice, what changed from the first implementation is that we just included `algebra.h` instead of having the cube function in the same file. So how will the compiler know about `cube`'s implementation? The magic happens during compilation. Aside from compiling `driver.cpp`, we also compile the implementation file, `algebra.cpp`.

```
g++ -std=c++17 algebra.cpp driver.cpp -o driver
```

When the compiler compiles `driver.cpp` it will see the `#include "algebra.hpp"` 
and include the `cube` function prototype. This way, when `cube(5)` is called, it knows that `cube` is a function which receives a single integer parameter and returns an integer value.

The compiler also compiles `algebra.cpp` that contains `cube`'s implementation. During the linking step, it connects the function call in `main` to its corresponding implementation. You should be able to run your program as usual.

```
./driver
```

## Reusing headers
If you happen to create another project that requires functions you already created then its as easy as just copying the header and implementation files then adding the appropriate `#include` directive.

In our case, we can reuse our algebraic functions by copying `algebra.hpp` and `algebra.cpp` and add the `#include "algebra.hpp"` in any of our projects. Don't forget to compile `algebra.cpp` together with your other project files.

## Facilitating reuse
Why do we need to create separate header and implementation files? Can't we just put everything in one file and include it? Actually, you can and some projects do that. However, that structure makes the code less readable.

In practice, all function prototypes are placed in the header file so that you can easily see the entire list of functions. Having a single function prototype, like cube, is easily manageable, but imagine having a project that contains hundreds of functions! Moreover, the header file is also where most developers provide the documentation. You help other developers reuse your headers by providing them the information they need to call your function and also some documentation if necessary. They don't really need to see the implementation; just what it does and how to use it.

Naturally, the function implementations will be in a separate file because we separate them from the function prototypes. The advantage of this multi-file structure is that developers who want to know more about a function implementation can do so by reading the implementation file. You can also provide documentation about the inner workings of your function there. It is good coding practice to ensure that all function prototypes in the header file have their implementations in the implementation file and vice versa.

# Additional reading
## Include guards
A common issue with using the `#include` directive is the possibility of including the same header twice. This potentially leads to a *redefinition error* that causes compilation to fail. Developers often use what is called an *include guard* to prevent adding header files twice.

```cpp
#ifndef ALGEBRA_HPP
#define ALGEBRA_HPP

int cube(int number);

#endif
```

`#ifndef` and `#endif` are a pair of macros the work like `if` statements. Anything between the pair is implemented if a particular identifier is defined; in this case, `ALGEBRA_HPP`. If it has not been defined yet, the `#define` macro defines the identifier. If the `#include` is called more than once, it fails the condition because the identifier has already been defined previously. In effect, the statements inside the include guard are only performed once during compilation.
