# Design Patterns 设计模式

All the codes in this note comes from [bethrobson/Head-First-Design-Patterns](https://github.com/bethrobson/Head-First-Design-Patterns).

## Creational Patterns 创建模式

### Simple Factory

(Not a design pattern)

![](design-patterns/simple-factory.png)

```java
public class SimplePizzaFactory {

	public Pizza createPizza(String type) {
		Pizza pizza = null;

		if (type.equals("cheese")) {
			pizza = new CheesePizza();
		} else if (type.equals("pepperoni")) {
			pizza = new PepperoniPizza();
		} else if (type.equals("clam")) {
			pizza = new ClamPizza();
		} else if (type.equals("veggie")) {
			pizza = new VeggiePizza();
		}
		return pizza;
	}
}
```

### Factory Method

The Factory Method Pattern **defines an interface for creating an object, but lets subclasses decide which class to instantiate**. Factory Method lets a class defer instantiation to subclasses.

![](design-patterns/factory-method.png)

```java
public abstract class PizzaStore {

	abstract Pizza createPizza(String item);

	public Pizza orderPizza(String type) {
		Pizza pizza = createPizza(type);
		System.out.println("--- Making a " + pizza.getName() + " ---");
		pizza.prepare();
		pizza.bake();
		pizza.cut();
		pizza.box();
		return pizza;
	}
}
```

```java
public class NYPizzaStore extends PizzaStore {

	Pizza createPizza(String item) {
		if (item.equals("cheese")) {
			return new NYStyleCheesePizza();
		} else if (item.equals("veggie")) {
			return new NYStyleVeggiePizza();
		} else if (item.equals("clam")) {
			return new NYStyleClamPizza();
		} else if (item.equals("pepperoni")) {
			return new NYStylePepperoniPizza();
		} else return null;
	}
}
```

```java
public class NYStyleCheesePizza extends Pizza {

	public NYStyleCheesePizza() {
		name = "NY Style Sauce and Cheese Pizza";
		dough = "Thin Crust Dough";
		sauce = "Marinara Sauce";

		toppings.add("Grated Reggiano Cheese");
	}
}
```

### Abstract Factory

The Abstract Factory Pattern **provides an interface for creating families of related or dependent objects without specifying their concrete classes**.

![](design-patterns/abstract-factory.png)

```java
public interface PizzaIngredientFactory {

	public Dough createDough();
	public Sauce createSauce();
	public Cheese createCheese();
	public Veggies[] createVeggies();
	public Pepperoni createPepperoni();
	public Clams createClam();

}
```

```java
public class NYPizzaStore extends PizzaStore {

	protected Pizza createPizza(String item) {
		Pizza pizza = null;
		PizzaIngredientFactory ingredientFactory =
			new NYPizzaIngredientFactory();

		if (item.equals("cheese")) {

			pizza = new CheesePizza(ingredientFactory);
			pizza.setName("New York Style Cheese Pizza");

		} else if (item.equals("veggie")) {

			pizza = new VeggiePizza(ingredientFactory);
			pizza.setName("New York Style Veggie Pizza");

		} else if (item.equals("clam")) {

			pizza = new ClamPizza(ingredientFactory);
			pizza.setName("New York Style Clam Pizza");

		} else if (item.equals("pepperoni")) {

			pizza = new PepperoniPizza(ingredientFactory);
			pizza.setName("New York Style Pepperoni Pizza");

		}
		return pizza;
	}
}
```

```java
public class CheesePizza extends Pizza {
	PizzaIngredientFactory ingredientFactory;

	public CheesePizza(PizzaIngredientFactory ingredientFactory) {
		this.ingredientFactory = ingredientFactory;
	}

	void prepare() {
		System.out.println("Preparing " + name);
		dough = ingredientFactory.createDough();
		sauce = ingredientFactory.createSauce();
		cheese = ingredientFactory.createCheese();
	}
}
```

1. `PizzaStore nyPizzaStore = new NYPizzaStore();`
2. `nyPizzaStore.orderPizza(“cheese”);`
3. `Pizza pizza = createPizza(“cheese”);`
4. `Pizza pizza = new CheesePizza(nyIngredientFactory);`
5. `pizza.prepare();`

### Singleton

The Singleton Pattern ensures **a class has only one instance, and provides a global point of access to it**.

![](design-patterns/singleton.png)

#### Thread-safe singleton

Double-checked locking

```java
public class Singleton {
	private volatile static Singleton uniqueInstance;

	private Singleton() {}

	public static Singleton getInstance() {
		if (uniqueInstance == null) {
			synchronized (Singleton.class) {
				if (uniqueInstance == null) {
					uniqueInstance = new Singleton();
				}
			}
		}
		return uniqueInstance;
	}
}
```

## Structural Patterns 结构模式

### Adapter

The Adapter Pattern **converts the interface of a class into another interface the clients expect**. Adapter lets classes work together that couldn’t otherwise because of incompatible interfaces.

#### Object Adapter

![](design-patterns/adapter.png)

#### Class Adapter

Needs multiple inheritance, impossible in Java.

![](design-patterns/class-adapter.png)

```java
public class TurkeyAdapter implements Duck {
	Turkey turkey;

	public TurkeyAdapter(Turkey turkey) {
		this.turkey = turkey;
	}

	public void quack() {
		turkey.gobble();
	}

	public void fly() {
		for(int i=0; i < 5; i++) {
			turkey.fly();
		}
	}
}
```

### Facade

The Facade Pattern **provides a unified interface to a set of interfaces in a subsytem**. Facade defines a higher- level interface that makes the subsystem easier to use.

![](design-patterns/facade.png)

```java
public class HomeTheaterFacade {
	Amplifier amp;
	Tuner tuner;
	DvdPlayer dvd;
	CdPlayer cd;
	Projector projector;
	TheaterLights lights;
	Screen screen;
	PopcornPopper popper;

	public HomeTheaterFacade(Amplifier amp,
				 Tuner tuner,
				 DvdPlayer dvd,
				 CdPlayer cd,
				 Projector projector,
				 Screen screen,
				 TheaterLights lights,
				 PopcornPopper popper) {

		this.amp = amp;
		this.tuner = tuner;
		this.dvd = dvd;
		this.cd = cd;
		this.projector = projector;
		this.screen = screen;
		this.lights = lights;
		this.popper = popper;
	}

	public void watchMovie(String movie) {
		System.out.println("Get ready to watch a movie...");
		popper.on();
		popper.pop();
		lights.dim(10);
		screen.down();
		projector.on();
		projector.wideScreenMode();
		amp.on();
		amp.setDvd(dvd);
		amp.setSurroundSound();
		amp.setVolume(5);
		dvd.on();
		dvd.play(movie);
	}
  ...
}
```

## Behavioral Patterns 行为模式

## Concurrency Patterns 并发模式

### Double-checked locking

volatile
