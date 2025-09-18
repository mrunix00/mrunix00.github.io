---
title: "Object-Oriented Programming in C"
date: 2025-09-18T18:30:59+01:00
draft: false
---

## Introduction

[Object-Oriented programming](https://en.wikipedia.org/wiki/Object-oriented_programming) is a powerful (although controversial) programming paradigm that empowers developers to design modular systems and abstractions.

[The C programming language](<https://en.wikipedia.org/wiki/C_(programming_language)>) is also a very powerful language that is often used for performance-critical applications such as operating systems, video/audio processing, scientific computing and more.

However, one thing that C isn't known for is its support for object-oriented programming. While C does not have built-in support for classes and inheritance, it is still possible to implement object-oriented programming principles.

## Classes & Objects

We're gonna start with the (obvious) basics, classes can be implemented simply using structs, which are a very simple way to combine variables into a single entity.

```c
struct Player {
    int health;
    int mana;
    int level;
};

struct Player player;
```

The syntax for instantiating a struct might be a bit ugly for some developers, but we can simplify it by defining the struct as a type using the keyword `typedef`:

```c
typedef struct {
    int health;
    int mana;
    int level;
} Player;

Player player;
```

## Contructors & Destructors

Contructors are methods that are used to initialize objects, while destructors are methods that are used to clean up resources when an object is no longer needed.

Implementing constructors is as simple as defining functions that return a struct instance:

```c
typedef struct {
    int *data;
    int size;
    int capacity;
} List;

List newList() {
    return (List) {
        .data = malloc(sizeof(int) * 10),
        .size = 0,
        .capacity = 10
    };
}
```

Destructors on the other hand can be implemented by defining a function that takes a struct instance as a parameter:

```c
void destroyList(List *list) {
    free(list->data);
    memset(list, 0, sizeof(List));
}
```

## Methods

There are two ways to implement methods in C, the first one is by defining functions that take a struct instance as a parameter:

```c
void playerHeal(Player *player, int amount) {
    player->health += amount;
}

void main() {
    Player player = newPlayer(100, 50, 1);
    playerHeal(&player, 10);
}
```

The other way is using function pointers as parameters for the struct:

```c
typedef struct _player Player;

struct _player {
    int health;
    void (*heal)(Player *player, int amount);
};

static void _playerHeal(Player *player, int amount) {
    player->health += amount;
}

static Player newPlayer(int health, int mana, int level) {
    return (Player) {
        .health = health,
        .mana = mana,
        .level = level,
        .heal = _playerHeal
    };
}

void main() {
  Player player = newPlayer(100, 50, 1);
  player.heal(&player, 10);
}
```

While the second way provides a syntax that is more similar to object-oriented programming languages, I'd recommend most of the time going for the first one for the following reasons:

- The use of function pointers can be often more error prone than using functions, especially considering that you don't want to deal with NULL pointer dereferencing.
- Function pointers are slower than regular function calls because they cause more cache misses, and also makes it harder for the compiler to optimize the code because the function to be called is not known at compile time.

## Inheritance

[Inheritance](<https://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming)>) in object-oriented programming is the ability of a class to inherit the properties of another class, it can be achieved in C can be achieved through composition. This means that a struct can contain another struct as a member. The derived struct can then access the members of the base struct.

As you can see in the example below, we can create a `Player` struct that inherits from the `Entity` struct, and then typecast it to an `Entity` pointer, allowing us to access the base struct's members.

```c
typedef struct {
    int x, y, z;
    bool isAlive;
} Entity;

typedef struct {
    Entity base;
    int health;
    int mana;
    int level;
} Player;

void entityMoveForward(Entity *entity) {
    entity->x += 1;
}

void main() {
    Player player;
    entityMoveForward(&player);
}
```

## Polymorphism

[Polymorphism](<https://en.wikipedia.org/wiki/Polymorphism_(computer_science)>) in object-oriented programming refers to the ability of an object to take on many forms, allowing it to be used in different contexts without knowing its exact type.

Polymorphism can be achieved in object-oriented languages by using virtual functions. Virtual functions are functions that can be overridden by derived classes. When a virtual function is called, the version of the function in the derived class is called instead of the version in the base class.

In C, the most common way to achieve polymorphism is through function pointers. This allows us to define a function pointer in the base struct that can be overridden by derived structs.

```c
typedef struct {
    int (*getArea)(Shape *);
} Shape;

typedef struct {
    Shape base;
    int radius;
} Circle;

static int _circleGetArea(Shape *shape) {
    Circle *circle = (Circle *) shape; // We cast back to Circle
    return 3.14 * circle->radius * circle->radius;
}

// The constructor for Circle
Circle newCircle(int radius) {
    return (Circle) {
        .base = { .getArea = _circleGetArea },
        .radius = radius,
    };
}

void printShapeArea(Shape *shape) {
    printf("%d\n", shape->getArea());
}

void main() {
    Circle circle = newCircle(5);
    printShapeArea(&circle);
}
```

Remember as I mentioned earlier, function pointers are often slower than using function calls directly, so it's best to only use them when necessary. Most of the time it's better to just use `switch` statements as that's faster (due to the use of jump tables), and is also less error-prone and often easier to debug.

However, Polymorphism through function pointers can be extremely useful under certain cases, one example of a common usecase is [file systems](https://en.wikipedia.org/wiki/Virtual_file_system) in operating systems.
