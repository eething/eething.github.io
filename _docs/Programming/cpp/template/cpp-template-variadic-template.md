---
title: "C++ 템플릿 - 가변인자 템플릿"
date: 2019-05-20 15:30:00 +0900
tags:
  - programming
  - cpp
  - template
---


Variadic Template
---
* C++11
{% highlight cpp %}
template<typename ... Args>
class Test {};

void bar( int n, doubld d, const char* s)
{
    cout << n << d << s << endl;
}

template<typename ... Args>
void foo( Args ... args )
{
    //args : Parameter Pack

    cout << sizeof...(Args) << endl;
    cout << sizeof...(args) << endl;

    bar(args...); // pack expansion
}

int main()
{
    Test<> t0;
    Test<int> t1;
    Test<int, char> t2;

    foo(1, 3.14, "str");
}
{% endhighlight %}

* Pack Expansion
{% highlight cpp %}
int sq(int x)
{
    return x * x;
}
void print(int x)
{
    cout << x << ", ";
}

template<typename ... Args> void bar(Args ... args)
{}

template<typename ... Args> void foo(Args ... args)
{
    int x1[] = { args... };     // { 1, 2, 3 }
    int x2[] = { (++args)... }; // { 2, 3, 4 }

    bar( sq(args...) ); // error bar( sq(1, 2, 3) );
    bar( sq(args)... ); // bar( sq(1), sq(2), sq(3) );

    sq(args);    // error
    sq(args...); // error sq(1, 2, 3);
    sq(args)...; // error sq(1), sq(2), sq(3);
    int x3[] = { sq(args)... }; // error on foo();
    int x4[] = { 0, sq(args)... }; // error on foo();
    int x5[] = { 0, print(args)... }; // error
    int x6[] = { 0, (print(args), 0)... };
            // { 0, (print(1), 0), (print(2), 0), };
            // { 0, 0, 0, 0 };
    initializer_list<int> e = { (print(args), 0)... };

    tuple<Args...> t1;
    tuple<pair<Args...>> t2;
    tuple<pair<Args>...> t3; // error
                             // tuple< pair<A1>, pair<A2> >
    tuple<pair<int, Args>...> t4;

    pair<Args...> p1;
    pair<tuple<Args...>> p2; // error
                             // pair< tuple<A1,A2> >
    pair<tuple<Args>...> p3;
}

int main()
{
    foo(1, 2, 3);
    foo();
}
{% endhighlight %}


* recursive
{% highlight cpp %}
void foo() {}

template<typename T, typename ... Args>
void foo(T t, Args ... args)
{
    static int n = 0; // 함수가 여러개!!!
    cout << t << endl;
    foo(args...);
}

int main()
{
    foo(1, 3.14, "str");
}
{% endhighlight %}



Fold Expression
----
* C++17
* unary right fold
* unary left fold
* binary right fold
* binary left fold

{% highlight cpp %}
template<typename ... Args> void foo(Args ... args)
{
    // fold expression C++17
    int n1 = (args + ...);     // 1 + (2 + (3 + 4))
    int n2 = (... + args);     // ((1 + 2) + 3) + 4
    int n3 = (args + ... + 9); // 1 + (2 + (3 + (4 + 9)))
    int n4 = (9 + ... + args); // (((9 + 1) + 2) + 3) + 4

    (cout << ... << args);     //  (((cout << 1) << 2 ) << 3) << 4

    vector<int> v;
    (v.push_back(args), ...);  //  v.pb(1), (v.pb(2), (v.pb(3), v.pb(4)))
}

int main()
{
    int n = foo(1, 2, 3, 4);
}
{% endhighlight %}

* 함수 파라미터 타입, 리턴 타입
{% highlight cpp %}
void bar0() {}
int bar1( int ) { return 0; }
char bar2( int, char ) { return 0; }

/*
template<typename T> struct result_type
{
    //typedef T type;
    static_assert( is_function<T>::value, "error" );
};
*/
template<typename T> struct result_type;

template<typename R, typename ... Args>
struct result_type<R( Args... )>
{
    typedef R type;
};

template<size_t N, typename T> struct argument_type;

template<size_t N, typename R, typename A, typename ... Args>
struct argument_type<N, R( A, Args... )>
{
    typedef typename argument_type<N-1, R( Args... )>::type type;
};

template<size_t N, typename R>
struct argument_type<N, R()>
{
    typedef void type;
};

template<typename R, typename A, typename ... Args>
struct argument_type<0, R( A, Args... )>
{
    typedef A type;
};

template<typename T> void foo( T& t )
{
    cout << typeid(typename result_type<T>::type).name() << "(";
    cout << typeid(typename argument_type<0, T>::type).name() << ", ";
    cout << typeid(typename argument_type<1, T>::type).name() << ", ";
    cout << typeid(typename argument_type<2, T>::type).name() << ")";
    cout << endl;
}

int main()
{
    foo( bar0 );
    foo( bar1 );
    foo( bar2 );
}
{% endhighlight %}


* tuple
{% highlight cpp %}
// tuple
template<typename ... Types> struct xtuple
{
    static constexpr int N = 0;
};

template<typename T, typename ... Types>
struct xtuple<T, Types...> : public xtuple<Types...>
{
    static constexpr int N = xtuple<Types...>::N + 1;

    T value;

    xtuple() = default;
    xtuple(const T& v, const Types& ... args)
        : value(v), xtuple<Types...>(args...)
    {}
};
// tuple_element
template<size_t N, typename TP> struct xtuple_element;
template<typename T, typename ... Types>
struct xtuple_element<0, xtuple<T, Types...>>
{
    typedef T type;
    typedef xtuple<T, Types...> tupleType;
};
template<size_t N, typename T, typename ... Types>
struct xtuple_element<N, xtuple<T, Types...>>
{
    typedef typename xtuple_element<N-1, xtuple<Types...>>::type type;
    typedef typename xtuple_element<N-1, xtuple<Types...>>::tupleType tupleType;
};
// xget
template<size_t N, typename TP>
typename xtuple_element<N, TP>::type& xget(TP& tp)
{
/*
    static_cast<Base>(tp).value = 0; // error, rvalue
    static_cast<Base&>(tp).value = 0;// ok, lvalue

    typedef typename xtuple_element<N, TP>::tupleType Base1;
    using Base2 = typename xtuple_element<N, TP>::tupleType;
    return tp.Base::value;
*/
    return static_cast<typename xtuple_element<N, TP>::tupleType&>(tp).value;
}
// print_tuple
template<size_t ... N> struct xindex_sequence {};
template<typename TP, size_t ... S>
void print_tuple_imp(const TP& tp, const xindex_sequence<S...>&)
{
    int x[] = { xget<S>(tp)... };

    for(auto& n : x)
        cout << n << endl;
}
template<typename TP>
void print_tuple(const TP& tp)
{
    print_tuple_imp( tp, make_index_sequence<tuple_size<TP>::value>() );
}
int main()
{
    xtuple<> t0;
    xtuple<int, double, const char*> t3(1, 3.14, "str");
    xtuple<int, int, int, int> t4(1, 2, 3, 4);

    xget<0>(t3) = 10;
    //print_tuple_imp( t4, xindex_sequence<0, 1, 2, 3>() );
    print_tuple( t4 );
}
{% endhighlight %}
