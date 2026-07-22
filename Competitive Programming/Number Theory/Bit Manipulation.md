# Binary Number
A binary number is a number expressed in the base-$2$ numeral system.
We say that a certain bit is **set** if it is $1$ and **cleared** if it is $0$.
The binary number $(a_ka_{k-1}\dots a_1a_0)_2$ represents the number:
$$(a_ka_{k-1}\dots a_1a_0)_2 = 2^ka_k+2^{k-1}a_{k-1}\cdots2^1a_1+2^0a_0$$
CPU's are very fast manipulating bits with specific operations.
```cpp
unsigned int unigned_number = 13;
assert(unsigned_number == 0b1101);

int positive_signed_number = 13; // 32-bit
assert(positive_signed_number == 0b1101);

short negative_signed_number = -13; // 16-bit
assert(negative_signed_number == 0b1111'1111'1111'0011); // Two's Complement
```
### Bitwise Operators
All these operators are instant on a CPU for fixed-length integers.
- `&` : The bitwise AND operator.
- `|` : The bitwise inclusive OR operator.
- `^` : The bitwise exclusive OR (XOR) operator.
- `~` : The bitwise complement (NOT) operator.
### Shift Operators
- `>>` : `a >> b` shifts the binary representation of $a$ by $b$ bits to the right. Represents integer division by $2^b$.
- `<<` : `a << b` shifts the binary representation of $a$ by $b$ bits to the left by appending zero's. Represents multiplication by $2^b$. Because of fixed-length integer, if we shift left too much, the bits start dropping and we will end up with $0$.
## Tricks
##### Set/Flip/Clear/Check a Bit
- `1 << x` is a number with only the $x^{\text{th}}$ bit set.
- `~(1 << x)` is a number with all the bits set except the $x^{\text{th}}$ bit.
- `n | (1 << x)` sets the $x^{\text{th}}$ bit in the number $n$.
- `n ^ (1 << x)` flips the $x^{\text{th}}$ bit in the number $n$.
- `n & ~(1 << x)` clears the $x^{\text{th}}$ bit in the number $n$.
- `(n >> x) & 1` checks if the $x^{\text{th}}$ bit is set in the number $n$
##### Check if Number Divisible by $2^k$
```cpp
bool isDivisibleByPowerOf2(int n, int k)
{
	int power_of_2 = 1 << k;
	return !(n & (power_of_2 - 1));
}
```
#### Brian Kernighan's Algorithm
Using this, we can count the number of bits set.
The idea is to consider only the set bits of an integer by turning off it's rightmost set bit (after counting it), so the next iteration of the loop considers the next rightmost bit.
```cpp
int countSetBits(int n)
{
	int count = 0;
	while (n)
	{
		n &= n - 1; // clears the rightmost set bit
		count++;
	}
	return count;
}
```
### Standard Library
The following operations are supported since C++20:
- `has_single_bit` : checks if the number is a power of $2$.
- `bit_ciel` / `bit_floor` : round up/down to the closest power of $2$.
- `rotl` / `rotr` : rotate the bits in the number.
- `countl_zero` / `countr_zero` / `countl_one` / `countr_one` : count the leading/trailing zero's/one's.
- `popcount` : count the number of set bits.