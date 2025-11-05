


# 🚀 **Flutter Cheatsheet (2025 Edition)**

---

## 🌳 **1. Flutter Core Concepts**

| Concept             | Explanation                                                                                             | Example                                      |
| ------------------- | ------------------------------------------------------------------------------------------------------- | -------------------------------------------- |
| **Widget**          | Everything in Flutter is a widget — UI elements or structural components.                               | `Text('Hello')`, `Column()`, `MaterialApp()` |
| **Widget Tree**     | Hierarchical arrangement of widgets that make up your UI.                                               | Parent → Child → Sub-child                   |
| **StatelessWidget** | UI that doesn’t change once built.                                                                      | `class MyWidget extends StatelessWidget`     |
| **StatefulWidget**  | UI that changes over time (using `setState`).                                                           | `class MyWidget extends StatefulWidget`      |
| **BuildContext**    | Reference to a widget’s location in the widget tree (used to access theme, navigation, etc.).           | `Theme.of(context)`                          |
| **mounted**         | Checks if a `State` object is still part of the widget tree (avoid calling `setState()` after unmount). | `if (mounted) setState(() {});`              |
| **Hot Reload**      | Preserves state, reloads UI instantly.                                                                  | For UI tweaks.                               |
| **Hot Restart**     | Restarts entire app (loses state).                                                                      | For logic changes.                           |

---

## 🔄 **2. Widget Lifecycle (StatefulWidget)**

1. `createState()` → creates the state object
2. `initState()` → called once when widget is inserted
3. `build()` → called whenever UI needs rebuild
4. `didUpdateWidget()` → when parent widget changes
5. `dispose()` → cleanup (controllers, streams)

🧠 **Tip:** Place listeners and animation setup in `initState()`, cleanup in `dispose()`.

---

## ⚙️ **3. Layout System**

| Widget            | Purpose                                 | Example                                    |
| ----------------- | --------------------------------------- | ------------------------------------------ |
| **Row / Column**  | Arrange widgets horizontally/vertically | `Row(children: [...])`                     |
| **Expanded**      | Expands child to fill available space   | `Expanded(child: Container())`             |
| **Spacer**        | Adds flexible empty space               | `Spacer()`                                 |
| **Flexible**      | Like Expanded, but with more control    | `Flexible(fit: FlexFit.tight)`             |
| **Container**     | Basic box with padding, color, etc.     | `Container(color: Colors.blue)`            |
| **Stack**         | Overlapping widgets                     | `Stack(children: [bg, text])`              |
| **Center, Align** | Positioning                             | `Align(alignment: Alignment.bottomCenter)` |
| **SizedBox**      | Fixed size spacer                       | `SizedBox(height: 20)`                     |

🧠 **Layout Rule:**
Flutter layout = **constraints → sizes → positions**.
Every widget tells its children how big they can be, and children tell parents how big they want to be.

---

## 🧱 **4. Keys & Widget Identity**

| Concept                  | Why It Matters                                                         |
| ------------------------ | ---------------------------------------------------------------------- |
| **Key**                  | Helps Flutter identify which widget changed after rebuild.             |
| **GlobalKey**            | Used when you need access to a widget’s state (e.g., form validation). |
| **ValueKey / ObjectKey** | Useful in lists to keep item identity when order changes.              |

🧠 **Example:**

```dart
ListView(
  children: [
    TodoItem(key: ValueKey(todo.id), todo: todo),
  ],
);
```

Without keys → Flutter may rebuild wrong widget after reordering.

---

## 🧭 **5. Navigation**

| Method             | Description                                                  | Example                                                               |
| ------------------ | ------------------------------------------------------------ | --------------------------------------------------------------------- |
| **Navigator.push** | Push new screen on top                                       | `Navigator.push(context, MaterialPageRoute(builder: (_) => Page2()))` |
| **Navigator.pop**  | Go back                                                      | `Navigator.pop(context)`                                              |
| **Named Routes**   | Define names for routes                                      | `Navigator.pushNamed(context, '/profile')`                            |
| **GoRouter**       | Modern, declarative navigation with deep links and redirects | `context.go('/home')`                                                 |

---

## 🧩 **6. GoRouter (Modern Navigation)**

| Feature            | Example                                                      |
| ------------------ | ------------------------------------------------------------ |
| **Define routes**  | `GoRoute(path: '/', builder: (context, state) => Home())`    |
| **Nested routes**  | `routes: [GoRoute(path: 'details', builder: ...)]`           |
| **Redirect**       | `redirect: (context, state) => isLoggedIn ? null : '/login'` |
| **Dynamic params** | `/user/:id → state.params['id']`                             |
| **Query params**   | `/user?id=12 → state.queryParams['id']`                      |

🧠 **Best Practice:** Use `GoRouter` for scalable apps with authentication and deep links.

---

## 🔁 **7. State Management**

| Type                    | Description                    | Example                   |
| ----------------------- | ------------------------------ | ------------------------- |
| **setState**            | Simple, local state updates    | `setState(() => count++)` |
| **InheritedWidget**     | Pass data down the widget tree |                           |
| **Provider / Riverpod** | Reactive, global state mgmt    |                           |
| **Bloc / Cubit**        | Event-driven state management  |                           |
| **Recoil / MobX**       | Alternative reactive systems   |                           |

🧠 **Choose depending on app size:**

* Small apps → `setState`
* Medium → `Provider` / `Riverpod`
* Large, reactive → `Bloc`, `Riverpod`

---

## ⚡ **8. Async & Streams**

| Concept           | Description                      | Example                                            |
| ----------------- | -------------------------------- | -------------------------------------------------- |
| **Future**        | Represents a single async result | `Future.delayed(Duration(seconds: 1))`             |
| **async / await** | Wait for future result           | `await fetchData()`                                |
| **Stream**        | Sequence of async values         | `Stream.periodic(Duration(seconds: 1))`            |
| **StreamBuilder** | Rebuilds UI on new stream events | `StreamBuilder(stream: myStream, builder: ...)`    |
| **FutureBuilder** | Builds UI from Future result     | `FutureBuilder(future: fetchData(), builder: ...)` |

---

## 💾 **9. Local Storage**

| Type                            | Use Case                                                                                          |
| ------------------------------- | ------------------------------------------------------------------------------------------------- |
| **SharedPreferences**           | Simple key-value pairs (tokens, theme)                                                            |
| **Hive**                        | Lightweight local DB (offline caching)                                                            |
| **Sqflite / Drift**             | Relational data with SQL                                                                          |
| **Local DB helps offline apps** | It lets you **cache posts, recent searches, and tokens** → app still works offline & syncs later. |

---

## 🎨 **10. Theming & Styling**

| Concept          | Example                                                           |                                                |
| ---------------- | ----------------------------------------------------------------- | ---------------------------------------------- |
| **ThemeData**    | Global app theme                                                  | `theme: ThemeData(primarySwatch: Colors.blue)` |
| **Dark Mode**    | `themeMode: ThemeMode.system`                                     |                                                |
| **TextTheme**    | Reuse text styles                                                 | `Theme.of(context).textTheme.bodyMedium`       |
| **Custom Fonts** | Add to `pubspec.yaml` → use in `TextStyle(fontFamily: 'Poppins')` |                                                |

---

## 🔧 **11. Performance Tips**

✅ Use `const` widgets
✅ Minimize rebuilds
✅ Use `ListView.builder` for long lists
✅ Avoid heavy layouts inside loops
✅ Use `RepaintBoundary` for isolated redraws

---

## 🧠 **12. Architecture & Best Practices**

| Concept                       | Description                                                  |
| ----------------------------- | ------------------------------------------------------------ |
| **MVVM / Clean Architecture** | Separate UI (View), Logic (ViewModel), and Data (Repository) |
| **Immutable State**           | Avoid direct mutation, rebuild from copies                   |
| **Repository Layer**          | Wrap Firebase, REST, or local DB calls                       |
| **Dependency Injection**      | Pass dependencies via Provider/Riverpod                      |

🧩 **Rule of Thumb:**

> UI = reactive + declarative
> Logic = pure functions or streams
> Data = repository abstraction

---

## 🪄 **13. Common Commands**

| Command             | Description      |
| ------------------- | ---------------- |
| `flutter doctor`    | Check setup      |
| `flutter run`       | Run app          |
| `flutter pub get`   | Get dependencies |
| `flutter build apk` | Build release    |
| `flutter clean`     | Clear cache      |
| `flutter format .`  | Format all code  |

---

## ✅ **14. Quick Mental Map**

```
Flutter App
│
├── MaterialApp.router (GoRouter)
│
├── Widget Tree
│     ├── StatelessWidget
│     └── StatefulWidget
│
├── Layout (Row, Column, Stack)
│
├── State (setState, Provider, Riverpod)
│
├── Async (FutureBuilder, StreamBuilder)
│
├── Local Storage (Hive, SharedPreferences)
│
└── Theme, Keys, Performance
```

---

### 🌟 **Key Takeaways**

* Flutter = **Everything is a widget**
* Use **GoRouter** for scalable navigation
* **BuildContext** defines location of widgets
* **Keys** preserve widget identity
* **State** drives the UI → when it changes, Flutter rebuilds the tree
* **Local DB** = crucial for offline support
* Keep logic out of UI → use **ViewModels / Providers**

---

