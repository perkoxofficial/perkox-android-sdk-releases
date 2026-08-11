# Perkox Offerwall SDK for Android

A lightweight Android SDK for integrating the Perkox Offerwall into your mobile application. Allow your users to earn rewards by completing offers, surveys, and other engagement activities.

---

## Requirements

| Requirement | Version |
|-------------|---------|
| Android minSdk | 21 (Android 5.0 Lollipop) |
| Android targetSdk | 36 |
| Java | 17 |
| Kotlin | 1.9.0+ |
| AndroidX | Required |

---

## Permissions

The SDK requires the following permission (automatically included via manifest merging):

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## Installation

### Option 1: Gradle with JitPack (Recommended)

1. Add the JitPack repository to your root `settings.gradle` (or project-level `build.gradle`):

   **`settings.gradle` (Dependency Resolution Mode):**
   ```groovy
   dependencyResolutionManagement {
       repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
       repositories {
           google()
           mavenCentral()
           maven { url 'https://jitpack.io' }
       }
   }
   ```

2. Add the Perkox SDK dependency to your app-level `build.gradle` or `build.gradle.kts`:

   **Groovy (`build.gradle`):**
   ```groovy
   dependencies {
       implementation 'com.perkox:perkox-android-sdk-releases:1.0.4'
   }
   ```

   **Kotlin DSL (`build.gradle.kts`):**
   ```kotlin
   dependencies {
       implementation("com.perkox:perkox-android-sdk-releases:1.0.4")
   }
   ```

---

### Option 2: Manual AAR Integration

1. Download `perkox-android-sdk-release.aar` from the [GitHub Releases](https://github.com/perkoxofficial/perkox-android-sdk-releases/releases) page.
2. Copy the downloaded `.aar` file to your app's `libs` folder:
   ```
   your-app/
   └── app/
       └── libs/
           └── perkox-android-sdk-release.aar
   ```
3. Add the following to your app-level `build.gradle`:
   ```groovy
   dependencies {
       implementation files('libs/perkox-android-sdk-release.aar')
       implementation 'androidx.appcompat:appcompat:1.6.1'
       implementation 'androidx.core:core-ktx:1.10.1'
   }
   ```


## Quick Start

### Basic Implementation

```kotlin
import com.perkoxofferwall.sdk.PerkoxOfferwall

private fun showOfferwall() {
    
    // Create and launch the offerwall
    val offerwall = PerkoxOfferwall.create(
        "YOUR_APP_ID",      // Your App ID 
        "YOUR_SDK_KEY",     // Your SDK key
        "Player_123"        // Unique player id
    )        

    offerwall.launch(this)
}
```

### Java Implementation

```java
import com.perkoxofferwall.sdk.PerkoxOfferwall;
import com.perkoxofferwall.sdk.Offerwall;
private void showOfferwall() {
    Offerwall offerwall = PerkoxOfferwall.INSTANCE.create(
        "YOUR_APP_ID",    // Your App ID 
        "YOUR_SDK_KEY",   // Your SDK key
        "USER_123"        // Unique player id
    );        
    offerwall.launch(this);
}
```

---

### PerkoxOfferwall

The main entry point for the SDK.

| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `create()` | `appId: String`, `sdkKey: String`, `playerId: String` | `Offerwall` | Creates a new Offerwall instance |

### Offerwall
| Method | Parameters | Description |
|--------|------------|-------------|
| `launch()` | `activity: Activity` | Launches the offerwall |
| `onReward` | `(Map<String, Any?>) -> Unit` | Callback triggered when a reward is received |
| `onClose` | `() -> Unit` | Callback triggered when the offerwall is closed |

### Listening to Events

> **Note:** ⚠️ Important Note : Do **not** rely on the SDK's reward callbacks to grant rewards to users, as these callbacks only work when the offerwall is launched. Instead, use the postback URL you provided to Perkox to handle rewards on your server, or distribute the reward data to your system using webhooks or similar server-side technologies for accurate and reliable reward processing.


> **Note:** The `onReward` callback may be called multiple times for the same transaction with different status.

You can listen to reward and close events by setting callbacks before launching the offerwall.

**Kotlin:**

```kotlin
val offerwall = PerkoxOfferwall.create("YOUR_APP_ID", "YOUR_SDK_KEY", "Player_123")

offerwall.onReward = { reward ->
    val amount = reward["amount"]   // Double - reward amount
    val status = reward["status"]   // String - "pending" or "approved"
    val txid = reward["txid"]       // String - unique transaction ID
    val playerId = reward["player_id"] // String - player ID
    Log.d("Perkox", "Reward received! Amount: $amount, Status: $status")
}

offerwall.onClose = {
    Log.d("Perkox", "Offerwall closed")
}

offerwall.launch(this)
```

**Java:**

```java
Offerwall offerwall = PerkoxOfferwall.INSTANCE.create("YOUR_APP_ID", "YOUR_SDK_KEY", "Player_123");

offerwall.setOnReward(reward -> {
    Double amount = (Double) reward.get("amount");
    String status = (String) reward.get("status");
    String txid = (String) reward.get("txid");
    String playerId = (String) reward.get("player_id");
    Log.d("Perkox", "Reward received! Amount: " + amount + ", Status: " + status);
    return null;
});

offerwall.setOnClose(() -> {
    Log.d("Perkox", "Offerwall closed");
    return null;
});

offerwall.launch(this);
```

#### Reward Data Fields

| Field | Type | Description |
|-------|------|-------------|
| `amount` | `Double` | The reward amount |
| `txid` | `String` | Unique transaction ID |
| `status` | `String` | `"pending"` / `"approved"` / `"reversed"` / `"rejected"` |
| `publisher_id` | `Int` | Publisher ID |
| `player_id` | `String` | Player ID |
| `timestamp` | `Long` | Event timestamp in milliseconds |

### Common Issues

**1. Offerwall loading with 0 offers**
- Verify your application package name (`applicationContext.packageName`) matches the exact Package ID registered for your `appId` in the Perkox Publisher Dashboard (`https://pub.perkox.com`).
- Verify your `appId` and `sdkKey` are correct.
- Ensure the `playerId` is not empty.
- Check internet connectivity.

**2. AAR not found**
- Make sure the `.aar` file is in the `libs` folder
- Verify the file name matches your Gradle configuration

**3. Class not found errors**
- Ensure you've added the required dependencies:
  - `androidx.appcompat:appcompat`
  - `androidx.core:core-ktx`

---

## Changelog

### v1.0.1
- Upgraded `compileSdk` and `targetSdk` to 36
- Optimized background thread execution for URL construction
- Added Package ID validation documentation

### v1.0.0
- Initial release
- Seamless offerwall integration

---

## Support

For questions, issues, or feature requests:

- **Email**: support@perkox.com

---