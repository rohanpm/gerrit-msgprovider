IMGPREFIX=rohanpm/
CPREFIX=gerrit-msgprovider-

IMAGES=\
	gerrit \
	gerrit-httpd \
	javabase \
	generate-config

# top-level image(s) used by run target; dependencies will be run too
RUN_IMAGES=\
  generate-config

gerrit_IMGDEPS = javabase
gerrit_CARGS   = -d

gerrit-httpd_CDEPS = gerrit
gerrit-httpd_CARGS = -d --publish-all --link $(CPREFIX)gerrit:gerrit

generate-config_CDEPS = gerrit-httpd
generate-config_CARGS = --rm --link $(CPREFIX)gerrit-httpd:gerrit-httpd

############ DOCKER MACROS ####################################################

docker-get-image-id-from-tag = docker inspect -f '{{.Id}}' "$(IMGPREFIX)$(1)"
docker-get-image-id-from-container = docker inspect -f '{{.Image}}' "$(CPREFIX)$(1)" 2>/dev/null
docker-container-exists-p = docker inspect "$(CPREFIX)$(1)" >/dev/null 2>/dev/null
docker-container-running-p = [ "x$$(docker inspect -f '{{.State.Running}}' $(CPREFIX)$(1) 2>/dev/null)" = "xtrue" ]
docker-container-outdated-p = $(call docker-container-exists-p,$(1)) && [ \
  x$$($(call docker-get-image-id-from-tag,$(1))) \
  != \
  x$$($(call docker-get-image-id-from-container,$(1))) \
]
docker-container-should-start-p = ! $(call docker-container-exists-p,$(1)) \
  || $(call docker-container-outdated-p,$(1))
docker-container-should-stop-p = $(call docker-container-running-p,$(1)) \
  && $(call docker-container-outdated-p,$(1))
docker-stop = echo -n "Stopping $(1): "; docker stop $(CPREFIX)$(1)
docker-rm = echo -n "Removing $(1): "; docker rm $(CPREFIX)$(1)

docker-run-container = \
  if $(call docker-container-should-stop-p,$(1)); then \
    $(call docker-stop,$(1)); \
  fi; \
  if $(call docker-container-outdated-p,$(1)); then \
    $(call docker-rm,$(1)); \
  fi; \
  if $(call docker-container-should-start-p,$(1)); then \
    echo -n "Starting $(1): "; \
    docker run $(2) --name $(CPREFIX)$(1) $(IMGPREFIX)$(1); \
  else \
    echo "$(1) is running."; \
  fi

docker-clean-container = \
  if $(call docker-container-running-p,$(1)); then \
    $(call docker-stop,$(1)); \
  fi; \
  if $(call docker-container-exists-p,$(1)); then \
    $(call docker-rm,$(1)); \
  else \
    echo "$(1) is clean."; \
  fi

############ BUILD TARGETS ####################################################

.PHONY: all
all: $(foreach dir,$(IMAGES),$(dir)/.built)

%/.built: %/*
	docker build -t "$(IMGPREFIX)$*" "$*"
	$(call docker-get-image-id-from-tag,$*) > $*/.built

define BUILD_template
$(1)/.built: $(foreach x,$(call $(1)_IMGDEPS),$(x)/.built)
endef

$(foreach img,$(IMAGES),$(eval $(call BUILD_template,$(img))))

############ PUSH/CLEAN TARGETS ###############################################

.PHONY: push
push: $(foreach dir,$(IMAGES),$(dir)-push)

%-push: $*/.built
	docker push "$(IMGPREFIX)$*"

.PHONY: clean
clean: $(foreach dir,$(IMAGES),$(dir)-clean)

%-clean:
	@$(call docker-clean-container,$*)

############ RUN TARGETS ######################################################

.PHONY: run
run: $(foreach x,$(RUN_IMAGES),run-$(x))

define RUN_template
.PHONY: run-$(1)
run-$(1): $(1)/.built $(foreach x,$(call $(1)_CDEPS),run-$(x))
	@$$(call docker-run-container,$(1),$(call $(1)_CARGS))
endef

$(foreach img,$(IMAGES),$(eval $(call RUN_template,$(img))))
