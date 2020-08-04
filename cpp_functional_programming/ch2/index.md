## 2. Getting started with functional programming

### This chapter covers:
* Understanding higher-order functions
* Using higher-order functions from the STL
* Problems with composability of STL algorithms
* Recursion and tail-call optimization
* The power of the folding algorithm

### Functions taking functions?
The main feature of all functional programming (FP) languages is that functions can be treated like ordinary values.  
**Functions that take other functions as arguments or that return new functions are called higher-order functions.**

#### More notation for specifying function types
When I want to denote an arbitrary collection containing items of a certain type T, I’ll write collection<T> or just C<T>. To say that an argument for a function is another function, I’ll write its type in the list of arguments.

##### Filter
The filter construct takes a collection and a predicate function (T → bool) as arguments, and it returns a collection of filtered items, so its type is written like this:
```cpp
filter: (collection<T>, (T → bool)) → collection<T>
```

##### Transform 
Note that the output collection doesn’t need to contain items of the same type as the input collection (unlike with filtering). This construct is called map or transform, and its type is as follows:
```cpp
transform: (collection<In>, (In → Out)) → collection<Out>
```

### Examples from the STL
#### Calculating Averages

##### Calculating the average score imperatively
```cpp
double average_score(const std::vector<int>& scores)
{
    int sum = 0;    ①  

    for (int score : scores) {    ②  
        sum += score;    ②  
    }    ②  

    return sum / (double)scores.size();    ③  
}
```
##### Calculating the average score functionally
```cpp
double average_score(const std::vector<int>& scores)
{
    return std::accumulate(    ②  
               scores.cbegin(), scores.cend(),    ②  
               0    ①  
           ) / (double)scores.size();    ③  
}
```
##### Calculating the product of all scores
```cpp
double scores_product(const std::vector<int>& scores)
{
    return std::accumulate(    ②  
               scores.cbegin(), scores.cend(),    ②  
               1,    ①  
               std::multiplies<int>()    ③  
           );
}
```

#### Folding
We often need to process a collection of items one item at a time, in order to calculate something. The std::accumulate algorithm is an implementation of a general concept called folding (or reduction). This is a higher-order function that abstracts the process of iterating over recursive structures such as vectors, lists, trees, and so on and lets you gradually build the result you need.

##### Counting newline characters with std::accumulate
```cpp
int f(int previous_count, char c)
{
    return (c != '\n') ? previous_count    ①  
                       : previous_count + 1;    ①  
}

int count_lines(const std::string& s)
{
    return std::accumulate(
               s.cbegin(), s.cend(),    ②  
               0,    ③  
               f
           );
}
```

#### STRING TRIMMING
The algorithm you need is std::find_if. It searches for the first item in the collection that satisfies the specified predicate.
```cpp
std::string trim_left(std::string s)
{
    s.erase(s.begin(),
            std::find_if(s.begin(), s.end(), is_not_space));
    return s;
}

std::string trim_right(std::string s)
{
    s.erase(std::find_if(s.rbegin(), s.rend(), is_not_space).base(),
            s.end());
    return s;
}

std::string trim(std::string s)
{
    return trim_left(trim_right(std::move(s)));
}
```
##### Passing by value
In the previous example, it’s better to pass the string by value instead of passing it as a const reference, because you’re modifying it and returning the modified version. Otherwise, you’d need to create a local copy of the string inside the function. That might be slower if the user calls the function with a temporary value (rvalue) that will be copied instead of being moved into the function. The only downside of passing by value here is that if the copy constructor for the string throws an exception, the backtrace may be confusing to the user of the function.

#### PARTITIONING COLLECTIONS BASED ON A PREDICATE
Before we move on to something more involved, imagine that you have a collection of people, and your task is to move all females to the beginning of the collection. To do this, you can use the std::partition algorithm and its variant, std::stable_partition.
The difference between these two algorithms is that std::stable_partition retains the ordering between elements from the same group. 

##### Females first
```cpp
std::partition(
        people.begin(), people.end(),
        is_female
    );
```
##### Moving selected items to a specific point
```cpp
std::stable_partition(first, destination, is_not_selected);
std::stable_partition(destination, last,  is_selected);
```

####  FILTERING AND TRANSFORMING
```cpp
bool is_female(const person_t& person);
bool is_not_female(const person_t& person);
std::string name(const person_t& person);

/// Filtering items by removing undesired ones
people.erase(    ②  
    std::remove_if(people.begin(), people.end(),    ①  
                   is_not_female),    ①  
    people.end());
    
/// Filtering items by copying them to a new collection
std::vector<person_t> females;    ①  

std::copy_if(people.cbegin(), people.cend(),    ②  
             std::back_inserter(females),    ②  
             is_female);    ②  
             
/// Getting the names
std::vector<std::string> names(females.size()); 
std::transform(females.cbegin(), females.cend(),    ① 
                names.begin(),    ②
                name);    ③  
```

### Composability problems of STL algorithms



