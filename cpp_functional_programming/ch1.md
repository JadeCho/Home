## Book
<b>Functional Programming in C++</b>  
by Ivan Cukic  
Published by Manning Publications, 2019  


## 1 Introduction to functional programming

### What is functional programming?
- Functional programming is a style of programming that emphasizes the evaluation of expressions, rather than execution of commands. The expressions in these languages are formed by using functions to combine basic values. A functional language is a language that supports and encourages programming in a functional style.
- Broadly speaking, FP is a style of programming in which the main program building blocks are functions as opposed to objects and procedures. A program written in the functional style doesn’t specify the commands that should be performed to achieve the result, but rather defines what the result is.

### Imperative programming vs declarative programming
- Imperative means you command the computer to do something by explicitly stating each step it needs to perform in order to calculate the result.
- Declarative means you state what should be done, and the programming language has the task of figuring out how to do it. You define what a sum of a list of numbers is, and the language uses that definition to calculate the sum of a given list of numbers.

### Relationship with Object-Oriented Programming
- The object-oriented paradigm is based on creating abstractions for data. It allows the programmer to hide the inner representation inside an object and provide only a view of it to the rest of the world via the object’s API.
- The FP style creates abstractions on the functions. This lets you create more-complex control structures than the underlying language provides. When C++11 introduced the range-based for loop (sometimes called foreach), it had to be implemented in every C++ compiler (and there are many of them). Using FP techniques, it was possible to do this without changing the compiler. Many third-party libraries implemented their own versions of the range-based for loop over the years. When we use FP idioms, we can create new language constructs like the range-based for loop and other, more advanced ones; these will be useful even when writing programs in the imperative style.
In some cases, one paradigm is more suitable than the other, and vice versa. Often, a combination of the two hits the sweet spot. This is evident from the fact that many old and new programming languages have become multiparadigm instead of sticking to their primary paradigm.
