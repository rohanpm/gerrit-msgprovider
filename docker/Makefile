PREFIX=rohanpm/
IMAGES=\
	gerrit \
	gerrit-httpd \
	javabase

all: $(foreach dir,$(IMAGES),$(dir)/.built)

%/.built: %/Dockerfile
	docker build -t "$(PREFIX)$*" "$*"
	docker inspect -f '{{.Id}}' "$(PREFIX)$*" > $*/.built-new
	@cd $* && mv .built-new .built; \
          echo -n "$(PREFIX)$* "; cat .built

gerrit/.built: javabase/.built
gerrit-httpd.built: gerrit/.built