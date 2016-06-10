#JVM Mechanics

1. Compiler 
2. Adaptive Behavious
3. Ordering 
4. Garbage Collection


#### Compiler

- JIT Compilers transform code, the code that gets executed can be very different from the code that was written.
```java
  int doMath(int x, int y, int z) {
  int a = x + y;
  int b = x - y;
  int c = z + x;
  return a + b;
}
//Can be reordered to:
  int doMath(int x, int y, int z) {
  int c = z + x;
  int b = x - y;
  int a = x + y;
  return a + b;
}
```
- Dead code can be removed
```java
  int doMath(int x, int y, int z) {
  int a = x + y;
  int b = x - y;
  int c = z + x;
  return a + b;
}
//Can be reduced to:
  int doMath(int x, int y, int z) {
  int a = x + y;
  int b = x - y;
  return a + b;
}
```
