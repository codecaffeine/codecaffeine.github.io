---
layout: post
title: "Writing a “String to Number” Function in C"
date: 2011-12-17 22:57
comments: true
categories: C
---
Years ago I was in a job interview where the employer took me to a whiteboard and asked me to implement `atoi()` in C. They also asked me to talk about any issues with my implementation. While there is hardly any reason to implement `atoi()` yourself nowadays, sometimes it's good practice to try it anyway.

<!--more-->

## C Standard Library interface ##
First lets looks at the function definition:
``` bash
$ man atoi
atoi -- convert ASCII string to integer
int atoi(const char *str);
```

The C standard library method `atoi()` takes in a string (character array) and returns a number. You'll actually notice that the man page deprecated `atoi()` in favor of `strtol()` which uses long instead of int. Our implementation will use a long instead of an int as well:

``` c
long cc_stringToNumber(const char *string);
```

## Number Theory ##
Before we start digging into code, what do we actually mean when we write out a number? Well according to Wikipedia's [entry on numbers](http://en.wikipedia.org/wiki/Number):

{% blockquote %}
In this base ten system, the rightmost digit of a natural number has a place value of one, and every other digit has a place value ten times that of the place value of the digit to its right.
{% endblockquote %}

Lets look at the numbers 7, 42, and 365 to see how this would work:

**7** = 7×1 = 7×10<sup>0</sup>
    
**42** = 4×10 + 2×1 = 4×10<sup>1</sup> + 2×10<sup>0</sup>
    
**365** = 3×100 + 6×10 + 5×1 = 3×10<sup>2</sup> + 6×10<sup>1</sup> + 5×10<sup>0</sup>

### General Rule ###
Each character in the number string can be represented by ** *n*×10<sup>*p*</sup>** where ***n*** is the **numerical value** of the character and ***p*** is the **place value** of the character decreasing from *stringLength-1* to *0*

#### Getting the Numerical Value for a Character ####
According to the [ASCII Table](http://en.wikipedia.org/wiki/ASCII), numbers 0–9 have the values 48–57 respectively. We can get the numerical position by taking the ASCII value and subtracting 48.

#### Getting the Place Value of the Character ####
The place value is interesting to get because while most of our counting increases from left to right, the place value increases from right to left. We can figure out the place value by finding the string length using the function `strlen()`

``` bash
$ man strlen
strlen -- find length of string
size_t strlen(const char *s);
```

Once we know the string's length, we can calculate the place value by subtracting the character's position from left to right from the string's length. We will also need to subtract 1 because the place value is 0 based—the right most place value is 10<sup>**0**</sup>.

## First Attempt ##
``` objc
long cc_stringToNumber(const char *string) {
    long returnValue = 0;
    long stringLength = strlen(string);
    for (unsigned long position = 0; position < stringLength; position++) {
        unsigned int characterValue = string[position] - 48;
        long exponent = (stringLength - position) - 1;
        long exponentValue = pow(10, exponent);
        returnValue += characterValue * exponentValue;
    }
    return returnValue;
}
```

Lets see how it works:
<div class="table-figure">
<table>
  <tr>
    <td><code>cc_stringToNumber("1234")</code></td>
	<td><strong>1234</strong></td>
    <td style="color:green">Correct!</td>
  </tr>
  <tr>
    <td><code>cc_stringToNumber("0")</code></td>
	<td><strong>0</strong></td>
    <td style="color:green">Correct!</td>
  </tr>
  <tr>
    <td><code>cc_stringToNumber("Foo")</code></td>
	<td><strong>2893</strong></td>
    <td style="color:Red;">Wrong!</td>
  </tr>
  <tr>
    <td><code>cc_stringToNumber("-42")</code></td>
	<td><strong>-258</strong></td>
    <td style="color:Red;">Wrong!</td>
  </tr>
</table>
</div>


There are 2 issues here. The first is that we're taking the integer value of each character wether they are a number or not. 'F' has an ASCII value of 70 which means we calculate it's value to be 70-48 or 22. 'o' has an ASCII value of 111 so we calculate it's value to be 63. This gives us the overall value: (22 * 10^2) + (63 * 10^1) + (63 * 10^0) = 2893. We need a solution: If we encounter any character < '0' or > '9', we need to return 0 for the value.

## Second Attempt ##
``` objc
long cc_stringToNumber(const char *string) {
    long returnValue = 0;
    unsigned long stringLength = strlen(string);
    for (unsigned long position = 0; position < stringLength; position++) {
        char currentCharacter = string[position];
        if (currentCharacter < '0' || currentCharacter > '9') {
            returnValue = 0;
            break;
        } else {
            unsigned int characterValue = string[position] - 48;
            long exponent = (stringLength - position) - 1;
            long exponentValue = pow(10, exponent);
            returnValue += characterValue * exponentValue;
        }
    }
    return returnValue;
}
```

Lets see how it works:
<div class="table-figure">
<table>
  <tr>
    <td><code>cc_stringToNumber("1234")</code></td>
	<td><strong>1234</strong></td>
    <td style="color:green">Correct!</td>
  </tr>
  <tr>
    <td><code>cc_stringToNumber("0")</code></td>
	<td><strong>0</strong></td>
    <td style="color:green">Correct!</td>
  </tr>
  <tr>
    <td><code>cc_stringToNumber("Foo")</code></td>
	<td><strong>0</strong></td>
    <td style="color:green">Correct!</td>
  </tr>
  <tr>
    <td><code>cc_stringToNumber("-42")</code></td>
	<td><strong>0</strong></td>
    <td style="color:Red;">Wrong!</td>
  </tr>
</table>
</div>

Well, this is an improvement, but we're not handling negative numbers correctly. Negative numbers are hard for a couple of reason. The `-` sign will move the character positions off by one. The minus sign any other place in a number is also not valid numerical number. It either has to be at the beginning or not there at all.

Our solution is to look at the first character: If it's not a minus sign, we'll run the function as normal. If it is, we'll run the function with a subset of the data

## Final Attempt ##
``` objc
long cc_stringToNumber(const char *string) {
    long returnValue = 0;
    unsigned long stringLength = strlen(string);
    if ((stringLength > 1) && (*string == '-')) {
        returnValue = 0 - cc_positiveStringToNumber(++string);
    } else {
        returnValue = cc_positiveStringToNumber(string);
    }
    return returnValue;
}

long cc_positiveStringToNumber(const char *string) {
    long returnValue = 0;
    unsigned long stringLength = strlen(string);
    for (unsigned long position = 0; position < stringLength; position++) {
        char currentCharacter = string[position];
        if (currentCharacter < '0' || currentCharacter > '9') {
            returnValue = 0;
            break;
        } else {
            unsigned int characterValue = string[position] - 48;
            long exponent = (stringLength - position) - 1;
            long exponentValue = pow(10, exponent);
            returnValue += characterValue * exponentValue;
        }
    }
    return returnValue;
}
```

Lets see how it works:
<div class="table-figure">
<table>
  <tr>
    <td><code>cc_stringToNumber("1234")</code></td>
	<td><strong>1234</strong></td>
    <td style="color:green">Correct!</td>
  </tr>
  <tr>
    <td><code>cc_stringToNumber("0")</code></td>
	<td><strong>0</strong></td>
    <td style="color:green">Correct!</td>
  </tr>
  <tr>
    <td><code>cc_stringToNumber("Foo")</code></td>
	<td><strong>0</strong></td>
    <td style="color:green">Correct!</td>
  </tr>
  <tr>
    <td><code>cc_stringToNumber("-42")</code></td>
	<td><strong>-42</strong></td>
    <td style="color:green">Correct!</td>
  </tr>
</table>
</div>


You'll see I split up the function into two different methods. `cc_positiveStringToNumber()` assumes that the string is always positive and has our current logic. `cc_stringToNumber()` has the logic to handle the negative sign. If it encounters a negative sign as the first character (and has at least one more character after it), then it performs `cc_positiveStringToNumber()` with the minus sign stripped out (++string moves the string pointer forward a character).

This also handles just a minus sign and an empty string.

## In Conclusion ##
While it may be fun and educational to implement a standard function, it's worth noting that our method has a few limitations the C Standard Library functions don't have. We don't support the "+" sign for positive number (though this is an easy feature to add). More importantly, `strtol()` supports different bases like octal and hexadecimal. Finally, keep in mind that c standard library functions have been tested and optimized for years.
