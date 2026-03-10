# VoteCore Developer API

Official API for VoteCore, a premium voting ecosystem for Minecraft. Use this API to hook into voting events, access player statistics, streaks, and community goals.

## 📦 Dependency

The easiest way to include the VoteCore API is via **JitPack**.

### Gradle (Groovy)
```groovy
repositories {
    maven { url 'https://jitpack.io' }
}

dependencies {
    compileOnly 'com.github.mattwithabat:votecore-api:v1.0.0'
}
```

### Gradle (Kotlin)
```kotlin
repositories {
    maven("https://jitpack.io")
}

dependencies {
    compileOnly("com.github.mattwithabat:votecore-api:v1.0.0")
}
```

### Maven
```xml
<repository>
    <id>jitpack.io</id>
    <url>https://jitpack.io</url>
</repository>

<dependency>
    <groupId>com.github.mattwithabat</groupId>
    <artifactId>votecore-api</artifactId>
    <version>v1.0.0</version>
    <scope>provided</scope>
</dependency>
```

---

### Manual Installation (Local JAR)
If you prefer not to use JitPack, you can download the `.jar` from the [Releases](https://github.com/mattwithabat/votecore-api/releases) tab and install it to your local Maven cache:

```bash
mvn install:install-file -Dfile=votecore-api-1.0.0.jar -DgroupId=dev.mattwithabat -DartifactId=votecore-api -Dversion=1.0.0 -Dpackaging=jar
```

## 📄 plugin.yml
Add `VoteCore` as a soft dependency so your plugin loads after it:
```yaml
softdepend:
  - VoteCore
```

## 🛠 Usage

### Accessing the API
```java
import dev.mattwithabat.votecore.api.VoteCoreAPI;
import dev.mattwithabat.votecore.api.VoteCoreProvider;

VoteCoreAPI api = VoteCoreProvider.get();

// Get a player's vote data
api.getPlayerData(player.getUniqueId()).thenAccept(data -> {
    int votes = data.getTotalVotes();
    int streak = data.getCurrentStreak();
    boolean onCooldown = data.hasCooldown("TopMinecraftServers", 24);
});

// Get top voters leaderboard
api.getTopVoters(10).thenAccept(topList -> {
    for (Map.Entry<String, Integer> entry : topList) {
        System.out.println(entry.getKey() + ": " + entry.getValue());
    }
});
```

### Listening to Events
```java
import dev.mattwithabat.votecore.api.event.VoteReceivedEvent;
import dev.mattwithabat.votecore.api.event.VoteRewardedEvent;
import dev.mattwithabat.votecore.api.event.VotePartyTriggeredEvent;

// Called asynchronously when a vote is received via the socket
@EventHandler
public void onVoteReceived(VoteReceivedEvent event) {
    String site = event.getServiceName();
    String username = event.getPlayerName();
}

// Called after rewards and milestones are processed
@EventHandler
public void onVoteRewarded(VoteRewardedEvent event) {
    int total = event.getNewTotalVotes();
}

// Called when a community vote party goal is hit
@EventHandler
public void onParty(VotePartyTriggeredEvent event) {
    int goal = event.getGoal();
}
```
