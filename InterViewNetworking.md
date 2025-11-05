

## 🧩 **I. Basic Networking Questions**

### 1️⃣ What is HTTP?

**Answer:**
HTTP (HyperText Transfer Protocol) is the protocol that defines how data is exchanged between a client (Flutter app) and a server (backend). It uses methods like **GET, POST, PUT, DELETE** to send and receive data.

---

### 2️⃣ What is an API?

**Answer:**
An API (Application Programming Interface) is an interface exposed by the backend to allow the frontend (like Flutter) to interact with the server’s data using specific **endpoints** (URLs).

---

### 3️⃣ What are HTTP methods and when do we use them?

**Answer:**

| Method      | Purpose     |
| ----------- | ----------- |
| GET         | Fetch data  |
| POST        | Create data |
| PUT / PATCH | Update data |
| DELETE      | Remove data |

---

### 4️⃣ What is a REST API?

**Answer:**
A REST API follows the **Representational State Transfer** style — it uses URLs (endpoints) to represent resources and HTTP methods to manipulate them.
Example:
`GET /users` → fetch all users
`POST /users` → create new user

---

### 5️⃣ What is the difference between HTTP and HTTPS?

**Answer:**

* **HTTP**: Unsecured, plain text transfer.
* **HTTPS**: Secure version that uses **SSL/TLS encryption**.

---

## ⚙️ **II. Dio and API Handling Questions**

### 6️⃣ What is Dio in Flutter?

**Answer:**
Dio is a powerful HTTP client for Flutter/Dart.
It supports:

* Interceptors
* Global configuration
* Cancellation tokens
* Timeout
* Response parsing and error handling

---

### 7️⃣ How do you make a simple GET request using Dio?

```dart
final dio = Dio();
final response = await dio.get('https://api.example.com/users');
print(response.data);
```

---

### 8️⃣ What are interceptors in Dio?

**Answer:**
Interceptors are middleware that let you **modify requests, responses, or handle errors globally** before they reach your app logic.

Example uses:

* Adding headers (like tokens)
* Logging
* Token refresh on 401 errors

---

### 9️⃣ How do you handle errors in Dio?

**Answer:**
Using `try-catch` or `onError` inside interceptors:

```dart
try {
  final response = await dio.get('/users');
} on DioException catch (e) {
  print(e.response?.statusCode);
}
```

---

### 🔟 What are 401 and 403 errors, and how do you handle them?

**Answer:**

* **401 Unauthorized:** Token missing/expired → refresh token.
* **403 Forbidden:** Token valid but no permission → show “Access Denied.”

Handled automatically using interceptors.

---

## 🧠 **III. Intermediate Concepts**

### 11️⃣ What is JSON serialization and why do we need it?

**Answer:**
It’s converting JSON (from API) into Dart objects and vice versa using `fromJson` / `toJson`.
We need it because Dart doesn’t understand JSON strings directly.

---

### 12️⃣ What’s the role of `factory` constructors in models?

**Answer:**
They allow you to create objects from JSON data efficiently.

```dart
factory User.fromJson(Map<String, dynamic> json)
```

→ creates a `User` object from a JSON map.

---

### 13️⃣ What is caching and why is it important in networking?

**Answer:**
Caching stores fetched API data locally (in SharedPreferences, Hive, or cache plugins) to reduce API calls and improve performance or offline support.

---

### 14️⃣ What is pagination and how do you implement it?

**Answer:**
It’s fetching data page by page using parameters like `page` and `limit`.
Used with lists or infinite scrolling for large data.

---

### 15️⃣ How do you handle timeouts in Dio?

**Answer:**
You can configure request timeouts:

```dart
dio.options.connectTimeout = Duration(seconds: 10);
```

If exceeded, Dio throws a timeout error.

---

### 16️⃣ How do you attach headers like tokens to all requests?

**Answer:**
Use interceptors or global options:

```dart
dio.options.headers['Authorization'] = 'Bearer $token';
```

---

### 17️⃣ What’s the difference between `response.data` and `response`?

**Answer:**

* `response.data` → the actual data (usually JSON).
* `response` → includes metadata like status code, headers, etc.

---

## 🧭 **IV. Advanced & Real-World Questions**

### 18️⃣ How do you handle token refresh automatically in Dio?

**Answer:**
By intercepting `401` errors and retrying with a new token:

```dart
dio.interceptors.add(InterceptorsWrapper(
  onError: (e, handler) async {
    if (e.response?.statusCode == 401) {
      final newToken = await refreshToken();
      e.requestOptions.headers['Authorization'] = 'Bearer $newToken';
      final cloneReq = await dio.request(
        e.requestOptions.path,
        options: Options(headers: e.requestOptions.headers),
      );
      return handler.resolve(cloneReq);
    }
    return handler.next(e);
  },
));
```

---

### 19️⃣ How can you cancel a Dio request?

**Answer:**

```dart
final cancelToken = CancelToken();
dio.get('/users', cancelToken: cancelToken);
cancelToken.cancel("Request canceled");
```

---

### 20️⃣ How can you log all requests and responses for debugging?

**Answer:**
Use `LogInterceptor`:

```dart
dio.interceptors.add(LogInterceptor(responseBody: true));
```

---

### 21️⃣ How would you test API calls in Flutter?

**Answer:**

* Use **mock APIs** with tools like `mockito`.
* Inject a **mock Dio client** into repositories.
* Test the function output based on fake responses.

---

## ✅ **Takeaway: Key Topics to Revise for Interviews**

| Topic                               | Importance |
| ----------------------------------- | ---------- |
| HTTP basics (methods, status codes) | ⭐⭐⭐⭐       |
| REST API structure                  | ⭐⭐⭐        |
| Dio setup + interceptors            | ⭐⭐⭐⭐⭐      |
| Error handling (401, 403, timeout)  | ⭐⭐⭐⭐       |
| JSON serialization                  | ⭐⭐⭐⭐       |
| Caching & pagination                | ⭐⭐⭐        |
| Token refresh flow                  | ⭐⭐⭐⭐⭐      |
| Request cancellation & logging      | ⭐⭐         |

---

## 💡 **Flutter Networking + Dio Interview Questions**

---

### 🧩 **1. HTTP vs API**

👉 What’s the difference between **HTTP** and an **API**, and how do they work together in a Flutter app?

---

### ⚙️ **2. REST API Structure**

👉 What is a **REST API**, and what do the following parts represent in an API call?

```
POST https://api.example.com/users?page=1&limit=10
Headers: { "Authorization": "Bearer token" }
Body: { "name": "Pooja" }
```

---

### 🌍 **3. Dio Basics**

👉 What is **Dio**, and why would you use it instead of the built-in `http` package?

---

### 🧱 **4. Dio Interceptors**

👉 What are **interceptors** in Dio?
Can you explain a practical use case for them?

---

### ⚠️ **5. Error Handling**

👉 What’s the difference between **401 Unauthorized** and **403 Forbidden** errors,
and how should your app respond to each?

---

### 🧠 **6. JSON Serialization**

👉 Why do we convert JSON responses into Dart model classes using `fromJson` / `toJson` instead of directly using raw JSON maps?

---

### 🗂️ **7. Caching**

👉 What is **caching**, and how would you implement it in a Flutter app that uses Dio to fetch data?

---

### 📜 **8. Pagination**

👉 What is **pagination**, and how would you implement it for an infinite scrolling list of posts using Flutter + Dio?

---

### 🔑 **9. Token Refresh Flow**

👉 Suppose your access token expires while the app is running.
How would you **automatically refresh the token** in Dio and retry the failed request?

---

### 🧩 **10. Real-World Architecture**

👉 In a large app, how would you **organize your networking code** (Dio setup, interceptors, API calls, models) for scalability and clean architecture?
