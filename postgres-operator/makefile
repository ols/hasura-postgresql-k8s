
.PHONY: deploy_dev

deploy_dev:
	kustomize build k8s/dev | kubectl apply -f -
