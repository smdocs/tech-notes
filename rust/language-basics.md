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
