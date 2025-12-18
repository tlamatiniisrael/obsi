
![](https://miro.medium.com/v2/resize:fit:1400/1*CIIlqFzMahZpjAmVRFYDKA.png)

The Decorator Design Pattern is a type of Structural Design Pattern.

Using this pattern, we can dynamically add new features to objects. We wrap the objects in special wrapper objects.

These wrapper objects have the same interface as the objects they wrap.

> My articles are free for everyone. Non-members can read this full article for free [**HERE**](https://medium.com/@saquibdev/ed6788b2dacf?sk=27e95a35d10bed679e246ded7fd237a2).

The best part is that other instances of the same class are not affected. Each object gets its own modified behavior.

> We can use the analogy of a plain coffee cup. We can add milk to it. Then we can add sugar to the milk-filled cup. After that, we can add whipped cream. Each thing we add decorates the coffee. But the basic coffee cup stays the same. You can keep adding things or stop whenever you want.

## What does Decorator Design Pattern Solves?

Without decorators, imagine if we need 5 different features. **_We need 2⁵ = 32 different classes to handle all combinations._** That is 32 classes for just 5 features. By using the Decorator Design Pattern, we need only 5 classes.

**The Decorator Pattern has four main parts:**

- **Component Interface**: This is the common interface. Both wrappers and wrapped objects. All classes implement this interface.
- **Concrete Component**: This is the real object you want to add features to. It implements the component interface. It defines the basic behavior.
- **Base Decorator**: This is an abstract class. It implements the component interface. It holds a reference to a component object. It delegates all operations to the wrapped object.
- **Concrete Decorators**: These are specific wrappers. They extend the base decoration. They add extra features. Each concrete decorator adds one specific feature.

## Decorator Design Pattern Example Code

Let’s build a pizza ordering system. We start with a basic pizza. Then we add toppings as decorations.

First, let’s create the component interface:

```java
public interface Pizza {  
    String getDescription();  
    double getCost();  
}
```

This interface has two methods. One gets the description. One gets the cost.

Now, let’s create the concrete component. This is our basic pizza:

```java
public class SimplePizza implements Pizza {  
    @Override  
    public String getDescription() {  
        return "Simple Pizza";  
    }  
  
    @Override  
    public double getCost() {  
        return 5.0;  // Base price  
    }  
}
```

Now, let’s create the base decorator. This is the abstract wrapper:

```java
public abstract class PizzaDecorator implements Pizza {  
    protected Pizza pizza;  // Reference to wrapped pizza  
  
    public PizzaDecorator(Pizza pizza) {  
        this.pizza = pizza;  
    }  
  
    @Override  
    public String getDescription() {  
        return pizza.getDescription();  
    }  
  
    @Override  
    public double getCost() {  
        return pizza.getCost();  
    }  
}
```

The decorator implements the same interface as the component. The decorator also holds a reference to a component. This is called composition.

**Now, let’s create concrete decorators.**

First, the cheese decorator:

```java
public class CheeseDecorator extends PizzaDecorator {  
    public CheeseDecorator(Pizza pizza) {  
        super(pizza);  
    }  
  
    @Override  
    public String getDescription() {  
        return pizza.getDescription() + ", Cheese";  
    }  
  
    @Override  
    public double getCost() {  
        return pizza.getCost() + 1.5;  // Cheese costs 1.5  
    }  
}
```

Now, let’s create another decorator for pepperoni:

```java
public class PepperoniDecorator extends PizzaDecorator {  
    public PepperoniDecorator(Pizza pizza) {  
        super(pizza);  
    }  
  
    @Override  
    public String getDescription() {  
        return pizza.getDescription() + ", Pepperoni";  
    }  
  
    @Override  
    public double getCost() {  
        return pizza.getCost() + 2.0;  // Pepperoni costs 2.0  
    }  
}
```

Now let’s create a decorator for mushrooms:

```java
public class MushroomDecorator extends PizzaDecorator {  
    public MushroomDecorator(Pizza pizza) {  
        super(pizza);  
    }  
  
    @Override  
    public String getDescription() {  
        return pizza.getDescription() + ", Mushrooms";  
    }  
  
    @Override  
    public double getCost() {  
        return pizza.getCost() + 1.0;  // Mushrooms cost 1.0  
    }  
}
```

Now let’s use these decorators:

```java
public class PizzaShop {  
    public static void main(String[] args) {  
        // Create a simple pizza  
        Pizza pizza1 = new SimplePizza();  
        System.out.println(pizza1.getDescription());  // Output: Simple Pizza  
        System.out.println("Cost: $" + pizza1.getCost());  // Output: Cost: $5.0  
  
        // Add cheese  
        pizza1 = new CheeseDecorator(pizza1);  
        System.out.println(pizza1.getDescription());  // Output: Simple Pizza, Cheese  
        System.out.println("Cost: $" + pizza1.getCost());  // Output: Cost: $6.5  
  
        // Add pepperoni  
        pizza1 = new PepperoniDecorator(pizza1);  
        System.out.println(pizza1.getDescription());  // Output: Simple Pizza, Cheese, Pepperoni  
        System.out.println("Cost: $" + pizza1.getCost());  // Output: Cost: $8.5  
  
        // Add mushrooms  
        pizza1 = new MushroomDecorator(pizza1);  
        System.out.println(pizza1.getDescription());  // Output: Simple Pizza, Cheese, Pepperoni, Mushrooms  
        System.out.println("Cost: $" + pizza1.getCost());  // Output: Cost: $9.5  
  
        // Create another pizza with different combination  
        Pizza pizza2 = new SimplePizza();  
        pizza2 = new PepperoniDecorator(pizza2);  
        pizza2 = new MushroomDecorator(pizza2);  
        System.out.println(pizza2.getDescription());  // Output: Simple Pizza, Pepperoni, Mushrooms  
        System.out.println("Cost: $" + pizza2.getCost());  // Output: Cost: $8.0  
    }  
}
```

## How Does It Work Step by Step?

1. When you create a decorator, you pass an object to it.
2. The decorator stores that object in a variable.
3. When someone calls a method on the decorator, the decorator does its own work first.
4. Then it calls the same method on the stored object.
5. This is called delegation.
6. Because of this, you can chain decorators endlessly.
7. Each decorator adds one layer.
8. The topmost decorator is what the client sees.
9. But inside, it contains another decorator, which contains another decorator, which finally contains the real object.

## Decorator Pattern vs Inheritance

Decorators are better than Inheritance in many cases. Below is a code using inheritance.

```java
public class BasicCar {  
    public void assemble() {  
        System.out.println("Basic Car");  
    }  
}  
  
public class LuxuryCar extends BasicCar {  
    @Override  
    public void assemble() {  
        super.assemble();  
        System.out.println(" with Luxury Features");  
    }  
}  
  
public class SportsCar extends BasicCar {  
    @Override  
    public void assemble() {  
        super.assemble();  
        System.out.println(" with Sports Features");  
    }  
}  
  
public class LuxurySportsCar extends LuxuryCar {  
    @Override  
    public void assemble() {  
        super.assemble();  
        System.out.println(" and Sports Features");  
    }  
}
```

What happens if we want a sports car with luxury features?  
We need to create LuxurySportsCar.

What if we want a luxury sports car with navigation?  
Now we need LuxurySportsCarWithNavigation.

This explodes very quickly.

Below is the code using decorators:

```java
public interface Car {  
    void assemble();  
}  
  
public class BasicCar implements Car {  
    @Override  
    public void assemble() {  
        System.out.println("Basic Car");  
    }  
}  
  
public abstract class CarDecorator implements Car {  
    protected Car car;  
  
    public CarDecorator(Car car) {  
        this.car = car;  
    }  
  
    @Override  
    public void assemble() {  
        this.car.assemble();  
    }  
}  
  
public class LuxuryDecorator extends CarDecorator {  
    public LuxuryDecorator(Car car) {  
        super(car);  
    }  
  
    @Override  
    public void assemble() {  
        super.assemble();  
        System.out.print(" with Luxury Features");  
    }  
}  
  
public class SportsDecorator extends CarDecorator {  
    public SportsDecorator(Car car) {  
        super(car);  
    }  
  
    @Override  
    public void assemble() {  
        super.assemble();  
        System.out.print(" and Sports Features");  
    }  
}  
  
public class NavigationDecorator extends CarDecorator {  
    public NavigationDecorator(Car car) {  
        super(car);  
    }  
  
    @Override  
    public void assemble() {  
        super.assemble();  
        System.out.print(" with Navigation");  
    }  
}  
  
public class TestCar {  
    public static void main(String[] args) {  
        // Luxury sports car  
        Car car1 = new BasicCar();  
        car1 = new LuxuryDecorator(car1);  
        car1 = new SportsDecorator(car1);  
        car1.assemble();  
        // Output: Basic Car with Luxury Features and Sports Features  
  
        // Sports car with navigation  
        Car car2 = new BasicCar();  
        car2 = new SportsDecorator(car2);  
        car2 = new NavigationDecorator(car2);  
        car2.assemble();  
        // Output: Basic Car and Sports Features with Navigation  
  
        // Luxury car with navigation (no sports)  
        Car car3 = new BasicCar();  
        car3 = new LuxuryDecorator(car3);  
        car3 = new NavigationDecorator(car3);  
        car3.assemble();  
        // Output: Basic Car with Luxury Features with Navigation  
    }  
}
```

In above case with decorators, we only have 3 decorator classes. With inheritance, we would need many subclasses for all combinations.

### Advantages of the Decorator Pattern

1. **Greater flexibility than inheritance**: We can add features at runtime. With inheritance, features are fixed at compile time.
2. **Avoids class explosion**: We don’t need to create a new class for every combination of features. Just chain decorators.
3. **Single Responsibility Principle**: Each decorator handles one specific responsibility. This makes the code easier to understand and maintain.
4. **Open-Closed Principle**: We can extend functionality without modifying existing code. Just create new decorators.
5. **Easy to combine behaviours**: We can mix and match decorators in any order. You can add the same decorator multiple times if needed.
6. **Transparent to the client**: The client sees the same interface regardless of how many decorators wrap the object.

### Disadvantages of the Decorator Pattern

1. **Increased complexity**: Too many decorators can make code hard to follow. You have many small classes instead of fewer large classes.
2. **Hard to debug**: When something breaks, you have to unwrap many layers to find the problem. The call chain can be deep
3. **Not beginner friendly**: The pattern is not intuitive for beginners. The composition of many objects can be confusing.
4. **Performance overhead**: Each decorator adds a layer. Many layers mean more method calls. This can slow down your program.
5. **Large number of objects**: You create many small objects. This uses more memory. Garbage collection becomes more expensive.
6. **Type checking issues**: A decorator and its component are not identical. Tests for object type will fail. You cannot use **instanceof** reliably.

The **Decorator Design Pattern** is a powerful tool. It lets us add functionality to objects without modifying their code. It avoids class explosion. It promotes clean, modular code.

The Decorator Pattern is one of the most practical patterns. We can see it in many real Java libraries. Understanding it well makes us a better programmer.