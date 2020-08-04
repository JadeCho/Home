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

### Examples
#### Imperative
```cpp
std::vector<int>
count_lines_in_files(const std::vector<std::string>& files)
{
    std::vector<int> results;
    char c = 0;

    for (const auto& file : files) {
        int line_count = 0;

        std::ifstream in(file);

        while (in.get(c)) {
            if (c == '\n') {
                line_count++;
            }
        }

        results.push_back(line_count);
    }

    return results;
}
```
#### Declarative
```cpp
int count_lines(const std::string& filename)
{
    std::ifstream in(filename);

    return std::count(    ①  
        std::istreambuf_iterator<char>(in),    ①  
        std::istreambuf_iterator<char>(),    ①  
        '\n');    ①  
}

std::vector<int>
count_lines_in_files(const std::vector<std::string>& files)
{
    return files | transform(count_lines);
}
```
##### Transformation using ranges, modified
This solution is much less verbose than the imperative solution and much more obvious. You start with a collection of filenames—it doesn’t even matter which collection type you’re using—and perform two transformations on each element in that collection. First you take the filename and create a stream from it, and then you go through that stream to count the newline characters. This is exactly what the preceding code says—without any excess syntax, and without any boilerplate.
```cpp
std::vector<int>
count_lines_in_files(const std::vector<std::string>& files)
{
    return files | transform(open_file)
                 | transform(count_lines);
}
```
### Pure functions
- **“OO makes code understandable by encapsulating moving parts. FP makes code understandable by minimizing moving parts.”**
- One of FP’s most powerful ideas is pure functions: functions that only use (but don’t modify) the arguments passed to them in order to calculate the result. If a pure function is called multiple times with the same arguments, it must return the same result every time and leave no trace that it was ever invoked (no side effects). This all implies that pure functions are unable to alter the state of the program.
- A pure function is any function that doesn’t have observable (at a higher level) side effects. The function caller shouldn’t be able to see any trace that the function was executed, other than getting the result of the call.

### Avoiding Mutable State
Mutable 상태를 제거한다는게 무슨 의미?
아래 코드를 보면 변수 c가 지속적으로 변경된다.
```cpp
for (const auto& file: files) {
    int line_count = 0;

    std::ifstream in(file);

    while (in.get(c)) {
        if (c == '\n') {
            line_count++;
        }
    }

    results.push_back(line_count);
}
```
여기서 counting 부분을 count_lines함수로 분리를 하게되면 pure 함수처럼 보이게 만들수 있으나 count_lines_in_files함수는 특별히 개선되지 않는다.
```cpp
int count_lines(const std::string& filename)
{
    std::ifstream in(filename);

    return std::count(
        std::istreambuf_iterator<char>(in),
        std::istreambuf_iterator<char>(),
        '\n');
}
```
여기에 range notation을 추가한다면 다음과 같이 개선이 가능하다.
```cpp
std::vector<int>
count_lines_in_files(const std::vector<std::string>& files)
{
    return files | transform(count_lines);
}
```
### Thinking functionally
Simplifying the problem by analyzing the needed transformation only for a single item from the given collection.
컬렉션이 아닌 하나의 아이템에 대한 문제로 단순화 시켜서 컬렉션 문제를 해결한다.

### Evolution of C++ as a functional programming language
For FP in C++, the importance of templates isn’t (mainly) in the creation of container classes such as vectors, but in the fact that it allowed creation of STL algorithms—a set of common algorithm patterns such as sorting and counting. Most of these algorithms let you pass custom functions to customize the algorithms’ behavior without resorting to function pointers and void*. This way, for example, you can change the sorting order, define which items should be included when counting, and so on.  

The capability to pass functions as arguments to another function, and to have functions that return new functions (or, more precisely, things that look like functions, as we’ll discuss in chapter 3), made even the first standardized version of C++ an FP language. C++11, C++14, and C++17 introduced quite a few features that make writing programs in the functional style much easier. The additional features are mostly syntactic sugar—but important syntactic sugar, in the form of the auto keyword and lambdas (discussed in chapter 3). These features also brought significant improvements to the set of standard algorithms. The next revision of the standard is planned for 2020, and it’s expected to introduce even more FP-inspired features such as ranges, concepts, and coroutines, which are currently Technical Specifications.

### Summary
- The main philosophy of functional programming is that you shouldn’t concern yourself with the way something should work, but rather with what it should do.
- Both approaches—functional programming and object-oriented programming—have a lot to offer. You should know when to use one, when to use the other, and when to combine them.
- C++ is a multiparadigm programming language you can use to write programs in various styles—procedural, object-oriented, and functional—and combine those styles with generic programming.
- Functional programming goes hand-in-hand with generic programming, especially in C++. They both inspire programmers not to think at the hardware level, but to move higher.
- Function lifting lets you create functions that operate on collections of values from functions that operate only on single values. With function composition, you can pass a value through a chain of transformations, and each transformation passes its result to the next.
- Avoiding mutable state improves the correctness of code and removes the need for mutexes in multithreaded code.
Thinking functionally means thinking about the input data and the transformations you need to perform to get the desired output.

