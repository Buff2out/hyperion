You are absolutely right. **My previous example was "Tutorial Mode" (easy to start), not "Professional/Solid Mode".**

For a "Solid Practice" workflow, **you should never commit credentials** to `reshape.toml`. Since you are already using **SOPS**, we should leverage it to inject the credentials at runtime.

Here is the **Professional "Rustacean" Workflow** using `sops`, `just`, and environment variables.

### 1. The Strategy

1.  **`.env.enc` (Encrypted)**: Holds the `POSTGRES_PASSWORD` and `POSTGRES_USER`.
2.  **`reshape.toml` (Generic)**: Contains only structure (host, port, db name), **no secrets**.
3.  **`Justfile` (The Glue)**: Unwraps the secrets from SOPS and injects them as environment variables (`PGPASSWORD`) which the underlying Rust Postgres driver (used by Reshape) should respect.

***

### 2. Implementation

#### Step A: Configure `.env` and Encrypt it
Create your `.env` (or edit your existing one) to include the standard Postgres environment variables.

```bash
# .env (Local unencrypted version for setup)
POSTGRES_USER=bootcamp
POSTGRES_PASSWORD=my_super_secure_password
POSTGRES_DB=pizzeria
# Standard env var that libpq/rust-postgres clients read automatically
PGPASSWORD=my_super_secure_password
PGUSER=bootcamp
PGHOST=localhost
```

**Encrypt it with SOPS:**
*(Assuming you ran `just setup-secrets` from my previous answer)*
```bash
sops --encrypt --age $(grep -oP "public key: \K.*" .config/keys.txt) --input-type 
dotenv --output-type dotenv .env > .env.enc
```
*Add `.env` to `.gitignore`. Commit `.env.enc`.*

#### Step B: Clean `reshape.toml`

Remove the credentials from the configuration file.

```toml
# reshape.toml
[database]
host = "localhost"
port = 5432
name = "pizzeria"
# username = ...  <-- REMOVE THESE
# password = ...  <-- REMOVE THESE

[migrations]
directory = "migrations"
```

#### Step C: Update `Justfile` (The Magic)

We will use `sops exec-env` to inject the `PGPASSWORD` variable into the environment before `reshape` starts.

**How this works:**
Most Rust database tools (including those using `tokio-postgres` which Reshape likely uses) will look for standard environment variables like `PGPASSWORD` or `PGUSER` if they are missing from the explicit config.

```just
set dotenv-load

# Wrapper to run commands with decrypted secrets
with-secrets cmd:
    sops exec-env .env.enc "{{cmd}}"

# ---------------------------------------------------------
# DATABASE MIGRATION COMMANDS (Using Sops)
# ---------------------------------------------------------

# Init: Starts DB and runs migrations securely
init: up
    @echo "Waiting for Postgres..."
    @sleep 2
    just migrate

# Migrate: Injects PGPASSWORD/PGUSER from .env.enc -> reshape
migrate:
    @echo "Applying migrations..."
    # We explicitly export PGUSER/PGPASSWORD from the Sops env for Reshape
    sops exec-env .env.enc -- reshape migrate

# Create new migration
add name:
    reshape migration create {{name}}

# Undo/Abort
undo:
    sops exec-env .env.enc -- reshape migration abort

# ---------------------------------------------------------
# INFRASTRUCTURE
# ---------------------------------------------------------

up:
    # Docker also needs the secrets to start the container
    just with-secrets "docker-compose up -d"

down:
    docker-compose down

tui:
    # Gobang also needs secrets
    just with-secrets "gobang -h localhost -p 5432 -d ${POSTGRES_DB}"
```

### 3. The "Paranoid" Alternative (Template Rendering)

If `reshape` (in some specific version) decides to **ignore** environment variables and *strictly* demands a password in `reshape.toml`, the "Solid" pattern is **Config Generation**.

You would treat `reshape.toml` as a template.

1.  Create `reshape.toml.tmpl` (with placeholders like `${POSTGRES_PASSWORD}`).
2.  Add `reshape.toml` to `.gitignore`.
3.  Update `Justfile` to render it on the fly:

```just
# Justfile (Alternative "Template" approach)
migrate:
    # 1. Decrypt env
    # 2. Use envsubst to replace vars in template -> create real toml
    # 3. Run reshape
    # 4. Delete real toml (cleanup)
    sops exec-env .env.enc -- bash -c "envsubst < reshape.toml.tmpl > reshape.toml && reshape migrate && rm reshape.toml"
```

**My Recommendation:** Try the **Environment Variable (Step C)** method first. It is cleaner, standard for "Cloud Native" apps, and doesn't involve creating/deleting temporary files. Only use the Template method if the tool forces you to.