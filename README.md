# Shoplive Android SDK

The Shoplive Android SDK, distributed as **AAR + POM** through a Gradle Maven repository. Every
release tag carries the AAR/POM assets, and the `maven-repo` branch serves the same binaries in
Maven layout for Gradle to resolve.

It covers live playback and broadcasting, and the Shortform SDK (short-form video feed and video
editor), which has its own version line.

## Requirements

| | |
| --- | --- |
| Min SDK | **23** (see release notes if a release raises the floor) |
| Distribution | Gradle Maven repository |
| Android Gradle Plugin | 8.x recommended |

## Installation

### 1. Set up the Maven repository

`settings.gradle.kts` (or the root `repositories` block):

```kotlin
dependencyResolutionManagement {
    repositories {
        google()
        mavenCentral()
        maven {
            url = uri("https://sdk.shoplive.cloud/maven-repo")
        }
    }
}
```

Use the `sdk.shoplive.cloud` endpoint as the canonical Maven repository URL.

### 2. Dependencies

Declare the product coordinates you need, all on the same version:

```kotlin
dependencies {
    implementation("cloud.shoplive:shoplive-player-sdk:3.0.0")
}
```

Or through a version catalog (`gradle/libs.versions.toml`):

```toml
[versions]
shoplive = "3.0.0"

[libraries]
shoplive-player-sdk = { module = "cloud.shoplive:shoplive-player-sdk", version.ref = "shoplive" }
shoplive-streamer-sdk = { module = "cloud.shoplive:shoplive-streamer-sdk", version.ref = "shoplive" }
```

```kotlin
implementation(libs.shoplive.player.sdk)
```

For the player and streamer, everything else — `shoplive-core`, `shoplive-core-player`,
`shoplive-exoplayer`, `shoplive-webrtc`, `shoplive-android-webrtc`, `shoplive-rtmp` — is an
implementation detail that arrives transitively through each product's POM. You never declare those
yourself. The Shortform SDK is the exception; see **Shortform** below.

### 3. Products

| Product coordinate | Purpose |
| --- | --- |
| `cloud.shoplive:shoplive-player-sdk` | Live / VOD playback |
| `cloud.shoplive:shoplive-streamer-sdk` | Broadcasting |
| `cloud.shoplive:shoplive-short-form` | Short-form video feed and player (Shortform SDK) |
| `cloud.shoplive:shoplive-video-editor` | Short-form video editor and uploader (Shortform SDK) |

**Player**

```kotlin
implementation("cloud.shoplive:shoplive-player-sdk:3.0.0")
```

**Streamer**

```kotlin
implementation("cloud.shoplive:shoplive-streamer-sdk:3.0.0")
```

**All**

```kotlin
implementation("cloud.shoplive:shoplive-player-sdk:3.0.0")
implementation("cloud.shoplive:shoplive-streamer-sdk:3.0.0")
```

Using both products still resolves each shared module exactly once — Gradle deduplicates the
identical coordinates coming from both POMs.

With any of the three, the core surface is available without declaring it:

```kotlin
import cloud.shoplive.core.publicsurface.Shoplive
import cloud.shoplive.core.publicsurface.ShopliveConfiguration
import cloud.shoplive.core.publicsurface.ShopliveUser

Shoplive.initialize(context, ShopliveConfiguration(accessKey = "{ACCESS_KEY}"))
Shoplive.setUser(ShopliveUser.Guest)
```

**Shortform**

The Shortform SDK is versioned separately from the player and streamer. Unlike them, it does not
pull in the shared modules or the player engine it is built against, so declare those too, on the
versions shown here — including exactly one engine (`shoplive-media3` or `shoplive-exoplayer`):

<!-- shoplive-shortform-versions:start -->
```kotlin
implementation("cloud.shoplive:shoplive-filter:1.9.1")
implementation("cloud.shoplive:shoplive-short-form:1.9.1")
implementation("cloud.shoplive:shoplive-video-editor:1.9.1")

// Required: the Shortform SDK is built against these and does not pull them in — declare them too.
implementation("cloud.shoplive:shoplive-common:1.9.0")
implementation("cloud.shoplive:shoplive-network:1.9.0")
// Player engine — declare exactly one of the two.
implementation("cloud.shoplive:shoplive-media3:1.10.1.3")
// implementation("cloud.shoplive:shoplive-exoplayer:2.19.1.20")
```
<!-- shoplive-shortform-versions:end -->

Leave out `shoplive-video-editor` (and `shoplive-filter`) if you only play short-form videos.

## Releases

- [Releases](https://github.com/shoplive/shoplive-sdk-android/releases) — tagged versions with the
  AAR/POM assets attached, plus the release notes for each version. Shortform SDK releases are
  tagged `shortform-<version>`.
- The `maven-repo` branch holds the same binaries in Maven path layout for Gradle to resolve.
- The auto-generated "Source code (zip / tar.gz)" archives on a release are this distribution repo,
  not the SDK sources — use the AAR/POM assets or the Maven repository above.

## Ownership & Support

- Team: Shoplive Mobile
- Contact: [ask@shoplive.cloud](mailto:ask@shoplive.cloud)

