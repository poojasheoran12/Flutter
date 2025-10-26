

## **1. What is an API?**

**Formal:**
An API (Application Programming Interface) is a way for your app to communicate with a server or another system to **send or receive data**.

**Layman:**
An API is like a waiter in a restaurant — your app tells the waiter what you want, the waiter goes to the kitchen (server), and brings the food (data) back.

---

## **2. Flutter Packages to Call APIs**

The most common packages are:

1. **http** → Simple and easy to use.
2. **Dio** → Advanced, supports interceptors, timeout, and error handling.

We’ll start with **http**, which is enough for most apps.

---

## **3. Add Dependencies**

Open `pubspec.yaml` and add:

```yaml
dependencies:
  flutter:
    sdk: flutter
  http: ^1.1.0
```

Then run:

```bash
flutter pub get
```

---

## **4. Calling a GET API**

Example: Fetching data from `https://jsonplaceholder.typicode.com/posts`

```dart
import 'dart:convert';
import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;

class ApiExample extends StatefulWidget {
  @override
  _ApiExampleState createState() => _ApiExampleState();
}

class _ApiExampleState extends State<ApiExample> {
  List posts = [];
  bool loading = true;

  @override
  void initState() {
    super.initState();
    fetchPosts();
  }

  Future<void> fetchPosts() async {
    final response = await http.get(Uri.parse('https://jsonplaceholder.typicode.com/posts'));
    if (response.statusCode == 200) {
      setState(() {
        posts = json.decode(response.body);
        loading = false;
      });
    } else {
      print('Failed to load posts');
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('API Example')),
      body: loading
          ? Center(child: CircularProgressIndicator())
          : ListView.builder(
              itemCount: posts.length,
              itemBuilder: (context, index) {
                return ListTile(
                  title: Text(posts[index]['title']),
                  subtitle: Text(posts[index]['body']),
                );
              },
            ),
    );
  }
}
```

**Explanation:**

1. Use `http.get()` to fetch data.
2. `json.decode()` converts JSON string to Dart objects (List/Map).
3. `setState()` updates the UI with fetched data.

---

## **5. Calling a POST API**

Example: Sending data to a server

```dart
Future<void> createPost() async {
  final response = await http.post(
    Uri.parse('https://jsonplaceholder.typicode.com/posts'),
    headers: {"Content-Type": "application/json"},
    body: json.encode({
      'title': 'New Post',
      'body': 'This is the body',
      'userId': 1,
    }),
  );

  if (response.statusCode == 201) {
    print('Post created: ${response.body}');
  } else {
    print('Failed to create post');
  }
}
```

**Layman:**

* GET → “Give me data.”
* POST → “Here is data, save it.”

---

## **6. Error Handling**

Always handle errors and loading states:

```dart
try {
  final response = await http.get(Uri.parse(url));
  if (response.statusCode == 200) {
    // success
  } else {
    // server error
  }
} catch (e) {
  // network error
  print('Error: $e');
}
```

---

## **7. Async/Await**

* API calls are **asynchronous**.
* `async` + `await` lets you wait for the response **without freezing the UI**.

---

## ✅ **Takeaways**

1. Add `http` package for network requests.
2. Use `http.get()` for fetching data, `http.post()` for sending data.
3. Convert JSON with `json.decode()` or `json.encode()`.
4. Handle loading and errors with `setState()` and try/catch.
5. Always use `async/await` to avoid freezing the UI.
6. Wrap your API calls in **functions** and call them from `initState()` or button clicks.

---

