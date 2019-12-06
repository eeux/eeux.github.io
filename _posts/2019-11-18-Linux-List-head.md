---
layout: post
title: "Linux kernel - list head usage"
date: 2019-11-19 15:17
categories: Linux
keywords: list c
---





When you first read the code of Linux kernel, you may troubled by many **list head**s.

This passage will tell you the usage of list in Linux kernel.





#### see some code first

this is definition of `list head`:

```c
struct list_head {
    struct list_head *prev, *next;
};
```

You can see that it's a [Doubly linked list](https://en.wikipedia.org/wiki/Doubly_linked_list). Very simple definition.

And this is the reference of it in Linux process scheduler **task_struct**:

```c
struct task_struct {
    // ...
    
    struct list_head		tasks;
    
    // ...
};
```

By adding this struct into `task_struct`, all task_struct has linked with previous and next task, which forms a `list`.

#### Linux also provides some API for list_head access:

| function                      | usage                                          |
| ----------------------------- | ---------------------------------------------- |
| LIST_HEAD(name)               | define and init a list with name               |
| list_add(new, head)           | add a new node after list head(insert front)   |
| list_del(entry)               | deletes entry from list                        |
| list_move(list, head)         | delete from one list and add as another's head |
| list_empty(head)              | tests whether a list is empty                  |
| list_entry(ptr, type, member) | get the struct for this entry                  |

you need to take care of the last one function. It's awesome and useful! Actually, it is a macro.

here is the definition:

```c
#define list_entry(ptr, type, member) \
	container_of(ptr, type, member)
```

```c
// cast a member of a structure out to the containing structure
#define container_of(ptr, type, member) {( \
	void *__mptr = (void *)(ptr);	\
	((type *)(__mptr - offsetof(type, member))); })
```

```c
/**
 * get address offset of this member and it's containing structure
 *         +--------+ ^
 *         +        + | offsetof(TYPE, MEMBER)
 *         +        + |
 *         +        + |
 * *ptr -> + MEMBER + V
 *         +        +
 *         +--------+
 */
#define offsetof(TYPE, MEMBER)	((size_t)&((TYPE *)0)->MEMBER)
```

By this macro, you can traverse from list head, and get each containing structure (here is `task_struct`).

This method is very common in Kernel, especially in `process scheduler`.

#### Later

Also, you can see Linux kernel c also uses **object oriented programming**.

I will cover that in later passages.

---
