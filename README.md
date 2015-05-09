# C-compiler-optimizations


###Value Range Optimization
```c
for(int i = 1; i < 100; i++) {
    if (i)
        g();
}
```

The ```if``` expression can be eliminated since it is already known that ```i``` is a positive integer.

```c

for(int i = 1; i < 100; i++) {
    g();
}
```



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
Calling ```printf()``` invokes the external library function ```printf()```

```
#include <stdio.h>

void f (char *s)
{
  printf ("%s", s);
}
```

The string can be formatted at compile time using
```c
#include <stdio.h>

void f (char *s)
{
  fputs (s, stdout);
}
```

###New expression optimization
This is more of a thing in java

```
int a[];
a = new int[100];
```

If ```a``` is never used then memory is never allocated for it


###Narrowing

```c
unsigned short int s;

(s >> 20)      /* all bits of precision have been shifted out, thus 0 */
(s > 0x10000)  /* 16 bit value can't be greater than 17 bit, thus 0 */
(s == -1)      /* can't be negative, thus 0 */
```

###Integer Multiply

This a well known one, given an expression 

```c
int f (int i,int n)
{
  return i * n;
}  
```

Multiplication can be replaced by leftwise bitwise shifting

```c
int f (int i)
{
  return i << (n-1);
}
```

###Integer mod optimization
Another known one, integer divide is really expensive on hardware.
```c
int f (int x,int y)
{
  return x % y;
}
```

Hacker's delight is a wonderful book that's encyclopedic in its treatment of cool bit tricks such as the one below.

```c

int f (int x)
{
  int temp = x & (y-1);
  return (x < 0) ? ((temp == 0) ? 0 : (temp | ~(y-1))) : temp;
}
```

###Block Merging

Suppose you had the following code fragment

```c
int a;
int b;

void f (int x, int y)
{
  goto L1;                   /* basic block 1 */

L2:                          /* basic block 2 */
  b = x + y;
  goto L3;

L1:                          /* basic block 3 */
  a = x + y;
  goto L2;

L3:                          /* basic block 4 */
  return;
}
```


The different blocks will be optimized into one



```c
int a;
int b;

void f (int x, int y)
{
  a = x + y;                 /* basic block 1 */
  b = x + y;
  return;
}

```

###Common SubExpression
The second code fragment above can further be optimzed into 

```c
tmp = x + y
a   = tmp
b   = tmp
return;
```