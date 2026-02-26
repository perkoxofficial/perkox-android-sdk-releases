# Perkox Offerwall SDK for Android

A lightweight Android SDK for integrating the Perkox Offerwall into your mobile application. Allow your users to earn rewards by completing offers, surveys, and other engagement activities.

---

## Requirements

| Requirement | Version |
|-------------|---------|
| Android minSdk | 21 (Android 5.0 Lollipop) |
| Android targetSdk | 33 |
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

### Step 1: Download the AAR

Download the latest and`perkox-android-sdk-release.aar` from the [GitHub Releases](https://github.com/perkoxofficial/perkox-android-sdk-releases/releases) page.

### Step 2: Add the AAR to Your Project

1. Copy the downloaded `.aar` file to your app's `libs` folder:
   ```
   your-app/
   └── app/
       └── libs/
           └── perkox-android-sdk-release.aar
   ```

2. Add the following to your app-level `build.gradle` or `build.gradle.kts`:

   **Groovy (`build.gradle`):**
   ```groovy
   dependencies {
       implementation files('libs/perkox-android-sdk-release.aar')
       
       // Required dependencies
       implementation 'androidx.appcompat:appcompat:1.6.1'
       implementation 'androidx.core:core-ktx:1.10.1'
   }
   ```

   **Kotlin DSL (`build.gradle.kts`):**
   ```kotlin
   dependencies {
       implementation(files("libs/perkox-android-sdk-release.aar"))
       
       // Required dependencies
       implementation("androidx.appcompat:appcompat:1.6.1")
       implementation("androidx.core:core-ktx:1.10.1")
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
| `create()` | `appId: String`, `playerId: String` | `Offerwall` | Creates a new Offerwall instance |

### Offerwall
| Method | Parameters | Description |
|--------|------------|-------------|
| `launch()` | `activity: Activity` | Launches the offerwall |

### Listening to Events

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

> **Note:** The `onReward` callback may be called multiple times for the same transaction with different status.

### Common Issues

**1. Offerwall not loading**
- Verify your `appId` is correct
- Check internet connectivity
- Ensure the `playerId` is not empty

**2. AAR not found**
- Make sure the `.aar` file is in the `libs` folder
- Verify the file name matches your Gradle configuration

**3. Class not found errors**
- Ensure you've added the required dependencies:
  - `androidx.appcompat:appcompat`
  - `androidx.core:core-ktx`

---

## Changelog

### v1.0.0
- Initial release
- Seamless offerwall integration

---

## Support

For questions, issues, or feature requests:

- **Email**: support@perkox.com

---