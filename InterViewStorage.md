

# **### 💾 6. Local Storage & Offline Data**

---

## **1. Hive / Sqflite Basics**

### **Hive**

* Lightweight **NoSQL database** written in pure Dart.
* Works **offline** and is very fast.
* Stores **key-value pairs** or **objects**.
* Example:

```dart
import 'package:hive/hive.dart';

class User {
  final String name;
  final int age;
  User({required this.name, required this.age});
}

// Open a box
var box = await Hive.openBox('userBox');

// Save data
await box.put('user', User(name: 'Alice', age: 25));

// Read data
User? user = box.get('user');
```

**Pros**

* Fast
* Easy object storage
* Offline ready

---

### **Sqflite**

* **SQLite plugin** for Flutter (SQL-based relational DB).
* Useful for **complex queries, joins, or relational data**.

```dart
import 'package:sqflite/sqflite.dart';

final db = await openDatabase('my_db.db', version: 1,
  onCreate: (db, version) async {
    await db.execute(
      'CREATE TABLE users(id INTEGER PRIMARY KEY, name TEXT, age INTEGER)'
    );
  });

// Insert
await db.insert('users', {'id': 1, 'name': 'Alice', 'age': 25});

// Query
List<Map<String, dynamic>> users = await db.query('users');
```

**Pros**

* Relational data support
* Complex queries
* Mature and widely used

---

## **2. SharedPreferences vs SecureStorage**

| Storage                  | Type                  | Use Cases                         | Security                                    |
| ------------------------ | --------------------- | --------------------------------- | ------------------------------------------- |
| **SharedPreferences**    | Key-value             | Settings, theme, simple flags     | Not encrypted → not safe for sensitive data |
| **FlutterSecureStorage** | Key-value (encrypted) | Tokens, passwords, sensitive info | Encrypted → safe for tokens/passwords       |

**Example: SecureStorage**

```dart
import 'package:flutter_secure_storage/flutter_secure_storage.dart';

final storage = FlutterSecureStorage();
await storage.write(key: 'accessToken', value: token);
final token = await storage.read(key: 'accessToken');
await storage.delete(key: 'accessToken');
```

---

## **3. Caching Strategies**

* **In-memory cache** → fast but lost on app restart (e.g., simple Map).
* **Disk cache** → persistent storage using Hive/Sqflite.
* **Cache invalidation** → ensure cached data doesn’t become stale:

  * Time-based (e.g., expire after 1 hour)
  * Event-based (e.g., update after API call)

**Example: Cached API response**

```dart
Future<List<Post>> fetchPosts() async {
  final cached = await Hive.box('postsBox').get('posts');
  if (cached != null) return cached;

  final response = await dio.get('/posts');
  final posts = response.data.map((e) => Post.fromJson(e)).toList();
  await Hive.box('postsBox').put('posts', posts);
  return posts;
}
```

---

## **4. Real-World Use Cases**

1. **Recent searches**

   * Store last 5–10 search queries locally for quick access.
2. **Cached posts / news feed**

   * Allows app to work offline and reduces API calls.
3. **Tokens**

   * Securely store JWT or refresh tokens using **SecureStorage**.
4. **Offline apps**

   * Users can continue reading content or performing tasks without internet.
   * Data sync occurs when connection restores.

---

### ✅ **Key Takeaways**

1. **Hive** → lightweight NoSQL, fast, offline-friendly.
2. **Sqflite** → relational DB, supports complex queries.
3. **SharedPreferences** → simple key-value, not secure.
4. **SecureStorage** → encrypted storage for sensitive info.
5. **Caching** → improve performance and enable offline usage.
6. **Offline-first apps** → store recent searches, cached posts, tokens locally.

---


## **1. How to Store Recent Searches, Cached Posts, or Tokens**

### **A. Recent Searches**

* Use **Hive or SharedPreferences** because data is small and key-value based.

```dart
final box = await Hive.openBox('searchBox');

// Save recent search
void saveSearch(String query) {
  List<String> recent = box.get('recentSearches', defaultValue: []);
  recent.insert(0, query); // add to start
  if (recent.length > 5) recent = recent.sublist(0, 5); // keep last 5
  box.put('recentSearches', recent);
}

// Read recent searches
List<String> getRecentSearches() {
  return box.get('recentSearches', defaultValue: []);
}
```

---

### **B. Cached Posts / API Data**

* Use **Hive or Sqflite** for offline persistence and fast reads.

```dart
final postsBox = await Hive.openBox('postsBox');

Future<List<Post>> getPosts() async {
  // Return cached data if available
  final cached = postsBox.get('posts');
  if (cached != null) return cached.cast<Post>();

  // Otherwise, fetch from API
  final response = await dio.get('/posts');
  final posts = response.data.map((e) => Post.fromJson(e)).toList();
  postsBox.put('posts', posts); // save to cache
  return posts;
}
```

---

### **C. Tokens (JWT, Refresh Tokens)**

* Use **SecureStorage** for sensitive information.

```dart
final storage = FlutterSecureStorage();

// Store token
await storage.write(key: 'accessToken', value: token);

// Read token
final token = await storage.read(key: 'accessToken');

// Delete token on logout
await storage.delete(key: 'accessToken');
```

---

## **2. Why Local DB Helps Offline Apps**

1. **Offline Availability**

   * Users can still view recent posts, searches, or cached data even without internet.
   * Example: News app shows last downloaded articles offline.

2. **Reduced API Calls**

   * Improves performance and saves bandwidth.
   * Example: Only fetch new posts if cache is expired.

3. **Faster Load Times**

   * Reading from local storage is faster than network calls.

4. **Persistent State**

   * User preferences, last searches, and auth tokens remain even after app restart.

---

### **Layman Analogy**

* Imagine your app is like a **library**:

  * **Cached posts** = books you borrowed last time stored at home → can read anytime.
  * **Recent searches** = bookmarks or notes → easily accessible.
  * **Tokens** = library ID card → secure, used to access services.
* Without storing these locally, every time you open the app, you’d **have to go to the library** → slow and inconvenient.

---

### ✅ **Key Takeaways**

1. **Recent searches:** Hive / SharedPreferences → small key-value data.
2. **Cached posts:** Hive / Sqflite → offline content & performance boost.
3. **Tokens:** SecureStorage → encrypted, safe from attacks.
4. **Local DB** → enables offline apps, reduces API calls, improves speed, and keeps persistent user data.

---





