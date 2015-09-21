# Lessons Learnt Note

## Questions

P1U2

1. What is the relationship between containment and encapsulation (as applied in this project), when building components?
2. What are some ways to analyze data (presented in requiredments) to design Objects?
3. What strategies can be used to design core classes, for future requirements, so that they are reusable, extensible and easily modifiable?
4. What are good conventions for making a Java class readable?
5. What are the advantages and disadvantages of reading data from sources such as text files or databases in a single pass and not use intermediary buffering?
6. What is the advantage of using Serialization? What issues can occur, when using Serialization with Inner classes?
7. Where can following object relationships be used: encapsulation, association, containment, inheritance and polymorphism?
8. How can you design objects, which are self-contained and independent?

---

P1U3

1. What role(s) does an interface play in building an API?
2. What is the best way to create a framework, for exposing a complex product, in a simple way and at the same time making your implementation extensible?
3. What is the advantage of exposing methods using different interfaces?
4. Is there any advantage of creating an abstract class, which contains interface method implementations only?
5. How can you create a software architecture, which addresses the needs of exception handling and recovery?
6. What is the advantage of exposing fix methods for exception management?
7. Why did we have to make the Automobile object static in ProxyAutomobile class?
8. What is the advantage of adding choice variable in OptionSet class, what measures had to be implemented to expose the choice property in Auto class?
9. When implementing LinkedHashMap for Auto object in proxyAuto class, what was your consideration for managing CRUD operations on this structure? Did you end up doing the implementation of CRUD operation in proxyAuto or did you consider adding another class in Model for encapuslating Auto for the collection and then introducing an instance of this new class in proxyAuto. (Think about this and if this part of your design is not self-contained, then fix it.)
