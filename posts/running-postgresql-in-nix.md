# 1. Nix?

See [my post about Nix](https://giang-nghg.github.io/posts/nix-as-docker-vm-alternative.html) first.

# 2. Nix script

`default.nix` or `shell.nix`:

```
{ pkgs ? import <nixpkgs> {} }:

pkgs.mkShell {
    buildInputs = with pkgs; [ postgresql ];
}
```

Then `nix-shell` to start a new Nix environment with PostgreSQL installed.

# 3. Running PostgreSQL (inside Nix shell)

Modified from the [official docs](https://www.postgresql.org/docs/14/install-short.html).

Init a PostgreSQL database server in a desired directory:

```
initdb -D /path/to/data
```

Then edit `/path/to/data/postgresql.conf`, set

```
unix_socket_directories = '/tmp' # or any directories that you have edit permissions
```

Shame there doesn't seems to be a way to specify relative directories here (I'm not sure).

Then

```
pg_ctl -D /path/to/data start

# and

pg_ctl -D /path/to/data stop
```
