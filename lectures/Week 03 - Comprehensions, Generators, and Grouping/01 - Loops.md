---
layout: default
title: 01 - Loops
parent: Week 03 - Comprehensions, Generators, and Grouping
grand_parent: Lectures
nav_order: 1
---

## for loops


```python
# for loops
for i in range(1, 10, 2):
    print(i)
```

    1
    3
    5
    7
    9



```python
# for...else loop
for i in range(1, 5):
    print(i)
else:
    print('done')
```

    1
    2
    3
    4
    done



```python
# for...else loop with a break
for i in range(1, 5):
    print(i)
    if i % 2 == 0:
        break
else:
    print('done')
```

    1
    2


## while loops


```python
# while loop
condition = True
i = 0
while condition:
    print(i)
    i += 1
    if i > 3:
        condition = False
```

    0
    1
    2
    3



```python
# while...else loop
condition = True
i = 0
while condition:
    print(i)
    i += 1
    if i > 3:
        condition = False
else:
    print('done')
```

    0
    1
    2
    3
    done



```python
# while...else loop with a break
condition = True
i = 0
while condition:
    print(i)
    i += 1
    if i > 3:
        break
else:
    print('done')
```

    0
    1
    2
    3


## nested loops


```python
width = 5
chars = "*@"
for char in chars:
    for i in range(1, width):
        print(char * i)
    for j in range(width, 0, -1):
        print(char * j)
```

    *
    **
    ***
    ****
    *****
    ****
    ***
    **
    *
    @
    @@
    @@@
    @@@@
    @@@@@
    @@@@
    @@@
    @@
    @



```python
width = 5
chars = "*@"
for char in chars:
    for i in range(1, width):
        for spaces in range(width - i, 0, -1):
            print(' ', end='')
        print(char * i)
    for j in range(width, 0, -1):
        for spaces in range(width - j, 0, -1):
            print(' ', end='')
        print(char * j)
```

        *
       **
      ***
     ****
    *****
     ****
      ***
       **
        *
        @
       @@
      @@@
     @@@@
    @@@@@
     @@@@
      @@@
       @@
        @



```python

```