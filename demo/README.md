# Demo: a user profile attribute required per client scope

Not meant to be merged. It shows what `required.scopes` in `keycloak_userprofile` is for:
one realm, two clients, and `lastName` required by only one of them.

- `billing` puts the full name on invoices. It carries the `last-name-required` client scope, so it asks for the last name.
- `chat` shows the first name only. It does not carry the scope and lets the user in without a last name.

Without `required.scopes` the attribute is either required for every client in the realm or for none.

## Run it

Needs Docker with Compose, and `ansible-playbook` (or `uvx`, which the Makefile falls back to).

```bash
cd demo
make          # starts Keycloak with docker compose and applies playbook.yml from this checkout
make down     # stops and removes it
```

`KEYCLOAK_PORT` (default `8080`) and `KEYCLOAK_IMAGE` change the port and the Keycloak version.

The playbook ends by logging the user in through both clients with the password grant and asserting
that only `billing` refuses with `Account is not fully set up`.

## See it in the browser

Both clients redirect to Keycloak's hosted test app. Sign in as `no-last-name` / `password`:

- <https://www.keycloak.org/app/?url=http://localhost:8080&realm=TestRealm&client=chat> lets the user straight in.
- <https://www.keycloak.org/app/?url=http://localhost:8080&realm=TestRealm&client=billing> asks for the last name first.

The admin console is at <http://localhost:8080/admin>, `admin` / `remembertochangeme`, realm `TestRealm`.
Rerunning `make` recreates the user, so the last name entered through `billing` is gone again.
