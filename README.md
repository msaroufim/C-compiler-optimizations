# C-compiler-optimizations


###Value Range Optimization
```c
for(int i = 1; i < 100; i++) {
    if (i)
        g();
}
```

The ```if``` expression can be eliminated since it is already known that ```i``` is a positive integer.

###Tail Recursion

A tail recursive call can be replaced by a ```goto``` statement which avoids keeping track of the stack frame.

Let's take a simple recursive function

```c
int f(int i) {
    if (i > 0) {
        g(i);
        return f(i - 1)
    }
    else
        return 0;
}
```

Now let's optimize it

```c
int f (int i) {
    entry:

    if (i > 0) {
        g(i);
        i--;
        goto entry;
    }
    else
        return 0;
}

```

###Try/Catch block optimization

Try/Catch blocks that never throw an exception can be optimized

```c
try {
    int i = 1;
} catch (Exception e) {
    //some code
}
```

Can be turned into ```int i = 1;```

###Loop unrolling

When the different iterations of a loop are independent

```c
for (int i = 0; i < 100; i++) {
    g();
}
```

The loop can be optimized

```c
for (int i = 0; i < 100; i += 2) {
    g();
    g();
}
```

This can of course be done even more aggressively

###Unswitching

As opposed to checking if some condition or the other is true inside of a loop, you can take the ```if``` out of the loop and then loop.

```c
for (int i = 0; i < N; i++) 
    if (x)
        a[i] = 0;
    else
        b[i] = 0;
```

```c
if (x)
    for (int i = 0; i < N; i++)
        a[i] = 0
else
    for (int i = 0; i < N; i++)
        b[i] = 0;
```

###Virtual Function Optimization
```java
class TestClass {
    public void VirtualGetOne() {return ;}
    public void GetOne() {return ;}
}

```


###Volative Optimization
```volatile``` keyword is used to declare objects that may have unintended side effects.

```c
volatile int x,y;
int a[SIZE];

void f (void) {
    int i;
    for (i = 0;  i < SIZE; i++)
        a[i] = x + y;
}
```

You would think that you could hoist the computation of ```x+y``` outside of the loop 

```c
volatile int x,y;
int a[SIZE];

void f (void) {
    int i;
    int temp = x + y;
    for (i = 0;  i < SIZE; i++)
        a[i] = temp;
}

```

However if ```x``` and ```y``` are volatile then this optimization might in fact be incorrect which is why compilers will not perform it.


###Quick Optimization

Accessed objects can be cached into a temporary variable
```java
{
    for(i = 0; i < 10; i++)
          arr[i] = obj.i + volatile_var;
}
```

Below is the code fragment after Quick Optimization.

```java
{
         t = obj.i;
         for(i = 0; i < 10; i++)
           arr[i] = t + volatile_var;
}
```
  
###printf Optimization
```
#include <stdio.h>

void f (char *s)
{
  printf ("%s", s);
}
```