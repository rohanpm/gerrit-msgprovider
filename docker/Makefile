IMGPREFIX=rohanpm/
CPREFIX=gerrit-msgprovider-
IMAGES=\
	gerrit \
	gerrit-httpd \
	javabase \
	generate-config
RUN_IMAGES=gerrit gerrit-httpd generate-config

.PHONY: all
all: $(foreach dir,$(IMAGES),$(dir)/.built)

.PHONY: push
push: $(foreach dir,$(IMAGES),$(dir)-push)

%-push:
	docker push "$(IMGPREFIX)$*"

.PHONY: clean
clean: $(foreach dir,$(RUN_IMAGES),$(dir)-clean)

%-clean:
	docker stop $(CPREFIX)$* || true
	docker rm $(CPREFIX)$* || true

.PHONY: run
run: all
	-docker run -d --name $(CPREFIX)gerrit $(IMGPREFIX)gerrit:latest
	-docker run -d --publish-all --name $(CPREFIX)gerrit-httpd --link $(CPREFIX)gerrit:gerrit $(IMGPREFIX)gerrit-httpd:latest
	docker run --rm --link $(CPREFIX)gerrit-httpd:gerrit-httpd $(IMGPREFIX)generate-config:latest
	docker ps

%/.built: %/*
	docker build -t "$(IMGPREFIX)$*" "$*"
	docker inspect -f '{{.Id}}' "$(IMGPREFIX)$*" > $*/.built-new
	@cd $* && mv .built-new .built; \
          echo -n "$(IMGPREFIX)$* "; cat .built

gerrit/.built: javabase/.built
gerrit-httpd.built: gerrit/.built