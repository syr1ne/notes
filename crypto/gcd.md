# greatest common divisor (GCD)

also commonly known as highest common factor (HCF). is the largest number which divides two positive integers.

as the name suggests, the greatest common divisor number of 2 positive integer value.


for example:

`GCD(12, 8)`

what are the number that fully divides both the numbers and gives remainder 1. the greatest one of them is the GCD of these numbers.

```
12					8
-- = 6  |   - = 4
 2					2

12					8
-- = 3  |   -	= 2
 4					4
```

we can see 2 and 4 are the two common divisors we discovered of 12 and 8.

the greater one in both of them is 4, 

so `GCD(12, 8) = 4`

### few things to understand

if we have GCD(a, b)

where, a and b are prime, then the answer will always be `GCD(a, b) = 1`

where, `GCD(a, b) = 1` then a and b are coprimes.

where, a is prime and b < a, then a and b are coprime.

where, a is prime and b > a, then a and b are **not necessarily** coprime.


there are multiple ways to calculate GCD, one is **Euclid's Algorithm**


