---
layout: post
title:  User-defined Hash Functor in C++
date:   2017-08-12 23:44:52 -0500
categories: Programming Notes
tags: [C++,]
comments: true
---

This is the first post of my website, I am currently working on some leetcode
questions and most answers are written in C++. I feel that I tend to forget
some usages related to user-defined classes, so I decide to write them down. If
there are mistakes, feel free to point them out. Thanks.


The `unordered_map` or `unordered_set` in the C++ STL are useful when someone
would like to cache some computed results and reuse them later. If the template
class type is one of existing class under the std namespace or a primitive type,
a hash function for the object or the variable has been defined. One can
directly specify the class type in the template.


If the class type is a user-defined class or a container (e.g. vector), one has
to define the hash functor and specify it in the second template variable.

A simple declaration of unordered_set is

```cpp
std::unordered_set<std::string> my_set;
```

A hash function has been defined in string class. No additional codes are needed
to use the my_set container.

However, if the declaration is like this:

```cpp
std::unordered_set<std::pair<string, string>> my_set;
```

There will be an error message when one compiles the code. The compiler does not
know how to get the hash value of each key. A user-defined functor like the
following must be included.

```cpp
struct HashFunctor {
    std::size_t operator()(const pair<string, string> &pair) const {
        std::hash<std::string> hash_fn;
    	  return static_cast<std::size_t>(hash_fn(pair.first))
                * static_cast<std::size_t>(hash_fn(pair.second));
    }
};
std::unordered_set<std::pair<string, string>, HashFunctor> my_set;
```

The Hash functor override the `operator()` function so that the compiler can
adopt the definition and determine hash values of keys. I use the
`std::hash<typename>` here to retrieve the hash functor in string class and
use it to construct the hash functor of a `std::pair<string, string>` object.
