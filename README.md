# Hello

To start your Phoenix server:

- Install dependencies with `mix deps.get`
- Create and migrate your database with `mix ecto.setup`
- Install Node.js dependencies with `cd assets && npm install`
- Start Phoenix endpoint with `mix phx.server`

Now you can visit [`localhost:4000`](http://localhost:4000) from your browser.

Ready to run in production? Please [check our deployment guides](https://hexdocs.pm/phoenix/deployment.html).

## Learn more

- Official website: https://www.phoenixframework.org/
- Guides: https://hexdocs.pm/phoenix/overview.html
- Docs: https://hexdocs.pm/phoenix
- Forum: https://elixirforum.com/c/phoenix-forum
- Source: https://github.com/phoenixframework/phoenix

## Journal

1. init project with `mix phx.new hello`.
2. setup runtime configuration:
   1. rename `config/prod.secret.exs` to `config/releases.exs`.
   2. replace `use Mix.Config` with `import Config`.
   3. remove call of `import_config "prod.secret.exs"`.
3. setup Ecto custom commands for release:
   1. add new module `Hello.Release` from [here](https://hexdocs.pm/phoenix/releases.html#ecto-migrations-and-custom-commands).
4. get deps with `mix deps.get`.
5. create a example migration with `mix ecto.gen.migration create_users`.
6. create release with `MIX_ENV=prod mix release`.
7. create a database:
   - database host: `localhost`
   - database port: `5432`
   - database name: `hello`
   - username: `postgres`
   - password: `postgres`
8. load environment variables `DATABASE_URL` `SECRET_KEY_BASE` with running `source .envrc` in shell.
9. run migrations related commands:
   - `_build/prod/rel/hello/bin/hello eval 'Hello.Release.migrate()'` - SUCCEED
   - `_build/prod/rel/hello/bin/hello eval 'Hello.Release.rollback(Hello.Repo, 20200405040500)'` - FAILED with following errors.

```text
12:14:03.919 [error] GenServer #PID<0.209.0> terminating
** (RuntimeError) connect raised KeyError exception: key :database not found. The exception details are hidden, as they may contain sensitive data such as database credentials. You may set :show_sensitive_data_on_connection_error to true when starting your connection if you wish to see all of the details
    (elixir 1.10.2) lib/keyword.ex:399: Keyword.fetch!/2
    (postgrex 0.15.3) lib/postgrex/protocol.ex:92: Postgrex.Protocol.connect/1
    (db_connection 2.2.1) lib/db_connection/connection.ex:69: DBConnection.Connection.connect/2
    (connection 1.0.4) lib/connection.ex:622: Connection.enter_connect/5
    (stdlib 3.10) proc_lib.erl:249: :proc_lib.init_p_do_apply/3
Last message: nil
```

I try to inspect the error, so I set `:show_sensitive_data_on_connection_error` option for `Hello.Repo` like the error messages said:

```elixir
config :hello, Hello.Repo,
  show_sensitive_data_on_connection_error: true
```

Then, I rebuilt the release and run again, but the error message is same as above. It looks like the option has no effects.
