# Effective Modern C++ - Part 1 : Deducing Types 

## decltype with known types

Whereas auto works on types, and decltype works on expressions.

decltype can be used in many cases auto can be used, but doesn't require the variable to be initialzied yet.

Example useage

```
#include <iostream>
#include <cxxabi.h> // the libstdc++ used by g++ does contain this header

#include <type_traits>
#define typeof(x) std::remove_reference<decltype((x))>::type

using namespace std;


template <typename type>
void print(const type *addr) // you wanted a pointer
{
  char * name = abi::__cxa_demangle(typeid(*addr).name(), 0, 0, NULL);
  printf("type is: %s\n", name);
  delete name;
}

int main()
{
    decltype(100) x = 90;      // narrowest type that evaluates to 100 (which is int)
    print(new typeof( x ));    // type is: int 
    cout<<"x: " << ++x;        // x: 91

    return 0;
}
```


## decltype and unknown types

Only the compiler knows the types of auto functions.  Check it out!
```
#include <iostream>
#include <typeinfo>

int main ()
{

  auto fn  = [] {};
  auto mo = []{ int x = 1; };
  decltype(mo) mo2 = mo;
  
  std::cout<<typeid( *fn ).name() << std::endl; // FvvE
  std::cout<<typeid( *([]{}) ).name() << std::endl; // FvvE
  
  std::cout<<typeid( mo ).name() << std::endl; // Z4mainEUlvE0_
  std::cout<<typeid( *mo ).name() << std::endl; // FvvE
  std::cout<<typeid( mo2 ).name() << std::endl; // Z4mainEUlvE0_
  std::cout<<typeid( *mo2 ).name() << std::endl; // FvvE

}

```

## declytype for lambda returns

Based on templates, types T and U below might implement return values in a way we might not know generically
In this case decltype and determine our return value for us!  (Fixed point example)
```

template<typename T, typename U>
auto add(T t, U u) -> decltype(t + u) // return type depends on template parameters
                                      // return type can be deduced since C++14
{
    return t+u;
}
```
## plus sign 

auto test = +[]{}; is therefore deduced to void(*)()

## std::function vs auto closures

