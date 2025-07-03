# CustomFS: Encrypted, Versioned, Compressed Userspace Filesystem

**CustomFS** is a modern C++17+ virtual filesystem built using FUSE. It supports transparent file encryption, automatic versioning, and optional compression while providing a POSIX-compliant interface. Designed for performance, modularity, and security, it serves as a practical and educational demonstration of systems-level programming in C++.

## Features

### Core
- Full read/write support for files and directories
- Mount/unmount via CLI
- Transparent AES-256 encryption of file contents and metadata
- POSIX-compatible permissions and timestamps
- Graceful crash recovery and safe writes

### Advanced
- Per-file versioning with historical access via pseudo-directory
- Zstandard or LZ4 compression support
- Modular pluggable storage backends (file blob, SQLite)
- Fast lookup via in-memory and persisted indexing
- Secure password-based key derivation (Argon2)

### Developer Tooling
- Built using modern CMake
- Unit and integration tests (Catch2 / Google Test)
- Extensible and documented architecture
- Optional: logging via `spdlog`, fuzzing support

---

## Getting Started

### Dependencies

- CMake >= 3.16
- C++17-compatible compiler
- FUSE 3.x
- zstd or lz4 (optional)
- libsodium or OpenSSL
- SQLite (optional)

### Build

```
git clone https://github.com/yourusername/customfs.git
cd customfs
mkdir build && cd build
cmake ..
make -j$(nproc)
```

### Mounting the Filesystem

```
./customfs mount -d /mnt/customfs -p mypassword
```

- `-d`: Mount directory
- `-p`: Encryption passphrase

Unmount:

```
fusermount3 -u /mnt/customfs
```

---

## Architecture Overview

The system is modularized into key layers:

- **Frontend (`filesystem/`)**: Implements the FUSE callbacks and high-level interface
- **Encryption (`encryption/`)**: AES-based symmetric encryption and key derivation
- **Compression (`compression/`)**: Optional zstd or lz4 wrapper
- **Metadata (`metadata/`)**: Inodes, timestamps, permissions, and file trees
- **Storage (`backend/`)**: Persistent store interface; file-based or SQLite-backed
- **Versioning (`versioning/`)**: Maintains file history with snapshot and rollback support

All layers are swappable and tested in isolation.

---

## Example Use Case

1. Mount a secure workspace:

```
./customfs mount -d /mnt/secure -p hunter2
```

2. Work with files normally:

```
echo "confidential" > /mnt/secure/note.txt
```

3. Access file history:

```
ls /mnt/secure/.versions/note.txt
cat /mnt/secure/.versions/note.txt/v1
```

4. Unmount when done:

```
fusermount3 -u /mnt/secure
```

---

## Tools

### fsctl

Utility to inspect, snapshot, or extract files from the filesystem without mounting.

```
fsctl snapshot --mount /mnt/secure --output snapshot.tar.gz
fsctl extract --version v2 --file note.txt --output note_old.txt
```

---

## File Format

Each file is stored as:

- Encrypted and optionally compressed blob
- Accompanied by metadata (timestamp, size, permissions)
- Versioned with immutable history stored as deltas or full snapshots (configurable)

---

## Roadmap

### MVP (v1.0)
- Basic FS operations
- Encryption
- Simple blob backend

### v1.1
- Versioning + pseudo-directory exposure
- Compression layer

### v1.2
- SQLite backend + indexing
- CLI snapshot/export tools

### v2.x (Stretch)
- Web UI
- Cloud sync (S3 backend)
- Access control (ACLs), multi-user keys

---

## Security

- Files and metadata are encrypted using AES-256-GCM
- Key derivation via Argon2id
- Secure memory erasure and random IVs
- Optional HMAC integrity checks

---

## Testing

Run tests via CTest:

```
cd build
ctest --output-on-failure
```

---

## License

MIT License

---

## Contributing

Contributions are welcome. See `CONTRIBUTING.md` or open an issue for design discussions.

---

## Acknowledgements

- [libfuse](https://github.com/libfuse/libfuse)
- [libsodium](https://libsodium.org/)
- [zstd](https://github.com/facebook/zstd)
- [Catch2](https://github.com/catchorg/Catch2)

