

# **GoRouter in Flutter**

---

## **1. What is GoRouter?**

* **GoRouter** is an official Flutter package for navigation.
* It provides:

  * **Declarative routing** (routes as objects rather than imperative push/pop)
  * **Nested routing**
  * **Deep linking support**
  * **Authentication guards / redirects**

> Think of it as a **URL-based router**, similar to routing in web frameworks.

---

## **2. Installing GoRouter**

```yaml
dependencies:
  go_router: ^10.0.0
```

---

## **3. Basic Setup**

```dart
final router = GoRouter(
  routes: [
    GoRoute(
      path: '/',
      builder: (context, state) => HomeScreen(),
    ),
    GoRoute(
      path: '/profile/:id',
      builder: (context, state) {
        final id = state.params['id']!;
        return ProfileScreen(id: id);
      },
    ),
  ],
);
```

* **path:** route URL
* **builder:** widget to show for that route
* **params:** dynamic segments in the path (`/profile/:id` → `id`)

---

## **4. Navigating Between Screens**

### **A. Navigate to a route**

```dart
// Simple navigation
context.go('/profile/42');
```

* `go()` → replaces the current route
* `push()` → pushes a new route on top (like Navigator.push)

---

### **B. Passing Query Parameters**

```dart
context.go('/profile/42?tab=posts');

final tab = state.queryParams['tab']; // 'posts'
```

---

### **C. Returning Data**

```dart
// Similar to Navigator.pop
context.pop('result');
```

---

## **5. Nested Routes**

* Useful for **tabs or subpages**

```dart
GoRoute(
  path: '/dashboard',
  builder: (context, state) => DashboardScreen(),
  routes: [
    GoRoute(
      path: 'settings',
      builder: (context, state) => SettingsScreen(),
    ),
  ],
);
```

* Navigate: `/dashboard/settings` → renders nested screen.

---

## **6. Redirects & Auth Guards**

* Redirect users if not authenticated:

```dart
final router = GoRouter(
  routes: [...],
  redirect: (context, state) {
    final loggedIn = AuthService.isLoggedIn;
    final loggingIn = state.location == '/login';
    if (!loggedIn && !loggingIn) return '/login';
    if (loggedIn && loggingIn) return '/';
    return null; // no redirect
  },
);
```

---

## **7. Real-World Advantages**

1. **Declarative Routing**

   * Easier to read and maintain in large apps
   * Compatible with Flutter web
2. **Deep Linking**

   * Supports URL parameters and query parameters
3. **Nested & Subroutes**

   * Perfect for tabbed layouts or nested pages
4. **Authentication & Redirects**

   * Handle login flows and guards declaratively
5. **Cleaner than Navigator**

   * Avoids imperative push/pop boilerplate

---

### ✅ **Key Takeaways**

* **GoRouter** = modern, declarative routing solution for Flutter.
* Supports **nested routes, dynamic params, query params, deep links, and auth guards**.
* Replace complex Navigator logic → **centralized and maintainable**.
* Use **`context.go()`** for replacing, **`context.push()`** for stacking, and **`context.pop()`** for returning data.
* Ideal for **large apps or apps with web support**.

---

# **1. What is a “Route” and “GoRoute”**

* **Route** = a **screen/page in your app**.

  * Example: HomeScreen, ProfileScreen, SettingsScreen.
* **GoRoute** = the **GoRouter way to define a route**.

  * It contains:

    * `path` → URL path (`/profile/:id`)
    * `builder` → function that returns the widget for that route
    * `routes` → optional **nested routes** (subpages)

**Example:**

```dart
GoRoute(
  path: '/profile/:id',
  builder: (context, state) {
    final id = state.params['id']!; // access dynamic id
    return ProfileScreen(id: id);
  },
);
```

---

# **2. What is GoRouter?**

* **GoRouter** = the **main router object** that manages all your routes.
* It:

  * Knows all routes in your app
  * Handles **navigation, nested routes, redirects, and deep linking**
* You assign it to your app:

```dart
final router = GoRouter(
  routes: [
    GoRoute(path: '/', builder: (context, state) => HomeScreen()),
    GoRoute(path: '/profile/:id', builder: (context, state) => ProfileScreen(id: state.params['id']!)),
  ],
);
```

* And then use in `MaterialApp.router`:

```dart
MaterialApp.router(
  routerConfig: router,
);
```

---

# **3. What is “state” in GoRouter**

* The **`state` parameter** in `builder: (context, state)` contains **all the info about the route**:

  1. `state.params` → dynamic path segments (`/profile/:id` → id)
  2. `state.queryParams` → query parameters (`/profile/42?tab=posts`)
  3. `state.location` → full route URL (`/profile/42?tab=posts`)

**Example:**

```dart
GoRoute(
  path: '/profile/:id',
  builder: (context, state) {
    final userId = state.params['id']!;
    final tab = state.queryParams['tab'];
    return ProfileScreen(id: userId, initialTab: tab);
  },
);
```

---

# **4. What is Nested Routing**

* Nested routing = **routes inside routes**, useful for subpages or tabs.
* Example: Dashboard has Settings as a subpage:

```dart
GoRoute(
  path: '/dashboard',
  builder: (context, state) => DashboardScreen(),
  routes: [
    GoRoute(
      path: 'settings', // note: no leading slash
      builder: (context, state) => SettingsScreen(),
    ),
  ],
);
```

* Access nested route: `/dashboard/settings` → shows SettingsScreen inside the stack.

**Why Nested Routes?**

* Organize routes hierarchically
* Share layout between parent and child routes (like a common AppBar)
* Useful for tabs or dashboards

---

# **5. How to Access Nested Routes**

* Use **full path** in `context.go()` or `context.push()`:

```dart
context.go('/dashboard/settings');
```

* Access **route parameters or query params** via `state` in the nested route:

```dart
final tab = state.queryParams['tab'];
```

---

# **6. Redirects in GoRouter**

* **Redirect** = automatically navigate to a different route based on a condition.
* Common use: **authentication guard**

  * Example: redirect to login if user is not logged in

```dart
final router = GoRouter(
  routes: [...],
  redirect: (context, state) {
    final loggedIn = AuthService.isLoggedIn;
    final loggingIn = state.location == '/login';
    
    if (!loggedIn && !loggingIn) return '/login'; // force login
    if (loggedIn && loggingIn) return '/'; // already logged in
    return null; // no redirect
  },
);
```

* **How it works:**

  1. User tries to go to `/dashboard`
  2. `redirect` function checks `loggedIn`
  3. If false → automatically goes to `/login`

---

# **7. Summary Table**

| Concept           | Explanation                                                       |
| ----------------- | ----------------------------------------------------------------- |
| **GoRouter**      | Main router object managing all routes                            |
| **GoRoute**       | Defines a single route (path, builder, optional nested routes)    |
| **state**         | Info about the current route (params, queryParams, location)      |
| **Nested routes** | Routes inside routes → useful for subpages/tabs                   |
| **Redirect**      | Conditionally navigates user to another route (e.g., auth checks) |

---

### ✅ **Key Takeaways**

1. **GoRouter** → central routing system.
2. **GoRoute** → defines screens, dynamic paths, and nested routes.
3. **state** → contains params, query params, location.
4. **Nested routing** → organize complex layouts and dashboards.
5. **Redirect** → automatically route users based on conditions like auth.

---


