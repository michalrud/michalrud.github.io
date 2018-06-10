---
title:  std::variant
description: Quick introduction into std::variant, that will be available in C++17 onwards.
---

Strongly typed languages provide programmer an awesome ability to find certain bugs quicker by raising compilation errors when the program attempts to use certain memory in an improper way - like treating strings as numbers, and so on. In general it's a great thing, as it's possible to find issues before the program is even executed.

# The Issue

Let's take a look at the following piece of JSON:

```json
hello: [
    1337,
    "hi",
    [1, 2, "oh noes"]
]
```

How can we store such input in C++? Type of the value stored must be known at compilation time, so out job is a little bit harder.

# Solutions

## Store everything as a string

The one that we all saw at some point of our life.

```cpp
std::string value;
```

Storing everything as string *kinda works*, but it's far from optimal, both when it comes to space occupied by such structures, and by the need to parse them each time we'd need to get anything that is not a `std::string`. And this is just a tip of an iceberg, if we would start considering any potential problems (how to determine a type of the value?).

We don't like this solution, so let's move on.

## Store everything in a class that contains everything

Quite obvious idea that comes to mind is to create a simple class that would hold all of those, but know which one is actually used:

```cpp
class MyData {
    enum Type {NUM, STR, VEC};
public:
    MyData(int i_) : i(i_), type(NUM) {}
    MyData(const std::string& s_) : s(s_), type(STR) {}
    MyData(const std::vector<MyData>& v_) :
        v(v_), type(VEC) {}
    int& getInt() {assert(NUM == type); return i;};
    std::string& getString()
        {assert(STR == type); return s;};
    std::vector<MyData>& getVec()
        {assert(VEC == type); return v;};
private:
    Type type;
    int i;
    std::string s;
    std::vector<MyData> l;
};
```

That's a lot of writing, and a lot of potential problems. Also, there's an overhead - every object stores three members, while only one can be used at given time.

## Union

This is the one that looks like `struct`, but instead of storing all of its members, it can store only one of them.

```cpp
union A {
    int i;
    std::string s;  // just in C++11 onwards!
    std::vector<A> v; // just in C++11 onwards!
    // this won't compile, as it needs a destructor
};
```

`union`s are very powerful constructs, but one needs to be careful while using them:
 * They provide little type safety - if you'd initialize one of union's members, reading any other works just like `reinterpret_cast<>`.
 That can be actually pretty useful - imagine putting a `char` as one member, and a [bit field](https://en.wikipedia.org/wiki/Bit_field) as another - modifying one would automatically "update" the other (as they are all one thing underneath) for free. Nice for low-level binary structures!
 * Until C++11 only POD types could be stored in `union`s,
 * And while after C++11 you can store your beloved `std::string`s in there, you need to remember that it is unknown at compilation time what type will be stored in an `union`, so it is unknown which destructor to call, so *none will be called automatically*.
 * Of course compiler will not call constructors for you as well - want to use a copy constructor on one of union's members? Prepare yourself for segfaults, as this member is not initialized (or initialized by some other type). You need to *manually* initialize memory using a placement-new.
 * Implementing a copy constructor? Remember to manually destroy what's already in your union!

So, in C++11 if we would want to care about freeing the memory and type safety we would need to do something like this:

```cpp
struct A {
    enum{NUM, STR, VEC} type;
    A(int i_) : type(NUM), i(i_) {}
    A(const std::string& s_) : type(STR) {
        new((void*)&s) std::string(s_);
    }
    A(const std::vector<A>& v_) : type(VEC) {
        new((void*)&v) std::vector<A>(v_);
    }
    A(const A& a) : type(a.type) {
        switch(type) {
            case NUM:
                i = a.i;
                break;
            case STR:
                new((void*)&s) std::string(a.s);
                break;
            case VEC:
                new((void*)&v) std::vector<A>(a.v);
                break;
        }
    }
    A& operator=(const A& a) {
        switch(type) {
            case STR:
                s.~basic_string<char>();
                break;
            case VEC:
                v.~vector<A>();
                break;
            case NUM:
                break;
        }
        type = a.type;
        switch(type) {
            case NUM:
                i = a.i;
                break;
            case STR:
                new((void*)&s) std::string(a.s);
                break;
            case VEC:
                new((void*)&v) std::vector<A>(a.v);
                break;
        }
        return *this;
    }
    ~A() {
        switch(type) {
            case STR:
                s.~basic_string<char>();
                break;
            case VEC:
                v.~vector<A>();
                break;
            case NUM:
                break;
        }
    }
    int& getInt() {assert(NUM == type); return i;};
    std::string& getString()
        {assert(STR == type); return s;};
    std::vector<A>& getVec()
        {assert(VEC == type); return v;};
private:
    union {
        int i;
        std::string s;
        std::vector<A> v;
    };
};
```

That's *far too much* work that I'm willing to do here, and I'm still pretty sure that anyone who knows C++ better than me would be able to find more issues with the code than I've found. Is there an easier way?

## std::variant

C++17 gives us a nice, type-safe alternative to union that will take care of calling the destructor automatically, while forbidding us from getting the wrong type by accident (so, no bit field tricks for us here).

```cpp
std::variant<int, std::string> v;
```

This one will properly free memory after it goes out of scope, and will throw an exception when program would try to obtain value of type, which is not actually stored there. But while `get<T>()` and `get_if<T>()` are obvious, the real gem is `std::visit`, which lets the programmer to use a [visitor pattern](https://en.wikipedia.org/wiki/Visitor_pattern) to obtain values without the need for any checking, like this:

```cpp
struct MyVisitor {
    std::string operator()(const int input) const {
        return std::to_string(input);
    }
    std::string operator()(const std::string& input) const {
        return input;
    }
};

void print(const std::variant<int, std::string>& v) {
    std::cout << std::visitor(MyVisitor(), v) << std::endl;
}
```

### Multiple values of the same type

`std::variant` has no problem accepting the same type multiple times:

```cpp
std::variant<int, int> v;
```

How to get the value we would need? Turns out we can not only call `get()` with the type we would like to get, but also with the index of the type we would like to obtain. Not only that, but thanks to `std::in_place_index` we can tell which element should be initialized:

```cpp
std::variant<int, int> v2(std::in_place_index<0>, 12);
int i = std::get<0>(v1);
int i = std::get<1>(v1); // would throw

std::variant<int, int> v2(std::in_place_index<1>, 123);
int i = std::get<0>(v2); // would throw
int i = std::get<1>(v2);

 // would prevent compilation, as it is ambiguous
std::get<int>(v);
```

This should be rather rarely needed, but can be useful in case of ultra generic code, where two seemingly different types boil down to the same type.

### Empty variants are not allowed

`std::variant` cannot be created with `void` as one of its types. Therefore [`std::monostate`](http://en.cppreference.com/w/cpp/utility/variant/monostate) has been introduced - a type which can have only one value, so can be nicely used to represent an empty state.

### But somehow variants can be empty

What if the value cannot be obtained when it is being set? If the old value has already been destroyed, and the new one cannot be set (for example, its constructor throws an exception), then variant becomes *valueless by exception*. That means:
* `valueless_by_exception()` method returns `true`,
* any attempt to get a value would result in `std::bad_variant_access` being thrown.

### Recursive variants

The example for `union` was overly complicated, as the union was *recursive* - it contained an argument which type also contained such union. But, since all memory management issues are solved for us automatically, the only thing that we need to do is to wrap our variant in a structure and have a pointer to its incomplete type as one of variant's types (as variant itself cannot accept incomplete types):

```cpp
struct RecursiveVariant;

struct RecursiveVariant {
    using Value = std::variant<int,
        std::string,
        std::unique_ptr<RecursiveVariant> >;
    Value value;
};
```

### Boost.Variant

It's worth noting that `std::variant` is essentially a nicer version of the much older [Boost.Variant](https://www.boost.org/doc/libs/1_67_0/doc/html/variant.html), so if you cannot use `std::variant`, you can still try to get Boost and use a variant from there - they're pretty similar. The biggest difference is how do they behave when they cannot get a value:
 * Boost temporarily saves a copy of the old value, so it will be rolled back when the new one cannot be set - so Boost.Variant will never be "unset")
 * Boost.Variant also allows for more straightforward [recursive usage](https://www.boost.org/doc/libs/1_67_0/doc/html/variant/tutorial.html#variant.tutorial.recursive) than std::variant. I'm not really sure if it's *easier*, though.

# See also

* [David Sankel - Variants: Past, Present, and Future (from CppCon 2016)](https://www.youtube.com/watch?v=k3O4EKX4z1c)
* [cppreference on std::variant](http://en.cppreference.com/w/cpp/utility/variant)