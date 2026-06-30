# FeralHeart Clone Game Client and Server Source Code

FeralHeart is a free-to-play, 3D online multiplayer roleplaying game developed and released by KovuLKD in 2011 as the successor of another game called Impressive Title. For over a decade the game has been closed-source with the original FeralHeart game and multiple spin-offs created with permission from KovuLKD. This code is a recreation of the FeralHeart source code, originally released by community member Vaynrat with permission from KovuLKD.

This code is NOT 1:1 the original version of KovuLKD's FeralHeart source code, and is rather a recreation that might be hybridized code- as has been discovered by Phloxenfree and Bingus- full credit to them for pointing this out.

This version of the code also contains patches for security vulnerabilities that had been discovered, as well.

This repository contains FeralHeart’s Windows (Win32) C++ codebase for running a FeralHeart-style game client and a trio of servers (front, main, map). It is provided to enable community duplication and learning, including running your own private FeralHeart-compatible game for personal or community use subject to the Acceptable Use Policy and license terms below. This repository contains source code only; third‑party SDKs and game assets are not included.

Important notices, acceptable use, and third‑party license details are included further below. Please read them before building or distributing anything based on this code.


## IMPORTANT NOTICES

This codebase is being released without a full build compilation setup. Not all of these instructions may work for you, but the codebase includes everything that is technically necessary to duplicate and create a new FH instance. This is a complex project and it will take strong commitment if an individual decides they wish to deploy their own FeralHeart game client and server. The following instructions are non-exhaustive and more work may be needed to ready a FH game client and server.



## Repository structure

- `Client/` – Visual Studio solution for the game client (`FeralHeart.sln`)
- `Server/` – Visual Studio solution for the servers (`FHServers.sln`) containing:
  - `FHFrontServer` (login/front)
  - `FHMainServer` (session/main)
  - `FHMapServer` (map/world)
- `Server/feralheart.sql` – MySQL schema
- `README.md` – this file


## Status

- Target platform: Windows (Win32, 32‑bit)
- Compiler/toolset: MSVC v120 (Visual Studio 2013 toolset). You may be able to use newer Visual Studio versions if you install the "VC++ 2013 v120 toolset" component.
- Build system: Visual Studio solutions and projects. Not currently configured for Code::Blocks, CMake, or cross‑platform builds.


## Quick start (overview)

1) Install Visual Studio with the MSVC v120 toolset and Windows SDK (x86).

2) Prepare dependencies (x86 builds) in the `deps/` folders expected by the projects (see Dependency layout):
   - Ogre SDK 1.7.4 (VS12) with OIS and Boost 1.57
   - RakNet 4.023
   - zlib and zziplib 0.13.57
   - irrKlang 1.5.0 (proprietary)
   - SkyX 0.4 (build from source)
   - PagedGeometry (ogre-paged) 1.1.4 (build from source)
   - (Debug only) FMOD 4.02 (proprietary)
   - MySQL++ 3.2.4 and MySQL Connector/C 6.1.11
Some dependencies are already included, but others are not, so be sure to download them and review what you need.

3) Build the client via `Client/FeralHeart.sln` and the servers via `Server/FHServers.sln` using `Release|Win32`.

4) Configure OGRE runtime files (`plugins.cfg`, `resources.cfg`) and add media/assets in `Client/build/client/`. Imagine `Client/build/client/` as the FH game directory you might be familiar with if you’ve played the original FeralHeart. 

5) Import `Server/feralheart.sql` into MySQL and create a `db` file for server DB credentials (see Database configuration).

6) Start servers (Front → Main → Map), then run the client.


## Requirements

- Windows 7 or later, 32‑bit target
- Visual Studio with the MSVC v120 (VS 2013) toolset
- MySQL Server 5.7 recommended (MySQL 8.0 may require legacy auth adjustments).
- Third‑party SDKs/libraries (x86):
  - Ogre SDK 1.7.4 (VS12), including OIS and Boost 1.57
  - RakNet 4.023
  - zlib and zziplib 0.13.57
  - irrKlang 1.5.0
  - SkyX 0.4
  - PagedGeometry (ogre-paged) 1.1.4
  - (Debug only) FMOD 4.02
  - MySQL++ 3.2.4 and MySQL Connector/C 6.1.11


## Dependency layout

The project files reference dependencies relative to each solution directory under `deps/`. Expected layout (folder names must match):

Client side (`Client/deps/`):
- `OgreSDK_vs12_v1-7-4/` (contains `include/OGRE`, `include/OIS`, `boost_1_57`, `lib/{debug,release}`, `bin/{debug,release}`)
- `RakNet_PC-4.023/` (headers in `Source/`, libraries in `Lib/`)
- `zziplib-0.13.57/` (`lib/Debug`, `lib/Release` with zziplib and zlib libs)
- `irrKlang-1.5.0/` (`include`, `lib`, `bin`)
- `SkyX-0.4/` (build to produce `SkyX{_d}.dll` and libs)
- `ogre-paged-1.1.4/` (PagedGeometry; build to produce libs)
- (Debug only) `fmod-4.02/` (DLL used in XDebug config)

Server side (`Server/deps/`):
- `RakNet_PC-4.023/`
- `mysql++-3.2.4/` (headers and built libs; projects reference `vc2008/Win32/{Debug,Release}`)
- `mysql-connector-c-6.1.11-win32/` (headers and `libmysql.dll`)

Notes:
- One client configuration references an absolute path (`C:\Users\[REPLACE WITH USERNAME]\...\Server\src\common`). If you see include errors for common headers, update the include path locally to the repo's `Client\src\common` or mirror the expected directory. Do not commit local environment changes.


## Building the client

1. Open `Client/FeralHeart.sln` in Visual Studio.
2. Select `Release|Win32` and Build. (`XDebug|Win32` is a debug configuration with extra deps like FMOD.)
3. The post‑build step copies required DLLs into `Client/build/client/` and creates `Client/build/client/media/`.
4. Provide OGRE runtime configs and media (see OGRE runtime and assets) before launching.

Linking details (from project):

- Links against: `OgreMain{_d}.lib`, `OIS{_d}.lib`, `RakNetLibStatic{Debug}.lib`, `zziplib{d}.lib`, `zlib{d}.lib`, `irrKlang.lib`, `SkyX{_d}.lib`, and Boost libs `libboost_{system,date_time,thread}-vc120-mt[-gd]-1_57.lib`.


## Building the servers

1. Open `Server/FHServers.sln` in Visual Studio.
2. Build `Release|Win32` for:
   - `FHFrontServer`
   - `FHMainServer`
   - `FHMapServer`
3. Post‑build steps copy server executables (and required MySQL DLLs for the front server) into `Server/build/server/`.


## Database configuration

1. Create a MySQL database and import `Server/feralheart.sql`.
2. In each server's working directory (or where you run the EXE), create a file named `db` with four lines:

```
<database_name>
<host_or_ip>
<username>
<password>
```

3. Start servers in this order: `FHFrontServer` → `FHMainServer` → `FHMapServer`.

Default networking (see `Server/src/common/NetworkDefines.h`):

- IPs: `127.0.0.1` by default
- Ports: Front 30000/30001, Main 30002, Map 30003


## OGRE runtime and assets

- Place OGRE runtime config files in `Client/build/client/`:
  - `plugins.cfg` – OGRE plugins; start from the OGRE SDK defaults and point to copied DLLs.
  - `resources.cfg` – resource locations; add your media paths.
  - (`ogre.cfg` is generated at first run if missing.)
- Media/assets are not included. Put your game media under `Client/build/client/media/` with paths matching `resources.cfg`.


## Running

1. Ensure servers are running and connected to the database.
2. Launch the client EXE from `Client/build/client/`.
3. If OGRE complains about missing plugins or resources, verify `plugins.cfg`, `resources.cfg`, and that required DLLs are present next to the EXE.


## Known issues and tips

- Absolute include path in one client configuration may fail on your machine. Adjust locally under Project Properties → C/C++ → Additional Include Directories.
- All projects build for 32‑bit. Ensure all dependencies are x86.
- MySQL 8.0: if using 8.0, you may need to enable legacy password auth or build MySQL++ against your exact client libs.
- Firewalls: when running across machines, open/forward TCP ports 30000–30003.
- In-game bans will not work correctly without some configuration and code fixes. Keep this in mind before deploying to production.


## Security and secrets

- Do not commit the `db` file or any credentials. Use environment‑specific copies.
- `Server/src/common/NetworkDefines.h` contains placeholders such as `SERVER_PASSWORD`. Never hardcode real secrets. Prefer runtime configuration or environment variables when you customize this codebase.


## Contributing

Contributions are welcome, especially around build modernization (CMake, x64), dependency updates, and replacing proprietary components with open alternatives. Please open an issue to discuss large changes before submitting a PR.


## Acknowledgements

This project builds upon the OGRE ecosystem and other open‑source components. "FeralHeart" and any related marks are the property of KovuLKD. We thank KovuLKD for granting us permission to open source his game, and for everyone that has helped positively foster this game community along the way.
