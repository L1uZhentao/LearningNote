# Chapter 13 - Concurrency

## Summary

This chapter focuses on the challenges and best practices of writing clean concurrent code. The key takeaways are:

1. **Why Concurrency?**

   - Decouples what gets done from when it gets done
   - Improves throughput and structure of applications
   - Enables better resource utilization
   - Handles multiple users and tasks simultaneously

2. **Concurrency Challenges**

   - Writing correct concurrent code is difficult
   - Bugs are often non-repeatable and hard to detect
   - Multiple execution paths can lead to unexpected results
   - Requires careful consideration of shared resources

3. **Concurrency Defense Principles**

   - Single Responsibility Principle for concurrency
   - Limit scope of shared data
   - Use copies of data when possible
   - Keep threads as independent as possible
   - Know your concurrency libraries

4. **Execution Models**

   - Producer-Consumer
   - Readers-Writers
   - Dining Philosophers
   - Understanding these patterns helps solve common concurrency problems

5. **Best Practices**

   - Keep synchronized sections small
   - Beware of dependencies between synchronized methods
   - Write correct shutdown code
   - Test threaded code thoroughly
   - Use proper instrumentation

6. **Testing Concurrent Code**
   - Treat spurious failures as potential threading issues
   - Get non-threaded code working first
   - Make threaded code pluggable and tunable
   - Run with more threads than processors
   - Test on different platforms
   - Use instrumentation to force failures

The chapter emphasizes that writing clean concurrent code requires careful design, thorough testing, and a deep understanding of concurrency principles. Following these practices helps create more reliable and maintainable concurrent systems.

## Details

### Why Concurrency?

- Decoupling strategy that separates what from when
- Improves application throughput and structure
- Enables better resource utilization
- Handles multiple users and tasks simultaneously
- Common in web applications and distributed systems

### Concurrency Challenges

- Example of a simple class with concurrency issues:

```java
public class X {
    private int lastIdUsed;
    public int getNextId() {
        return ++lastIdUsed;
    }
}
```

### Concurrency Defense Principles

1. **Single Responsibility Principle for Concurrency**

   - Concurrency design is complex enough to be a separate concern
   - Concurrency code has its own lifecycle and challenges
   - Keep concurrency implementation separate from business logic
   - Example:

   ```java
   // Bad: Mixed concerns
   public class UserService {
       private final Map<String, User> users = Collections.synchronizedMap(new HashMap<>());

       public void processUser(String id) {
           // Business logic mixed with concurrency
           synchronized(users) {
               User user = users.get(id);
               // Process user...
           }
       }
   }

   // Good: Separated concerns
   public class UserService {
       private final UserRepository repository;

       public void processUser(String id) {
           // Pure business logic
           User user = repository.findById(id);
           // Process user...
       }
   }

   public class UserRepository {
       private final Map<String, User> users = Collections.synchronizedMap(new HashMap<>());

       public User findById(String id) {
           return users.get(id);
       }
   }
   ```

2. **Limit Scope of Shared Data**

   - Minimize the number of critical sections
   - Encapsulate shared data access
   - Use thread-safe collections
   - Example:

   ```java
   public class SafeCounter {
       private final AtomicInteger counter = new AtomicInteger(0);

       public int increment() {
           return counter.incrementAndGet();
       }
   }
   ```

3. **Use Copies of Data**

   - Avoid sharing data when possible
   - Use read-only copies
   - Merge results in a single thread
   - Example:

   ```java
   public class DataProcessor {
       public List<Result> processData(List<Data> dataList) {
           // Create copies for each thread
           List<Future<Result>> futures = new ArrayList<>();
           for (Data data : dataList) {
               Data copy = data.copy(); // Create a copy
               futures.add(executor.submit(() -> processSingleData(copy)));
           }

           // Merge results in main thread
           return futures.stream()
               .map(Future::get)
               .collect(Collectors.toList());
       }
   }
   ```

4. **Independent Threads**

   - Each thread should be self-contained
   - Use local variables when possible
   - Pass data through parameters
   - Example:

   ```java
   public class RequestHandler extends HttpServlet {
       @Override
       protected void doGet(HttpServletRequest req, HttpServletResponse resp) {
           // Each request gets its own thread
           // All data is passed through parameters
           String userId = req.getParameter("userId");
           processRequest(userId);
       }

       private void processRequest(String userId) {
           // Use only local variables
           User user = userRepository.findById(userId);
           // Process user...
       }
   }
   ```

Summary:

1. Keep concurrency code separate from business logic
2. Minimize shared data and critical sections
3. Use copies of data when possible
4. Make threads as independent as possible
5. Use thread-safe collections and utilities
6. Encapsulate concurrency concerns

### Execution Models

1. **Basic Definitions**

   - **ReentrantLock**: A lock that can be acquired in one method and released in another
   - **Semaphore**: A lock with a count, controlling access to a resource
   - **CountDownLatch**: A lock that waits for a number of events before releasing threads
   - **Bound Resources**: Fixed-size resources (e.g., database connections, buffers)
   - **Mutual Exclusion**: Only one thread can access shared data at a time
   - **Starvation**: Threads prevented from proceeding for too long
   - **Deadlock**: Threads waiting for each other's resources
   - **Livelock**: Threads in lockstep, unable to make progress

2. **Producer-Consumer Pattern**

   - Producers create work and place it in a queue
   - Consumers take work from the queue and process it
   - Queue acts as a bound resource
   - Example:

   ```java
   public class ProducerConsumer {
       private final BlockingQueue<Item> queue;
       private final int maxSize;

       public ProducerConsumer(int maxSize) {
           this.queue = new ArrayBlockingQueue<>(maxSize);
           this.maxSize = maxSize;
       }

       public void produce(Item item) throws InterruptedException {
           // Wait if queue is full
           queue.put(item);
           System.out.println("Produced: " + item);
       }

       public Item consume() throws InterruptedException {
           // Wait if queue is empty
           Item item = queue.take();
           System.out.println("Consumed: " + item);
           return item;
       }
   }
   ```

3. **Readers-Writers Pattern**

   - Multiple readers can access shared resource simultaneously
   - Writers need exclusive access
   - Challenge: Balance throughput and fairness
   - Example:

   ```java
   public class ReadWriteLock {
       private int readers = 0;
       private boolean writer = false;

       public synchronized void acquireReadLock() throws InterruptedException {
           while (writer) {
               wait();
           }
           readers++;
       }

       public synchronized void releaseReadLock() {
           readers--;
           notifyAll();
       }

       public synchronized void acquireWriteLock() throws InterruptedException {
           while (writer || readers > 0) {
               wait();
           }
           writer = true;
       }

       public synchronized void releaseWriteLock() {
           writer = false;
           notifyAll();
       }
   }
   ```

4. **Dining Philosophers Problem**

   - Classic synchronization problem
   - Philosophers need two forks to eat
   - Potential for deadlock
   - Example:

   ```java
   public class Philosopher implements Runnable {
       private final Object leftFork;
       private final Object rightFork;

       public Philosopher(Object leftFork, Object rightFork) {
           this.leftFork = leftFork;
           this.rightFork = rightFork;
       }

       @Override
       public void run() {
           try {
               while (true) {
                   // Think
                   synchronized (leftFork) {
                       synchronized (rightFork) {
                           // Eat
                       }
                   }
               }
           } catch (InterruptedException e) {
               Thread.currentThread().interrupt();
           }
       }
   }
   ```

Key Recommendations:

1. Use appropriate thread-safe collections (e.g., ConcurrentHashMap)
2. Leverage the executor framework for task execution
3. Prefer non-blocking solutions when possible
4. Understand and implement these patterns correctly
5. Be aware of potential deadlocks and livelocks
6. Keep synchronized sections minimal

### Beware Dependencies Between Synchronized Methods

1. **The Problem**

   - Multiple synchronized methods on a shared class can cause subtle bugs
   - Individual method synchronization doesn't guarantee thread safety
   - Dependencies between methods can break concurrent code
   - Example of problematic code:

   ```java
   public class SharedResource {
       private int value;

       public synchronized void method1() {
           // Modifies value
           value++;
       }

       public synchronized void method2() {
           // Depends on value from method1
           if (value > 0) {
               // Do something
           }
       }
   }
   ```

2. **Solutions**

   - **Client-Based Locking**

     ```java
     public class Client {
         private final SharedResource resource;

         public void doSomething() {
             synchronized (resource) {
                 resource.method1();
                 resource.method2();
             }
         }
     }
     ```

   - **Server-Based Locking**

     ```java
     public class SharedResource {
         private int value;

         public synchronized void method1() {
             value++;
         }

         public synchronized void method2() {
             if (value > 0) {
                 // Do something
             }
         }

         public synchronized void combinedOperation() {
             method1();
             method2();
         }
     }
     ```

   - **Adapted Server**

     ```java
     public class ResourceAdapter {
         private final SharedResource resource;

         public synchronized void combinedOperation() {
             resource.method1();
             resource.method2();
         }
     }
     ```

3. **Best Practices**
   - Avoid multiple synchronized methods on shared objects
   - If multiple methods are needed, use one of the three locking strategies
   - Prefer server-based locking for better encapsulation
   - Use client-based locking when server can't be modified
   - Consider adapted server pattern for legacy code

### Testing Threaded Code

1. **Core Principles**

   - Testing concurrent code is more complex than single-threaded code
   - No guarantee of correctness, but good testing minimizes risk
   - Write tests that can expose concurrency problems
   - Run tests frequently with different configurations
   - Never ignore failures, even if they don't repeat

2. **Key Testing Strategies**

   - **Treat Spurious Failures as Threading Issues**

     - Don't dismiss failures as "one-offs"
     - Investigate all failures thoroughly
     - Assume failures indicate real problems

   - **Get Non-threaded Code Working First**

     - Create POJOs for business logic
     - Test business logic independently
     - Separate thread-aware and thread-ignorant code

   - **Make Code Pluggable and Tunable**
     - Support different thread configurations
     - Allow for test doubles
     - Enable performance tuning
     - Support variable execution speeds

3. **Testing Environment**

   - Run with more threads than processors
   - Test on all target platforms
   - Use different system configurations
   - Test under various loads

4. **Code Instrumentation**

   - **Purpose**: Force different execution paths
   - **Methods**:

     - Object.wait()
     - Thread.sleep()
     - Thread.yield()
     - Thread.setPriority()

   - **Approaches**:

     ```java
     // Hand-coded instrumentation
     public synchronized String nextUrlOrNull() {
         if (hasNext()) {
             String url = urlGenerator.next();
             Thread.yield(); // Inserted for testing
             updateHasNext();
             return url;
         }
         return null;
     }

     // Automated instrumentation
     public class ThreadJigglePoint {
         public static void jiggle() {
             // Randomly choose between:
             // - doing nothing
             // - sleeping
             // - yielding
         }
     }
     ```

5. **Best Practices**
   - Start testing early in development
   - Use automated testing tools when possible
   - Consider specialized tools like ConTest
   - Run tests continuously
   - Document test configurations
   - Monitor test results carefully
