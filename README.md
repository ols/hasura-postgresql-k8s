Using postgres-operator and postgresql with hasura, allowing auth0 jwt token access for users.
For auth0 user access update postgres-hasura/k8s/base/jwt-config.json with your well-known… https://[domain].auth0.com/.well-known/jwks.json
In case you want another database server/database name, change in k8s yamls.

Steps for k8s:
- cd postgres-operator/
- make deploy_dev
- cd ..
- cd postgres-hasura
- make deploy_dev (In case Error from server (NotFound): secrets "coregonus.app-coregonus-db.credentials" not found, just re-run make_deploy to get a new secret).

Steps to setup database:
- Download and install https://www.pgadmin.org/
- kubectl port-forward app-coregonus-db-0 5432:5432 -n app (To allow access to database through pgadmin locally).
- Run pgAdmin.
- Create/Server…
Spec for Server as configured in k8s:
- Name: coregonus-db (or change in yamls, search for coregonus-db)
- Host: localhost
- Port: 5432
- Username: coregonus
- Password: (make pg_password inside postgres-hasura)

Steps to get hasura running locally on http://localhost:8080:
- kubectl port-forward svc/coregonus-db 8080:80 -n app

Steps to deploy migrations and metadata:
- cd postgres-hasura
- make hs_migrate
- make hs_metadata

Steps to make hasura create migrations when changes is done through http://localhostL8080/
- make hs_console

Beside that it is now possible to test and make changes in database through hs_console checkout https://github.com/ols/nextjs-auth0-graphql to which is a nextjs application using auth0 for user access and graphql to connect to your postgres.