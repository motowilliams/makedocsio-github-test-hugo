GENERATOR=hugo

ifeq ($(OS),Windows_NT)
SHELL := '/usr/bin/sh.exe'
PWD := $(shell sh.exe -c pwd)
else
SHELL := /bin/bash
PWD := $(shell pwd)
endif

export BASH_CMD := $(SHELL) -c
export REPO_ROOT := $(shell [[ -z "${CI}" ]] && [[ -z "${GITHUB_ACTIONS}" ]] && echo '/app' || echo $(PWD) )
export IS_CI := $(shell [[ -z "${CI}" && -z "${GITHUB_ACTIONS}" ]] && echo "false" || echo "true" )
export VERSION ?= $(shell [[ -d ".git" ]] && git tag --sort=v:refname | grep -E '[0-9]' | tail -1 || echo 0.0.0)

export IMAGE_NAME ?= makedocsio-$(GENERATOR)
export IMAGE_VERSION ?= latest
export IMAGE_TAG = $(IMAGE_NAME):$(IMAGE_VERSION)
export ADDITIONAL_BUILD_ARGS ?=
export LOCAL_PORT ?= 1313

export DOCS_ROOT_DIR=docs
export PUBLISH_DIR:=public

ifeq ($(IS_CI),true)
	export IMAGE_TAG=
	export DOCKER_COMMAND :=
	export IMAGE_NAME :=
else
	export DOCKER_COMMAND := docker run -it \
		-v $(PWD):$(REPO_ROOT) \
		--name $(IMAGE_NAME) \
		--rm \
		-w $(REPO_ROOT)
endif

docker_build:
	docker build --pull $(ADDITIONAL_BUILD_ARGS) -t $(IMAGE_TAG) . -f $(DOCS_ROOT_DIR)/Dockerfile

debug: ## Runs the docker image interactively for debugging purposes
	@$(DOCKER_COMMAND) $(ADDITIONAL_BUILD_ARGS) $(IMAGE_TAG) $(CMD)

docs_build:
	$(eval CMD := cd $(REPO_ROOT)/$(DOCS_ROOT_DIR) && hugo -D)
	$(DOCKER_COMMAND) $(IMAGE_TAG) $(BASH_CMD) '$(CMD)'

docs_publish:
	$(eval CMD := mkdir -p $(REPO_ROOT)/$(PUBLISH_DIR) && cp -R $(REPO_ROOT)/$(DOCS_ROOT_DIR)/public/* $(REPO_ROOT)/$(PUBLISH_DIR))
	$(DOCKER_COMMAND) $(IMAGE_TAG) $(BASH_CMD) '$(CMD)'

docs_serve:
	$(eval CMD := \
	cd $(REPO_ROOT)/$(DOCS_ROOT_DIR) && \
	hugo server \
	--bind 0.0.0.0 \
	--navigateToChanged \
	--templateMetrics \
	--buildDrafts)
	$(DOCKER_COMMAND) -p $(LOCAL_PORT):$(LOCAL_PORT) --expose $(LOCAL_PORT) $(IMAGE_TAG) $(BASH_CMD) '$(CMD)'

print-%: ; @echo $*=$($*)

printenv:
	printenv | sort
