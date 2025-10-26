
## **1. StatelessWidget**

### **Formal Explanation**

* A `StatelessWidget` is a widget that **cannot change its state** once built.
* Its content is **immutable** — it depends entirely on the input **properties** you pass to it.
* Flutter will **rebuild** it only when its **parent widget changes** or when external input changes.

### **Layman Explanation**

* Imagine a **photo frame** hanging on the wall.
* Once you put a photo in it, it **doesn’t change on its own**. If you want a new photo, someone must replace it.
* That’s exactly like a StatelessWidget — it just **displays what you give it** and doesn’t remember anything internally.

### **Example**

```dart
import 'package:flutter/material.dart';

class MyStatelessWidget extends StatelessWidget {
  final String text;

  MyStatelessWidget({required this.text});

  @override
  Widget build(BuildContext context) {
    return Center(
      child: Text(
        text,
        style: TextStyle(fontSize: 24),
      ),
    );
  }
}

// Usage:
// MyStatelessWidget(text: "Hello Flutter!")
```

✅ **Key Points**

* No internal state variable.
* Efficient because Flutter doesn’t need to manage state.
* Good for **UI that doesn’t change dynamically**.

---

## **2. StatefulWidget**

### **Formal Explanation**

* A `StatefulWidget` is a widget that **can change its state** during its lifecycle.
* It is associated with a separate `State` class where **mutable variables** are stored.
* When the state changes, call `setState()` to **trigger a rebuild** of the widget.

### **Layman Explanation**

* Imagine a **scoreboard in a game**.
* The numbers on it **keep changing** as the game progresses.
* That’s like a StatefulWidget — it can **remember and update its internal data**.

### **Structure**

A StatefulWidget has **two classes**:

1. **The Widget Class**: Immutable, defines the widget itself.
2. **The State Class**: Mutable, holds variables that can change and triggers rebuilds.

### **Example**

```dart
import 'package:flutter/material.dart';

class MyCounterWidget extends StatefulWidget {
  @override
  _MyCounterWidgetState createState() => _MyCounterWidgetState();
}

class _MyCounterWidgetState extends State<MyCounterWidget> {
  int counter = 0;

  void incrementCounter() {
    setState(() {
      counter++; // Updates the state and rebuilds UI
    });
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisAlignment: MainAxisAlignment.center,
      children: [
        Text(
          'Counter: $counter',
          style: TextStyle(fontSize: 24),
        ),
        SizedBox(height: 20),
        ElevatedButton(
          onPressed: incrementCounter,
          child: Text('Increment'),
        ),
      ],
    );
  }
}

// Usage in a Scaffold:
// MyCounterWidget()
```

✅ **Key Points**

* Use **StatefulWidget** if the UI **changes over time** (user interaction, API call, animations).
* Call `setState()` to update **only the parts of UI affected**.
* Has a **lifecycle**: `initState()`, `build()`, `dispose()`, etc.

---

## **3. Differences Between StatelessWidget & StatefulWidget**

| Feature           | StatelessWidget               | StatefulWidget                        |
| ----------------- | ----------------------------- | ------------------------------------- |
| Mutable State     | ❌ No                          | ✅ Yes                                 |
| Rebuild Trigger   | When parent changes or inputs | When `setState()` is called           |
| Lifecycle Methods | Only `build()`                | `initState()`, `build()`, `dispose()` |
| Use Case          | Static UI                     | Dynamic UI, user interactions         |
| Efficiency        | More efficient                | Slightly less due to state tracking   |

---

## **4. When to Use Which**

* **StatelessWidget:** Static screens like Splash, Logo, Info pages.
* **StatefulWidget:** Counters, forms, animations, interactive buttons, API data updates.

---

### ✅ **Takeaways**

1. **StatelessWidget** → immutable, simple, faster.
2. **StatefulWidget** → mutable, dynamic, can update itself.
3. **setState()** is key for StatefulWidgets to rebuild UI.
4. Always decide **based on whether the widget needs to change over time**.

---

