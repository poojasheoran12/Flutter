
## **1. Basics of Navigation**

Flutter uses a **Navigator** stack. Every screen (page) is a **route**, and navigation is like **pushing and popping routes** on a stack.

* **Push** → go to a new screen.
* **Pop** → go back to the previous screen.

---

### **Step 1: Create Two Screens**

```dart
import 'package:flutter/material.dart';

class HomeScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Home')),
      body: Center(
        child: ElevatedButton(
          child: Text('Go to Details'),
          onPressed: () {
            Navigator.push(
              context,
              MaterialPageRoute(builder: (context) => DetailsScreen()),
            );
          },
        ),
      ),
    );
  }
}

class DetailsScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Details')),
      body: Center(
        child: ElevatedButton(
          child: Text('Go Back'),
          onPressed: () {
            Navigator.pop(context);
          },
        ),
      ),
    );
  }
}
```

**Layman explanation:**

* `Navigator.push` → move forward.
* `Navigator.pop` → go back.
* Think of it like a **stack of pages**.

---

## **2. Passing Data Between Screens**

### **Step 1: Passing Data Forward**

```dart
Navigator.push(
  context,
  MaterialPageRoute(
    builder: (context) => DetailsScreen(data: 'Hello from Home!'),
  ),
);
```

```dart
class DetailsScreen extends StatelessWidget {
  final String data;
  DetailsScreen({required this.data});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(child: Text(data)),
    );
  }
}
```

---

### **Step 2: Returning Data Back**

```dart
// In DetailsScreen
ElevatedButton(
  child: Text('Send Back Result'),
  onPressed: () {
    Navigator.pop(context, 'This is the result!');
  },
);
```

```dart
// In HomeScreen
void _navigateAndReceiveData(BuildContext context) async {
  final result = await Navigator.push(
    context,
    MaterialPageRoute(builder: (context) => DetailsScreen()),
  );
  print('Received: $result');
}
```

---

## **3. Named Routes (Optional, Cleaner for Big Apps)**

### **Step 1: Define Routes**

```dart
MaterialApp(
  initialRoute: '/',
  routes: {
    '/': (context) => HomeScreen(),
    '/details': (context) => DetailsScreen(),
  },
);
```

### **Step 2: Navigate**

```dart
Navigator.pushNamed(context, '/details');
```

### **Step 3: Go Back**

```dart
Navigator.pop(context);
```

---

## **4. Advanced Navigation Tips**

1. Use `Navigator.pushReplacement` → replace current screen.
2. Use `Navigator.pushAndRemoveUntil` → clear the stack (for login/logout flows).
3. For state management (Recoil, Provider, Bloc), navigation works the same, just wrap screens with your providers.

---

### ✅ **Takeaways**

1. **Navigator stack** controls screens.
2. `push` → forward, `pop` → back.
3. You can **pass data forward** by constructor and **back** by `Navigator.pop(context, result)`.
4. Named routes make navigation cleaner for large apps.

---

