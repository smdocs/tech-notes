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
- Values can be reduced
```java
  int doMath(int x, int y, int z) {
    int a = x + y;
    int b = x - y;
    int c = z + x;
    return a + b;
}
//Can be reduced to:
  int doMath(int x, int y, int z) {
    return x + y + x - y;
}
//or simplified
  int doMath(int x, int y, int z) {
    return x + x;
}
```
- Reads can be cached.
```java
  int distanceRatio(Object a) {
    int distanceTo = a.getX() - start;
    int distanceAfter = end - a.getX();
    return distanceTo/distanceAfter;
}
//Is the same as
  int distanceRatio(Object a) {
    int x = a.getX();
    int distanceTo = x - start;
    int distanceAfter = end - x;
    return distanceTo/distanceAfter;
}
//another example
  void loopUntilFlagSet(Object a) {
    while (!a.flagIsSet()) {
    loopcount++;
}

Is the same as:
void loopUntilFlagSet(Object a) {
  boolean flagIsSet = a.flagIsSet();
  while (!flagIsSet) {
    loopcount++;
  }
}
//That’s what volatile is for...
```
