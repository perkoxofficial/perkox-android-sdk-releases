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

The SDK automatically includes the necessary permissions via manifest merging:

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="com.google.android.gms.permission.AD_ID" />
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
       implementation 'com.perkox:perkox-android-sdk-releases:2.0.12'
   }
   ```

   **Kotlin DSL (`build.gradle.kts`):**
   ```kotlin
   dependencies {
       implementation("com.perkox:perkox-android-sdk-releases:2.0.12")
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
| `create()` | `appId: String`, `sdkKey: String`, `playerId: String`, `beta: Boolean = false` | `Offerwall` | Creates a new Offerwall instance. |
| `syncPendingRewards()` | `appId: String`, `sdkKey: String`, `playerId: String`, `beta: Boolean = false`, `callback?: (List<Map<String, Any?>>) -> Unit` | `Unit` | Synchronizes pending rewards completed while the app was closed. |

### Offerwall
| Method / Property | Type / Parameters | Description |
|-------------------|-------------------|-------------|
| `launch()` | `activity: Activity` | Launches the offerwall activity and automatically syncs pending rewards. |
| `onReward` | `((Map<String, Any?>) -> Unit)?` | Callback triggered when a reward is received (supports all dynamic server fields). |
| `onClose` | `(() -> Unit)?` | Callback triggered when the offerwall is closed. |

---

### ⚡ Offline & Pending Rewards Auto-Sync

When users complete offers (e.g. reaching a game level, finishing surveys) outside of your application while your app is closed or suspended, rewards are **never lost**:

1. **Automatic Sync on Launch:** When `offerwall.launch(this)` is called, the SDK automatically queries the backend for pending rewards, delivers them to your `onReward` listener on the Main Thread, and acknowledges receipt to avoid duplicate crediting.
2. **Explicit Background Sync:** You can also check for rewards on app startup or user login without launching the offerwall UI:

**Kotlin:**
```kotlin
PerkoxOfferwall.syncPendingRewards(
    appId = "YOUR_APP_ID",
    sdkKey = "YOUR_SDK_KEY",
    playerId = "Player_123"
) { rewards ->
    for (reward in rewards) {
        val amount = reward["amount"]
        val txid = reward["txid"]
        val offerName = reward["offer_name"]
        Log.d("Perkox", "Synced offline reward: $amount pts (TxID: $txid, Offer: $offerName)")
    }
}
```

**Java:**
```java
PerkoxOfferwall.INSTANCE.syncPendingRewards(
    "YOUR_APP_ID",
    "YOUR_SDK_KEY",
    "Player_123",
    false, // beta
    rewards -> {
        for (Map<String, Object> reward : rewards) {
            System.out.println("Synced reward: " + reward.get("amount") + " (TxID: " + reward.get("txid") + ")");
        }
        return null;
    }
);
```

---

### Listening to Events & Dynamic Reward Payloads

Server parameters (such as `click_id`, `cid`, `offer_id`, `sub1`..`sub5`, `payout`, `amount`, `status`) are **100% dynamically preserved** and accessible directly from the reward map:

**Kotlin:**

```kotlin
val offerwall = PerkoxOfferwall.create("YOUR_APP_ID", "YOUR_SDK_KEY", "Player_123")

offerwall.onReward = { reward ->
    val amount = reward["amount"]       // Reward amount credited
    val status = reward["status"]       // "approved", "pending", etc.
    val txid = reward["txid"]           // Unique transaction or click ID
    val playerId = reward["player_id"] // Player ID
    val clickId = reward["click_id"]   // Advertiser / tracker click ID
    val offerId = reward["offer_id"]   // Completed offer ID

    Log.d("Perkox", "Reward received! Amount: $amount, TxID: $txid, Offer: $offerId")
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
    Object clickId = reward.get("click_id");
    
    Log.d("Perkox", "Reward received! Amount: " + amount + ", TxID: " + txid);
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
| `amount` / `payout` | `Double` / `Number` | The reward points or currency amount |
| `txid` | `String` | Unique transaction identifier |
| `status` | `String` | `"approved"`, `"pending"`, `"reversed"`, `"rejected"` |
| `player_id` | `String` | The player / user identifier |
| `click_id` | `String` | The conversion click ID (dynamic) |
| `offer_id` | `Any` | Offer ID (dynamic) |
| `offer_name` | `String` | Name of the completed offer (when available) |
| `...custom` | `Any?` | All custom advertiser/postback parameters preserved dynamically |

> **Anti-Duplicate Guarantee:** The SDK automatically acknowledges claimed reward transaction IDs to the backend (`POST /rewards/claim`), guaranteeing idempotent delivery across app restarts.

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

### v1.0.0
- Initial release
- Seamless offerwall integration

---

## Support

For questions, issues, or feature requests:

- **Email**: support@perkox.com

---