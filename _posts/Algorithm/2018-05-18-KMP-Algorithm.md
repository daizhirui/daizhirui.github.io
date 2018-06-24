---
layout: default
title: "KMP Algorithm"
author: Zhirui Dai
date: 2018-05-18 00:12:46 +0200
categories: Algorithm
---

KMP Algorithm is another important algorithm for word recognition. With KMP algorithm, we can find all reserved words that the input string is their substring. We can also find out all occurrences of reserved words in the input string.

# Principle

KMP Algorithm also uses `fail edge` but it just build `fail edge` within a reserved string itself. So in order to complete a word recognition task, the input should be compared with every reserved word by this algorithm. Therefore, the complexity is `O(n*k + m)`. Here `n` is the length of the input, `k` is the number of reserved words and `m` is the total length of all the reserved words.

For the detail of how to build `fail edge`, read the [Aho Corasick Algorithm](http://localhost:4000/2018/05/17/Aho-Corasick-Algorithm/).

**Let's take an example that `A = abababaab` is a reserved word and `B = ababc` is the input. Accept these two sequences, we use `i` and `j` to respectively represent the postion we are reading in `A` and the position in `B`.**

We can find that until `i = j = 4`, the match still works well. Now, `A[4] = a` and `B[4] = c`, so **according to the `fail edge` of `state 5` (it corresponds to `A[4]`), we should jump to `A[2]` (`state 3`). So we shall slide `B` 2 (`j - failure[i]`) characters back.**

![](/img/Aho_Corasick_Algorithm_1.png)

```shell
The failure function for abababaab is:
s   |	0|	1|	2|	3|	4|	5|	6|	7|	8|	9|
f(s)|	0|	0|	0|	1|	2|	3|	4|	5|	1|	2|
* s and f(s) is the label of the state in the transition diagram.
```

# Code

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <stdbool.h>

int*
failureFunction(char* str)
{
    if (str == NULL) { perror("The pointer shouldn't be NULL.\n"); }
    int* failure = (int*)malloc(strlen(str) * sizeof(int));
    memset(failure, 0, strlen(str) * sizeof(int));

    int j = 0;  // j represents state number
    failure[0] = 0;
    for (int i = 1; i < strlen(str); ++i) { // i+1 represents state number, state[i]'s input is str[i-1]
        while (j > 0 && str[j] != str[i]) { j = failure[j - 1]; }
        if (str[j] == str[i]) { j++; }
        failure[i] = j;
    }
    return failure;
}

void
KMP_scan(char* dict[], int dict_num, char* input)
{
    if (dict == NULL) { perror("The pointer shouldn't be NULL.\n"); }
    if (input == NULL) { perror("The pointer shouldn't be NULL.\n"); }

    for (int k = 0; k < dict_num; ++k) {
        bool isSubstring = false;
        int* failure = failureFunction(dict[k]);
        int j = 0;
        for (int i = 0; i < strlen(dict[k]); ++i) {
            while (j>0 && input[j] != dict[k][i]) {
                j = failure[j];
                if (j > 0) { j--; } // failure[j] is a state number, should minus 1.
            }
            if (input[j] == dict[k][i]) { j++; }
            if (j == strlen(input)) { isSubstring = true; break; }
        }
        if (isSubstring) {
            printf("\"%s\" is the substring of \"%s\".\n", input, dict[k]);
        } else {
            printf("\"%s\" is not the substring of \"%s\".\n", input, dict[k]);
        }
    }
}

int main() {
    char* dict[] = {"abababaab", "abababbaa"};
    KMP_scan(dict, 2, "ababaa");
    return 0;
}
```

**Output:**

```shell
"ababaa" is the substring of "abababaab".
"ababaa" is not the substring of "abababbaa".
```

# A Problem

The *Fibonacci* strings are defined as follows:

1. s<sub>1</sub> = b
2. s<sub>2</sub> = a
3. s<sub>k</sub> = s<sub>k-1</sub>s<sub>k-2</sub>, for k > 2.

For example, s<sub>3</sub> = ab, s<sub>4</sub> = aba, and s<sub>5</sub> = abaab.

a. What is the length of s<sub>n</sub>?
b. Construct the failure function for s<sub>6</sub>.
c. Construct the failure function for s<sub>7</sub>.
d. Show that the failure function for any s<sub>n</sub> can be expressed by f(1) = f(2) = 0, and for 2\<j<=|s<sub>n</sub>|, f(j) is **j-|s<sub>k-1</sub>|**, where k is the largest integer such that |s<sub>k</sub>|<= j+1.
e. In the KMP algorithm, what is the largest number of consecutive applications of the failure function, when we try to determine whether keyword s<sub>k</sub> appears in text string s<sub>k+1</sub>?

Question a to c are easy. So we just talk about question d and e.

**For question d**, we first think about how to reassemble s<sub>k</sub>.

From s<sub>k</sub> = s<sub>k-1</sub>s<sub>k-2</sub> = s<sub>k-2</sub>s<sub>k-3</sub>s<sub>k-2</sub> = s<sub>k-3</sub>s<sub>k-4</sub>s<sub>k-3</sub>s<sub>k-4</sub>s<sub>k-5</sub>s<sub>k-4</sub> = s<sub>k-2</sub>s<sub>k-2</sub>s<sub>k-5</sub>s<sub>k-4</sub>, we get

**s<sub>k</sub> = s<sub>k-1</sub>s<sub>k-2</sub> = s<sub>k-2</sub>s<sub>k-3</sub>s<sub>k-2</sub> = s<sub>k-2</sub>s<sub>k-2</sub>s<sub>k-5</sub>s<sub>k-4</sub>**

This is showed in the following picture.

![](/img/Fabonacci_string.png)

Now, we will only talk about what if the failure function is invoked in the s<sub>n-2</sub> after s<sub>n-1</sub>.

- If it takes place in the s<sub>n-2</sub> before the s<sub>n-5</sub> but after s<sub>n-1</sub>, it will jump to a position (which has the same letter) in the beginning s<sub>n-2</sub>. So the distance is |s<sub>n-2</sub>|, and f(j) = j - |s<sub>n-2</sub>|. And it is obvious that |s<sub>n-1</sub>| <= j+1.
- If it takes place in the s<sub>n-5</sub> or in the s<sub>n-4</sub> but not the last but one nor the last one, similarly it will jump to a position with the same letter in the beginning s<sub>n-2</sub>. So f(j) is also equal to j - |s<sub>n-2</sub>| and |s<sub>n-1</sub>| <= j+1.
- When it takes place in one of the final two letters, the condition changes. Now |s<sub>n</sub>| <= j+1. Let's consider **s<sub>n</sub> = s<sub>n-2</sub>s<sub>n-3</sub>s<sub>n-2</sub>**. Obviously, it will jump from the second s<sub>n-2</sub> to the first s<sub>n-2</sub>, so the distance is |s<sub>n-2</sub>| + |s<sub>n-3</sub>| = |s<sub>n-1</sub>|. Therefore, f(j) = j - |s<sub>n-1</sub>|.

**For question e**, the maximum amount of applications of failure function is |s<sub>n-2</sub>| - (|s<sub>n-1</sub>| - |s<sub>n-2</sub>|) when we try to determine whether keyword s<sub>k</sub> appears in text string s<sub>k+1</sub>.

But the amount of consecutive applications will be [k/2] - 1. Here are the reasons:

- By checking the final two letters of every item in Fabonacci sequence, we find that the final two letters between the adjacent items in the sequence are always opposite. For example, s<sub>4</sub> = aba, it ends with `ba`. s<sub>5</sub> = abaab, it ends with `ab`. s<sub>6</sub> = abaababa, it ends with `ba`. Suffixes of adjacent items are different.
- Because the suffix of s<sub>k</sub> and s<sub>k+1</sub> is different. Failure function will be applied when it reaches the last but one letter of s<sub>k</sub> and s<sub>k+1</sub>. And because this difference of suffix cannot be fixed, it will jump to the beginning of s<sub>k</sub>.
- To jump to the beginning of s<sub>k</sub>, the path is: s<sub>k</sub> --> s<sub>k-2</sub> --> s<sub>k-4</sub> ... --> s<sub>3</sub> or s<sub>2</sub>.
- Therefore, the largest number of consecutive applications of failure function is [k/2] - 1.

# Reference

[KMP算法详解](http://www.matrix67.com/blog/archives/115)
