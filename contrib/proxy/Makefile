VERSION=$(shell node -pe 'JSON.parse(process.argv[1]).version' "$$(cat ../../package.json)")
GOPATH=$(PWD)/build~:$(PWD)
GO=go
export GOPATH

# Verbosity
V=0
V_gobuild = $(v_gobuild_$(V))
v_gobuild_0 = @echo "  GO BUILD " $@; $(GO) build
v_gobuild_1 = $(GO) build -v -x
V_goget = $(v_goget_$(V))
v_goget_0 = @echo   "  GO GET   " $@; $(GO) get
v_goget_1 = $(GO) get -v
V_gocmd = $(v_gocmd_$(V))
v_gocmd_0 = @echo   "  GO" $(shell echo $@ | tr a-z A-Z); $(GO)
v_gocmd_1 = $(GO)
V_gotest = $(v_gotest_$(V))
v_gotest_0 = @echo  "  GO TEST  "; $(GO) test
v_gotest_1 = $(GO) test -v

# Compute list of dependencies. We assume that built-in dependencies
# are those without a dot before the first `/'.
DEPS  = $(shell $(GO) list -f '{{join .Deps "\n"}}' ./src/... | grep '^[a-z]*\.' | sort | uniq)
DEPS += $(shell $(GO) list -f '{{join .TestImports "\n"}}' ./src/... | grep '^[a-z]*\.' | sort | uniq)

# Build proxy
all: $(PWD)/build~/proxy
$(PWD)/build~/proxy: deps fmt vet check
	$(V_gobuild) -ldflags "-X main.version $(VERSION)" -o $@ ./src

# Grab dependencies
deps: $(DEPS)
$(DEPS):
	$(V_goget) $@

# Cleanup
clean:
	rm -rf $(PWD)/build~

# Wrapper around go commands
fmt:
	$(V_gocmd) fmt ./src/...
vet:
	$(V_gocmd) vet ./src/...
doc:
	$(V_gocmd)doc -http=:8000 -index
check:
	$(V_gotest) ./src/dk/...
	@rm -f src/dk/dk.test

.PHONY: all deps clean fmt vet doc check
