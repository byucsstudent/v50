# Fibonacci Exam

The Fibonacci sequence is a fascinating mathematical concept with a rich history and practical applications in computer science and nature. This exam will test your understanding of the Fibonacci sequence, its historical context, implementation in JavaScript, handling unexpected inputs, and the implications of using recursion.

### The Fibonacci Sequence: A Brief History

The Fibonacci sequence is a series of numbers where each number is the sum of the two preceding ones, usually starting with 0 and 1. So, the sequence goes: 0, 1, 1, 2, 3, 5, 8, 13, 21, and so on.

While named after Leonardo Pisano, known as Fibonacci, who introduced the sequence to Western European mathematics in his 1202 book *Liber Abaci*, the sequence was described earlier in Indian mathematics. Fibonacci used the sequence to model the growth of rabbit populations, demonstrating its applicability to real-world scenarios.

### Implementing the Fibonacci Sequence in JavaScript

There are several ways to implement the Fibonacci sequence in JavaScript. Two common approaches are iterative and recursive methods.

#### Iterative Approach

The iterative approach uses a loop to calculate each Fibonacci number based on the previous two. This method is generally more efficient than recursion, especially for larger numbers.

```javascript
function fibonacciIterative(n) {
  if (n <= 1) {
    return n;
  }

  let a = 0;
  let b = 1;
  for (let i = 2; i <= n; i++) {
    let temp = a + b;
    a = b;
    b = temp;
  }
  return b;
}

console.log(fibonacciIterative(10)); // Output: 55
```

This code initializes `a` and `b` to the first two Fibonacci numbers (0 and 1). The loop then iterates from 2 to `n`, calculating the next Fibonacci number by summing `a` and `b`, and updating `a` and `b` accordingly.

#### Recursive Approach

The recursive approach defines the Fibonacci number as the sum of the two preceding Fibonacci numbers, calling the function itself to calculate those values.

```javascript
function fibonacciRecursive(n) {
  if (n <= 1) {
    return n;
  }
  return fibonacciRecursive(n - 1) + fibonacciRecursive(n - 2);
}

console.log(fibonacciRecursive(10)); // Output: 55
```

While elegant and concise, the recursive approach can be significantly less efficient than the iterative approach due to repeated calculations. Each call to `fibonacciRecursive(n)` results in two more calls, leading to exponential time complexity.

### Handling Unexpected Inputs

When implementing the Fibonacci sequence, it's crucial to consider how to handle unexpected inputs, such as negative numbers, non-integer values, or very large numbers.

#### Input Validation

Before calculating the Fibonacci number, it's good practice to validate the input to ensure it is a non-negative integer.

```javascript
function fibonacciValidated(n) {
  if (typeof n !== 'number' || !Number.isInteger(n) || n < 0) {
    return "Invalid input. Please provide a non-negative integer.";
  }
  // ... rest of the fibonacci logic
}

console.log(fibonacciValidated(-1)); // Output: Invalid input. Please provide a non-negative integer.
console.log(fibonacciValidated(3.14)); // Output: Invalid input. Please provide a non-negative integer.
```

This example checks if the input is a number, an integer, and non-negative. If any of these conditions are not met, an error message is returned.

#### Handling Large Numbers

For very large values of `n`, the Fibonacci numbers can quickly exceed the maximum safe integer value in JavaScript.  Libraries like `BigInt` can be used to handle arbitrarily large integers.

```javascript
function fibonacciBigInt(n) {
    if (n <= 1) {
        return BigInt(n);
    }

    let a = BigInt(0);
    let b = BigInt(1);

    for (let i = 2; i <= n; i++) {
        let temp = a + b;
        a = b;
        b = temp;
    }

    return b;
}

console.log(fibonacciBigInt(50)); // Output: 12586269025n
```

### The Impact of Recursion

Recursion, while elegant, comes with performance costs. Each recursive call adds overhead to the call stack.  For the Fibonacci sequence, the recursive approach has exponential time complexity (O(2^n)), making it impractical for larger values of *n*.

The iterative approach, on the other hand, has linear time complexity (O(n)), making it much more efficient for calculating Fibonacci numbers for larger values.  Understanding the trade-offs between recursion and iteration is crucial for writing efficient code.

### Common Challenges and Solutions

*   **Stack Overflow:**  Recursive Fibonacci implementations can lead to stack overflow errors for large values of `n` due to excessive function calls.  The iterative approach avoids this issue.
*   **Performance Bottlenecks:** The inefficiency of the recursive approach can be mitigated using memoization, a technique where the results of expensive function calls are stored and reused when the same inputs occur again.
*   **Input Validation:**  Failing to validate inputs can lead to unexpected behavior or errors.  Always validate inputs to ensure they are within the expected range and data type.
*   **Integer Overflow:** Standard JavaScript numbers have limitations in representing very large integers. Consider using `BigInt` for calculations involving large Fibonacci numbers.

### Summary

The Fibonacci sequence is a fundamental concept in mathematics and computer science. Understanding its history, implementation using both iterative and recursive approaches, handling unexpected inputs, and the implications of recursion are essential for any programmer.  By considering the performance trade-offs and potential challenges, you can write efficient and robust Fibonacci sequence implementations in JavaScript.

---

# Exam

### Question 1: Historical Significance

```masteryls
### Question 1: Historical Significance

```masteryls
{"id":"a7f8c9e1-2b3d-4e5f-6789-0123456789ab","title":"Historical Significance", "type":"multiple-choice", "body":"Who is credited with introducing the Fibonacci sequence to Western European mathematics?" }
- [ ] Isaac Newton
- [x] Leonardo Pisano
- [ ] Pythagoras
- [ ] Euclid
```

### Question 2: Iterative Implementation

```masteryls
{"id":"b2e7d4f3-8c9a-1b2e-5678-9012345678cd","title":"Iterative Implementation", "type":"essay", "body":"Write a JavaScript function called `fibonacciIterative` that takes an integer `n` as input and returns the nth Fibonacci number using an iterative approach.  Include error handling for non-integer or negative inputs. Explain the time complexity of your function." }
```

### Question 3: Recursive Implementation

```masteryls
{"id":"c3f8e5g4-9d0b-2c3f-6789-0123456789de","title":"Recursive Implementation", "type":"multiple-choice", "body":"Which of the following best describes the time complexity of a naive recursive implementation of the Fibonacci sequence?" }
- [ ] O(n)
- [ ] O(log n)
- [x] O(2^n)
- [ ] O(n^2)
```

### Question 4: Handling Invalid Input

```masteryls
{"id":"d4g9f6h5-0e1c-3d4g-7890-123456789ef0","title":"Handling Invalid Input", "type":"essay", "body":"Describe the importance of input validation when implementing the Fibonacci sequence. Provide an example of how you would handle invalid input (e.g., negative numbers, non-integer values) in your JavaScript code." }
```

### Question 5: Recursion vs. Iteration

```masteryls
{"id":"e5h0g7i6-1f2d-4e5h-8901-23456789f012","title":"Recursion vs. Iteration", "type":"multiple-choice", "body":"What is the primary disadvantage of using recursion to calculate Fibonacci numbers compared to using iteration?" }
- [ ] Recursion is more difficult to understand.
- [x] Recursion can lead to stack overflow errors and is generally less efficient.
- [ ] Iteration requires more code.
- [ ] Iteration cannot be used for certain values of n.
```

### Question 6: Memoization

```masteryls
{"id":"f6i1h8j7-2g3e-5f6i-9012-3456789012ab","title":"Memoization", "type":"essay", "body":"Explain the concept of memoization and how it can be used to optimize the recursive Fibonacci implementation. Provide a code example demonstrating memoization in JavaScript." }
```

### Question 7: BigInt

```masteryls
{"id":"g7j2i9k8-3h4f-6g7j-0123-456789abcdef","title":"BigInt", "type":"multiple-choice", "body":"Why might you need to use `BigInt` when calculating Fibonacci numbers?" }
- [ ] To handle negative numbers.
- [x] To handle numbers larger than the maximum safe integer in JavaScript.
- [ ] To improve the performance of the iterative approach.
- [ ] `BigInt` is not needed for Fibonacci calculations.
```

### Question 8: Real-World Applications

```masteryls
{"id":"h8k3j0l9-4i5g-7h8k-1234-56789bcdef01","title":"Real-World Applications", "type":"essay", "body":"Describe at least one real-world application of the Fibonacci sequence or the golden ratio, which is closely related to the Fibonacci sequence. Explain how it is applied in that context." }
```

### Question 9: Code Debugging

```masteryls
{"id":"i9l4k1m0-5j6h-8i9l-2345-6789cdef0123","title":"Code Debugging", "type":"multiple-choice", "body":"Consider the following Javascript code snippet. What is the issue, and how would you fix it? \n\n```javascript\nfunction fib(n) {\n  if (n <= 1) {\n    return 1;\n  }\n  return fib(n - 1) + fib(n - 1);\n}\n```" }
- [ ] The base case is incorrect; it should return 0 for n = 0.
- [x] The function calls the fib function with `n-1` twice, and should call it with `n-1` and `n-2`.
- [ ] There is no issue; the code calculates the Fibonacci sequence correctly.
- [ ] The function will cause a stack overflow error for small values of `n`.
```

### Question 10: Algorithmic Efficiency

```masteryls
{"id":"j0m5l2n1-6k7i-9j0m-3456-789def012345","title":"Algorithmic Efficiency", "type":"essay", "body":"You are tasked with calculating the 100th Fibonacci number. Which approach – iterative, recursive, or memoized recursive – would you choose and why?  Explain your reasoning in terms of time and space complexity." }
```
