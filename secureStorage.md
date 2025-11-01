

## 🔐 What is Flutter Secure Storage?

`flutter_secure_storage` is a Flutter plugin that lets you **store sensitive data securely on the device**, such as:

* JWT tokens (for login)
* API keys
* User credentials (if needed)
* Refresh tokens, etc.

It uses:

* **Keychain** on iOS (Apple’s secure storage)
* **Keystore** on Android (Google’s secure system)

So the data is **encrypted** and cannot be accessed by other apps or by the user directly.

---

## ⚙️ Setup

### Step 1: Add dependency

In your `pubspec.yaml`:

```yaml
dependencies:
  flutter_secure_storage: ^9.0.0
```

Run:

```bash
flutter pub get
```

---

## 🧱 Step 2: Import and Initialize

```dart
import 'package:flutter_secure_storage/flutter_secure_storage.dart';

// Create storage instance
final storage = FlutterSecureStorage();
```

---

## 💾 Step 3: Store data

For example, when a user logs in and you receive a JWT token from your API:

```dart
await storage.write(key: 'authToken', value: 'YOUR_JWT_TOKEN_HERE');
```

---

## 📖 Step 4: Read data

You can get it later when making API requests:

```dart
String? token = await storage.read(key: 'authToken');
print('Token: $token');
```

---

## ❌ Step 5: Delete data (e.g., on logout)

```dart
await storage.delete(key: 'authToken');
```

or delete all stored data:

```dart
await storage.deleteAll();
```

---

## 🔄 Step 6: Common Real-World Usage Pattern

### Example: After login

```dart
Future<void> loginUser(String email, String password) async {
  final response = await dio.post('https://api.example.com/login', data: {
    'email': email,
    'password': password,
  });

  if (response.statusCode == 200) {
    final token = response.data['token'];
    await storage.write(key: 'authToken', value: token);
  }
}
```

### Example: Use token in future requests

You can automatically add the token to Dio requests:

```dart
final dio = Dio();

dio.interceptors.add(
  InterceptorsWrapper(
    onRequest: (options, handler) async {
      final token = await storage.read(key: 'authToken');
      if (token != null) {
        options.headers['Authorization'] = 'Bearer $token';
      }
      return handler.next(options);
    },
  ),
);
```

Now every request automatically includes your token. ✅

---

## 🧩 Bonus: Common Mistake

Do **not** use `SharedPreferences` for storing tokens!
That’s plain text, not encrypted — easily accessible.

Always use:

* `flutter_secure_storage` for tokens/keys
* `SharedPreferences` only for non-sensitive data like theme, onboarding flag, etc.

---

## 🧠 Advanced Tip: Combine with Riverpod

You can create a provider for SecureStorage:

```dart
final secureStorageProvider = Provider((ref) => const FlutterSecureStorage());
```

Then use it anywhere in your app (e.g., auth repository or Dio interceptor) with:

```dart
final storage = ref.read(secureStorageProvider);
```

---

### 🚀 Takeaways

| Concept                  | Purpose                                                      |
| ------------------------ | ------------------------------------------------------------ |
| `flutter_secure_storage` | Encrypts and safely stores small sensitive data              |
| Use case                 | Login tokens, API keys, refresh tokens                       |
| Safe on                  | Android (Keystore) and iOS (Keychain)                        |
| Never use                | SharedPreferences for tokens                                 |
| Combine with             | Dio interceptors + Riverpod for seamless authentication flow |

---

