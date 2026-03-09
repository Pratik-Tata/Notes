1. How to implement payment gateway?
2. How to store customer sensitive data like account information in database ?
3. what is @ComponentScan what is the use of it.
4. We have a list of integers. Find out all the numbers starting with 1 using stream function
	a. 11, 18, 20, 24, 85, 66, 13
5. We have a list of employees, in which we have id, name, age, gender and salary.
a. How many male and female employees are there in the organization.
b. Take out average salary based on employee gender.
6. Write a java program to find sum of even numbers and sum of odd numbers in a given list using java 8 streams.
7. How to find duplicate elements in a given integers list in java using Streams function.
8. I need to compare if two arrays are same, but the order does not matter, just compare the elements in arr1 to elements in arr2
	a. arr1 = [3, 2, 5, 7]
	b. arr2 = [2, 3, 5, 7]
9. I will provide a string, remove all the occurrences of a given character from that string?
10. Finding special character in the String, special characters are those characters which are not alphabets or not numbers.
11. Write a program to check if given strings are rotations of each other or not
12. Write a program to Find the missing number from the array
13. Write a program to convert first half of the String in lower case and second half in upper case
14. Java 8 Program to get Highest paid Employee in Each department using stream api?
15. Write a java 8 program to find the words starting with vowels
16. Write a java 8 program to print employees count working in each department 
17. Write a java 8 program to print active and inactive employees in the given collection
18. Write a java 8 program to print employee details working in each department
19. Write a java 8 program to print max salary of employee in each department max/min employee salary in given collection.
20. Write a Java 8 method to find the sum of all elements in a List of integers.

1)You migrated a monolith to microservices how did you identify service boundaries when data was tightly coupled?  
2)After the split, where did the system break first (data consistency, APIs, transactions), and how did you stabilize it?  
3)How did you handle cross-service transactions without using distributed transactions?  
4)How did you design service-to-service communication to avoid cascading failures under load?  
5)You used Kafka what problem was Kafka solving that REST calls couldn’t?  
6)How did you decide the Kafka partitioning strategy, and what would break if it was chosen incorrectly?  
7)How did you handle duplicate events and ensure idempotency on the consumer side?  
8)What happens in your system if a Kafka consumer crashes mid-processing, and how is data consistency preserved?  
9)You worked with Spring Batch why was batch processing required instead of streaming the same data through Kafka?  
10)How did Spring Batch jobs authenticate with AWS (S3 / infrastructure), and what security risks did this introduce?


JAVA INTERVIEW QUESTIONS - PART-1  
🟢 BASIC (Must Know)  
1. Why is String immutable, and why useful?  
2. Difference between ==, equals(), hashCode()  
3. How does Java achieve runtime platform independence?  
4. What happens with new String("Java") internally?  
5. Why no pointers in Java, yet efficient memory?  
6. Explain pass-by-value with object refs.  
7. What GC solves, and what it doesn’t?  
8. When use abstract class vs interface?  
9. Why wrapper classes immutable, and caching impact?  
10. Compilation vs runtime error examples.  
11. How Java handles integer overflow, why risky?  
12. Why StringBuilder faster than StringBuffer, uses?  
13. explain final, static, private.  
14. Why Object class is important?  
15. How Java ensures backward compatibility?  
  
🟡 INTERMEDIATE (Most Candidate Fail Here)  
16. How HashMap works ? explain resize, treeify.  
17. Why Why is hashCode() contract matters, breakage if violated.  
18. HashMap vs LinkedHashMap vs TreeMap failure cases.  
19. Behaviour of HashMap in Multi-threaded env.  
20. How memory model ensures thread visibility.  
21. Stack vs heap vs metaspace.  
22. Why volatile ≠ synchronized.  
23. How synchronized works in JVM.  
24. What is object escape analysis, how it improves performance.  
25. Why double-checked locking fails w/o volatile.  
26. How ConcurrentHashMap avoids full lock.  
27. Fail-fast iterator how detects modification.  
28. Why Optional bad for fields/params.  
29. Checked exceptions why controversial.  
30. Overloading with autoboxing + varargs.  
31. Exception inside constructor effect.  
32. Why finalize() dangerous + deprecated.  
33. Shallow vs deep copy, clone pitfalls.  
34. Internals working of Collections.synchronizedList()  
35. Why prefer composition over inheritance.  
  
🔴 HARD (Checks in Depth Knowledge)  
36. Class loading phases, static block timing.  
37. How JVM picks GC algorithm.  
38. G1 vs ZGC vs Shenandoah when use.  
39. Cause of Stop-The-World pauses, minimize.  
40. Memory leaks despite GC.  
41. Diagnosing high CPU in production Java.  
42. CompletableFuture vs traditional threads.  
43. How JNI breaks JVM safety.  
44. How JVM handles thread context switch.  
45. Parallel streams internal working.  
46. Why blocking in parallel streams harmful.  
47. Backpressure in reactive systems and how its handled ?  
48. JVM hot code path optimization.  
