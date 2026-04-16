# enum
An enum is just a way to give **names to numbers**.
```c
enum Color
{
    RED = 0,
    GREEN = 1,
    BLUE = 2
};

enum Color c = RED;
// RED → 0
// GREEN → 1
// BLUE → 2
```
The computer still stores a number, but humans **read a meaningful name**.

# typedef enum
**typedef** lets you **rename a type** so it’s easier to use.
```c
typedef enum
{
    RED,
    GREEN,
    BLUE
} Color;

Color c;  // Shorter and cleaner
```

In embedded code:
```c
typedef enum
{
    GPIO_PORT_0_PIN_3 = 0x0003,
    GPIO_PORT_0_PIN_4 = 0x0004,
    GPIO_PORT_1_PIN_2 = 0x0102,
    ...
} gpio_port_pin_t;
```
This means:

“Create a new type called *gpio_port_pin_t* that can only hold these named values.”

## How to use gpio_port_pin_t
In function definition:
```c
void gpio_set(gpio_port_pin_t gpio){
    uint8_t port = GPIO_GET_PORT(gpio);
    uint8_t pin  = GPIO_GET_PIN(gpio);
    ...
}
```

In function call:
```c
gpio_set(GPIO_PORT_0_PIN_4, HIGH);
```
We can directly use **GPIO_PORT_0_PIN_4**, but only in places that expect a **gpio_port_pin_t** value
