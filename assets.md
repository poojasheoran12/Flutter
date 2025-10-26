

## **1. Adding Local Images**

### **Step 1: Create an `assets` folder**

* In your project root, create a folder called `assets/images` (or just `assets`).
* Put your images inside, e.g., `logo.png`.

```
my_app/
  assets/
    images/
      logo.png
```

---

### **Step 2: Update `pubspec.yaml`**

```yaml
flutter:
  assets:
    - assets/images/logo.png
    # or include all images in the folder
    - assets/images/
```

> Make sure to **indent correctly** in YAML.

Then run:

```bash
flutter pub get
```

---

### **Step 3: Use Image.asset**

```dart
import 'package:flutter/material.dart';

class LocalImageExample extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Local Image Example')),
      body: Center(
        child: Image.asset('assets/images/logo.png'),
      ),
    );
  }
}
```

**Layman:**

* `assets/images/logo.png` → path to your image.
* `Image.asset()` → tells Flutter to load the image from your app.

---

## **2. Adding Network Images**

### **Step 1: Use Image.network**

```dart
import 'package:flutter/material.dart';

class NetworkImageExample extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Network Image Example')),
      body: Center(
        child: Image.network(
          'https://flutter.dev/assets/homepage/carousel/slide_1-bg-ef08b8e6d6b462c869cf6e918438f019f7c5c9b5f0805c2c54b2a4d9cd39104a.jpg',
          loadingBuilder: (context, child, loadingProgress) {
            if (loadingProgress == null) return child;
            return CircularProgressIndicator(
              value: loadingProgress.expectedTotalBytes != null
                  ? loadingProgress.cumulativeBytesLoaded /
                      loadingProgress.expectedTotalBytes!
                  : null,
            );
          },
        ),
      ),
    );
  }
}
```

**Layman:**

* `Image.network(url)` → loads image from the internet.
* `loadingBuilder` → optional, shows a loader while image is loading.

---

## **3. Other Resources (Fonts, JSON, etc.)**

### **Step 1: Add in `pubspec.yaml`**

**Fonts:**

```yaml
flutter:
  fonts:
    - family: Roboto
      fonts:
        - asset: assets/fonts/Roboto-Regular.ttf
```

**JSON / Text Files:**

```yaml
flutter:
  assets:
    - assets/data/sample.json
```

### **Step 2: Load JSON**

```dart
import 'dart:convert';
import 'package:flutter/services.dart' show rootBundle;

Future<void> loadJson() async {
  String jsonString = await rootBundle.loadString('assets/data/sample.json');
  var data = json.decode(jsonString);
  print(data);
}
```

---

## **4. Tips**

1. Always **organize assets** into folders: `images/`, `fonts/`, `data/`.
2. Restart app after adding assets if they don’t load (hot reload sometimes fails).
3. Use **network images** for dynamic content, **local images** for static UI elements.

---

## ✅ **Takeaways**

1. Create `assets/` folder for local images and resources.
2. Register them in `pubspec.yaml`.
3. Use `Image.asset()` for local images, `Image.network()` for online images.
4. Fonts, JSON, and other resources also go in `assets/` and need registration.
5. Hot restart may be required after adding new assets.


