# Makefile to build MCP server Docker images

# Detect sub‑directories that contain a Dockerfile (one level deep)
MCPS := $(shell find . -mindepth 2 -maxdepth 2 -name Dockerfile)
MCP_DIRS := $(patsubst ./%/Dockerfile,%,$(shell find . -mindepth 2 -maxdepth 2 -name Dockerfile))

# Default target builds every detected server image
all:
	@for mcp in $(MCP_DIRS); do \
		make $$mcp; \
	done

# Build a specific server image, e.g. `make example-python`
%: %/Dockerfile
	@mcp=$*; \
	echo "Building $$mcp from $$mcp/Dockerfile"; \
	docker build -t $$mcp $$mcp

.PHONY: all clean $(MCPS) clean-%

# Remove only images built by this Makefile (named after the server directories)
clean:
	@for img in $(MCP_DIRS); do \
		make clean-$$img; \
	 done

# Remove a single image: `make clean-<servername>`
clean-%:
	@img=$*; \
	if docker image inspect $$img > /dev/null 2>&1; then \
		echo "Removing $$img"; \
		docker rmi -f $$img; \
	else \
		echo "Image $$img not found"; \
	fi
