

# 🚀 **Flutter App Creation Cheatsheet (Extended & Practical)**

---

## 🏁 **1. Folder Structure (Best Practice)**

```
lib/
├── main.dart
├── app.dart                  → MaterialApp.router & themes
├── routes/                   → GoRouter config
├── models/                   → Data classes (User, Post, etc.)
├── services/                 → API, Firebase, local storage
├── repositories/             → Business logic (fetch, cache, combine)
├── providers/                → Riverpod state management
├── screens/                  → UI (each feature = folder)
│   ├── login/
│   │   ├── login_screen.dart
│   │   ├── login_viewmodel.dart
│   └── home/
│       ├── home_screen.dart
│       ├── post_card.dart
└── widgets/                  → Reusable UI components
```

---

## 🧠 **2. Riverpod Deep Dive (Modern State Management)**

### 🔹 What Riverpod Solves:

* `setState()` works only in one widget
* `Provider` is tied to the widget tree
* `Riverpod` → independent of widget tree (so testable, scalable, reusable)

---

### 🧩 **Riverpod Core Concepts**

| Term                      | Description                                     | Example                                                                                         |
| ------------------------- | ----------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| **ProviderScope**         | Root container for all providers                | `runApp(ProviderScope(child: MyApp()))`                                                         |
| **ref**                   | Reference object used to access other providers | `ref.read(userProvider)`                                                                        |
| **Provider**              | Exposes read-only data                          | `final themeProvider = Provider((_) => ThemeData.light());`                                     |
| **StateProvider**         | For simple mutable state (like counters)        | `final countProvider = StateProvider((_) => 0);`                                                |
| **FutureProvider**        | For async data (API, DB)                        | `final postsProvider = FutureProvider(fetchPosts);`                                             |
| **StateNotifierProvider** | For complex app logic                           | `final authProvider = StateNotifierProvider<AuthNotifier, AuthState>((ref) => AuthNotifier());` |

---

### ⚙️ **How Riverpod Works Under the Hood**

When a provider changes →
👉 Riverpod automatically rebuilds **only** the widgets that `ref.watch` that provider.

It keeps a **dependency graph**, so rebuilds are efficient.

Example:

```dart
final counterProvider = StateProvider<int>((ref) => 0);

Consumer(
  builder: (context, ref, _) {
    final count = ref.watch(counterProvider);
    return Text('$count');
  },
);
```

✅ Only this `Text` widget rebuilds when count changes — **not the entire widget tree**.

---

### ⚡ **Common Riverpod Mistakes**

| Mistake                                            | Correct Way                                     |
| -------------------------------------------------- | ----------------------------------------------- |
| Using `ref.watch` inside async method              | Use `ref.read()` instead                        |
| Forgetting to use `autoDispose` for temp providers | Always use `autoDispose` for screens that close |
| Storing UI logic inside provider                   | Keep only logic, no widget code                 |

---

### 🧱 **StateNotifier Example (Clean Architecture)**

```dart
class AuthState {
  final bool isLoading;
  final String? token;
  final String? error;

  const AuthState({this.isLoading = false, this.token, this.error});
}

class AuthNotifier extends StateNotifier<AuthState> {
  AuthNotifier() : super(const AuthState());

  Future<void> login(String email, String password) async {
    state = const AuthState(isLoading: true);
    try {
      final token = await AuthService.login(email, password);
      state = AuthState(token: token);
    } catch (e) {
      state = AuthState(error: e.toString());
    }
  }
}

final authProvider = StateNotifierProvider<AuthNotifier, AuthState>((ref) => AuthNotifier());
```

✅ Clear separation of state and logic
✅ Easy to test and debug

---

## 🌐 **3. GoRouter Deep Dive**

### 🔹 What It Does:

* Handles **screen navigation**, **deep linking**, **auth guards**, **redirects**, and **nested navigation**

---

### **Basic Setup**

```dart
final router = GoRouter(
  initialLocation: '/login',
  routes: [
    GoRoute(path: '/login', builder: (_, __) => const LoginScreen()),
    GoRoute(
      path: '/home',
      builder: (_, __) => const HomeScreen(),
      routes: [
        GoRoute(path: 'details/:id', builder: (_, state) {
          final id = state.params['id'];
          return DetailsScreen(id: id!);
        }),
      ],
    ),
  ],
);
```

---

### **Navigation**

| Task           | Code                              |
| -------------- | --------------------------------- |
| Go to route    | `context.go('/home')`             |
| Push on stack  | `context.push('/home/details/1')` |
| Pop route      | `context.pop()`                   |
| Dynamic params | `context.go('/home/details/$id')` |

---

### **Redirect Example (Authentication Guard)**

```dart
final router = GoRouter(
  redirect: (context, state) {
    final loggedIn = AuthService.isLoggedIn;
    final loggingIn = state.matchedLocation == '/login';
    if (!loggedIn && !loggingIn) return '/login';
    if (loggedIn && loggingIn) return '/home';
    return null;
  },
);
```

✅ Ensures users can’t access protected screens without login.

---

### **Nested Routes Example**

```dart
GoRoute(
  path: '/home',
  builder: (_, __) => const HomeScreen(),
  routes: [
    GoRoute(path: 'profile', builder: (_, __) => const ProfileScreen()),
  ],
);
```

Access with `/home/profile`
Nested routes share layout and navigation context.

---

## ❗ **4. Error Handling (Flutter + Dio + Riverpod)**

### 🔹 **Types of Errors**

| Type       | Example              | Handling                        |
| ---------- | -------------------- | ------------------------------- |
| Network    | No internet          | Retry or show offline banner    |
| API        | 400, 401, 500 errors | Parse response and show message |
| Validation | Wrong input          | UI-level error text             |
| App logic  | Null, timeout        | Wrap in try-catch               |

---

### **Dio Error Handling**

```dart
try {
  final res = await dio.get('/users');
} on DioException catch (e) {
  if (e.response?.statusCode == 401) {
    // Token expired → refresh or logout
  } else if (e.type == DioExceptionType.connectionTimeout) {
    print('Connection timeout');
  } else {
    print('Unknown error: ${e.message}');
  }
}
```

---

### **Centralized Interceptor**

```dart
dio.interceptors.add(InterceptorsWrapper(
  onError: (e, handler) async {
    if (e.response?.statusCode == 401) {
      // Refresh token logic
    } else {
      print('API error: ${e.response?.statusCode}');
    }
    return handler.next(e);
  },
));
```

✅ Keeps error handling **consistent** across the app.

---

### **Riverpod + AsyncValue**

```dart
ref.watch(postsProvider).when(
  data: (posts) => ListView(...),
  loading: () => const CircularProgressIndicator(),
  error: (err, _) => Text('Error: $err'),
);
```

✅ Clean handling of **loading**, **error**, and **data** states
✅ No `try-catch` in UI → logic stays clean.

---

## 📦 **5. Bonus: Global Error Handling**

You can catch all uncaught Flutter errors using:

```dart
FlutterError.onError = (details) {
  print('Flutter error: ${details.exception}');
};

runZonedGuarded(
  () => runApp(ProviderScope(child: MyApp())),
  (error, stack) => print('Unhandled error: $error'),
);
```

✅ Helps you log errors to a service like Sentry or Firebase Crashlytics.

---

## 🧭 **6. Flutter App Workflow Summary**

| Stage       | Task                                | Example / Tools                 |
| ----------- | ----------------------------------- | ------------------------------- |
| 1️⃣ Setup   | Create app + folders                | `flutter create my_app`         |
| 2️⃣ Routing | Define GoRouter                     | `routes/router.dart`            |
| 3️⃣ State   | Setup Riverpod providers            | `authProvider`, `postsProvider` |
| 4️⃣ Network | Setup Dio client + interceptors     | `services/api_service.dart`     |
| 5️⃣ Logic   | Repository layer                    | Combines API + Cache            |
| 6️⃣ UI      | Consume providers using `ref.watch` | `screens/home_screen.dart`      |
| 7️⃣ Error   | Use AsyncValue + interceptors       | Clean UX feedback               |
| 8️⃣ Storage | Cache data via Hive                 | Offline support                 |
| 9️⃣ Auth    | JWT, SecureStorage                  | Persistent login                |
| 🔟 Deploy   | Optimize + build                    | `flutter build apk --release`   |

---

## ✅ **Key Takeaways**

1. **GoRouter** → modern, declarative navigation with auth guards
2. **Riverpod** → scalable, testable state management
3. **AsyncValue** → clean async handling (no messy try-catch in UI)
4. **Interceptors** → central place for logging & refresh logic
5. **Separation of concerns** → screens (UI), providers (state), repos (logic), services (API)

---

```
dependencies:
  flutter:
    sdk: flutter
  flutter_riverpod: ^3.0.0
  dio: ^5.3.0
  go_router: ^14.0.0
  flutter_secure_storage: ^9.0.0
  shared_preferences: ^2.2.2
  hive_flutter: ^1.1.0
  json_annotation: ^4.8.1
  freezed_annotation: ^2.4.1
  connectivity_plus: ^5.0.0
  intl: ^0.19.0
  lottie: ^3.0.0
  url_launcher: ^6.2.5
```

 — here’s a **curated list of powerful and production-proven Flutter libraries** that Fortune 500–level teams and top MNCs actually use, categorized by purpose:

---

## ⚙️ **🧩 Core Utilities**

| Purpose                  | Library                                | Why Use It                                                                                |
| ------------------------ | -------------------------------------- | ----------------------------------------------------------------------------------------- |
| **State Management**     | `flutter_riverpod`                     | Scalable, compile-time safe, testable — preferred over Provider for big apps.             |
| **Dependency Injection** | `get_it` or use Riverpod’s built-in DI | Makes managing shared dependencies (e.g., services, repos) easier.                        |
| **Routing**              | `go_router`                            | Officially recommended by Flutter team; supports nested, declarative, and guarded routes. |
| **HTTP Networking**      | `dio`                                  | Advanced API client with interceptors, cancel tokens, and form-data handling.             |
| **JSON Serialization**   | `json_serializable` + `build_runner`   | Generates model boilerplate automatically.                                                |

---

## 💾 **Data & Local Storage**

| Purpose               | Library                  | Why Use It                                                          |
| --------------------- | ------------------------ | ------------------------------------------------------------------- |
| **Local Database**    | `hive`                   | Super fast NoSQL database — good for caching and offline apps.      |
| **SQL Database**      | `sqflite`                | For structured relational data.                                     |
| **Key–Value Storage** | `shared_preferences`     | Store user preferences and simple key-value data.                   |
| **Secure Storage**    | `flutter_secure_storage` | Store JWT tokens, passwords, etc., safely (uses Keychain/Keystore). |

---

## ☁️ **Networking + API + Auth**

| Purpose                       | Library                            | Why Use It                                             |
| ----------------------------- | ---------------------------------- | ------------------------------------------------------ |
| **Firebase Auth & Firestore** | `firebase_auth`, `cloud_firestore` | Production-ready auth & realtime DB.                   |
| **REST API Handling**         | `retrofit` (with `dio`)            | Auto-generates API clients; great with large projects. |
| **GraphQL API**               | `graphql_flutter`                  | For projects using GraphQL endpoints.                  |
| **Error Reporting**           | `sentry_flutter`                   | Tracks crashes, logs, and errors in production.        |

---

## 🧭 **Navigation & Routing**

| Purpose                   | Library      | Why Use It                                                |
| ------------------------- | ------------ | --------------------------------------------------------- |
| **Declarative Routing**   | `go_router`  | Handles deep links, nested navigation, and guards easily. |
| **Navigation Management** | `auto_route` | Alternative to go_router with code generation.            |

---

## 🎨 **UI / Animation / UX**

| Purpose                | Library                                                | Why Use It                                          |
| ---------------------- | ------------------------------------------------------ | --------------------------------------------------- |
| **UI Components**      | `flutter_hooks`, `flutter_bloc`, `shadcn_ui`, `rxdart` | Helps structure reactive UIs cleanly.               |
| **Animations**         | `lottie`, `rive`, `animations`                         | Lottie = JSON animations, Rive = vector animations. |
| **Carousel / Swiper**  | `carousel_slider`                                      | For image or card sliders.                          |
| **Shimmer Loading**    | `shimmer`                                              | Beautiful loading placeholders.                     |
| **Responsive Layouts** | `flutter_screenutil`, `responsive_framework`           | Handle multiple screen sizes easily.                |

---

## 🔐 **Authentication + Security**

| Purpose                    | Library              | Why Use It                          |
| -------------------------- | -------------------- | ----------------------------------- |
| **OAuth / Google Sign-In** | `google_sign_in`     | Easy Google login integration.      |
| **Apple Sign-In**          | `sign_in_with_apple` | Required for iOS apps with sign-in. |
| **Biometrics**             | `local_auth`         | Fingerprint/FaceID authentication.  |

---

## 🧠 **State Management Ecosystem**

| Library            | Key Concept                          | Ideal Use Case                             |
| ------------------ | ------------------------------------ | ------------------------------------------ |
| `flutter_riverpod` | Providers, StateNotifier, AsyncValue | Scalable apps, predictable rebuilds        |
| `bloc` / `cubit`   | Streams + Events + States            | Complex business logic with separation     |
| `getx`             | Reactive controllers                 | Quick apps, small teams (less boilerplate) |

---

## ⚡ **Error Handling + Logging**

| Purpose              | Library                   | Why Use It                           |
| -------------------- | ------------------------- | ------------------------------------ |
| **Error Monitoring** | `sentry_flutter`          | Tracks crashes in production.        |
| **Logging**          | `logger`                  | Better debug prints with formatting. |
| **Retry & Timeout**  | Built-in Dio interceptors | Handle 401/403/500 gracefully.       |
| **Crash Analytics**  | `firebase_crashlytics`    | Detect issues on user devices.       |

---

## 💬 **Real-time & Notifications**

| Purpose                 | Library              | Why Use It                      |
| ----------------------- | -------------------- | ------------------------------- |
| **WebSockets**          | `web_socket_channel` | Real-time chat or data streams. |
| **Push Notifications**  | `firebase_messaging` | Cloud notifications.            |
| **Background Services** | `workmanager`        | Run periodic background tasks.  |

---

## 📱 **UI Enhancement**

| Purpose                     | Library                             | Why Use It                         |
| --------------------------- | ----------------------------------- | ---------------------------------- |
| **Image Picker**            | `image_picker`                      | Select images from gallery/camera. |
| **Cached Images**           | `cached_network_image`              | Cache images efficiently.          |
| **Custom Toasts/Snackbars** | `fluttertoast` / `another_flushbar` | Better feedback messages.          |
| **Pull to Refresh**         | `pull_to_refresh`                   | Smooth refresh experience.         |

---

## 🧱 **Architecture Support**

| Pattern                  | Libraries / Tools            | Why It Matters                 |
| ------------------------ | ---------------------------- | ------------------------------ |
| **Repository Pattern**   | Manual + Riverpod            | Separates data & UI logic.     |
| **Dependency Injection** | Riverpod, get_it             | Makes testing & scaling easy.  |
| **Clean Architecture**   | domain → data → presentation | Keeps large apps maintainable. |

---

## 🧾 **Testing**

| Type               | Library          | Purpose                            |
| ------------------ | ---------------- | ---------------------------------- |
| **Unit Testing**   | `test`           | Core logic tests.                  |
| **Widget Testing** | `flutter_test`   | UI and layout behavior.            |
| **Mocking**        | `mockito`        | Simulate dependencies (APIs, DBs). |
| **Golden Tests**   | `golden_toolkit` | Pixel-perfect UI verification.     |

---



