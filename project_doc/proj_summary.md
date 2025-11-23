# CobolCraft Project Summary

## Overview

CobolCraft is a fully functional Minecraft server implementation written entirely in COBOL. The project demonstrates modern COBOL programming capabilities by implementing a complex, real-time multiplayer game server that supports Minecraft 1.21.4 protocol.

## Project Purpose

This project serves as both a practical application and a learning exercise, showcasing:
- COBOL's capabilities in handling modern software development challenges
- Implementation of complex networking protocols
- Real-time multiplayer game server architecture
- Integration with existing game ecosystems (Minecraft)

## Architecture

### Core Components

1. **Main Server** (`src/server.cob`, `src/main.cob`)
   - Server initialization and game loop
   - Multiplayer session management
   - Real-time game state synchronization

2. **Network Layer** (`src/packets/`)
   - Clientbound packets (server → client)
   - Serverbound packets (client → server)
   - Protocol implementation for Minecraft 1.21.4
   - Handles handshake, status, login, configuration, and play phases

3. **World Management** (`src/world/`)
   - Infinite terrain generation
   - Dynamic chunk loading and persistence
   - Block and entity management
   - Region file I/O (Minecraft-compatible format)

4. **Game Logic**
   - **Blocks** (`src/blocks/`): Block types, interactions, state management
   - **Items** (`src/items/`): Item definitions, inventory management
   - **Entities** (`src/entities/`): Entity physics, collision detection
   - **Players** (`src/players/`): Player state, I/O, movement
   - **Physics** (`src/physics.cob`): Basic collision and physics simulation

5. **Data Processing**
   - **Encoding/Decoding** (`src/encoding/`): JSON, NBT, binary data formats
   - **Parsers** (`src/parsers/`): Recipe parsing, data extraction
   - **Registries** (`src/registries.cob`): Block/item/entity type registries

6. **Code Generator** (`codegen/`)
   - COBOL-based code generator
   - Generates COBOL source from Minecraft's JSON datapacks
   - Template-based code generation system
   - Automates creation of loot tables, item definitions, and registry code

7. **System Integration** (`cpp/`)
   - C++ utilities for low-level operations
   - TCP socket management
   - Precise timing and signal handling
   - OS-level operations not feasible in pure COBOL

## Key Features

### Implemented Features
- ✅ Infinite terrain generation with dynamic chunk loading
- ✅ World and player data persistence (Minecraft-compatible formats)
- ✅ Multiplayer support (configurable concurrent players, max 100)
- ✅ Server status/ping (appears online in server list)
- ✅ Block breaking and placing with auto-generated loot tables
- ✅ Block interactions (doors, crafting tables, signs, etc.)
- ✅ Player inventory system
- ✅ Crafting (2x2 and 3x3 grids)
- ✅ Item entities and pickup mechanics
- ✅ Chat system
- ✅ Command system (in-game and console)
- ✅ Server configuration via `server.properties`
- ✅ Whitelist system (persistent JSON storage)
- ✅ Basic collision detection and entity physics
- ✅ Fall damage, void damage, death, and respawn mechanics

### Supported Block Types
- Torches, slabs, stairs
- Rotated pillars (logs)
- Buttons (non-interactive)
- Doors, trapdoors
- Beds, signs
- Crafting tables
- Water, lava
- And more...

## Technical Stack

- **Language**: COBOL (GnuCOBOL 3.1.2+, 3.2+ recommended)
- **Build System**: Make
- **Compilers**: GnuCOBOL, GCC/G++
- **Dependencies**: zlib, curl, Java 21+ (for data extraction)
- **Deployment**: Linux (x86_64/arm64), Docker support

## Project Structure

```
CobolCraft/
├── src/                    # Main COBOL source code
│   ├── main.cob           # Entry point
│   ├── server.cob         # Server core
│   ├── packets/           # Network protocol implementation
│   ├── world/             # World management
│   ├── blocks/            # Block implementations
│   ├── items/             # Item implementations
│   ├── entities/          # Entity implementations
│   ├── encoding/          # Data encoding/decoding
│   ├── _copybooks/        # COBOL copybooks (includes)
│   └── ...
├── codegen/               # Code generator (COBOL)
│   ├── codegen.cob       # Generator main
│   ├── common/           # Generator utilities
│   ├── generators/       # Code generation modules
│   └── templates/        # Template files
├── cpp/                   # C++ system utilities
├── tests/                 # Unit tests
├── project_doc/          # Project documentation
└── Makefile              # Build configuration
```

## Development Approach

### Code Organization
- Modular design with separate programs for distinct functionality
- Copybooks for shared constants, procedures, and data structures
- Template-based code generation to reduce boilerplate
- Custom testing framework for unit tests

### Data Extraction
- Downloads official Minecraft server .jar
- Extracts JSON data (blocks, items, entities, recipes, loot tables)
- Generates COBOL code at compile-time
- Loads runtime data from JSON using custom parser

### Testing
- Custom COBOL-based testing framework
- Unit tests for encoding/decoding operations
- Focus on testing complex data transformations
- Run with `make test`

## Build and Deployment

### Local Build
```bash
make --jobs=$(nproc)
make run
```

### Docker Deployment
```bash
docker pull meyfa/cobolcraft:latest
# or build from source
docker build --tag meyfa/cobolcraft .
```

## Configuration

Server configuration via `server.properties`:
- `server-port`: Network port (default: 25565)
- `level-name`: World name (default: "world")
- `white-list`: Enable whitelist (default: false)
- `motd`: Server message (default: "CobolCraft")
- `max-players`: Maximum concurrent players (default: 10, max: 100)

## Challenges and Solutions

### Language Limitations
- **Challenge**: COBOL lacks modern language features (objects, dynamic memory, etc.)
- **Solution**: Procedural design patterns, external C++ utilities for system operations

### Protocol Complexity
- **Challenge**: Minecraft protocol is complex and object-oriented
- **Solution**: Careful translation to procedural COBOL, extensive packet handling code

### Data Formats
- **Challenge**: Need to parse/encode JSON, NBT, binary formats
- **Solution**: Custom COBOL implementations of parsers and encoders

### Code Generation
- **Challenge**: Large amounts of repetitive code for blocks/items
- **Solution**: COBOL-based code generator with template system

## Project Status

The project is actively maintained and supports the latest Minecraft version (1.21.4). It demonstrates that COBOL can be used for modern software development beyond traditional business applications.

## Learning Resources

- GnuCOBOL Programmer's Guide: https://gnucobol.sourceforge.io/HTML/gnucobpg.html
- Minecraft Protocol Documentation: https://minecraft.wiki/w/Minecraft_Wiki:Projects/wiki.vg_merge/Protocol

## License

MIT License - See LICENSE file for details.

## Notes

- "Minecraft" is a trademark of Mojang Synergies AB
- CobolCraft is not affiliated with or endorsed by Mojang
- This project serves as an educational demonstration of COBOL capabilities

