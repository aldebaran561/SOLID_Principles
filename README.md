# SOLID principles in Python

## Table of Contents

1. [Cohesion](#cohesion)
2. [Coupled](#coupled)
3. [SOLID](#solid)
    1. [Single Responsibility Principle (SRP)](#srp)
    2. [Open and Closed Principle (OCP)](#ocp)
    3. [Lisvok Sub situation Principle (LSP)](#lsp)
    4. [Interface Segregation Principle (ISP)](#isp)
    5. [Dependency Inversion Principle (DIP)](#dip)
4. [Notes](#notes)
    
The SOLID **principles** were defined in the early 2000s by Robert C. Martin (Uncle Bob). He says this are not mandatory for web developers but using SOLID should be used to get good management of dependencies in our code.

SOLID is an acronym of: 

1. Single Responsibility Principle (SRP)
2. Open and Closed Principle (OCP)
3. Lisvok Sub situation Principle (LSP)
4. Interface Segregation Principle (ISP)
5. Dependency Inversion Principle (DIP)

Before talking about SOLID, it is important to say that this principles are too correlated with **design patterns**, which let us to guarantee the basic premise on web development: ***HIGH COHESIONED*** and ***LOW COUPLED***. But what means these terms.

## 1. Cohesion <a name="cohesion"></a>

It means that each module completes its own functions as independently as possible, independent of the code outside the module. In other words, within a class should be the methods who are related to the class not others. For example:

**Low cohesioned**
```python
class Animal:

   def __init__(self, noise):
       self.noise = noise
   
   def get_noise(self):
       print(self.noise)
        
   def saveDB(self):
       pass
```

**High Cohesioned**
```python
class Animal:

   def __init__(self, noise):
       self.noise = noise
   
   def get_noise(self):
       print(self.noise)

class Animal_DB(Animal):
    
   def saveDB(self):
       pass
```


## 2. Coupled <a name="coupled"></a>

It is referred to a measure of the degree of interconnectedness of modules in the structure of a software system. It is important to think that tighter the connections between the modules, the stronger the coupling， the less independent a module is. In other words, coupled levels how related are different classes of the code and, if a class is changed how much you must change the other classes; in that way, your code is higly coupled, which means you have a problem in your code.

**High Coupled**
```python
class Book:     
    def __init__(self, title, subtitle, author):
         self.title = title
         self.subtitle = subtitle         
         self.author = author
     
    def display_book_info(book):
         print('{}: {} by {}'.format(book.title, book.subtitle, book.author))
```

**Low Coupled**
```python
class Book:     
    def __init__(self, title, subtitle, author):
         self.title = title
         self.subtitle = subtitle         
         self.author = author
     
    def display_book_info(self):
         print('{}: {} by {}'.format(self.title, self.subtitle, self.author))
```

## 3. SOLID <a name="solid"></a>

### 3.1. Single Responsibility Principle (SRP) <a name="srp"></a>

*A class should have only one responsibility and only one reason to change*. That means a class does not perform multiple jobs. This is the most known principle but the hardest to keep in practice. In words of Uncle Bob: *“Gather together the things that change for the same reasons. Separate those things that change for different reasons”*

Here's an example: 

```python
class Account:
   """Demo bank account class"""
   
    def __init__(self, account_no: str):
       self.account_no = account_no
    
    def get_account_number(self):
       """Get account number"""
       return self.account_no
   
    def save(self):
     """Save account number into DB"""
     pass
```

This code is violating the SRP because in one single class there are two responsibilities: Get the account number and save it to the DB, so here's a proposal to keep it clear the principle:

```python
class AccountDB:
    """Account DB management class """

    def get_account_number(self, _id):
       """Get account number"""
       pass

    def account_save(self, obj):
       """Save account number into DB"""
       pass


class Account:
    """Demo bank account class """

    def __init__(self, account_no: str):
       self.account_no = account_no
       self._db = AccountDB()

    def get_account_number(self):
       """Get account number"""
       return self.account_no

    def get(self, _id):
       """
       :param _id:
       :return:
       """
       return self._db.get_account_number(_id=_id)

    def save(self):
       """account save"""
       self._db.account_save(obj=self)
```

### 3.2. Open and Closed Principle (OCP)<a name="ocp"></a>

Software entities (classes, function, module) open for extension, but not for modification (or closed for modification). 

Here's an example: 

```python
class Discount:
    """Demo customer discount class"""
    def __init__(self, customer, price):
        self.customer = customer
        self.price = price
    def give_discount(self):
        """A discount method"""
        if self.customer == 'normal':
            return self.price * 0.2
        elif self.customer == 'vip':
            return self.price * 0.4
```

This code is violating the OCP because when the store decides to add a new type of discount, the code must have changed to ensure the new discount is added. Below you can find a proposal of the new code to mantain the OCP clear, look that this principle is highly correlated with **Inheritance Pillar**.

```python
class Discount:
    """Demo customer discount class"""
    def __init__(self, customer, price):
        self.customer = customer
        self.price = price
    def get_discount(self):
        """A discount method"""
        return self.price * 0.2

class VIPDiscount(Discount):
    """Demo VIP customer discount class"""
    def get_discount(self):
        """A discount method"""
        return super().get_discount() * 2

class SuperVIPDiscount(VIPDiscount): #New discount added
    """Demo super vip customer discount class"""
    def get_discount(self):
        """A discount method"""
        return super().get_discount() * 2
```

### 3.1. Lisvok Sub situation Principle (LSP) <a name="lsp"></a>

The aim of this principle is to ascertain that a sub-class can assume the place of its super-class without errors. In other words, the code should work with a sub-class without any problem.

Here's an example:

```python
class Vehicle:
    """A demo Vehicle class"""

    def __init__(self, name: str, speed: float):
        self.name = name
        self.speed = speed

    def get_name(self) -> str:
        """Get vehicle name"""
        return f"The vehicle name {self.name}"
 
    def get_speed(self) -> str:
        """Get vehicle speed"""
        return f"The vehicle speed {self.speed}"

    def engine(self):
        """A vehicle engine"""
        pass

    def start_engine(self):
        """A vehicle engine start"""
        self.engine()


class Car(Vehicle):
   """A demo Car Vehicle class"""
   def start_engine(self):
       pass


class Bicycle(Vehicle):
   """A demo Bicycle Vehicle class"""
   def start_engine(self):
       pass
```

This clearly violate LSP priciple because a vehicle like Bicycle does not have engine, so this can not replace the super() class. Below there's an example of refactorizing the code to clarify LSP principle:

```python
class Vehicle:
    """A demo Vehicle class"""
    
    def __init__(self, name: str, speed: float):
        self.name = name
        self.speed = speed
  
    def get_name(self) -> str:
        """Get vehicle name"""
        return f"The vehicle name {self.name}"
   
    def get_speed(self) -> str:
        """Get vehicle speed"""
        return f"The vehicle speed {self.speed}"

class VehicleWithoutEngine(Vehicle):
    """A demo Vehicle without engine class"""
  
    def start_moving(self):
        """Moving"""
        raise NotImplemented

class VehicleWithEngine(Vehicle):
    """A demo Vehicle engine class"""
        
    def engine(self):
        """A vehicle engine"""
        pass
   
    def start_engine(self):
        """A vehicle engine start"""
        self.engine()

class Car(VehicleWithEngine):
    """A demo Car Vehicle class"""
 
    def start_engine(self):
        pass

class Bicycle(VehicleWithoutEngine):
    """A demo Bicycle Vehicle class""" 
   
    def start_moving(self):
        pass
```

### 3.4. Interface Segregation Principle (ISP)<a name="isp"></a>

Clients should not be forced to depend upon interfaces that they do not use. In other words, it's better to have lots of interfaces applied to a few methods than lots of methods forced in one interface. This principle is highly correlated with **Abstaction Pillar**

Here's an example:

```python
class IShape:
    def draw_square(self):
        raise NotImplementedError
    
    def draw_rectangle(self):
        raise NotImplementedError
    
    def draw_circle(self):
        raise NotImplementedError
```

On the code shown above, it is necessary to set classes (Circle, Rectangle and Square), but every sub-class defined should have (draw_square, draw_rectangle and draw_circle), but if you think about it does not have sense a class like this:

```python
class Circle(IShape):
    def draw_square(self):
        pass

    def draw_rectangle(self):
        pass
    
    def draw_circle(self):
        pass
```

So, you can say ISP is not good on this code because, think for a moment, what happened if another shape is added to supper class (```IShape()```). So here comes the magig of ISP:

```python
class IShape:
    @abstractmethod
    def draw(self):
        raise NotImplementedError

class Circle(IShape):
    def draw(self):
        pass

class Square(IShape):
    def draw(self):
        pass

class Rectangle(IShape):
    def draw(self):
        pass

class Triangle(IShape): #If you wish to add a new, you can create a new sub-class with Inheritance on IShape()
    def draw(self):
        pass
```



### 3.5. Dependency Inversion Principle (DIP)<a name="dip"></a>

This principle suggests that below two points.

1. High-level modules should not depend on low-level modules. Both should depend on abstractions.
2. Abstractions should not depend on details. Details should depend on abstractions.

Here's an example:

```python
class BackendDeveloper:
    """This is a low-level module"""
    
    @staticmethod
    def python():
        print("Writing Python code")

class FrontendDeveloper:
    """This is a low-level module"""
    
    @staticmethod
    def javascript():
        print("Writing JavaScript code")

class Project:
    """This is a high-level module"""
    
    def __init__(self):
        self.backend = BackendDeveloper()
        self.frontend = FrontendDeveloper()
    
    def develop(self):
        self.backend.python()
        self.frontend.javascript()
        return "Develop codebase"

project = Project()
print(project.develop())
```

It easy to see that super() has a low-level dependency, which clearly is against DIP, so here's a possible solution:

```python
class BackendDeveloper:
    """This is a low-level module"""
   
    def develop(self):
        self.__python_code()
   
    @staticmethod
    def __python_code():
        print("Writing Python code")

class FrontendDeveloper:
    """This is a low-level module"""
   
    def develop(self):
        self.__javascript()
   
    @staticmethod
    def __javascript():
        print("Writing JavaScript code")

class Developers:
    """An Abstract module"""
    
    def __init__(self):
        self.backend = BackendDeveloper()
        self.frontend = FrontendDeveloper()
    
    def develop(self):
        self.backend.develop()
        self.frontend.develop()

class Project:
    """This is a high-level module"""
  
    def __init__(self):
        self.__developers = Developers()
   
    def develops(self):
        return self.__developers.develop()

project = Project()
print(project.develops())
```

## Notes <a name="notes"></a>

It is common to confuse these concepts:

- Inversion of Control
- Dependency Inversion
- Dependency Injection 

So, here's a brief explanation:

- Inversion of Control (IoC): means to create instances of dependencies first and latter instance of a class (optionally injecting them through constructor), instead of creating an instance of the class first and then the class instance creating instances of dependencies. Thus, inversion of control inverts the flow of control of the program. 

- Dependency Inversion: depend on abstractions, not on concretions (5 principle of SOLID)

- Dependency Injection: is where a piece of code allows the calling code to control its dependencies.

    ```python
    from hello_world import hello_world #This is dependency injection example
    
    if __name__ == "__main__":
        hello_world()
    ```

