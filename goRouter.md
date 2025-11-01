

## 🧭 GO_ROUTER — Step-by-Step Structure & Explanation

### 🪄 Overview

When you use `GoRouter`, you don’t do navigation imperatively like:

```dart
Navigator.push(context, MaterialPageRoute(builder: (context) => Home()));
```

Instead, you **declare all your routes in one place**, and then navigate using URLs like `/home`, `/profile`, etc.

This makes your navigation:

* **Cleaner**
* **Scalable**
* **Declarative (State-driven)**
* **Web-friendly**

---

## 📁 Project Structure (Example)

Here’s how a Flutter project using GoRouter usually looks:

```
lib/
 ├── main.dart
 ├── router/
 │    └── app_router.dart   👈 (GoRouter setup)
 ├── screens/
 │    ├── home_screen.dart
 │    ├── profile_screen.dart
 │    └── settings_screen.dart
 └── widgets/
      └── common_widgets.dart
```

---

## 🧩 Step 1: Create Screens

define a few basic screens first
---

## ⚙️ Step 2: Define the Router Configuration

Now create a new file called `app_router.dart` inside a `router` folder.

```dart
// lib/router/app_router.dart
import 'package:go_router/go_router.dart';
import 'package:flutter/material.dart';
import '../screens/home_screen.dart';
import '../screens/profile_screen.dart';
import '../screens/settings_screen.dart';

// Step 1️⃣: Create a global GoRouter instance
final GoRouter appRouter = GoRouter(
  routes: [
    // Step 2️⃣: Define routes
    GoRoute(
      path: '/',
      name: 'home',
      builder: (context, state) => const HomeScreen(),
    ),
    GoRoute(
      path: '/profile',
      name: 'profile',
      builder: (context, state) => const ProfileScreen(),
    ),
    GoRoute(
      path: '/settings',
      name: 'settings',
      builder: (context, state) => const SettingsScreen(),
    ),
  ],
);
```

---

## 🏗️ Step 3: Connect Router to App

In `main.dart`:

```dart
import 'package:flutter/material.dart';
import 'router/app_router.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp.router(
      routerConfig: appRouter, // ✅ Using GoRouter
      title: 'GoRouter Demo',
      theme: ThemeData(primarySwatch: Colors.teal),
    );
  }
}
```

---

## 🧩 Step 4: Navigation in Action

Now navigation becomes **super simple** anywhere in your app:

| Action              | Code                          | Description                     |
| ------------------- | ----------------------------- | ------------------------------- |
| Navigate to a route | `context.go('/profile')`      | Replaces the current screen     |
| Push on top         | `context.push('/settings')`   | Pushes new screen over old one  |
| Navigate back       | `context.pop()`               | Goes to previous screen         |
| Named route         | `context.goNamed('settings')` | Uses route name instead of path |

---

## 🔍 Breakdown of `GoRoute` Components

Each `GoRoute` object in your configuration has specific parts:

```dart
GoRoute(
  path: '/profile',                     // 🔹 URL path (like a route)
  name: 'profile',                      // 🔹 Optional name for named navigation
  builder: (context, state) {           // 🔹 Function that builds the widget for this route
    return ProfileScreen();             // 🔹 Screen widget
  },
),
```

### 🧠 Let’s explain each property:

| Property   | Description                                               |
| ---------- | --------------------------------------------------------- |
| `path`     | Defines the URL route (like `/`, `/home`, `/product/:id`) |
| `name`     | Optional name for route (useful for `goNamed()`)          |
| `builder`  | Function that returns the screen (Widget) to show         |
| `routes`   | List of child routes (nested routes)                      |
| `redirect` | Optional logic to redirect (e.g., login required)         |

---

## 🧠 Example: Dynamic Route (Passing Data)

```dart
GoRoute(
  path: '/product/:id',
  name: 'product',
  builder: (context, state) {
    final id = state.pathParameters['id'];  // extract value
    return ProductScreen(productId: id!);
  },
);
```

Then navigate:

```dart
context.go('/product/101');
```

or using name:

```dart
context.goNamed('product', pathParameters: {'id': '101'});
```

---

## 🧭 Example: Redirect / Guard (Authentication Flow)

```dart
final GoRouter appRouter = GoRouter(
  routes: [
    GoRoute(path: '/', builder: (context, state) => const HomeScreen()),
    GoRoute(path: '/login', builder: (context, state) => const LoginScreen()),
  ],
  redirect: (context, state) {
    final loggedIn = AuthService.isLoggedIn;
    final loggingIn = state.subloc == '/login';

    if (!loggedIn && !loggingIn) return '/login';
    if (loggedIn && loggingIn) return '/';
    return null;
  },
);
```

💡 This means:

* If not logged in → always go to `/login`
* If already logged in → can’t go to `/login`

---

## 🚦 Visual Summary of Flow

```
User clicks button ➜ context.go('/profile')
       ↓
GoRouter looks up '/profile'
       ↓
Calls builder function for that route
       ↓
Builds ProfileScreen widget
       ↓
Shows it on screen
```

---

## ✅ TAKEAWAYS

1. **`GoRouter` centralizes navigation** — all routes are declared in one place.
2. Use `MaterialApp.router` with `routerConfig` instead of `MaterialApp`.
3. **Navigate** using simple functions: `context.go()`, `context.push()`, `context.pop()`.
4. Supports **named routes**, **path parameters**, **deep links**, and **redirects**.
5. Perfect for **big apps** with login, nested tabs, or multiple screens.


## 🧭 What Is `ShellRoute`?

`ShellRoute` is a special GoRouter feature that allows you to define a **common UI shell** (like a bottom navigation bar or drawer) that stays **persistent**, while the inner pages (tabs) change inside it.

✅ Think of it like this:

```
[ BottomNavigationBar ]  ← stays the same
      ↓
[ Home | Profile | Settings ]  ← changes inside
```

Without `ShellRoute`, switching tabs would rebuild the whole app every time.
With `ShellRoute`, only the **child screen changes**, keeping the navigation bar and state consistent.

---

## 🧱 Example Project Structure

```
lib/
 ├── main.dart
 ├── router/app_router.dart
 ├── screens/
 │    ├── home_screen.dart
 │    ├── profile_screen.dart
 │    └── settings_screen.dart
 └── widgets/
      └── bottom_nav.dart
```

---

## 🪄 Step 1: Create Screens

---

## 🧩 Step 2: Create the Shell Layout (Bottom Navigation)

This widget will act as the **common shell** that wraps all tab screens.

```dart
// widgets/bottom_nav.dart
import 'package:flutter/material.dart';
import 'package:go_router/go_router.dart';

class BottomNav extends StatelessWidget {
  final Widget child;
  const BottomNav({super.key, required this.child});

  @override
  Widget build(BuildContext context) {
    final currentPath = GoRouterState.of(context).uri.toString();

    int currentIndex;
    if (currentPath.startsWith('/profile')) {
      currentIndex = 1;
    } else if (currentPath.startsWith('/settings')) {
      currentIndex = 2;
    } else {
      currentIndex = 0;
    }

    return Scaffold(
      body: child,
      bottomNavigationBar: BottomNavigationBar(
        currentIndex: currentIndex,
        onTap: (index) {
          switch (index) {
            case 0:
              context.go('/home');
              break;
            case 1:
              context.go('/profile');
              break;
            case 2:
              context.go('/settings');
              break;
          }
        },
        items: const [
          BottomNavigationBarItem(icon: Icon(Icons.home), label: 'Home'),
          BottomNavigationBarItem(icon: Icon(Icons.person), label: 'Profile'),
          BottomNavigationBarItem(icon: Icon(Icons.settings), label: 'Settings'),
        ],
      ),
    );
  }
}
```

✅ This layout:

* Shows a **BottomNavigationBar**
* Displays the active child screen above it
* Keeps the bottom bar visible across tabs

---

## 🗺️ Step 3: Define GoRouter with ShellRoute

Now define your router setup.

```dart
// router/app_router.dart
import 'package:flutter/material.dart';
import 'package:go_router/go_router.dart';
import '../screens/home_screen.dart';
import '../screens/profile_screen.dart';
import '../screens/settings_screen.dart';
import '../widgets/bottom_nav.dart';

final GoRouter appRouter = GoRouter(
  routes: [
    ShellRoute(
      builder: (context, state, child) {
        return BottomNav(child: child); // 🧩 Persistent bottom nav
      },
      routes: [
        GoRoute(
          path: '/home',
          builder: (context, state) => const HomeScreen(),
        ),
        GoRoute(
          path: '/profile',
          builder: (context, state) => const ProfileScreen(),
        ),
        GoRoute(
          path: '/settings',
          builder: (context, state) => const SettingsScreen(),
        ),
      ],
    ),
  ],
  initialLocation: '/home',
);
```

### Explanation:

| Part              | Description                                   |
| ----------------- | --------------------------------------------- |
| `ShellRoute`      | Defines a persistent UI wrapper (bottom nav)  |
| `builder`         | Returns a widget that wraps the child screens |
| `child`           | The currently active tab content              |
| `routes`          | The actual tab screens inside the shell       |
| `initialLocation` | Default screen to show at startup             |

---

## 🚀 Step 4: Connect in `main.dart`

```dart
// main.dart
import 'package:flutter/material.dart';
import 'router/app_router.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp.router(
      routerConfig: appRouter,
      title: 'GoRouter ShellRoute Demo',
      theme: ThemeData(primarySwatch: Colors.indigo),
    );
  }
}
```

---

## 🧠 How It Works

When you tap a bottom nav item:

1. The `onTap()` calls `context.go('/profile')`.
2. GoRouter switches to the new route (`/profile`).
3. The `ShellRoute` keeps the same parent widget (`BottomNav`).
4. Only the child (`ProfileScreen`) is swapped inside.
5. Bottom bar stays **persistent**, and app state remains intact.

---

## 🧭 Visual Flow

```
MaterialApp.router
        ↓
     GoRouter
        ↓
    ShellRoute ───> BottomNav (Scaffold + BottomNavigationBar)
         ↓
     GoRoute('/home') → HomeScreen
     GoRoute('/profile') → ProfileScreen
     GoRoute('/settings') → SettingsScreen
```

---


## ✅ TAKEAWAYS

1. Use **ShellRoute** to keep a **persistent layout** (e.g., bottom nav, drawer).
2. The **child** inside `ShellRoute` changes when routes change.
3. Perfect for **multi-tab apps** where each tab should maintain its own state.
4. Navigation happens via `context.go('/route')`.
5. Use `initialLocation` to set the default tab.

---

