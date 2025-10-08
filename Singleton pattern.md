There are multiple ways to implement the **Singleton Design Pattern** in Java, depending on requirements like *lazy initialization*, *thread safety*, and *serialization safety*.

---

### **1. Eager Initialization (Simple, but may waste memory)**

```java
public class Singleton {
    private static final Singleton instance = new Singleton();
    private Singleton() {} // private constructor
    public static Singleton getInstance() { return instance; }
}
```

✅ Thread-safe (JVM loads it once)
❌ Instance is created even if never used

---

### **2. Lazy Initialization (Not thread-safe)**

```java
public class Singleton {
    private static Singleton instance;
    private Singleton() {}
    public static Singleton getInstance() {
        if(instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

✅ Created only when needed
❌ Not thread-safe (can create multiple instances in concurrent threads)

---

### **3. Thread-safe using `synchronized`**

```java
public class Singleton {
    private static Singleton instance;
    private Singleton() {}
    public static synchronized Singleton getInstance() {
        if(instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

✅ Thread-safe
❌ Slower due to synchronization on every call

---

### **4. Double-Checked Locking (Best practice)**

```java
public class Singleton {
    private static volatile Singleton instance;
    private Singleton() {}
    public static Singleton getInstance() {
        if(instance == null) {
            synchronized(Singleton.class) {
                if(instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

✅ Thread-safe
✅ Lazy initialization
✅ Efficient (sync happens only once)

---

### **5. Bill Pugh Singleton (Recommended modern approach)**

```java
public class Singleton {
    private Singleton() {}
    private static class Helper {
        private static final Singleton INSTANCE = new Singleton();
    }
    public static Singleton getInstance() {
        return Helper.INSTANCE;
    }
}
```

✅ Thread-safe
✅ Lazy initialization
✅ No synchronization overhead

---

### **6. Enum Singleton (Serialization-safe)**

```java
public enum Singleton {
    INSTANCE;
    public void doSomething() {
        System.out.println("Doing something...");
    }
}
```

✅ Thread-safe
✅ Serialization-safe
✅ Handles reflection attacks

---

**Recommended approach:**
👉 Use **Bill Pugh Singleton** for general use cases.
👉 Use **Enum Singleton** when you need protection against serialization or reflection issues.
