# ThreadLocal in WebDriver Management

## What is ThreadLocal?
`ThreadLocal` is a Java class that provides thread-local variables. Each thread accessing such a variable gets its own, independently initialized copy of the variable. This ensures that changes made in one thread do not affect the variable in another thread.

In Selenium, `ThreadLocal` is used to ensure that each test thread has its own WebDriver instance, isolating browser sessions during parallel execution.

---

## Why Did We Use ThreadLocal?
We use `ThreadLocal<WebDriver>` in our framework to address the following challenges:

1. **Thread Safety**: Ensures each test thread has an independent WebDriver instance, preventing conflicts.
2. **Parallel Execution**: Supports running tests simultaneously without interference.
3. **Scalability**: Allows execution of large test suites across multiple threads.
4. **Cleaner Code**: Centralized WebDriver management makes the test code easier to maintain.

---

## How to Enable ThreadLocal?

### 1. Implementation in the Framework
We use a `DriverManager` class to manage `ThreadLocal<WebDriver>`. Below is an example:

```java
public class DriverManager {
    private static ThreadLocal<WebDriver> driver = new ThreadLocal<>();

    public static WebDriver getDriver() {
        return driver.get();
    }

    public static void setDriver(WebDriver driverInstance) {
        driver.set(driverInstance);
    }

    public static void removeDriver() {
        driver.get().quit();
        driver.remove();
    }
}
```

### 2. Enable Parallel Execution
To enable parallel execution with TestNG:

- Configure the `testng.xml` file to specify the parallel mode and thread count:
```xml
<suite name="Test Suite" parallel="tests" thread-count="4">
    <test name="Test 1">
        <classes>
            <class name="com.example.tests.SampleTest"/>
        </classes>
    </test>
</suite>
```
- Set the `parallel` attribute to `tests`, `methods`, or `classes` depending on your requirements.
- Adjust the `thread-count` for the desired level of parallelism.

### 3. What Can Happen If We Don't Use ThreadLocal?
If `ThreadLocal` is not used for managing WebDriver instances:

1. Shared WebDriver Instances:
- All threads share the same WebDriver instance, causing conflicts.
- Tests might navigate to incorrect URLs or interact with wrong elements.
- Browser sessions might close unexpectedly, affecting other tests.

2. Unstable Tests:
- Tests fail intermittently due to session interference.
- Debugging becomes challenging due to inconsistent results.
- Scalability Issues:

3. Running tests in parallel becomes unreliable.
- The framework cannot support large-scale or high-speed test executions.


