# SOLID Principle by example
## _1. Single Responsibility principle_

Every class should have one and only one responsibility.

#### Problem 
- Invoice class has several methods/responsibilities. so this principle state that class should have single responsibility.
``` sh
class Invoice(){
    fun AddInvoice(){}
    fun DeleteInvoice(){}
    fun GenerateReport(){}
    fun otherReportRelatedTask(){}
    fun EmailReport(){}
    fun otherEmailRelatedTask(){}
}
```
#### Solution ✨
- we can separate those methods into different classes like this,

```sh
class Invoice(){
   fun AddInvoice(){}
   fun DeleteInvoice(){}
}
class ReportInvoice(){
    fun GenerateReport(){}
    fun otherReportRelatedTask(){}
}
class EmailInvoice(){
    fun EmailReport(){}
    fun otherEmailRelatedTask(){}
}
```
> now Invoice class does not have any idea about Report & email

## _2. Open-Closed Principle_

Open - We should be able to add new features to our class easily & open for extension. 
Close - Base features of the class should not be changed & closed for modification .

``` sh
class Rectangle {
    private var length;
    private var height; 
    // getters/setters ... 
}

class Circle {
    private var radius; 
    // getters/setters ...
}

class AreaFactory {
    public fun calculateArea(shapes: ArrayList<Object>): Double {
        var area = 0;
        for (shape in shapes) {
            if (shape is Rectangle) {
                var rect = shape as Rectangle;
                area += (rect.getLength() * rect.getHeight());                
            } else if (shape is Circle) {
                var circle = shape as Circle;
                area += (circle.getRadius() * cirlce.getRadius() * Math.PI);
            } else {
                throw new RuntimeException("Shape not supported");
            }            
        }
        return area;
    }
}
```

#### Problem 

- In future, if we want to add new shape, we have to add one more if else statement to calculate area, to fix this problem Open-Closed Principle comes. The class is not closed for modification, and neither it is open for extension. Hence it is violating our open-closed principle.

#### Solution ✨

``` sh
interface Shape {
    fun getArea(): Int
}

class Rectangle : Shape {
    var height: Int? = 0
    var width: Int? = 0

    override fun getArea(): Int {
        height?.let { it ->
            width?.let { it1 ->
                return it * it1
            }
        }
        return 0
    }
}

class Circle : Shape {
    var radius: Int? = 0
    override fun getArea(): Int {
        radius?.let {
            return (it * it * Math.PI).toInt()
        }
        return 0
    }
}

fun main(){
    val r = Rectangle()
    r.width = 30
    r.height = 20
    println(r.getArea())

    val c = Circle()
    c.radius = 5
    println(c.getArea())
}
```

> Now if another shape will added, we can easily find area of that shape

## _3. Liskov Substitution Principle_
 
Purpose - remove code pollution and unnecessary code crowds  .
 
``` sh
abstract class Vehicle{
    abstract fun startEngine()
    abstract fun stopEngine()
    abstract fun moveForward()
    abstract fun moveBack()
}

class Car: Vehicle(){
    override fun startEngine() {
        println("Engine started")
    }

    override fun stopEngine() {
        println("Engine stopped")
    }

    override fun moveForward() {
        println("Moving forward")
    }

    override fun moveBack() {
        println("Moving back")
    }
}

class Bicycle: Vehicle(){
     override fun startEngine() {   //unnecessory method
        println("Engine started")
    }

    override fun stopEngine() {     //unnecessory method
        println("Engine stopped")
    }

    override fun moveForward() {
        println("Moving forward")
    }

    override fun moveBack() {
        println("Moving back")
    }
}
```

#### Problem 

- Here, In Bicycle class we have two unnecessory methods & to fix this problem Liskov Principle comes.

#### Solution ✨

``` sh
interface MoveableVehicle{
    fun moveForward()
    fun moveBack()
}

abstract class VehicleWithEngine: MoveableVehicle{
    abstract fun startEngine()
    abstract fun stopEngine()
}

class Car: VehicleWithEngine(){
    override fun startEngine() {
        println("Engine started")
    }

    override fun stopEngine() {
        println("Engine stopped")
    }

    override fun moveForward() {
        println("Moving forward")
    }

    override fun moveBack() {
        println("Moving back")
    }
}

class Bicycle: MoveableVehicle{
    override fun moveForward() {
        println("Moving forward")
    }

    override fun moveBack() {
        println("Moving back")
    }
}
```

> We can separate engine & moveable vehicle & use it according to requirement

## _4. Interface Segregation Principle_

Once an interface becomes too fat, it needs to be split into smaller interfaces so that client of the interface will only know about the methods that pertain to them .

``` sh
interface Animal{
    fun eat()
    fun sleep()
    fun fly()
}

class Cat: Animal{
    override fun eat() = println("Cat is eating fish")
    override fun sleep() = println("Cat is sleeping on its owner's bed")
    override fun fly() {
        TODO("Not yet implemented")     
    // Cats can not fly, to counter this Interface Segregation Principle comes
    }
}

class Bird: Animal{
    override fun eat() = println("Bird is eating forage")
    override fun sleep() = println("Bird is sleeping in the nest")
    override fun fly() {
        println("Bird is flying so high")
    }
}
```

#### Solution ✨

``` sh
interface Animal{
    fun eat()
    fun sleep()
}

interface FlyingAnimal{
    fun fly()
}

class Cat: Animal{
    override fun eat() {
        println("Cat is eating fish")
    }

    override fun sleep() {
        println("Cat is sleeping on its owner's bed")
    }
}

class Bird: Animal, FlyingAnimal{
    override fun eat() {
        println("Bird is eating forage")
    }

    override fun sleep() {
        println("Bird is sleeping in the nest")
    }

    override fun fly() {
        println("Bird is flying so high")
    }
}
```

## _5. Dependency Inversion Principle_

High level module should not depend on low level module. 
Abstraction should not depend on details but details should depend on abstraction. 

``` sh
class MySQL {

    fun get() {
        //get MySQL instance
    }

    fun insert() {
        //insert logic here
    }

    fun update() {
        //update logic here
    }

    fun delete() {
        //delete logic here
    }

}

class BudgetReportingSystem(private val mySQL: MySQL) {
    fun open() = mySQL.get()
    fun save() = mySQL.insert()
    fun update() = mySQL.update()
    fun delete() = mySQL.delete()
}

fun main() {
    val mySQL = MySQL()
    val budgetReportingSystem = BudgetReportingSystem(mySQL)
    budgetReportingSystem.open()
    budgetReportingSystem.save()
    budgetReportingSystem.update()
    budgetReportingSystem.delete()
}
```

#### Problem 

- Here, we depend on MySQL & if we want to implement new db like mongodb or sqlite then we have to change BudgetReportingSystem class, to fix this problem Dependency Inversion comes.

#### Solution ✨

``` sh
interface DatabaseInterface {
    //include the task that
    //needs to perform no matter which database we are using
    fun get()
    fun insert()
    fun update()
    fun delete()
}

class MySQL : DatabaseInterface {
    override fun get() {
        //get MySQL instance
    }

    override fun insert() {
        //insert logic here
    }

    override fun update() {
        //update logic here
    }

    override fun delete() {
        //delete logic here
    }

}

class MongoDB : DatabaseInterface {
    override fun get() {
        //get MongoDB instance
    }

    override fun insert() {
        //insert logic here
    }

    override fun update() {
        //update logic here
    }

    override fun delete() {
        //delete logic here
    }

}

class BudgetReportingSystem(private val databaseInterface: DatabaseInterface) {
    fun open() = databaseInterface.get()
    fun inset() = databaseInterface.insert()
    fun update() = databaseInterface.update()
    fun delete() = databaseInterface.delete()
}

fun main() {
    val mySQL = MySQL()
    val mongoDB = MongoDB()
//    val budgetReportingSystem = BudgetReportingSystem(mySQL)
    val budgetReportingSystem = BudgetReportingSystem(mongoDB)

    budgetReportingSystem.open()
    budgetReportingSystem.inset()
    budgetReportingSystem.update()
    budgetReportingSystem.delete()
}
```

> Now we can use mongo db, sqlite or any other db without changing BudgetReportingSystem
