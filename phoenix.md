# Phoenix

## Setup

### Installing Packages

Update to latest version of Elixir and Erlang

    brew update
    brew upgrade elixir

Install phoenix:

    mix archive.install https://github.com/phoenixframework/archives/raw/master/phoenix_new.ez

(Optional) Upgrade existing phoenix package:

    mix local.phoenix

### Postgres

Install postgres:

    brew install postgres

Run postgres:

	# Taken from `brew info postgres`
    pg_ctl -D /usr/local/var/postgres start

### Starting a Project

Create a project directory:

    mkdir ~/projects/projectname

Create a new phoenix installation:

    mix phoenix.new ~/projects/example

Move the readme:

    cd ~/projects/example
    mv README.md README-PHOENIX.md

Add .env:

```
export PORT=4010
```

Make a copy:

    cp .env .env.example

Keep .env file out of source control:

    echo .env >> .gitignore

Update `config/dev.exs` to read the PORT env variable:

```diff
- http: [port: 4000]
+ http: [port: {:system, "PORT"}]
```

### Postgres

Update `.env` and `.env.example`:

```
export POSTGRES_USER=postgres
export POSTGRES_PASS=postgres
export POSTGRES_HOST=localhost
export POSTGRES_DB=example
```

Update `config/dev.exs` to read the postgres env variables:

```
config :example, Example.Repo,
  adapter: Ecto.Adapters.Postgres,
  username: System.get_env("POSTGRES_USER"),
  password: System.get_env("POSTGRES_PASS"),
  database: System.get_env("POSTGRES_DB"),
  hostname: System.get_env("POSTGRES_HOST"),
  pool_size: 10
```

Create the database:

```
source .env && mix ecto.create
```

#### Troubleshooting

To connect to the console:

    psql postgres

To confirm roles exist:

    # Inside psql
    \du

### Starting the Server

```
source .env && iex -S mix phoenix.server
```

### Local DNS

(Optional) Install `puma-dev` if not already installed:

    brew install puma/puma/puma-dev
   	# Follow `brew info puma/puma/puma-dev` output:
    sudo puma-dev -setup
    puma-dev -install
    mkdir ~/.puma-dev

### Dev Environment Automation

Create a new `~/.puma-dev/example`:

    echo 4010 > ~/.puma-dev/example

Create a new `~/.tmuxinator/example.yml` file:

```yaml
name: example
root: ~/projects/example
socket_name: example

windows:
  - editor:
      layout: main-horizontal
      panes:
        - vim
        - # bash
  - services:
      layout: main-horizontal
      panes:
        - # bash
        - pg_ctl -D /usr/local/var/postgres start
  - server: source .env && iex -S mix phoenix.server
```

Then start a new tmuxinator session:

    mux example
