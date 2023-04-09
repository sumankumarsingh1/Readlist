# MATH for Programmers 
## Number System
A number system is a system for representing numbers.

The value of any digit in a number can be determined by a digit, it's position in the number, and the base of the number system.

For example:
* 123 with a base 10
* Can also be written as 123<sub>10</sub>
* Following Table will help us interpret the number using  the number position, the base to the power of position

    | Number | Position | Base<sup>position</sup> | Position Value (PV) | Number Value = PV * Number |
    | --- | --- | --- | --- | --- |
    | 3 | 0 | 10<sup>0</sup> | 1 | 10<sup>0</sup> * 3 =3 |
    | 2 | 1 | 10<sup>1</sup> | 10 | 10<sup>1</sup> * 10 = 20 |
    | 1 | 2 | 10<sup>2</sup> | 100 | 10<sup>2</sup> * 1 =100 |

    Total Value= 3 + 20 + 100 = 123

### Number Systems: Base and Symbols used.

| System | Base | Symbols |
| --- | --- | --- |
| Decimal | 10 | 0 to 9 |
| Binary | 2 | 0 to 1 |
| Octal | 8 | 0 to 7 |
| Hexa Decimal | 16 | 0 to 9, A to F |

### Lookup Table for Number Systems 

| Decimal | Binary | Octal | Hexadecimal |
| --- | --- | --- | --- |
| 00 | 0000 | 00 | 0 |
| 01 | 0001 | 01 | 1 |
| 02 | 0010 | 02 | 2 |
| 03 | 0011 | 03 | 3 |
| 04 | 0100 | 04 | 4 |
| 05 | 0101 | 05 | 5 |
| 06 | 0110 | 06 | 6 |
| 07 | 0111 | 07 | 7 |
| 08 | 1000 | 10 | 8 |
| 09 | 1001 | 11 | 9 |
| 10 | 1010 | 12 | A |
| 11 | 1011 | 13 | B |
| 12 | 1100 | 14 | C |
| 13 | 1101 | 15 | D |
| 14 | 1110 | 16 | E |
| 15 | 1111 | 17 | F |


## Converting Decimal to Binary

Convert 548 Binary
First we divide the number by 2 and again continue to divide the quotient again till we get the final quotient as 0.
Then we write down the remainders from bottom to top.

| Number | Divide By | Quotient | Remainder |
| --- | --- | --- | --- |
|  548  |  2  |  274  |  0  |
|  274  |  2  |  137  |  0  |
|  137  |  2  |  68  |  1  |
|  68  |  2  |  34  |  0  |
|  34  |  2  |  17  |  0  |
|  17  |  2  |  8  |  1  |
|  8  |  2  |  4  |  0  |
|  4  |  2  |  2  |  0  |
|  2  |  2  |  1  |  0  |
|  1  |  2  |  0  |  1  |

Binary = <b>1 0 0 0 1 0 0 1 0 0</b>  (write down the remainders from bottom to top)

## Converting Decimal to Octal


## Converting Decimal to Hexadecimal


## Converting Binary to Decimal


## Converting Binary to Octal


## Converting Binary to Hexadecimal

### Math Support in Markdown

When $a \ne 0$, there are two solutions to $(ax^2 + bx + c = 0)$ and they are 
$$ x = {-b \pm \sqrt{b^2-4ac} \over 2a} $$