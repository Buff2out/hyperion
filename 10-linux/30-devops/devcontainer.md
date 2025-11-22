Here is your content rewritten as a clean, well-formatted Markdown file suitable for pasting into Obsidian:

```md
# Development Workflow: Nix + Docker + Helix

Excellent question! You're thinking about the complete development workflow. Below is a clear guide to using your Nix development environment alongside a Docker test container—fully integrated with Helix.

---

## Environment Strategy: Nix vs Docker

### Nix Development Environment (`nix develop`)
- **Purpose**: Where you write, edit, and lint your code  
- **Location**: Your NixOS host system  
- **Editor**: Helix runs here, with access to `bash-language-server` and `shellcheck`  
- **Benefits**: Fast iteration, immediate feedback, native performance  

### Docker Test Container
- **Purpose**: Validate that your scripts work on **Ubuntu 20.04** (the assignment’s target OS)  
- **Location**: Isolated container mimicking the specified environment  
- **Usage**: Run scripts to ensure portability and compliance  
- **Benefits**: Guarantees compatibility with the assignment requirements  

---

## Dockerfile for Ubuntu 20.04 Testing

Place this `Dockerfile` in your project root (`DO4_LinuxMonitoring_v2.0.ID_356280-1/`):

```dockerfile
# Dockerfile
FROM ubuntu:20.04

# Prevent interactive prompts during package installation
ENV DEBIAN_FRONTEND=noninteractive

# Update package list and install essential tools
RUN apt-get update && apt-get install -y \
    bash \
    coreutils \
    gawk \
    findutils \
    util-linux \
    && rm -rf /var/lib/apt/lists/*

# Create a non-root user for safer script execution
RUN useradd -ms /bin/bash appuser
USER appuser

# Set working directory
WORKDIR /app

# Default to bash shell for interactive testing
CMD ["/bin/bash"]
```

---

## Building and Running the Test Container

```bash
# From project root (where Dockerfile is located)
docker build -t linux-monitoring-test .

# Run container and mount your task directories
docker run --rm -it -v "$(pwd)/src:/app/src" linux-monitoring-test

# Inside the container (as 'appuser' on Ubuntu 20.04):
cd /app/src/01
bash main.sh /tmp/monitor_test 4 az 5 az.az 3kb

# Exit when done
exit
```

---

## Helix Configuration for Optimal Workflow

Create a project-specific Helix configuration in your project root:

```bash
mkdir -p .helix
```

### `.helix/languages.toml`

```toml
# Project-specific Helix configuration

[[language]]
name = "bash"
scope = "source.bash"
file-types = ["sh", "bash"]
shebangs = ["sh", "bash"]
roots = []
comment-token = "#"
language-servers = [ "bash-language-server" ]
auto-format = false  # Keep as false; formatting can break script logic

[language-server.bash-language-server]
command = "bash-language-server"
args = ["start"]

# Configure ShellCheck for linting
[language.bash.config]
shellcheck = { enable = true, severity = "warning" }

# Key mappings for this project
[keys.normal]
# Quick save and run current script
"A-r" = ":w<ret>:sh bash %<ret>"
# Run shellcheck on current file
"A-c" = ":sh shellcheck %<ret>"
```

> 💡 **Note**: Place this file at `DO4_LinuxMonitoring_v2.0.ID_356280-1/.helix/languages.toml`.

---

## Recommended Workflow

### Development Cycle (in Nix environment)
1. **Enter dev environment**:  
   ```bash
   nix develop
   ```
2. **Open Helix**:  
   ```bash
   cd src/01 && helix main.sh
   ```
3. **Edit code**: ShellCheck provides real-time inline linting.
4. **Quick test**: Press `Alt+r` in Helix to save and run the current script.
5. **Lint manually**: Press `Alt+c` to run ShellCheck in the terminal.

### Validation Cycle (in Docker)
1. **Build the container**:  
   ```bash
   docker build -t linux-monitoring-test .
   ```
2. **Run with volume mount**:  
   ```bash
   docker run --rm -it -v "$(pwd)/src:/app/src" linux-monitoring-test
   ```
3. **Inside the container**:  
   ```bash
   cd /app/src/01 && bash main.sh /tmp/test 4 az 5 az.az 3kb
   ```
4. **Verify**: Ensure `generator.log` is created and contains expected output.

---

## Integration with ShellCheck

ShellCheck is already included in your `flake.nix` and will lint automatically in Helix. To run it manually:

```bash
# Lint a single file
shellcheck src/01/main.sh

# Lint all bash scripts
find src -name "*.sh" -exec shellcheck {} \;
```

---

## End-to-End Test for Part 1

```bash
# 1. Develop in Nix/Helix
nix develop
cd src/01
helix main.sh  # Edit and save

# 2. Quick test in Nix
bash main.sh /tmp/monitor_test 4 az 5 az.az 3kb
ls -la /tmp/monitor_test/
cat generator.log

# 3. Full validation in Docker
docker run --rm -it -v "$(pwd):/app" linux-monitoring-test
# Inside container:
cd /app/src/01
bash main.sh /tmp/test 4 az 5 az.az 3kb
# Confirm identical behavior on Ubuntu 20.04
```

---

This workflow gives you the best of both worlds:
- **Fast, responsive development** with Nix + Helix + ShellCheck  
- **Guaranteed compatibility** via Docker testing against the exact target OS  
```