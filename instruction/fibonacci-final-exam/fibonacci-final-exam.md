# Fibonacci Sequence and Algorithm Final Exam Questions

Here are 10 questions designed to test a university-level student's understanding of the Fibonacci sequence and algorithm, covering its history, applicability, and implementation.

```masteryls
{"id":"f5b573b1-cf5c-4ec0-9b6b-cb00a8be2b13", "title":"Multiple Choice: Fibonacci's Background", "type":"multiple-choice", "body":"Leonardo Pisano, famously known as Fibonacci, is best known for his contributions to which mathematical domain?" }
- [ ] Calculus
- [x] Number Theory
- [ ] Topology
- [ ] Set Theory
```

```masteryls
{"id":"18e13429-7322-49d3-9ec7-bcaf1d189122", "title":"Multiple Choice: Sequence Definition", "type":"multiple-choice", "body":"Which of the following is the correct recursive definition of the Fibonacci sequence, where F(n) represents the nth Fibonacci number?" }
- [ ] F(0) = 0, F(1) = 1, F(n) = F(n+1) + F(n+2) for n > 1
- [ ] F(0) = 1, F(1) = 1, F(n) = F(n-1) - F(n-2) for n > 1
- [x] F(0) = 0, F(1) = 1, F(n) = F(n-1) + F(n-2) for n > 1
- [ ] F(0) = 1, F(1) = 0, F(n) = F(n-1) + F(n-2) for n > 1
```

```masteryls
{"id":"6109cb3e-4cab-450a-b715-51fdcdb66676", "title":"Multiple Choice: Golden Ratio Connection", "type":"multiple-choice", "body":"As 'n' approaches infinity, what value does the ratio F(n+1)/F(n) tend towards in the Fibonacci sequence?" }
- [ ] π (Pi)
- [ ] e (Euler's number)
- [x] φ (The Golden Ratio)
- [ ] √2 (Square root of 2)
```

```masteryls
{"id":"2ad78def-2dfb-456c-89a7-6ef061007871", "title":"Multiple Choice: Application in Nature", "type":"multiple-choice", "body":"The Fibonacci sequence is often observed in the arrangement of petals in flowers. Which of the following petal counts is most likely to be a Fibonacci number?" }
- [ ] 7
- [ ] 11
- [x] 13
- [ ] 17
```

```masteryls
{"id":"6b0b0674-81dc-4c58-9ca1-c091eb5f3784", "title":"Multiple Choice: Iterative Implementation", "type":"multiple-choice", "body":"Which of the following Javascript code snippets correctly implements an *iterative* function to calculate the nth Fibonacci number?" }
- [ ] `function fib(n) { if (n <= 1) return n; return fib(n-1) + fib(n-2); }`
- [x] `function fib(n) { let a = 0, b = 1, temp; for (let i = 2; i <= n; i++) { temp = a + b; a = b; b = temp; } return b; }`
- [ ] `function fib(n) { let arr = [0, 1]; for (let i = 2; i <= n; i++) { arr[i] = arr[i-1] - arr[i-2]; } return arr[n]; }`
- [ ] `function fib(n) { return n <= 1 ? n : fib(n-1) * fib(n-2); }`
```

```masteryls
{"id":"ec257ad9-4555-45b5-82e1-9f9ad6055229", "title":"Essay: Historical Significance", "type":"essay", "body":"Discuss the historical context of Fibonacci's work, particularly his *Liber Abaci*.  How did this book contribute to the adoption of the Hindu-Arabic numeral system in Europe, and what role did the Fibonacci sequence play within it?" }
```

```masteryls
{"id":"9826b07b-93e3-47ff-94aa-ce1ff456c3e6", "title":"Essay: Algorithmic Efficiency", "type":"essay", "body":"Compare and contrast the recursive and iterative approaches to calculating Fibonacci numbers. Analyze the time complexity of each approach. Explain why the iterative approach is generally preferred for larger values of 'n'." }
```

```masteryls
{"id":"8302f3d3-18a0-4552-baca-90ae9911ac34", "title":"Essay: Applications Beyond Nature", "type":"essay", "body":"Beyond its presence in natural phenomena, the Fibonacci sequence and the Golden Ratio have found applications in diverse fields. Discuss at least two applications outside of biology, such as finance, computer science, or art/design, explaining how the sequence or ratio is utilized in each chosen application." }
```

```masteryls
{"id":"f992df5d-f2ab-48ba-920c-2a10b15a9d21", "title":"Essay: Dynamic Programming Optimization", "type":"essay", "body":"Explain how dynamic programming can be used to optimize the recursive Fibonacci calculation. Provide a Javascript code example demonstrating memoization or tabulation to improve efficiency.  Analyze the time complexity improvement achieved through dynamic programming." }
```

```masteryls
{"id":"a614ec00-72e8-40ee-8393-9ce7bbe555f6", "title":"Essay: Fibonacci in Computer Science", "type":"essay", "body":"Describe how the Fibonacci sequence is used in at least one computer science algorithm or data structure.  Explain the algorithm or data structure, and how the Fibonacci sequence contributes to its functionality or efficiency. (e.g., Fibonacci heaps, Fibonacci search, etc.)" }
```