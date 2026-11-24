SOLID: Answers
1. Reason to change. A reason to change is one actor (a stakeholder or group) whose needs can force the code to be modified. Single Responsibility: a class should have only one reason to change, meaning it serves one actor.
2. Class Report. It breaks the Single Responsibility Principle. The actors are:
calculate_totals(): the finance/business department (business rules)
to_pdf(): the design/presentation team (formatting and layout)
send_email(): IT/operations (delivery and infrastructure)
A change for one actor can break the others, since all three share one class.
3. Open/Closed. Software entities should be open for extension but closed for modification: add new behavior by adding new code, not by editing existing code. A growing if/elif chain on a type shows it is broken because every new type forces you to edit the same function (and often several like it). The function can never be finished, and each edit risks breaking existing branches.
4. Tool and "closed". Abstraction through polymorphism makes it possible: abstract base classes (ABC) or interfaces, with concrete subclasses that implement them. "Closed" does not mean "never touch" because bugs still get fixed and requirements still change. It means that adding a new variant should not require modifying tested, working code. You put the stable abstraction in place once, and you extend it from outside.
5. Liskov Substitution. A subclass object must be usable anywhere the parent is expected, without the program breaking or behaving surprisingly. Two of the four rules a child must keep:
Preconditions cannot be strengthened (it can't demand more than the parent).
Postconditions cannot be weakened (it must promise at least what the parent does).
The other two are that invariants must be preserved and that no new exception types may be raised.
6. Square(Rectangle). In geometry a square is a rectangle, but in code a Rectangle has independent width and height setters. A Square must keep them equal, so setting one silently changes the other. Code such as r.width = 5; r.height = 4; assert r.area() == 20 works for a Rectangle but fails for a Square. The child changes the parent's behavior, so substitution breaks. Behavior, not the "is-a" relationship in the real world, defines inheritance.
7. Fat interface. A fat interface is a large interface with many unrelated methods, so clients are forced to depend on methods they don't use. Symptoms:
Implementers have to write empty or stub methods, or raise NotImplementedError, for methods that don't apply to them.
Changing one method forces recompilation, retesting, or changes in clients that never use it (also: hard to understand and to mock).
The fix is to split it into small, role-specific interfaces.
8. ABC vs Protocol.
ABC uses nominal typing: a class must explicitly inherit from it (class Dog(Animal)). It can provide shared implementation and enforces abstract methods at instantiation time.
Protocol uses structural typing ("duck typing" checked statically): any class with the right methods matches, with no inheritance needed. It is checked by type checkers such as mypy, not at runtime (unless @runtime_checkable is used).
9. DIP, DI, IoC container.
DIP (Dependency Inversion Principle): a design principle. High-level modules should not depend on low-level modules; both should depend on abstractions, and abstractions should not depend on details.
DI (Dependency Injection): a technique for applying DIP. A class receives its dependencies from outside (constructor, setter, or argument) instead of creating them itself.
IoC container: a tool or framework that automates DI. It builds objects, resolves the dependency graph, and manages lifetimes (for example, a container that wires up classes from configuration).
In short: DIP is the what (principle), DI is the how (technique), and the container is the who (automation tool).
10. self.db = MySQLDatabase(). The class creates its own concrete dependency, so it is tightly coupled to MySQL. In a test it will try to connect to a real database (slow, needs setup, side effects), and you can't swap in a fake or mock. It also violates DIP.
Fix: depend on an abstraction (ABC or Protocol) and inject it:
class UserService:
    def __init__(self, db: Database):
        self.db = db
In production you pass MySQLDatabase(); in tests you pass a FakeDatabase() or a mock.
