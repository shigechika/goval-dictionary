.PHONY: \
	dep \
	depup \
	build \
	install \
	all \
	vendor \
	lint \
	vet \
	fmt \
	fmtcheck \
	pretest \
	test \
	integration \
	cov \
	clean

SRCS = $(shell git ls-files '*.go')
PKGS =  ./commands ./config ./db ./db/rdb ./fetcher ./models ./util
VERSION := $(shell git describe --tags --abbrev=0)
REVISION := $(shell git rev-parse --short HEAD)
LDFLAGS := -X 'main.version=$(VERSION)' \
	-X 'main.revision=$(REVISION)'

all: dep build test

dep:
	go get -u github.com/golang/dep/...
	dep ensure

depup:
	go get -u github.com/golang/dep/...
	dep ensure -update

build: main.go dep
	go build -ldflags "$(LDFLAGS)" -o goval-dictionary $<

install: main.go dep
	go install -ldflags "$(LDFLAGS)"

all: test

lint:
	@ go get -v golang.org/x/lint/golint
	golint $(PKGS)

vet:
	go vet ./... || exit;

fmt:
	gofmt -w $(SRCS)

fmtcheck:
	$(foreach file,$(SRCS),gofmt -d $(file);)

pretest: lint vet fmtcheck

test: pretest
	$(foreach pkg,$(PKGS),go test -v $(pkg) || exit;)

integration:
	go test -tags docker_integration -run TestIntegration -v

cov:
	@ go get -v github.com/axw/gocov/gocov
	@ go get golang.org/x/tools/cmd/cover
	gocov test | gocov report

clean:
	$(foreach pkg,$(PKGS),go clean $(pkg) || exit;)

