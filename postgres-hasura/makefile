.PHONY: pg_password hs_password hs_console hs_migrate create_secret create_hasura_secret deploy_dev deploy_dev_base

B64_FLAGS=-D

pg_password:
	@kubectl get secret -n app coregonus.app-coregonus-db.credentials -o 'jsonpath={.data.password}' |  base64 $(B64_FLAGS)

pg_password_app:
	@kubectl get secret -n app coregonus-app.app-coregonus-db.credentials -o 'jsonpath={.data.password}' |  base64 $(B64_FLAGS)

hs_password:
	@kubectl get secret -n app coregonus -o 'jsonpath={.data.graphql-admin-secret}' | base64 $(B64_FLAGS)

hs_console:
	cd schema && \
		hasura --endpoint http://localhost:8080 \
			--admin-secret $(shell make hs_password) console

create_secret:
	@kubectl delete secret -n app coregonus 2>/dev/null || true
	$(eval DB_PASSWORD := $(shell make pg_password --no-print-directory))
	$(eval DB_PASSWORD_APP := $(shell make pg_password_app --no-print-directory))
	@kubectl create secret -n app generic coregonus \
		--from-literal admin-conn-str=postgres://coregonus:$(DB_PASSWORD)@app-coregonus-db:5432/coregonus?sslmode=require \
		--from-literal conn-str=postgres://coregonus:$(DB_PASSWORD_APP)@app-coregonus-db:5432/coregonus?sslmode=require \
		--from-literal graphql-admin-secret=EE1B3967630F4564A28122616657817C

hs_migrate:
	cd schema && \
		hasura --endpoint http://localhost:8080 \
			--admin-secret $(shell make hs_password) \
			--skip-update-check \
			migrate apply

hs_metadata:
	cd schema && \
		hasura --endpoint http://localhost:8080 \
			--admin-secret $(shell make hs_password) \
			--skip-update-check \
			metadata apply

hs_squash:
	cd schema && \
		hasura --endpoint http://localhost:8080 \
			--admin-secret $(shell make hs_password) \
			--skip-update-check \
			migrate squash --name "x" --from x

deploy_dev_base:
	(kustomize build k8s/dev | kubectl apply -f -)

deploy_dev: deploy_dev_base create_secret
