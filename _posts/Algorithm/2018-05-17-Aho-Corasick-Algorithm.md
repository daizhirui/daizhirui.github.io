---
layout: default
title: Aho Corasick Algorithm
author: Zhirui Dai
date: 2018-05-17 11:13:16 +0200
categories: Algorithm
---

An algorithm for recognizing reserved words from input.

# Introduction

Given an array of reserved words, **Attr[]**, Aho Corasick Algorithm can find all occurrences of these words from the input. Like this:

```shell
The array of reserved words is:
"he"	"she"	"his"	"hers"
Die gescannte Zeichenfolge ist:
0	1	2	3	4	5	6	7	8	9	10	11	12
s	h	e	a	n	d	h	e	r	s	h	i	s
Sucht "she" von 0 bis 2.
Sucht "he" von 6 bis 7.
Sucht "hers" von 6 bis 9.
Sucht "his" von 10 bis 12.
```

There is another algorithm called **KMP** doing the same thing. Assuming the length of input is n, the complexity of KMP is `O(n + length(Attr[0])) + O(n + length(Attr[1])) + ... + O(n + length(Attr[k-1]))`, which is `O(n*k + m)`, m is the sum of all `length(Attr[])`. However, the complexity of **Aho Corasick Algorithm** is `O(n + m + z)`, 'z' is the amount of occurences of reserved words in the input.

# How to implement the algorithm

Aho Corasick Algorithm uses **a special transition diagram called `trie`**, which introduces **`fail edge`**.

`fail edge` is a pointer from a state `A` to another state `B` with the same input which is also in a higer layer in `trie`.

First, we shall make **`{"he", "she", "his", "hers"}` as an example**.

**The steps to build a `trie` are:**

- Build a tree structure for all reserved words, we can call them `patterns`. **For every pattern, a corresponding final state should be found.** For example, it should be like all the circles and all the real line arrows in the following picture. A single circle is a normal state and a circle with a smaller one inside is a final state.
- Build up `fail edge` for every state. For example, it should be like all the  dash line arrows in the following picture.

**The method to build a `fail edge` of state `A` is:**

- Jump to another state `B` according to the `fail edge` of the parent state.
- Try to find a child state with the same input as `A`'s in the child states of `B`.
- If such a child state `C` is found, the `fail edge` for `A` is from `A` to `C`. If not, jump to another state `D` according to the `fail edge` of `B`'s parent state and repeat operations above. **Worstly, the `fail edge` should be from `A` to `root`.**

For example, to build a `fail edge` for the state on the right side with an input of `h`(Let's call it `A`), we first jump to `root`, which is the `fail edge` of the state's parent, whose input is `s`. Then, we find that there is a child state (Let's call it `B`) of `root`, which also gets `h` as an input. So, the `fail edge` for `A` is the dash line arrow from `A` to `B`.

![Aho Corasick Algorithm](/img/Aho_Corasick_Algorithm_0.png)

# Code

The follwing code is an implementation by C.

**Aho_Corasick_Algorithm.c**

```c
#include <stddef.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "Aho_Corasick_Algorithm.h"
/**
 * @brief Überprüft wenn der Zeiger NULL ist.
 * @param pointer Der Zeiger zu überprüfen.
 */
void
pointerCheck(void* pointer)
{
    if (pointer == NULL) {
        perror("Der Zeiger soll nicht NULL sind.\n");
        exit(EXIT_FAILURE);
    }
}

/**
 * @brief Initialisiert einen Knoten.
 * @param node  Der Zeiger des zu initialisierende Knoten.
 */
void
acnodeInit(acnode_t* node)
{
    node->parent = NULL;
    node->firstChild = NULL;
    node->next = NULL;
    node->fail = NULL;
    node->inputChar = '\0';
    node->patternNode = false;
    node->pattern = NULL;
}

/**
 * @brief Initialisiert eine Baumstruktur.
 * @param self Der Zeiger der zu initialisierende Baumstruktur.
 */
void
actreeInit(actree_t* self)
{
    pointerCheck(self);
    self->root = (acnode_t*)malloc(sizeof(acnode_t));
    acnodeInit(self->root);
}

/**
 * @brief Fügen einen Knoten zu einem Baum hinzu.
 * @param parent Der Zeiger des Elternknoten.
 * @param child Der Zeiger des Kindknoten.
 */
void
acnodeAddChild(acnode_t* parent, acnode_t* child)
{
    pointerCheck(parent);
    pointerCheck(child);

    if (parent->firstChild == NULL) {
        parent->firstChild = child;
    } else {
        acnode_t* lastChild = parent->firstChild;
        while (lastChild->next != NULL) {lastChild = lastChild->next;}
        lastChild->next = child;
    }
}

/**
 * @brief Gibt das Kind mit spezifischer Letter aus.
 * @param parent    Der Zeiger des Elternknotens.
 * @param inputChar Die spezifischer Zeichenfolge.
 * @return  Das Kind mit spezifischer Letter.
 */
acnode_t*
acnodeSearchNode(acnode_t* parent, char inputChar)
{
    pointerCheck(parent);

    acnode_t* child = parent->firstChild;
    while (child != NULL) { // Sucht das Kind, dessen "inputChar" "inputChar" ist.
        if (child->inputChar == inputChar) {
            return child;
        } else {
            child = child->next;
        }
    }
    return NULL;
}

/**
 * @brief   Gibt den Fehlerpfad des Knotens aus.
 * @param node  Der Zeiger des Knotens.
 * @param inputChar Das spezifischer Zeichen.
 * @return Der Fehlerpfad des Knotens.
 */
acnode_t*
actreeGetFailPath(acnode_t* node, char inputChar)
{
    pointerCheck(node);
    acnode_t *parent = node->parent;
    if (parent == NULL) {   // Der Knoten ist "root".
        return node;
    } else if (parent->parent == NULL) {
        return parent;      // Die Eltern des Knotens ist "root".
    }

    acnode_t *failPath = parent->fail; // Springt zuerst zu "fail" der Eltern.
    acnode_t *child = acnodeSearchNode(failPath, inputChar);
    if (child == NULL) {
        return actreeGetFailPath(failPath, node->inputChar);
    } else {
        return child;
    }
}

/**
 * @brief Setzt den Fehlerpfad des Knotens.
 * @param node Der Knoten dessen Fehlerpfad zu setzen.
 */
void
acnodeSetFailPath(acnode_t* node)
{
    node->fail = actreeGetFailPath(node, node->inputChar);
    acnode_t* child = node->firstChild;
    while (child != NULL) {
        acnodeSetFailPath(child);
        child = child->next;
    }
}

/**
 * @brief Baut die Fehlerpfade der Baumstruktur.
 * @param self Der Zeiger der Baumstruktur.
 */
void
actreeBuildFailPath(actree_t* self)
{
    pointerCheck(self);
    acnodeSetFailPath(self->root);
}

/**
 * @brief Fügen die Knoten mit einem Muster zu einer Baumstruktur hinzu.
 * @param self Der Zeiger der Baumstruktur.
 * @param pattern Die Zeichenfolge des Musters.
 */
void
actreeAddPattern(actree_t* self, char* pattern)
{
    pointerCheck(self);
    pointerCheck((void*)pattern);

    unsigned long index = 0;
    acnode_t *parent = self->root;
    //acnode_t *lastChild = parent->firstChild;
    acnode_t *child = parent->firstChild;

    while (*(pattern + index) != '\0') {
        child = acnodeSearchNode(parent, *(pattern + index));
        if (child == NULL) {    // Macht einen neuen Knoten.
            acnode_t *newChild = (acnode_t*)malloc(sizeof(acnode_t));
            acnodeInit(newChild);
            newChild->parent = parent;
            newChild->inputChar = *(pattern + index);
            acnodeAddChild(parent, newChild);
            parent = newChild;
            index++;
        } else {
            parent = child;
            index++;
        }
    }
    parent->patternNode = true; // Jetzt ist das Kind ein Musterknoten.
    parent->pattern = pattern;
}

/**
 * @brief Initialisiert eine Baumsturktur für den Aho Corasick Algorithmus.
 * @param self Die Baumstruktur für den Algorithmus.
 * @param patterns Die Zeichenfolgemuster.
 * @param patternNum Die Menge der Muster.
 */
void
AC_init(actree_t* self, char** patterns, unsigned int patternNum)
{
    pointerCheck(self);
    pointerCheck(patterns);
    for (int i = 0; i < patternNum; ++i) {
        actreeAddPattern(self, patterns[i]);
    }
    actreeBuildFailPath(self);
}

/**
 * @brief Sucht und drückt ein gefundenes Muster.
 * @param tree Der Ziger der Baumstruktur für den Algorithmus.
 * @param input Die zu scande Zeichenfolge.
 */
void
AC_scanInput(actree_t* tree, char* input)
{
    pointerCheck(tree);
    puts("Die gescannte Zeichenfolge ist:");    // Drückt die zu scande Zeichenfolge.
    for (int i = 0; i < strlen(input); ++i) {
        printf("%d\t", i);
    }
    putchar('\n');
    for (int i = 0; i < strlen(input); ++i) {
        printf("%c\t", input[i]);
    }
    putchar('\n');

    unsigned long index = 0;
    acnode_t *currentNode = tree->root;
    acnode_t *matchedNode = NULL;
    while (*(input + index) != '\0') {  // Beendt, wenn es fertig ist.
        matchedNode = acnodeSearchNode(currentNode, *(input + index));  // Sucht einen Kindknoten mit spezischen Letter.
        if (matchedNode == NULL) {
            if (currentNode->parent == NULL) {  // Der aktuelle Knoten ist "root".
                index++;    // Es kann nicht den Charakter suchen.
            } else {
                currentNode = currentNode->fail;
            }
            continue;
        } else {
            currentNode = matchedNode;  // Ein Kindknoten wurde gefunden.
            if (matchedNode->patternNode == true) {
                printf("Sucht \"%s\" von %lu bis %lu.\n", matchedNode->pattern, \
                       index - strlen(matchedNode->pattern) + 1, index);
            }
            index++;
        }
    }
}
```

**Aho_Corasick_Algorithm.h**

```C
#ifndef AHO_CORASICK_ALGORITHM_H
#define AHO_CORASICK_ALGORITHM_H

#include <stdbool.h>

typedef struct acnode {
    struct acnode* parent;
    struct acnode* firstChild;
    struct acnode* next;
    struct acnode* fail;
    char inputChar;
    bool patternNode;
    char* pattern;
} acnode_t;

typedef struct actree {
    acnode_t* root;
} actree_t;

void
actreeInit(actree_t* self);

void
AC_scanInput(actree_t* tree, char* input);

void
AC_init(actree_t* tree, char** patterns, unsigned int patternNum);

#endif //AHO_CORASICK_ALGORITHM_H
```

**main.c**

```c
#include <stdio.h>
#include "Aho_Corasick_Algorithm.h"

int main() {
    actree_t actree;
    char* attr[] = {"he", "she", "his", "hers"};

    actreeInit(&actree);

    puts("The array of reserved words is:");
    for (int i = 0; i < 4; ++i) {
        printf("\"%s\"\t", attr[i]);
    }
    putchar('\n');

    AC_init(&actree, attr, 4);
    AC_scanInput(&actree, "sheandhershis");

    return 0;
}
```

## Application of Aho Corasick Algorithm

Accept recognizing reserved words from the input, we can also use this algorithm to find out all the key words that the input is their substring.

We can keep going far away from the root of `trie` and only slide the input string back when we fail to match a character in the input with a state in `trie`.

For more details, read [KMP Algorithm](http://localhost:4000/2018/05/17/KMP-Algorithm/).

# Fail Function: Build `fail edge` for a single reserved word

Let's take `abababaab` as an example.

![](/img/Aho_Corasick_Algorithm_1.png)

From the picture, we can find that when we visit `array[i]`, it means we are visiting the input for `state[i]`'s next state.

Let's use `j` to record the target state of the latest fail edge and use 'i' to represent which state we are looking at. In fact, we are building the `fail edge` for the next state `A` of `state[i]`.

When `array[j] == array[i]`, it is successful that we jump from `state A`'s parent to `state B` according to the parent's `fail edge`, which is also the latest fail edge (that is recorded by `j`), and reach the next state `C` of `state B` and `state C` has the same input as `state A` has.

When `array[j] != array[i]`, we cannot find a destination for the `fail edge` we are trying to build so we should jump at least twice.

# Code of Fail Function

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

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

int main() {
    char* str = "abababaab";
    int* failure = failureFunction(str);
    printf("The failure function for %s is:\n", str);
    printf("s   |\t0|\t");
    for (int i = 0; i < strlen(str); ++i) {
        printf("%d|\t", i+1);
    }
    putchar('\n');
    printf("f(s)|\t0|\t");
    for (int i = 0; i < strlen(str); ++i) {
        printf("%d|\t", failure[i]);
    }
    putchar('\n');
    puts("* s and f(s) is the label of the state in the transition diagram.");

    return 0;
}
```

**Output**

```shell
The failure function for abababaab is:
s   |	0|	1|	2|	3|	4|	5|	6|	7|	8|	9|
f(s)|	0|	0|	0|	1|	2|	3|	4|	5|	1|	2|
* s and f(s) is the label of the state in the transition diagram.
```

## [简要说明该函数完整](http://dragon-book.jcf94.com/book/ch03/3.4/3.4.html)

```
01  t = 0;
02  f(1) = 0;
03  for (s = 1; s < n; s ++){
04      while( t > 0 && b_s+1 != b_t+1) t = f(t);
05      if(b_s+1 == b_t+1){
06          t = t + 1;
07          f(s + 1) = t;
08      }else{
09          f(s + 1) = 0;
10      }
11  }
```

1. 已知 f(1) = 0
2. 在第 1 次 for 循环时，计算 f(2) 的值，当第5行代码 b_2 == b_1 成立时，代码进入到第7行得出 f(2) = 1，不成立时，则代码进入第9行得出 f(2) = 0。显然，这次循环正确的计算出了 f(2)。
3. 假设在第 i-1 次进入循环时，也正确的计算出了 f(i)，也有 f(i) = t (无论 t 是大于 0 还是等 0)。
4. 那么在第 1 次进入循环时，分两种情况进行考虑：
    a. t == 0
    这种情况比较简单，直接从第 5 行开始，当 b_i+1 == b_1 时，f(i+1) = 1，否则 f(i+1) = 0
    b. t > 0
    while 循环会不断缩小 t 值，试图找出最大可能的使得 b_i+1 == b_t+1 成立的 t 值，如果找到了，则进入第 5 行执行，得到 f(i+1) = t+1；或者直到 t == 0 时也没有找到，则跳出循环，这时进入第 5 行执行，过程类似于前一种情况。
