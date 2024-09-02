# Rust Language Basics

## Memory Management
- In Rust, memory is managed through a system of ownership with a set of rules that the compiler checks. If any of the rules are violated, the program won’t 
compile. None of the features of ownership will slow down your program while it’s running.

- When your code calls a function, the values passed into the function (including, potentially, pointers to data on the heap) and the function’s local 
variables get pushed onto the stack. When the function is over, those values get popped off the stack.

- Keeping track of what parts of code are using what data on the heap, minimizing the amount of duplicate data on the heap, and cleaning up unused data
  on the heap so you don’t run out of space are all problems that ownership addresses. The main purpose of ownership is to manage heap data can help explain why it works
  the way it does.

## Ownership rules
First, let’s take a look at the ownership rules. Keep these rules in mind as we work through the examples that illustrate them:
- Each value in Rust has an owner.
- There can only be one owner at a time.
- When the owner goes out of scope, the value will be dropped.

Rust takes a different path: the memory is automatically returned once the variable that owns it goes out of scope.
```rust
    {
        let s = String::from("hello"); // s is valid from this point forward

        // do stuff with s
    }                                  // this scope is now over, and s is no
                                       // longer valid
```
When a variable goes out of scope, Rust calls a special function for us. This function is called <b>drop</b>, and it’s where the author of String can put the code to return the memory. Rust calls drop automatically at the closing curly bracket.

o ensure memory safety, after the line let s2 = s1;, Rust considers s1 as no longer valid. Therefore, Rust doesn’t need to free anything when s1 goes out of scope. Check out what happens when you try to use s1 after s2 is created; it won’t work:
```rust
    let s1 = String::from("hello");
    let s2 = s1;

    println!("{s1}, world!");
```
This code does not compile! - You’ll get an error like this because Rust prevents you from using the invalidated reference:

 Terms shallow copy and deep copy while working with other languages, the concept of copying the pointer, length, and capacity without copying the data probably sounds like making a shallow copy. 
 
 > But because Rust also invalidates the first variable, instead of being called a shallow copy, it’s known as a     move. In this example, we would say that s1 was moved into s2. 

![](https://doc.rust-lang.org/book/img/trpl04-04.svg)

In addition, there’s a design choice that’s implied by this: Rust will never automatically create “deep” copies of your data. Therefore, any automatic copying can be assumed to be inexpensive in terms of runtime performance

For primitive types such as integers whose value is entirely stores in the stack, Rust simply copies the value. n other words, there’s no difference between deep and shallow copying here, so calling clone wouldn’t do anything different from the usual shallow copying, and we can leave it out.

Rust won’t let us annotate a type with Copy if the type, or any of its parts, has implemented the Drop trait. If the type needs something special to happen when the value goes out of scope and we add the Copy annotation to that type, we’ll get a compile-time error. 

So, what types implement the Copy trait? You can check the documentation for the given type to be sure, but as a general rule, any group of simple scalar values can implement Copy, and nothing that requires allocation or is some form of resource can implement Copy. Here are some of the types that implement Copy:

```
    All the integer types, such as u32.
    The Boolean type, bool, with values true and false.
    All the floating-point types, such as f64.
    The character type, char.
    Tuples, if they only contain types that also implement Copy. For example, (i32, i32) implements Copy, but (i32, String) does not.
```

## References
1. [Rust Programming Language Book](https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html)
