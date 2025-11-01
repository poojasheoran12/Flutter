

# 🧭 TABLE OF CONTENTS

1. What is Dio
2. Setting up Dio
3. GET, POST, PUT, DELETE requests
4. Handling responses
5. Error handling
6. Headers and query parameters
7. Uploading and downloading files
8. Interceptors (logging, tokens, retries)
9. Timeout and cancellation
10. Custom configuration
11. Using Dio with Models (JSON → Dart)
12. Repository Pattern with Dio
13. Dio + Riverpod/Provider (clean architecture)
14. Pro tips and best practices

---

## 🔹 1. What is Dio?

**Dio** is a **powerful, feature-rich HTTP client** for Dart & Flutter.
You use it to **call APIs** — e.g., login, get posts, upload files, etc.

It’s similar to the `http` package but **far more advanced**, offering:

* Base configuration
* Interceptors (for logging/auth)
* Timeout & cancellation
* File upload/download
* Global error handling

👉 Think of Dio as **“http on steroids.”**

---

## ⚙️ 2. Setting up Dio

### Step 1: Add Dependency

In `pubspec.yaml`:

```yaml
dependencies:
  dio: ^5.3.0
```

Then run:

```bash
flutter pub get
```

### Step 2: Create a Dio Instance

```dart
import 'package:dio/dio.dart';

final dio = Dio(
  BaseOptions(
    baseUrl: 'https://jsonplaceholder.typicode.com/',
    connectTimeout: const Duration(seconds: 5),
    receiveTimeout: const Duration(seconds: 5),
    headers: {'Content-Type': 'application/json'},
  ),
);
```

---

## 🌐 3. Making Requests

### ✅ GET Request

```dart
Future<void> getUsers() async {
  final response = await dio.get('/users');
  print(response.data);
}
```

### ✅ POST Request

```dart
Future<void> createUser() async {
  final response = await dio.post('/users', data: {
    'name': 'John',
    'email': 'john@example.com',
  });
  print(response.data);
}
```

### ✅ PUT Request

```dart
await dio.put('/users/1', data: {'name': 'Updated John'});
```

### ✅ DELETE Request

```dart
await dio.delete('/users/1');
```

---

## 🧱 4. Handling Responses

Each response gives:

```dart
print(response.statusCode); // e.g. 200
print(response.data); // actual JSON data
print(response.headers);
```

You can check:

```dart
if (response.statusCode == 200) {
  print('Success: ${response.data}');
} else {
  print('Failed: ${response.statusCode}');
}
```

---

## ⚠️ 5. Error Handling

Always wrap requests in `try-catch`:

```dart
try {
  final response = await dio.get('/users');
} on DioError catch (e) {
  if (e.type == DioErrorType.connectionTimeout) {
    print("Connection timeout");
  } else if (e.type == DioErrorType.badResponse) {
    print("Server error: ${e.response?.statusCode}");
  } else {
    print("Something went wrong: ${e.message}");
  }
}
```

---

## 🧾 6. Headers and Query Parameters

### Headers

```dart
await dio.get('/profile', options: Options(
  headers: {'Authorization': 'Bearer token123'},
));
```

### Query Parameters

```dart
await dio.get('/search', queryParameters: {'q': 'flutter'});
```

---

## 📦 7. Uploading & Downloading Files

### Upload File

```dart
final formData = FormData.fromMap({
  'file': await MultipartFile.fromFile('path/to/image.png'),
});

await dio.post('/upload', data: formData);
```

### Download File

```dart
await dio.download(
  'https://example.com/file.pdf',
  '/local/path/file.pdf',
  onReceiveProgress: (count, total) {
    print('Progress: ${(count / total * 100).toStringAsFixed(0)}%');
  },
);
```

---

## 🧩 8. Interceptors — Powerful Feature 🚀

Interceptors let you **control requests, responses, and errors globally**.

Example:

```dart
dio.interceptors.add(InterceptorsWrapper(
  onRequest: (options, handler) {
    print('➡️ Requesting: ${options.uri}');
    options.headers['Authorization'] = 'Bearer token123';
    return handler.next(options);
  },
  onResponse: (response, handler) {
    print('✅ Response: ${response.statusCode}');
    return handler.next(response);
  },
  onError: (e, handler) {
    print('❌ Error: ${e.message}');
    return handler.next(e);
  },
));
```

This avoids repeating logic (like adding headers) in every request.

---

## ⏱ 9. Timeout & Cancellation

### Timeout

Handled via BaseOptions:

```dart
Dio(BaseOptions(connectTimeout: const Duration(seconds: 5)));
```

### Cancel Requests

```dart
final cancelToken = CancelToken();

dio.get('/users', cancelToken: cancelToken);

// cancel request
cancelToken.cancel('User cancelled request');
```

---

## ⚙️ 10. Custom Configuration

```dart
dio.options.headers['Custom-Header'] = 'MyApp';
dio.options.responseType = ResponseType.json;
dio.options.baseUrl = 'https://api.example.com/';
```

---

## 🧩 11. Using Models (fromJson & toJson)

API responses are JSON — we convert them to Dart models.

```dart
class User {
  final int id;
  final String name;

  User({required this.id, required this.name});

  factory User.fromJson(Map<String, dynamic> json) => User(
    id: json['id'],
    name: json['name'],
  );
}
```

Then:

```dart
final response = await dio.get('/users');
final users = (response.data as List)
    .map((json) => User.fromJson(json))
    .toList();
```

---

## 🧱 12. Repository Pattern

This keeps your API logic **separate from UI**:

```dart
class UserRepository {
  final Dio dio;

  UserRepository(this.dio);

  Future<List<User>> getUsers() async {
    final response = await dio.get('/users');
    return (response.data as List)
        .map((e) => User.fromJson(e))
        .toList();
  }
}
```

Your UI calls:

```dart
final users = await userRepository.getUsers();
```

---

## 🧠 13. Dio + Riverpod (Clean Architecture)

Use **Riverpod** to manage Dio + repositories globally.

```dart
final dioProvider = Provider((ref) => Dio(BaseOptions(
  baseUrl: 'https://jsonplaceholder.typicode.com/',
)));

final userRepoProvider = Provider((ref) => UserRepository(ref.read(dioProvider)));

final usersProvider = FutureProvider((ref) async {
  return ref.read(userRepoProvider).getUsers();
});
```

In your UI:

```dart
final users = ref.watch(usersProvider);

users.when(
  data: (data) => ListView(...),
  loading: () => CircularProgressIndicator(),
  error: (e, _) => Text('Error: $e'),
);
```

---

## 💎 14. Pro Tips & Best Practices

✅ Always use **BaseOptions** for consistent setup
✅ Use **interceptors** for tokens/logging
✅ Convert responses into **model classes**
✅ Organize code using **Repository + Provider/Riverpod**
✅ Handle **timeouts and retries**
✅ Use **try-catch** for all requests

---

## 🏁 **Takeaway Summary**

| Concept                | Description                            |
| ---------------------- | -------------------------------------- |
| **Dio**                | Advanced HTTP client                   |
| **BaseOptions**        | Global API config                      |
| **Interceptors**       | Add auth/logging globally              |
| **Models**             | Convert JSON ↔ Dart                    |
| **Repository Pattern** | Clean separation of logic              |
| **Riverpod**           | Manage Dio & Repositories              |
| **Pro Features**       | Timeout, cancellation, upload/download |

---

