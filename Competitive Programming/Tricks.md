# Logarithm in Constant Time
The following is how logarithm can be calculated in constant space and time:
```cpp
// C++20 onwards
int log2_floor(unsigned long i)
{
	return bit_width(i) - 1;
}

// pre C++20
int log2_floor(unsigned long long i)
{
	return i ? __builtin_clzll(1) - __builtin_clzll(i) : -1;
}
```
# Fast I/O
Add the following lines in the start of `main()`.
`tie()` ensures the flushing of `std::cout` before `std::cin` accepts an input. Useful for interactive console programs which require the console to be updated constantly but also slows down program for large I/O. `NULL` just returns as a null pointer.
```cpp
ios::sync_with_stdio(false);
cin.tie(NULL);
```
Although, to get the fastest I/O for integers, use the following custom functions.
```cpp
// use long long x = fast_input(); to read in x 
inline long long int fast_input(void) 
{ 
    char t; 
    long long int x=0; 
    long long int neg=0; 
    t = getchar(); 
    while ((t<48 || t>57) && t!='-') 
        t = getchar(); 
    if (t == '-') //handle negative input 
    { 
        neg = 1; 
        t = getchar(); 
    } 
    while (t>=48 && t<=57) 
    { 
        x = (x<<3) + (x<<1) + t - 48; 
        // x<<3 means 8*x and x<<1 means 2*x so we 
        // have x = 10*x+(t - 48) 
        t = getchar(); 
    } 
    if (neg) 
        x = -x; 
    return x; 
} 
  
  
// use fast_output(x, 0); to print x and a newline 
// use fast_output(x, 1); to print x and a ' ' after 
// the x 
inline void fast_output(long long int x, int mode) 
{ 
    char a[20]; 
    long long int i=0, j; 
    a[0] = '0'; 
    if (x < 0) 
    { 
        putchar('-'); 
        x = -x; 
    } 
    if (x==0) 
       putchar('0'); 
    while (x) 
    { 
        // convert each digit to character and 
        // store in char array 
        a[i++] = x%10 + 48; 
        x /= 10; 
    } 
    // print each character from the array 
    for (j=i-1; j>=0; j--) 
        putchar(a[j]); 
    if (mode == 0) 
       putchar('\n'); 
    else putchar(' '); 
}
```

# Iterating Strings
When iterating over a string, avoid using `strlen()`, instead use the fact that `'\0'` is equivalent to `false`. When `s[i] == '\0'`, the loop will break.
```cpp
//  Use of strlen() can be avoided by:
for (i=0; s[i]; i++) 
{ }
// loop breaks when the character array ends.
```

# Macro `for` Loop
Reduces readability but improves speed of writing code.
```cpp
#define REP(i, start, end) for (ll i = start; (i) < (ll)(end); (i)++)
```
---
## Check power of 2
Normal technique checks if a number `N` is a power of $2$ or not in $O(\log N)$, however, it can be calculated in $O(v)$ where $v$ is the number of bits in binary representation of `N`.

```cpp
// Function to check if x is power of 2
bool isPowerOfTwo (int x)
{
	// First x in the below expression is for the case when x is 0
	return x && (!(x&(x-1)));
}
```

## Emplace instead of Push
Use emplace instead of push.
```cpp
    // declaring list 
    list<int> list1;
		
    // using emplace_front to insert elements at beginning 
    // inserts 7 at beginning 
    list1.emplace_front(7);

    // using emplace_back to insert elements at the end 
    // inserts 7 at end 
    list1.emplace_back(7);
```
---
### Range Based Loops
Use ranged `for` loops to iterate through an array or a vector when no modification of the value is needed.
```cpp
int main() 
{ 
    // Create a vector object that 
    // contains 5 elements 
    vector<int> vec = {0, 1, 2, 3, 4}; 

    // Type inference by reference using auto. 
    // Range based loops are preferred when no 
    // modification is needed in value 
    for (const auto &value : vec) 
        cout << value << ' '; 

    cout << '\n'; 

    // Basic 5 element integer array 
    int array[]= {1, 2, 3, 4, 5}; 
    for (const auto &value: array) 
        cout << value << " "; 

    return 0; 
}
```

### Most Significant Digit
Number of digits in a number `n` is $\log_{10}n + 1$
```cpp
int MSD(int n)
{
    if(n == 0)
        return 0;
    int k = log10(n);
    int x = pow(10,k);
    int ans = n/x;
    return ans;
}
```
---
#### Initializer List
Used to access the values in an initialization list. The objects of this type are automatically constructed by the compiler from initialization list declarations.
```cpp
template<typename T> 
void printList(initializer_list<T> text) 
{ 
    for (const auto &value: text) 
        cout << value << " "; 
} 

// Driver program 
int main() 
{ 
    // Initialization list 
    printList( {"One", "Two", "Three"} ); 
    return 0; 
}
```

#### Inbuilt GCD
Always use C++ inbuilt GCD function.
```cpp
res = __gcd(num1, num2);
```

#### Newline Character
Use `'\n'` instead of `endl` as it is faster and doesn't force flushing stream which is unnecessary.

#### Global Arrays
Maximum size of array declared locally is $10^6$, but a global array has maximum size of $10^7$.

###### Even Odd
Using `&` is faster than using `%` and can be used to check if a number is even or odd.

```cpp
if (num & 1)
	cout << "ODD";
else
	cout << "EVEN";
```

###### Multiplication and Division by 2
Use bit shifting to achieve fastest integer multiplication and division by 2.
```cpp
// Multiply n with 2
n = n << 1;

// Divide n by 2
n = n >> 1;
```

###### Swapping Numbers
Fastest way to swap numbers is using XOR
```cpp
// A quick way to swap a and b
a ^= b;
b ^= a;
a ^= b;
```
