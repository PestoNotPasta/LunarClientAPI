# LunarClientAPI
LunarClientAPI is a Spigot plugin that allows developers to integrate features with LunarClient.
 
[![](https://jitpack.io/v/PestoNotPasta/LunarClientAPI.svg)](https://jitpack.io/#PestoNotPasta/LunarClientAPI)
[![License: AGPL v3](https://img.shields.io/badge/License-AGPL_v3-blue.svg)](https://www.gnu.org/licenses/agpl-3.0)

## Setup:

Unlike the original API, there is no need to add the NetHandler as it's now directly shaded! 

Firstly, download the most recent [release](https://github.com/PestoNotPasta/LunarClientAPI/releases), drop it in your `plugins` folder and restart. Then, simply add
the dependency to your plugin as follows, replacing `[LATEST]` with latest version:

##### Maven:
```xml
  <repository>
      <id>jitpack-repo</id>
      <url>https://jitpack.io</url>
  </repository>
  
  <dependency>
      <groupId>com.github.PestoNotPasta</groupId>
      <artifactId>LunarClientAPI</artifactId>
      <version>[LATEST]</version>
      <scope>provided</scope>
  </dependency>
```
##### Gradle:
```groovy
repositories {
    url = 'https://jitpack.io/'
}

dependencies {
    compileOnly 'com.github.PestoNotPasta:LunarClientAPI:[LATEST]'
}
```
##### IMPORTANT:
Don't forget to update your `plugin.yml`:
```yaml
depend: [LunarClientAPI]
```

#### Basics:
`LunarClientAPI.java` is a singleton, you can access it through `LunarClientAPI#getInstance()`.

To check if someone claims to be running the client, and is therefore able to integrate with server features, use 
`LunarClientAPI#isRunningLunarClient(Player|UUID)`. When the API registers a `Player` as running Lunar Client, a
`LCPlayerRegisterEvent` is fired. When unregistered, a `LCPlayerUnregisterEvent`.

You can access a view of `org.bukkit.Player`s currently running the client with:
`LunarClientAPI#getPlayersRunningLunarClient()`.

###### Protocol:
`LCPacket.java` represents a packet that can be sent to and from players running Lunar Client.

There are protocol events for these, any time a `LCPacket` is received by the server, a `LCPacketReceivedEvent` is fired,
and likewise, when sent, a `LCPacketSentEvent` -- both of which let you access but not modify the packet itself,
 and the sender/target.

#### Disabling a mod
* Instantiate a `ModSetting` that sets the enabled status to `false`.
* Send a `LCPacketModSettings` with `ModSettings` that affect the mods you wish to disable, like so:

```
ModSettings.ModSetting disabled = new ModSettings.ModSetting(false, new HashMap<>());

sendPacket(event.getPlayer(), new LCPacketModSettings(
    new ModSettings()
        .addModSetting("Coordinates", disabled)
        .addModSetting("textHotKey", disabled)
));
```

#### Changing a Server Rule
`ServerRule.java` represents a rule your server sets for each client. You can, for example, enable a quitting
confirmation for competitive games by using `LunarClientAPIServerRule.setRule(ServerRule.COMPETITIVE_GAME, true)`. You
will still need to send these server rules to users using `LunarClientAPIServerRule.sendServerRule(Player)` when they join.
(This pattern work for all ServerRule)

#### FAQ
- **Q**: How do I color a Waypoint or Border?
- **A**: Use an `int` which represents the RGB value of the color. Examples: `java.awt.Color.BLUE.getRGB()` | `new java.awt.Color(0, 0, 255).getRGB()`
-- `org.bukkit.Color`s can also be used, but since they store the color in `hex`, you call `#asRGB()`; for example: `org.bukkit.Color.BLUE.asRGB()`
