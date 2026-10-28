export KEYCLOAK_PORT ?= 8080
ANSIBLE_PLAYBOOK ?= $(if $(shell command -v ansible-playbook),ansible-playbook,uvx --from ansible-core ansible-playbook)

# The playbook resolves middleware_automation.keycloak to this checkout.
COLLECTIONS_PATH := $(CURDIR)/.tmp/collections

.PHONY: demo up down

demo: up
	mkdir -p $(COLLECTIONS_PATH)/ansible_collections/middleware_automation
	ln -sfn $(abspath $(CURDIR)/..) $(COLLECTIONS_PATH)/ansible_collections/middleware_automation/keycloak
	ANSIBLE_COLLECTIONS_PATH=$(COLLECTIONS_PATH) ANSIBLE_LOCALHOST_WARNING=false $(ANSIBLE_PLAYBOOK) \
		-i localhost, -c local -e 'ansible_python_interpreter={{ ansible_playbook_python }}' \
		-e keycloak_url=http://localhost:$(KEYCLOAK_PORT) playbook.yml

up:
	docker compose up -d
	until curl -sf http://localhost:$(KEYCLOAK_PORT)/realms/master >/dev/null; do sleep 2; done

down:
	docker compose down
