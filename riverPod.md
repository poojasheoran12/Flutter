
## 🧠 What is Riverpod?

Think of **Riverpod** as a **smart manager for your app’s data and logic**.
It tells your Flutter widgets **what data to use** and **when to rebuild** when that data changes.

---

### 💡 Example:

Imagine you have a counter app.
Without Riverpod, you’d store the counter in your widget’s `setState`.

But with **Riverpod**, you can say:

> “Hey Riverpod, store my counter number and tell my widgets when it changes.”

So if you increase the counter, Riverpod automatically **updates** any widget using that number — no manual setState needed.

---

## ⚙️ How It Works (in very simple terms)

| Concept              | Meaning (Layman)                                | Example                                     |
| -------------------- | ----------------------------------------------- | ------------------------------------------- |
| **Provider**         | A box that holds data                           | A box that stores the current counter value |
| **StateProvider**    | A box that stores a **changeable value**        | Counter, theme, checkbox toggle             |
| **FutureProvider**   | A box that stores data from an **API call**     | User profile from the internet              |
| **StreamProvider**   | A box that gets **real-time data**              | Chat messages, live location                |
| **NotifierProvider** | A box with **custom logic and multiple states** | Cart management, login form, etc.           |

---

## 🧩 Basic Flow

1. You **create providers** to manage data.
2. Your **widgets read those providers**.
3. When data changes → **widgets rebuild automatically**.

---

## 🧠 Example Code — Counter

```dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';

final counterProvider = StateProvider<int>((ref) => 0);

void main() {
  runApp(ProviderScope(child: MyApp())); // Wrap app in ProviderScope
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(home: CounterScreen());
  }
}

class CounterScreen extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final count = ref.watch(counterProvider); // Watch the counter value

    return Scaffold(
      body: Center(child: Text('Count: $count')),
      floatingActionButton: FloatingActionButton(
        onPressed: () => ref.read(counterProvider.notifier).state++, // Update counter
        child: Icon(Icons.add),
      ),
    );
  }
}
```

---

## 💬 Compared to Android Hilt

| Feature                       | **Riverpod (Flutter)**               | **Hilt (Android)**                          |
| ----------------------------- | ------------------------------------ | ------------------------------------------- |
| Purpose                       | Manages app state + dependencies     | Manages dependencies (injection)            |
| Works with                    | Flutter widgets                      | Android components (Activities, ViewModels) |
| Rebuild UI?                   | ✅ Yes, rebuilds UI when data changes | ❌ No, only injects dependencies             |
| Simpler for UI logic          | ✅ Yes                                | ⚠️ Needs ViewModel / LiveData setup         |
| Works without Flutter context | ✅ Yes                                | ⚠️ Needs Android lifecycle                  |

So Riverpod = **State + Dependency Manager**
Hilt = **Dependency Injection only**

---

## 🚀 Advanced Usage

Once you’re comfortable, you can:

* Use **NotifierProvider** for complex logic (like cart, login, etc.)
* Combine with **Dio** for API calls
* Combine with **GoRouter** for navigation
* Use **AsyncValue** to handle loading/error/data states

---

## 🧭 Example Advanced Flow (You’ll Learn Next)

```
App
 ├── Router (GoRouter)
 ├── Providers
 │     ├── API provider (Dio)
 │     ├── Auth provider
 │     └── Theme provider
 ├── Screens
 │     ├── LoginScreen (uses authProvider)
 │     └── HomeScreen (uses userProvider)
 └── Shared widgets (buttons, loaders, etc.)
```

---

### 🧩 TAKEAWAY

* **Riverpod** = Smart state + dependency manager
* **Like Hilt**, but it also rebuilds UI automatically
* You can manage **everything** — API data, auth state, user info, etc.
* It works **without BuildContext** and is **type-safe**
---

---

# 🧩 STAGE 1 — The Basics

## 🧠 What is Riverpod?

Riverpod is a **state management + dependency injection library** for Flutter.

It helps you:

* Share data across widgets (like `Provider` or `InheritedWidget`)
* Automatically rebuild widgets when data changes
* Keep business logic separate from UI

---

## 🪣 1. Providers — the core concept

Think of a **Provider** as a *box that holds some data*.
Widgets can read from the box, and when data changes, the widgets rebuild automatically.

### ✨ Provider types

| Provider Type      | What it does                             | Example use              |
| ------------------ | ---------------------------------------- | ------------------------ |
| `Provider`         | Holds constant/read-only data            | A repository instance    |
| `StateProvider`    | Holds a **mutable state (simple value)** | Counter, toggle, index   |
| `FutureProvider`   | Handles async data (like API calls)      | Fetch user data from API |
| `StreamProvider`   | Subscribes to live data streams          | Chat updates, location   |
| `NotifierProvider` | Holds **complex logic/state management** | Auth, Cart, Theme, etc.  |

---

## 👶 Example 1: Counter App (StateProvider)

```dart
final counterProvider = StateProvider<int>((ref) => 0);

class CounterScreen extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final count = ref.watch(counterProvider);

    return Scaffold(
      body: Center(child: Text('Count: $count')),
      floatingActionButton: FloatingActionButton(
        onPressed: () => ref.read(counterProvider.notifier).state++,
        child: Icon(Icons.add),
      ),
    );
  }
}
```

✅ `ref.watch()` → listens for changes
✅ `ref.read()` → reads value once (for actions)
✅ `ProviderScope()` → must wrap the entire app (in `main.dart`)

---

# 🧩 STAGE 2 — Async Data (FutureProvider)

When your app needs to fetch data (like from an API), use `FutureProvider`.

```dart
final userProvider = FutureProvider<String>((ref) async {
  await Future.delayed(Duration(seconds: 2));
  return 'Pooja';
});

class HomeScreen extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final userAsync = ref.watch(userProvider);

    return userAsync.when(
      data: (user) => Text('Hello $user'),
      loading: () => CircularProgressIndicator(),
      error: (err, _) => Text('Error: $err'),
    );
  }
}
```

✅ Handles loading/error/data automatically
✅ No need for `setState` or FutureBuilder

---

# 🧩 STAGE 3 — Real Logic (NotifierProvider)

When state becomes complex — for example, managing login, cart, theme, etc. — use **Notifier** or **AsyncNotifier**.

```dart
class CounterNotifier extends Notifier<int> {
  @override
  int build() => 0;

  void increment() => state++;
  void reset() => state = 0;
}

final counterProvider = NotifierProvider<CounterNotifier, int>(() => CounterNotifier());
```

Usage:

```dart
final count = ref.watch(counterProvider);
ref.read(counterProvider.notifier).increment();
```

✅ Keeps UI clean
✅ Logic stays inside the Notifier class (like ViewModel in Android)

---

# 🧩 STAGE 4 — Dependency Injection (like Hilt)

Riverpod can create and inject dependencies automatically — for example, a **Dio instance**.

```dart
final dioProvider = Provider<Dio>((ref) {
  return Dio(BaseOptions(baseUrl: 'https://api.example.com'));
});
```

Now any repository or API call can access Dio:

```dart
final userRepoProvider = Provider<UserRepository>((ref) {
  final dio = ref.read(dioProvider);
  return UserRepository(dio);
});
```

✅ No need to manually pass Dio around
✅ Works like Hilt’s `@Provides`

---

# 🧩 STAGE 5 — API Integration (Dio + Riverpod)

```dart
final userProvider = FutureProvider<User>((ref) async {
  final dio = ref.read(dioProvider);
  final response = await dio.get('/user');
  return User.fromJson(response.data);
});
```

Then in UI:

```dart
final userAsync = ref.watch(userProvider);
```

✅ Automatically rebuilds on new data
✅ Manages async states cleanly

---

# 🧩 STAGE 6 — Navigation (GoRouter + Riverpod)

Riverpod and GoRouter integrate perfectly.

### router_provider.dart

```dart
final routerProvider = Provider<GoRouter>((ref) {
  return GoRouter(
    routes: [
      GoRoute(path: '/', builder: (_, __) => HomeScreen()),
      GoRoute(path: '/login', builder: (_, __) => LoginScreen()),
    ],
  );
});
```

### main.dart

```dart
void main() {
  runApp(ProviderScope(child: MyApp()));
}

class MyApp extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final router = ref.watch(routerProvider);
    return MaterialApp.router(
      routerConfig: router,
    );
  }
}
```

✅ You can control routing with providers
✅ Works great with authentication flows

---

# 🧩 STAGE 7 — AsyncNotifier (for API with loading/error state)

```dart
class UserNotifier extends AsyncNotifier<User?> {
  @override
  Future<User?> build() async => null;

  Future<void> fetchUser() async {
    state = const AsyncLoading();
    try {
      final dio = ref.read(dioProvider);
      final res = await dio.get('/user');
      state = AsyncData(User.fromJson(res.data));
    } catch (e) {
      state = AsyncError(e, StackTrace.current);
    }
  }
}

final userProvider = AsyncNotifierProvider<UserNotifier, User?>(() => UserNotifier());
```

✅ Cleaner than manually managing `FutureProvider`
✅ Can call `ref.watch()` inside your class
✅ Full lifecycle + error handling

---

# 🧩 STAGE 8 — Folder Structure Example

```
lib/
├── main.dart
├── core/
│   ├── dio_provider.dart
│   └── router_provider.dart
├── features/
│   ├── auth/
│   │   ├── data/auth_repository.dart
│   │   ├── providers/auth_provider.dart
│   │   └── presentation/login_screen.dart
│   └── home/
│       ├── data/home_repository.dart
│       ├── providers/home_provider.dart
│       └── presentation/home_screen.dart
```

✅ Clean Architecture ready
✅ Easy scaling for big projects
✅ All dependencies injected automatically

---

# 🧩 STAGE 9 — Common Mistakes

❌ Forgetting to wrap app in `ProviderScope`
❌ Using `ref.read()` instead of `ref.watch()` for UI updates
❌ Doing heavy work inside UI widgets
✅ Keep logic inside Notifier classes

---

# 🎯 TAKEAWAYS

✅ **Riverpod = State + Dependency Manager**
✅ Better than Provider (no BuildContext issues)
✅ Works with Dio, GoRouter, SharedPrefs, Firebase, etc.
✅ Ideal for scalable, testable Flutter apps
✅ Similar role to **Hilt + ViewModel + LiveData** combo in Android

---


