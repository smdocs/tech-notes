# JVM Mechanics

1. Compiler 
2. Adaptive Behaviour
3. Ordering 
4. Garbage Collection


#### 1. Compiler

    JIT Compilers transform code, the code that gets executed can be very different from the code that was written.
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
Dead code can be removed
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
- Writes can be eliminated, intermediate values might never be visible
```java
    void updateDistance(Object a) {
        a. setVisibleValue(0);
        for (int i = 0; i < 1000000; i++) {
            a.setInternalValue(i);
    }
        a.setVisibleValue(a.getInternalValue());
    }

//Is the same as
    void updateDistance(Object a) {
        a.setInternalValue(1000000);
        a.setVisibleValue(1000000);
    }
```
 JIT Compiler can also do the following
 1. Class Hierarchy analysis
    - Can perform global analysis on currently loaded code
    - Deduce stuff about inheritance, method overrides, etc.
    - Can make optimization decisions based on assumptions
    - Re-evaluate assumptions when loading new classes
    - Throw away code that conflicts with assumptions before class loading makes them invalid

 2. Inlining as long as there is one implementer of get(X)
 3. Speculative optimization
    - Being able throw away wrong code is very useful
    - Speculatively assuming callee type
    - polymorphic can be “monomorphic” or “megamorphic”
    - Can make virtual calls static even without CHA
    - Can speculatively inline things without CHA
    - Speculatively assuming branch behavior
 4. Adaptative compilation
    - Reduces need to trade off clean design against speed
        - E.g. “final” should be used on methods only when you want to prohibit extension, overriding. Has no effect on speed.
        - E.g. branching can be written “naturally”
 5. 
