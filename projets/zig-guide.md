# Cours Complet Zig 0.12+ - Développement Système et Surveillance

## Table des Matières
1. [Introduction à Zig](#introduction-à-zig)
2. [Installation et Configuration](#installation-et-configuration)
3. [Nouveau Système de Build](#nouveau-système-de-build)
4. [Syntaxe de Base](#syntaxe-de-base)
5. [Structures de Données](#structures-de-données)
6. [Gestion Mémoire](#gestion-mémoire)
7. [Programmation Réseau - Sniffer](#programmation-réseau---sniffer)
8. [Surveillance de Fichiers - Watcher](#surveillance-de-fichiers---watcher)
9. [Manipulation JSON](#manipulation-json)
10. [Projet Pratique - Système de Surveillance](#projet-pratique---système-de-surveillance)

---

## Introduction à Zig

Zig 0.12+ apporte de nombreuses améliorations :
- **Nouveau système de build** avec `build.zig.zon`
- **Package manager** intégré
- **Meilleure gestion des erreurs**
- **API std simplifiée**
- **Performance améliorée**

### Nouveautés importantes
- `std.Build` remplace l'ancien système
- Gestion native des dépendances
- Compilation cross-platform simplifiée
- Meilleure intégration avec C/C++

---

## Installation et Configuration

### Installation Zig 0.12+
```bash
# Télécharger depuis ziglang.org/download
# Linux
wget https://ziglang.org/download/0.12.0/zig-linux-x86_64-0.12.0.tar.xz
tar -xf zig-linux-x86_64-0.12.0.tar.xz
export PATH=$PATH:$(pwd)/zig-linux-x86_64-0.12.0

# macOS
wget https://ziglang.org/download/0.12.0/zig-macos-x86_64-0.12.0.tar.xz
tar -xf zig-macos-x86_64-0.12.0.tar.xz
export PATH=$PATH:$(pwd)/zig-macos-x86_64-0.12.0

# Vérifier l'installation
zig version
```

### Structure d'un Projet Moderne
```
surveillance_system/
├── build.zig
├── build.zig.zon          # Nouveau fichier de configuration
├── src/
│   ├── main.zig
│   ├── sniffer.zig
│   ├── watcher.zig
│   └── json_utils.zig
├── deps/                   # Dépendances (généré automatiquement)
└── zig-out/               # Sortie de compilation
    └── bin/
```

---

## Nouveau Système de Build

### build.zig.zon (Gestionnaire de dépendances)
```zig
.{
    .name = "surveillance_system",
    .version = "0.1.0",
    .minimum_zig_version = "0.12.0",
    
    .dependencies = .{
        // Exemple de dépendance (nous n'en avons pas besoin pour ce projet)
        // .some_lib = .{
        //     .url = "https://github.com/example/lib/archive/main.tar.gz",
        //     .hash = "1234567890abcdef...",
        // },
    },
    
    .paths = .{
        "build.zig",
        "build.zig.zon",
        "src",
    },
}
```

### build.zig Moderne
```zig
const std = @import("std");

pub fn build(b: *std.Build) void {
    // Options de target et d'optimisation
    const target = b.standardTargetOptions(.{});
    const optimize = b.standardOptimizeOption(.{});
    
    // Exécutable principal
    const exe = b.addExecutable(.{
        .name = "surveillance_system",
        .root_source_file = b.path("src/main.zig"),
        .target = target,
        .optimize = optimize,
    });
    
    // Ajouter des liens système pour les sockets raw
    exe.linkLibC();
    if (target.result.os.tag == .linux) {
        exe.linkSystemLibrary("cap"); // Pour les capabilities Linux
    }
    
    // Installer l'exécutable
    b.installArtifact(exe);
    
    // Commande run
    const run_cmd = b.addRunArtifact(exe);
    run_cmd.step.dependOn(b.getInstallStep());
    if (b.args) |args| {
        run_cmd.addArgs(args);
    }
    const run_step = b.step("run", "Run the application");
    run_step.dependOn(&run_cmd.step);
    
    // Tests
    const unit_tests = b.addTest(.{
        .root_source_file = b.path("src/main.zig"),
        .target = target,
        .optimize = optimize,
    });
    const run_unit_tests = b.addRunArtifact(unit_tests);
    const test_step = b.step("test", "Run unit tests");
    test_step.dependOn(&run_unit_tests.step);
}
```

---

## Syntaxe de Base

### Variables et Types (Zig 0.12+)
```zig
const std = @import("std");

pub fn main() !void {
    // Variables
    var number: i32 = 42;
    const pi: f64 = 3.14159;
    
    // Types de base
    const byte_val: u8 = 255;
    const signed_int: i64 = -1234567890;
    const boolean: bool = true;
    
    // Chaînes de caractères
    const message: []const u8 = "Hello, Zig 0.12!";
    
    // Arrays avec inférence de taille
    var array = [_]i32{1, 2, 3, 4, 5};
    
    // Slices
    const slice: []const i32 = array[1..4];
    
    // Nouveau système de print
    std.debug.print("Number: {}, Message: {s}\n", .{number, message});
    std.debug.print("Array: {any}\n", .{array});
    std.debug.print("Slice: {any}\n", .{slice});
}
```

### Gestion d'Erreurs Moderne
```zig
// Union d'erreurs
const FileError = error{
    FileNotFound,
    PermissionDenied,
    InvalidPath,
};

const MathError = error{
    DivisionByZero,
    Overflow,
};

// Fonction avec erreurs multiples
fn divide(a: f64, b: f64) (MathError || FileError)!f64 {
    if (b == 0) return MathError.DivisionByZero;
    if (a > 1e100) return MathError.Overflow;
    return a / b;
}

// Try avec capture d'erreur
pub fn main() !void {
    const result = divide(10.0, 2.0) catch |err| switch (err) {
        MathError.DivisionByZero => {
            std.debug.print("Erreur: Division par zéro!\n", .{});
            return;
        },
        MathError.Overflow => {
            std.debug.print("Erreur: Overflow!\n", .{});
            return;
        },
        else => {
            std.debug.print("Erreur inconnue: {}\n", .{err});
            return;
        },
    };
    
    std.debug.print("Résultat: {d:.2}\n", .{result});
}
```

### Structures et Méthodes
```zig
const std = @import("std");

const Person = struct {
    name: []const u8,
    age: u32,
    
    const Self = @This();
    
    // Constructeur
    pub fn init(name: []const u8, age: u32) Self {
        return Self{
            .name = name,
            .age = age,
        };
    }
    
    // Méthode
    pub fn greet(self: Self) void {
        std.debug.print("Salut, je suis {s} et j'ai {} ans\n", .{self.name, self.age});
    }
    
    // Méthode modifiant l'instance
    pub fn birthday(self: *Self) void {
        self.age += 1;
        std.debug.print("{s} a maintenant {} ans!\n", .{self.name, self.age});
    }
};

// Enum avec méthodes
const FileEvent = enum {
    created,
    modified,
    deleted,
    
    const Self = @This();
    
    pub fn toString(self: Self) []const u8 {
        return switch (self) {
            .created => "[CREATED]",
            .modified => "[MODIFIED]",
            .deleted => "[DELETED]",
        };
    }
    
    pub fn isDestructive(self: Self) bool {
        return self == .deleted;
    }
};

pub fn main() !void {
    var person = Person.init("Alice", 30);
    person.greet();
    person.birthday();
    
    const event = FileEvent.created;
    std.debug.print("Event: {s}, Destructive: {}\n", .{event.toString(), event.isDestructive()});
}
```

---

## Structures de Données

### ArrayList et Gestion Mémoire Moderne
```zig
const std = @import("std");

pub fn main() !void {
    // Allocateur moderne
    var gpa = std.heap.GeneralPurposeAllocator(.{}){};
    defer _ = gpa.deinit();
    const allocator = gpa.allocator();
    
    // ArrayList
    var list = std.ArrayList(i32).init(allocator);
    defer list.deinit();
    
    // Ajouter des éléments
    try list.appendSlice(&[_]i32{10, 20, 30, 40, 50});
    
    // Méthodes modernes
    try list.insert(2, 25); // Insérer à l'index 2
    
    // Itération moderne
    for (list.items, 0..) |item, i| {
        std.debug.print("[{}]: {}\n", .{i, item});
    }
    
    // Recherche
    if (std.mem.indexOf(i32, list.items, &[_]i32{25})) |index| {
        std.debug.print("25 trouvé à l'index: {}\n", .{index});
    }
    
    // Tri
    std.mem.sort(i32, list.items, {}, comptime std.sort.desc(i32));
    std.debug.print("Trié (desc): {any}\n", .{list.items});
}
```

### HashMap Moderne
```zig
const std = @import("std");

pub fn main() !void {
    var gpa = std.heap.GeneralPurposeAllocator(.{}){};
    defer _ = gpa.deinit();
    const allocator = gpa.allocator();
    
    // HashMap moderne avec contexte automatique
    var map = std.HashMap([]const u8, i32, std.HashMap.StringContext, std.hash_map.default_max_load_percentage).init(allocator);
    defer map.deinit();
    
    // Ajouter des éléments
    try map.put("Alice", 25);
    try map.put("Bob", 30);
    try map.put("Charlie", 35);
    
    // Méthodes modernes
    const result = try map.getOrPut("Diana");
    if (!result.found_existing) {
        result.value_ptr.* = 28;
    }
    
    // Itération
    var iterator = map.iterator();
    while (iterator.next()) |entry| {
        std.debug.print("{s}: {}\n", .{entry.key_ptr.*, entry.value_ptr.*});
    }
    
    // Vérification et récupération
    if (map.get("Alice")) |age| {
        std.debug.print("Alice a {} ans\n", .{age});
    }
}
```

---

## Programmation Réseau - Sniffer

### Module Sniffer Avancé
```zig
// src/sniffer.zig
const std = @import("std");
const net = std.net;
const posix = std.posix;

pub const Protocol = enum(u8) {
    icmp = 1,
    tcp = 6,
    udp = 17,
    
    pub fn toString(self: Protocol) []const u8 {
        return switch (self) {
            .icmp => "ICMP",
            .tcp => "TCP",
            .udp => "UDP",
        };
    }
};

pub const PacketInfo = struct {
    source_ip: [4]u8,
    dest_ip: [4]u8,
    protocol: Protocol,
    source_port: ?u16 = null,
    dest_port: ?u16 = null,
    size: usize,
    timestamp: i64,
    payload_preview: [64]u8,
    payload_size: usize,
    
    pub fn format(self: PacketInfo, comptime fmt: []const u8, options: std.fmt.FormatOptions, writer: anytype) !void {
        _ = fmt;
        _ = options;
        
        const timestamp = std.time.timestamp();
        try writer.print("[{}] {}.{}.{}.{}", .{
            timestamp,
            self.source_ip[0], self.source_ip[1], 
            self.source_ip[2], self.source_ip[3]
        });
        
        if (self.source_port) |port| {
            try writer.print(":{}", .{port});
        }
        
        try writer.print(" -> {}.{}.{}.{}", .{
            self.dest_ip[0], self.dest_ip[1], 
            self.dest_ip[2], self.dest_ip[3]
        });
        
        if (self.dest_port) |port| {
            try writer.print(":{}", .{port});
        }
        
        try writer.print(" [{}] {} bytes", .{self.protocol.toString(), self.size});
    }
};

pub const NetworkSniffer = struct {
    allocator: std.mem.Allocator,
    socket: posix.socket_t,
    running: std.atomic.Value(bool),
    packet_count: std.atomic.Value(u64),
    
    const Self = @This();
    
    pub fn init(allocator: std.mem.Allocator) !Self {
        // Créer un raw socket (nécessite root)
        const socket = try posix.socket(
            posix.AF.INET, 
            posix.SOCK.RAW, 
            posix.IPPROTO.TCP
        );
        
        // Option pour inclure l'en-tête IP
        const on: c_int = 1;
        _ = posix.setsockopt(
            socket, 
            posix.IPPROTO.IP, 
            posix.IP.HDRINCL, 
            std.mem.asBytes(&on)
        ) catch |err| {
            std.debug.print("Attention: Impossible de définir IP_HDRINCL: {}\n", .{err});
        };
        
        return Self{
            .allocator = allocator,
            .socket = socket,
            .running = std.atomic.Value(bool).init(false),
            .packet_count = std.atomic.Value(u64).init(0),
        };
    }
    
    pub fn deinit(self: *Self) void {
        self.stop();
        posix.close(self.socket);
    }
    
    pub fn start(self: *Self, callback: *const fn(PacketInfo) void) !void {
        self.running.store(true, .seq_cst);
        var buffer: [65536]u8 = undefined;
        
        std.debug.print("[SNIFFER] Démarrage de la capture réseau...\n");
        std.debug.print("[SNIFFER] ATTENTION: Nécessite des privilèges root!\n");
        
        while (self.running.load(.seq_cst)) {
            const bytes_received = posix.recv(self.socket, &buffer, 0) catch |err| switch (err) {
                error.WouldBlock => continue,
                else => {
                    std.debug.print("[SNIFFER] Erreur de réception: {}\n", .{err});
                    continue;
                },
            };
            
            if (bytes_received < 20) continue; // Trop petit pour être un paquet IP valide
            
            const packet = self.parsePacket(buffer[0..bytes_received]) catch continue;
            callback(packet);
            
            _ = self.packet_count.fetchAdd(1, .seq_cst);
        }
    }
    
    pub fn stop(self: *Self) void {
        self.running.store(false, .seq_cst);
        std.debug.print("[SNIFFER] Arrêt de la capture. Paquets capturés: {}\n", .{
            self.packet_count.load(.seq_cst)
        });
    }
    
    fn parsePacket(self: *Self, data: []const u8) !PacketInfo {
        _ = self;
        if (data.len < 20) return error.PacketTooSmall;
        
        // Parse IP header (simplified)
        const version_and_length = data[0];
        const ip_header_length = (version_and_length & 0x0F) * 4;
        
        if (data.len < ip_header_length) return error.InvalidPacket;
        
        const protocol_num = data[9];
        const protocol = switch (protocol_num) {
            1 => Protocol.icmp,
            6 => Protocol.tcp,
            17 => Protocol.udp,
            else => Protocol.icmp, // Default pour les protocoles non supportés
        };
        
        // Extract IP addresses
        var source_ip: [4]u8 = undefined;
        var dest_ip: [4]u8 = undefined;
        @memcpy(&source_ip, data[12..16]);
        @memcpy(&dest_ip, data[16..20]);
        
        // Extract ports pour TCP/UDP
        var source_port: ?u16 = null;
        var dest_port: ?u16 = null;
        
        if ((protocol == .tcp or protocol == .udp) and data.len >= ip_header_length + 4) {
            const transport_header = data[ip_header_length..];
            source_port = std.mem.readInt(u16, transport_header[0..2], .big);
            dest_port = std.mem.readInt(u16, transport_header[2..4], .big);
        }
        
        // Extract payload preview
        var payload_preview: [64]u8 = std.mem.zeroes([64]u8);
        const payload_start = ip_header_length + if (protocol == .tcp or protocol == .udp) @as(usize, 8) else 0;
        const payload_size = if (data.len > payload_start) data.len - payload_start else 0;
        
        if (payload_size > 0) {
            const copy_size = @min(payload_size, payload_preview.len);
            @memcpy(payload_preview[0..copy_size], data[payload_start..payload_start + copy_size]);
        }
        
        return PacketInfo{
            .source_ip = source_ip,
            .dest_ip = dest_ip,
            .protocol = protocol,
            .source_port = source_port,
            .dest_port = dest_port,
            .size = data.len,
            .timestamp = std.time.timestamp(),
            .payload_preview = payload_preview,
            .payload_size = payload_size,
        };
    }
};

// Fonction utilitaire pour analyser les paquets
pub fn analyzePacket(packet: PacketInfo, suspicious_patterns: []const []const u8) bool {
    // Convertir le payload en string pour l'analyse
    const payload_str = packet.payload_preview[0..@min(packet.payload_size, packet.payload_preview.len)];
    
    for (suspicious_patterns) |pattern| {
        if (std.mem.indexOf(u8, payload_str, pattern) != null) {
            return true;
        }
    }
    
    // Vérifier les ports suspects
    if (packet.dest_port) |port| {
        const suspicious_ports = [_]u16{22, 23, 135, 139, 445, 1433, 3389};
        for (suspicious_ports) |suspicious_port| {
            if (port == suspicious_port) {
                return true;
            }
        }
    }
    
    return false;
}
```

---

## Surveillance de Fichiers - Watcher

### Module Watcher Avancé
```zig
// src/watcher.zig
const std = @import("std");
const posix = std.posix;

pub const FileEventType = enum {
    created,
    modified,
    deleted,
    moved,
    
    pub fn toString(self: FileEventType) []const u8 {
        return switch (self) {
            .created => "[CREATED]",
            .modified => "[MODIFIED]", 
            .deleted => "[DELETED]",
            .moved => "[MOVED]",
        };
    }
};

pub const FileEvent = struct {
    path: []const u8,
    event_type: FileEventType,
    timestamp: i64,
    file_size: ?u64 = null,
    is_directory: bool = false,
    
    pub fn format(self: FileEvent, comptime fmt: []const u8, options: std.fmt.FormatOptions, writer: anytype) !void {
        _ = fmt;
        _ = options;
        
        try writer.print("{s} {s}", .{self.event_type.toString(), self.path});
        if (self.file_size) |size| {
            try writer.print(" ({} bytes)", .{size});
        }
        if (self.is_directory) {
            try writer.print(" [DIR]");
        }
    }
};

pub const WatchConfig = struct {
    recursive: bool = true,
    watch_hidden: bool = false,
    file_patterns: []const []const u8 = &[_][]const u8{}, // Si vide, surveille tout
    quarantine_patterns: []const []const u8 = &[_][]const u8{"autofill", ".tmp", ".temp"},
    quarantine_dir: []const u8 = "/tmp/quarantine",
};

pub const FileWatcher = struct {
    allocator: std.mem.Allocator,
    watch_paths: std.ArrayList([]const u8),
    config: WatchConfig,
    running: std.atomic.Value(bool),
    file_states: std.HashMap([]const u8, FileState, std.HashMap.StringContext, std.hash_map.default_max_load_percentage),
    quarantine_count: std.atomic.Value(u32),
    
    const Self = @This();
    
    const FileState = struct {
        size: u64,
        mtime: i64,
        exists: bool,
    };
    
    pub fn init(allocator: std.mem.Allocator, config: WatchConfig) !Self {
        // Créer le répertoire de quarantaine s'il n'existe pas
        std.fs.cwd().makeDir(config.quarantine_dir) catch |err| switch (err) {
            error.PathAlreadyExists => {},
            else => return err,
        };
        
        return Self{
            .allocator = allocator,
            .watch_paths = std.ArrayList([]const u8).init(allocator),
            .config = config,
            .running = std.atomic.Value(bool).init(false),
            .file_states = std.HashMap([]const u8, FileState, std.HashMap.StringContext, std.hash_map.default_max_load_percentage).init(allocator),
            .quarantine_count = std.atomic.Value(u32).init(0),
        };
    }
    
    pub fn deinit(self: *Self) void {
        self.stop();
        
        // Libérer les paths
        for (self.watch_paths.items) |path| {
            self.allocator.free(path);
        }
        self.watch_paths.deinit();
        
        // Libérer les clés du HashMap
        var iterator = self.file_states.iterator();
        while (iterator.next()) |entry| {
            self.allocator.free(entry.key_ptr.*);
        }
        self.file_states.deinit();
    }
    
    pub fn addPath(self: *Self, path: []const u8) !void {
        const owned_path = try self.allocator.dupe(u8, path);
        try self.watch_paths.append(owned_path);
        
        // Scanner initial pour établir l'état de base
        try self.scanDirectory(path);
    }
    
    pub fn start(self: *Self, callback: *const fn(FileEvent) void) !void {
        self.running.store(true, .seq_cst);
        
        std.debug.print("[WATCHER] Démarrage de la surveillance de fichiers...\n");
        for (self.watch_paths.items) |path| {
            std.debug.print("[WATCHER] Surveillance: {s}\n", .{path});
        }
        
        while (self.running.load(.seq_cst)) {
            for (self.watch_paths.items) |path| {
                try self.checkDirectory(path, callback);
            }
            
            // Attendre avant le prochain scan
            std.time.sleep(1 * std.time.ns_per_s); // 1 seconde
        }
    }
    
    pub fn stop(self: *Self) void {
        self.running.store(false, .seq_cst);
        std.debug.print("[WATCHER] Arrêt de la surveillance. Fichiers mis en quarantaine: {}\n", .{
            self.quarantine_count.load(.seq_cst)
        });
    }
    
    fn scanDirectory(self: *Self, dir_path: []const u8) !void {
        var dir = std.fs.cwd().openDir(dir_path, .{ .iterate = true }) catch |err| {
            std.debug.print("[WATCHER] Erreur ouverture {s}: {}\n", .{dir_path, err});
            return;
        };
        defer dir.close();
        
        var iterator = dir.iterate();
        while (try iterator.next()) |entry| {
            var path_buffer: [std.fs.MAX_PATH_BYTES]u8 = undefined;
            const full_path = try std.fmt.bufPrint(&path_buffer, "{s}/{s}", .{dir_path, entry.name});
            
            // Ignorer les fichiers cachés si non configuré
            if (!self.config.watch_hidden and entry.name[0] == '.') continue;
            
            const stat = std.fs.cwd().statFile(full_path) catch continue;
            
            const state = FileState{
                .size = stat.size,
                .mtime = stat.mtime,
                .exists = true,
            };
            
            const owned_path = try self.allocator.dupe(u8, full_path);
            try self.file_states.put(owned_path, state);
            
            // Récursion pour les dossiers
            if (self.config.recursive and entry.kind == .directory) {
                try self.scanDirectory(full_path);
            }
        }
    }
    
    fn checkDirectory(self: *Self, dir_path: []const u8, callback: *const fn(FileEvent) void) !void {
        var dir = std.fs.cwd().openDir(dir_path, .{ .iterate = true }) catch return;
        defer dir.close();
        
        var current_files = std.HashMap([]const u8, bool, std.HashMap.StringContext, std.hash_map.default_max_load_percentage).init(self.allocator);
        defer current_files.deinit();
        
        var iterator = dir.iterate();
        while (try iterator.next()) |entry| {
            if (!self.config.watch_hidden and entry.name[0] == '.') continue;
            
            var path_buffer: [std.fs.MAX_PATH_BYTES]u8 = undefined;
            const full_path = try std.fmt.bufPrint(&path_buffer, "{s}/{s}", .{dir_path, entry.name});
            
            try current_files.put(full_path, true);
            
            const stat = std.fs.cwd().statFile(full_path) catch continue;
            
            const current_state = FileState{
                .size = stat.size,
                .mtime = stat.mtime,
                .exists = true,
            };
            
            if (self.file_states.get(full_path)) |old_state| {
                // Fichier modifié
                if (old_state.mtime != current_state.mtime or old_state.size != current_state.size) {
                    const event = FileEvent{
                        .path = full_path,
                        .event_type = .modified,
                        .timestamp = std.time.timestamp(),
                        .file_size = current_state.size,
                        .is_directory = entry.kind == .directory,
                    };
                    callback(event);
                    
                    // Mise à jour de l'état
                    try self.file_states.put(full_path, current_state);
                }
            } else {
                // Nouveau fichier
                const event = FileEvent{
                    .path = full_path,
                    .event_type = .created,
                    .timestamp = std.time.timestamp(),
                    .file_size = current_state.size,
                    .is_directory = entry.kind == .directory,
                };
                callback(event);
                
                // Vérifier si le fichier doit être mis en quarantaine
                try self.checkQuarantine(full_path, entry.name);
                
                const owned_path = try self.allocator.dupe(u8, full_path);
                try self.file_states.put(owned_path, current_state);
            }
            
            // Récursion pour les dossiers
            if (self.config.recursive and entry.kind == .directory) {
                try self.checkDirectory(full_path, callback);
            }
        }
        
        // Vérifier les fichiers supprimés
        var state_iterator = self.file_states.iterator();
        while (state_iterator.next()) |state_entry| {
            if (std.mem.startsWith(u8, state_entry.key_ptr.*, dir_path)) {
                if (!current_files.contains(state_entry.key_ptr.*)) {
                    const event = FileEvent{
                        .path = state_entry.key_ptr.*,
                        .event_type = .deleted,
                        .timestamp = std.time.timestamp(),
                        .file_size = state_entry.value_ptr.size,
                        .is_directory = false,
                    };
                    callback(event);
                    
                    // Marquer comme supprimé (on pourrait aussi supprimer l'entrée)
                    state_entry.value_ptr.exists = false;
                }
            }
        }
    }
    
    fn checkQuarantine(self: *Self, file_path: []const u8, filename: []const u8) !void {
        // Vérifier si le fichier correspond aux patterns de quarantaine
        for (self.config.quarantine_patterns) |pattern| {
            if (std.mem.indexOf(u8, filename, pattern) != null) {
                try self.quarantineFile(file_path, filename);
                return;
            }
        }
    }
    
    fn quarantineFile(self: *Self, file_path: []const u8, filename: []const u8) !void {
        var quarantine_path_buffer: [std.fs.MAX_PATH_BYTES]u8 = undefined;
        const timestamp = std.time.timestamp();
        const quarantine_filename = try std.fmt.bufPrint(&quarantine_path_buffer, 
            "{s}/{s}_{}", .{self.config.quarantine_dir, filename, timestamp});
        
        // Copier le fichier vers la quarantaine
        std.fs.cwd().copyFile(file_path, std.fs.cwd(), quarantine_filename, .{}) catch |err| {
            std.debug.print("[WATCHER] Erreur quarantaine {s}: {}\n", .{file_path, err});
            return;
        };
        
        // Supprimer le fichier original
        std.fs.cwd().deleteFile(file_path) catch |err| {
            std.debug.print("[WATCHER] Erreur suppression {s}: {}\n", .{file_path, err});
            return;
        };
        
        _ = self.quarantine_count.fetchAdd(1, .seq_cst);
        std.debug.print("[QUARANTINE] {s} -> {s}\n", .{file_path, quarantine_filename});
    }
};

// Fonction utilitaire pour filtrer les événements
pub fn filterEvents(event: FileEvent, filters: []const []const u8) bool {
    if (filters.len == 0) return true;
    
    for (filters) |filter| {
        if (std.mem.indexOf(u8, event.path, filter) != null) {
            return true;
        }
    }
    return false;
}
```

---

## Manipulation JSON

### Module JSON Avancé
```zig
// src/json_utils.zig
const std = @import("std");

pub const JsonError = error{
    InvalidJson,
    MissingField,
    TypeError,
};

// Structure pour les logs de surveillance
pub const SurveillanceLog = struct {
    timestamp: i64,
    event_type: []const u8,
    source: []const u8, // "watcher" ou "sniffer"
    details: Details,
    severity: u8, // 1-10
    
    const Details = union(enum) {
        file_event: struct {
            path: []const u8,
            action: []const u8,
            size: ?u64,
        },
        network_event: struct {
            source_ip: []const u8,
            dest_ip: []const u8,
            protocol: []const u8,
            size: usize,
        },
    };
    
    pub fn toJson(self: SurveillanceLog, allocator: std.mem.Allocator) ![]u8 {
        var string = std.ArrayList(u8).init(allocator);
        defer string.deinit();
        
        try std.json.stringify(self, .{}, string.writer());
        return try allocator.dupe(u8, string.items);
    }
    
    pub fn fromJson(allocator: std.mem.Allocator, json_str: []const u8) !SurveillanceLog {
        const parsed = try std.json.parseFromSlice(SurveillanceLog, allocator, json_str, .{});
        defer parsed.deinit();
        return parsed.value;
    }
};

// Configuration du système
pub const SystemConfig = struct {
    watch_paths: [][]const u8,
    quarantine_patterns: [][]const u8,
    quarantine_dir: []const u8,
    log_file: []const u8,
    sniffer_enabled: bool,
    watcher_enabled: bool,
    max_log_size: usize,
    
    pub fn loadFromFile(allocator: std.mem.Allocator, file_path: []const u8) !SystemConfig {
        const file_content = try std.fs.cwd().readFileAlloc(allocator, file_path, 1024 * 1024);
        defer allocator.free(file_content);
        
        const parsed = try std.json.parseFromSlice(SystemConfig, allocator, file_content, .{});
        defer parsed.deinit();
        
        return parsed.value;
    }
    
    pub fn saveToFile(self: SystemConfig, allocator: std.mem.Allocator, file_path: []const u8) !void {
        const json_string = try std.json.stringifyAlloc(allocator, self, .{ .whitespace = .indent_2 });
        defer allocator.free(json_string);
        
        try std.fs.cwd().writeFile(.{
            .sub_path = file_path,
            .data = json_string,
        });
    }
    
    pub fn getDefault(allocator: std.mem.Allocator) !SystemConfig {
        const default_paths = try allocator.alloc([]const u8, 2);
        default_paths[0] = try allocator.dupe(u8, "/home");
        default_paths[1] = try allocator.dupe(u8, "/tmp");
        
        const default_patterns = try allocator.alloc([]const u8, 3);
        default_patterns[0] = try allocator.dupe(u8, "autofill");
        default_patterns[1] = try allocator.dupe(u8, ".tmp");
        default_patterns[2] = try allocator.dupe(u8, "suspicious");
        
        return SystemConfig{
            .watch_paths = default_paths,
            .quarantine_patterns = default_patterns,
            .quarantine_dir = try allocator.dupe(u8, "/tmp/quarantine"),
            .log_file = try allocator.dupe(u8, "/var/log/surveillance.json"),
            .sniffer_enabled = false, // Désactivé par défaut (nécessite root)
            .watcher_enabled = true,
            .max_log_size = 100 * 1024 * 1024, // 100MB
        };
    }
};

pub const JsonLogger = struct {
    allocator: std.mem.Allocator,
    log_file: []const u8,
    max_size: usize,
    current_size: usize,
    
    const Self = @This();
    
    pub fn init(allocator: std.mem.Allocator, log_file: []const u8, max_size: usize) Self {
        return Self{
            .allocator = allocator,
            .log_file = log_file,
            .max_size = max_size,
            .current_size = 0,
        };
    }
    
    pub fn log(self: *Self, entry: SurveillanceLog) !void {
        const json_string = try entry.toJson(self.allocator);
        defer self.allocator.free(json_string);
        
        // Vérifier la taille du fichier
        const stat = std.fs.cwd().statFile(self.log_file) catch |err| switch (err) {
            error.FileNotFound => blk: {
                self.current_size = 0;
                break :blk std.fs.File.Stat{
                    .size = 0,
                    .kind = .file,
                    .mode = 0,
                    .mtime = 0,
                    .atime = 0,
                    .ctime = 0,
                    .inode = 0,
                };
            },
            else => return err,
        };
        
        self.current_size = stat.size;
        
        // Rotation du log si nécessaire
        if (self.current_size + json_string.len > self.max_size) {
            try self.rotateLog();
        }
        
        // Écrire l'entrée
        const file = try std.fs.cwd().openFile(self.log_file, .{ .mode = .write_only });
        defer file.close();
        
        try file.seekFromEnd(0);
        try file.writeAll(json_string);
        try file.writeAll("\n");
    }
    
    fn rotateLog(self: *Self) !void {
        var backup_path_buffer: [std.fs.MAX_PATH_BYTES]u8 = undefined;
        const backup_path = try std.fmt.bufPrint(&backup_path_buffer, "{s}.old", .{self.log_file});
        
        // Supprimer l'ancien backup s'il existe
        std.fs.cwd().deleteFile(backup_path) catch {};
        
        // Renommer le fichier actuel
        try std.fs.cwd().rename(self.log_file, backup_path);
        
        self.current_size = 0;
        std.debug.print("[LOGGER] Rotation du log: {s} -> {s}\n", .{self.log_file, backup_path});
    }
    
    pub fn readLogs(self: Self, allocator: std.mem.Allocator, max_entries: usize) ![]SurveillanceLog {
        const file_content = std.fs.cwd().readFileAlloc(allocator, self.log_file, self.max_size) catch |err| switch (err) {
            error.FileNotFound => return &[_]SurveillanceLog{},
            else => return err,
        };
        defer allocator.free(file_content);
        
        var logs = std.ArrayList(SurveillanceLog).init(allocator);
        var lines = std.mem.split(u8, file_content, "\n");
        var count: usize = 0;
        
        while (lines.next()) |line| {
            if (line.len == 0) continue;
            if (count >= max_entries) break;
            
            const log_entry = SurveillanceLog.fromJson(allocator, line) catch continue;
            try logs.append(log_entry);
            count += 1;
        }
        
        return try logs.toOwnedSlice();
    }
};

// Utilitaires JSON génériques
pub fn parseJsonFile(comptime T: type, allocator: std.mem.Allocator, file_path: []const u8) !T {
    const file_content = try std.fs.cwd().readFileAlloc(allocator, file_path, 10 * 1024 * 1024);
    defer allocator.free(file_content);
    
    const parsed = try std.json.parseFromSlice(T, allocator, file_content, .{});
    defer parsed.deinit();
    
    return parsed.value;
}

pub fn saveJsonFile(comptime T: type, data: T, allocator: std.mem.Allocator, file_path: []const u8) !void {
    const json_string = try std.json.stringifyAlloc(allocator, data, .{ .whitespace = .indent_2 });
    defer allocator.free(json_string);
    
    try std.fs.cwd().writeFile(.{
        .sub_path = file_path,
        .data = json_string,
    });
}
```

---

## Projet Pratique - Système de Surveillance

### Fichier Principal
```zig
// src/main.zig
const std = @import("std");
const sniffer = @import("sniffer.zig");
const watcher = @import("watcher.zig");
const json_utils = @import("json_utils.zig");

const print = std.debug.print;

const SurveillanceSystem = struct {
    allocator: std.mem.Allocator,
    config: json_utils.SystemConfig,
    logger: json_utils.JsonLogger,
    network_sniffer: ?sniffer.NetworkSniffer,
    file_watcher: ?watcher.FileWatcher,
    running: std.atomic.Value(bool),
    
    const Self = @This();
    
    pub fn init(allocator: std.mem.Allocator, config_file: ?[]const u8) !Self {
        // Charger la configuration
        const config = if (config_file) |file|
            json_utils.SystemConfig.loadFromFile(allocator, file) catch blk: {
                print("[CONFIG] Impossible de charger {s}, utilisation des valeurs par défaut\n", .{file});
                break :blk try json_utils.SystemConfig.getDefault(allocator);
            }
        else 
            try json_utils.SystemConfig.getDefault(allocator);
        
        // Initialiser le logger
        var logger = json_utils.JsonLogger.init(allocator, config.log_file, config.max_log_size);
        
        // Initialiser le sniffer si activé
        var network_sniffer: ?sniffer.NetworkSniffer = null;
        if (config.sniffer_enabled) {
            network_sniffer = sniffer.NetworkSniffer.init(allocator) catch |err| {
                print("[SNIFFER] Erreur d'initialisation: {}\n", .{err});
                print("[SNIFFER] Le sniffer nécessite des privilèges root\n");
                null;
            };
        }
        
        // Initialiser le watcher si activé
        var file_watcher: ?watcher.FileWatcher = null;
        if (config.watcher_enabled) {
            const watch_config = watcher.WatchConfig{
                .recursive = true,
                .watch_hidden = false,
                .quarantine_patterns = config.quarantine_patterns,
                .quarantine_dir = config.quarantine_dir,
            };
            
            file_watcher = try watcher.FileWatcher.init(allocator, watch_config);
            
            // Ajouter les chemins à surveiller
            if (file_watcher) |*fw| {
                for (config.watch_paths) |path| {
                    fw.addPath(path) catch |err| {
                        print("[WATCHER] Erreur ajout path {s}: {}\n", .{path, err});
                    };
                }
            }
        }
        
        return Self{
            .allocator = allocator,
            .config = config,
            .logger = logger,
            .network_sniffer = network_sniffer,
            .file_watcher = file_watcher,
            .running = std.atomic.Value(bool).init(false),
        };
    }
    
    pub fn deinit(self: *Self) void {
        self.stop();
        
        if (self.network_sniffer) |*ns| {
            ns.deinit();
        }
        
        if (self.file_watcher) |*fw| {
            fw.deinit();
        }
    }
    
    pub fn start(self: *Self) !void {
        self.running.store(true, .seq_cst);
        
        print("=== SYSTÈME DE SURVEILLANCE DÉMARRÉ ===\n");
        print("Configuration:\n");
        print("  - Sniffer: {}\n", .{self.config.sniffer_enabled});
        print("  - Watcher: {}\n", .{self.config.watcher_enabled});
        print("  - Log: {s}\n", .{self.config.log_file});
        print("  - Quarantaine: {s}\n", .{self.config.quarantine_dir});
        
        // Threads pour les composants
        var threads: [2]?std.Thread = [_]?std.Thread{null, null};
        
        // Thread sniffer
        if (self.network_sniffer != null) {
            threads[0] = try std.Thread.spawn(.{}, runSniffer, .{self});
        }
        
        // Thread watcher
        if (self.file_watcher != null) {
            threads[1] = try std.Thread.spawn(.{}, runWatcher, .{self});
        }
        
        // Attendre l'arrêt
        while (self.running.load(.seq_cst)) {
            std.time.sleep(100 * std.time.ns_per_ms);
        }
        
        // Attendre les threads
        for (threads) |thread| {
            if (thread) |t| {
                t.join();
            }
        }
        
        print("=== SYSTÈME DE SURVEILLANCE ARRÊTÉ ===\n");
    }
    
    pub fn stop(self: *Self) void {
        self.running.store(false, .seq_cst);
    }
    
    fn runSniffer(self: *Self) !void {
        if (self.network_sniffer) |*ns| {
            try ns.start(onNetworkEvent);
        }
    }
    
    fn runWatcher(self: *Self) !void {
        if (self.file_watcher) |*fw| {
            try fw.start(onFileEvent);
        }
    }
    
    fn onNetworkEvent(packet: sniffer.PacketInfo) void {
        print("[NETWORK] {}\n", .{packet});
        
        // Analyser le paquet pour détecter des activités suspectes
        const suspicious_patterns = [_][]const u8{"password", "admin", "root", "hack"};
        const is_suspicious = sniffer.analyzePacket(packet, &suspicious_patterns);
        
        if (is_suspicious) {
            print("[ALERT] Paquet suspect détecté!\n");
        }
        
        // Log de l'événement (simplifiée pour l'exemple)
        // Dans une vraie implémentation, on passerait self en paramètre
    }
    
    fn onFileEvent(event: watcher.FileEvent) void {
        print("[FILE] {}\n", .{event});
        
        // Analyser l'événement
        const is_critical = std.mem.indexOf(u8, event.path, "autofill") != null or
                           std.mem.indexOf(u8, event.path, "suspicious") != null;
        
        if (is_critical) {
            print("[ALERT] Fichier critique détecté: {s}\n", .{event.path});
        }
    }
};

// Interface en ligne de commande
fn printUsage() void {
    print("Usage: surveillance_system [options]\n");
    print("Options:\n");
    print("  -c, --config <file>     Fichier de configuration JSON\n");
    print("  -h, --help             Afficher cette aide\n");
    print("  --create-config <file>  Créer un fichier de configuration par défaut\n");
    print("  --show-logs <n>        Afficher les n derniers logs\n");
}

pub fn main() !void {
    var gpa = std.heap.GeneralPurposeAllocator(.{}){};
    defer _ = gpa.deinit();
    const allocator = gpa.allocator();
    
    const args = try std.process.argsAlloc(allocator);
    defer std.process.argsFree(allocator, args);
    
    var config_file: ?[]const u8 = null;
    var create_config: ?[]const u8 = null;
    var show_logs: ?usize = null;
    
    // Parser les arguments
    var i: usize = 1;
    while (i < args.len) : (i += 1) {
        const arg = args[i];
        
        if (std.mem.eql(u8, arg, "-h") or std.mem.eql(u8, arg, "--help")) {
            printUsage();
            return;
        } else if (std.mem.eql(u8, arg, "-c") or std.mem.eql(u8, arg, "--config")) {
            if (i + 1 >= args.len) {
                print("Erreur: --config nécessite un argument\n");
                return;
            }
            i += 1;
            config_file = args[i];
        } else if (std.mem.eql(u8, arg, "--create-config")) {
            if (i + 1 >= args.len) {
                print("Erreur: --create-config nécessite un argument\n");
                return;
            }
            i += 1;
            create_config = args[i];
        } else if (std.mem.eql(u8, arg, "--show-logs")) {
            if (i + 1 >= args.len) {
                print("Erreur: --show-logs nécessite un argument\n");
                return;
            }
            i += 1;
            show_logs = std.fmt.parseInt(usize, args[i], 10) catch {
                print("Erreur: nombre invalide pour --show-logs\n");
                return;
            };
        }
    }
    
    // Créer un fichier de configuration
    if (create_config) |file| {
        const default_config = try json_utils.SystemConfig.getDefault(allocator);
        try default_config.saveToFile(allocator, file);
        print("Configuration par défaut créée: {s}\n", .{file});
        return;
    }
    
    // Afficher les logs
    if (show_logs) |n| {
        const logger = json_utils.JsonLogger.init(allocator, "/var/log/surveillance.json", 100 * 1024 * 1024);
        const logs = try logger.readLogs(allocator, n);
        defer allocator.free(logs);
        
        print("=== DERNIERS LOGS ({}) ===\n", .{logs.len});
        for (logs) |log| {
            print("[{}] {s} - {s}\n", .{log.timestamp, log.source, log.event_type});
        }
        return;
    }
    
    // Démarrer le système de surveillance
    var system = try SurveillanceSystem.init(allocator, config_file);
    defer system.deinit();
    
    // Gestionnaire de signal pour arrêt propre
    const signal_handler = struct {
        var sys: ?*SurveillanceSystem = null;
        
        fn handle(sig: c_int) callconv(.C) void {
            _ = sig;
            if (sys) |s| {
                s.stop();
            }
        }
    };
    
    signal_handler.sys = &system;
    
    // Installer les gestionnaires de signal (Unix only)
    if (@import("builtin").os.tag != .windows) {
        _ = std.c.signal(std.c.SIG.INT, signal_handler.handle);
        _ = std.c.signal(std.c.SIG.TERM, signal_handler.handle);
    }
    
    try system.start();
}
```

### Exemple de Configuration JSON
```json
{
  "watch_paths": [
    "/home/user/Documents",
    "/tmp",
    "/var/www"
  ],
  "quarantine_patterns": [
    "autofill",
    ".tmp",
    "suspicious",
    "malware"
  ],
  "quarantine_dir": "/home/user/quarantine",
  "log_file": "/home/user/surveillance.json",
  "sniffer_enabled": false,
  "watcher_enabled": true,
  "max_log_size": 104857600
}
```

### Compilation et Utilisation
```bash
# Compiler le projet
zig build

# Créer une configuration par défaut
./zig-out/bin/surveillance_system --create-config config.json

# Lancer la surveillance
sudo ./zig-out/bin/surveillance_system -c config.json

# Voir les logs
./zig-out/bin/surveillance_system --show-logs 10
```

## Conclusion

Ce cours couvre les aspects essentiels de Zig 0.12+ pour développer un système de surveillance complet :

1. **Système de build moderne** avec `build.zig.zon`
2. **Sniffer réseau** pour capturer et analyser le trafic
3. **Surveillance de fichiers** avec quarantaine automatique
4. **Gestion JSON** pour la configuration et les logs
5. **Architecture modulaire** et thread-safe

### Points Clés de Zig 0.12+
- Gestion explicite de la mémoire sans garbage collector
- Système d'erreurs robuste et explicite
- Performance comparable à C/C++
- Syntaxe moderne et lisible
- Excellent pour la programmation système

### Prochaines Étapes
- Ajouter une interface web de monitoring
- Implémenter des alertes par email/webhook
- Créer des règles de détection plus sophistiquées
- Ajouter la persistance en base de données
- Implémenter la surveillance distribuée