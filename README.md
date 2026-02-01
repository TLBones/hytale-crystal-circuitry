# Crystal Circuitry

A mod that aims to add functionality and creative in-game interactions to Hytale.

## Features

✅ **Crystals!** - Your crystals will have a purpose now! (No shame on Early-Access, I just found I had way too many to justify not doing anything with them)

✅ **Semi-Automation!** - Certain aspects of Hytale are semi-automatable with Crystal Magic! Each crystal has its own function, have fun exploring your creative options!

---

## Project Structure
# Pulled from Template information - Kept for Development steps/fallback & Credits - All Template Credit to GitHub user realBritakee for the amazing template design.
```
CrystalCircuitry/
├── .github/workflows/
│   └── build.yml                    # CI/CD workflow
├── buildSrc/
│   ├── build.gradle.kts             # Custom plugin configuration
│   └── src/main/kotlin/
│       └── RunHytalePlugin.kt       # Automated server testing
├── src/main/
│   ├── java/
│   │   ├── raven/crystalcircuitry/
│   │   │   └── CrystalCircuitry.java      # Minimal main class (example)
│   │   └── org/plugin/
│   │       ├──ExampleCommand.java      # Example plugin cases for fallback
│   │       └──ExamplePlugin.java      # Example plugin cases for fallback
│   └── resources/
│       ├── Server/Item/Recipes/Example_Recipe.json
│       └── manifest.json            # Plugin metadata
├── .gitignore                       # Git ignore rules
├── build.gradle.kts                 # Build configuration
├── gradle.properties                # Project properties
├── settings.gradle.kts              # Project settings
├── LICENSE                          # MIT License
└── README.md                        # This file
```

**Note:** This is a minimal template. Create your own folder structure:

- `commands/` - For command implementations
- `listeners/` - For event listeners
- `services/` - For business logic
- `storage/` - For data persistence
- `utils/` - For utility classes
- `config/` - For configuration management

---

## Development Workflow

### Building

```bash
# Compile only
./gradlew compileJava

# Build plugin JAR
./gradlew shadowJar

# Clean and rebuild
./gradlew clean shadowJar
```

### Testing

```bash
# Run server with your plugin
./gradlew runServer

# Run unit tests
./gradlew test

# Clean test server
rm -rf run/
```

### Debugging

```bash
# Run server in debug mode
./gradlew runServer -Pdebug

# Then connect your IDE debugger to localhost:5005
```

---

## Customization

### Adding Dependencies

Edit `build.gradle.kts`:

```kotlin
dependencies {
    // Hytale API (provided by server)
    compileOnly(files("./HytaleServer.jar"))

    // Your dependencies (will be bundled)
    implementation("com.google.code.gson:gson:2.10.1")

    // Test dependencies
    testImplementation("org.junit.jupiter:junit-jupiter:5.10.0")
}
```

### Configuring Server Testing

**Run Hytale Server** - A Gradle plugin to download and run a Hytale server for development and testing purposes. The server files will be located in the `run/` directory of the project. Before starting the server it will compile (shadowJar task) and copy the plugin jar to the server's `mods/` folder.

**Usage:**

Edit `build.gradle.kts`:

```kotlin
runHytale {
    jarUrl = "url to hytale server jar"
}
```

Run the server with:

```bash
# Windows
gradlew.bat runServer

# Linux/Mac
./gradlew runServer
```

**Features:**

- ✅ Automatic server JAR download and caching
- ✅ Compiles and deploys your plugin automatically
- ✅ Starts server with interactive console
- ✅ One-command workflow: `./gradlew runServer`
- ✅ Server files in `run/` directory (gitignored)

### Implementing Your Plugin
# Pulled from Template information - Kept for Development steps/fallback & Credits
**Recommended folder structure:**

```
src/main/java/com/yourname/yourplugin/
├── YourPlugin.java          # Main class
├── commands/                # Commands
├── listeners/               # Event listeners
├── services/                # Business logic
├── storage/                 # Data persistence
├── config/                  # Configuration
└── utils/                   # Utilities
```

**See our documentation for examples:**

- [Getting Started with Plugins](https://britakee-studios.gitbook.io/hytale-modding-documentation/plugins-java-development/07-getting-started-with-plugins)
- [Advanced Plugin Patterns](https://britakee-studios.gitbook.io/hytale-modding-documentation/plugins-java-development/12-advanced-plugin-patterns)
- [Common Plugin Features](https://britakee-studios.gitbook.io/hytale-modding-documentation/plugins-java-development/14-common-plugin-features)
# Pulled from Template information - Kept for Development steps/fallback & Credits
---

## CI/CD

This template includes a GitHub Actions workflow that:

1. ✅ Builds your plugin on every push
2. ✅ Runs tests
3. ✅ Uploads artifacts
4. ✅ Creates releases (when you tag)

### Creating a Release

```bash
git tag v1.0.0
git push origin v1.0.0
```

GitHub Actions will automatically build and create a release with your plugin JAR.

---

## Best Practices

### ✅ DO:

- Use the Service-Storage pattern for data management
- Write unit tests for your business logic
- Use structured logging (not `System.out.println`)
- Handle errors gracefully
- Document your public API
- Version your releases semantically (1.0.0, 1.1.0, etc.)

### ❌ DON'T:

- Hardcode configuration values
- Block the main thread with heavy operations
- Ignore exceptions
- Use deprecated APIs
- Commit sensitive data (API keys, passwords)

---

## Troubleshooting

### Build Fails

```bash
# Clean and rebuild
./gradlew clean build --refresh-dependencies
```

### Server Won't Start

1. Check that `jarUrl` in `build.gradle.kts` is correct
2. Verify Java 25 is installed: `java -version`
3. Check logs in `run/logs/`

### Plugin Not Loading

1. Verify `manifest.json` has correct `Main` class
2. Check server logs for errors
3. Ensure all dependencies are bundled in JAR

---

## Documentation

For detailed guides on plugin development, see:

- [Hytale Modding Documentation](https://github.com/yourusername/hytale-modding/tree/main/Documentation)
- [Getting Started with Plugins](../Documentation/07-getting-started-with-plugins.md)
- [Advanced Plugin Patterns](../Documentation/12-advanced-plugin-patterns.md)
- [Common Plugin Features](../Documentation/14-common-plugin-features.md)

---

## Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

---

## License

This template is released under the MIT License. You are free to use it for any purpose.

---

## Support

- **Issues:** [GitHub Issues](https://github.com/yourusername/hytale-plugin-template/issues)
- **Documentation:** [Hytale Modding Docs](https://github.com/yourusername/hytale-modding)
- **Community:** Join the Hytale modding community

---

## Credits

Created by the Hytale modding community.

Based on best practices from production Hytale plugins.

---

**Happy Modding! 🎮**
