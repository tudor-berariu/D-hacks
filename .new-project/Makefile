# Copyright (C) 2014 Tudor Berariu <tudor.berariu@gmail.com>

# Permission is hereby granted, free of charge, to any person
# obtaining a copy of this software and associated documentation
# files (the “Software”), to deal in the Software without
# restriction, including without limitation the rights to use, copy,
# modify, merge, publish, distribute, sublicense, and/or sell copies
# of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:

# The above copyright notice and this permission notice shall be
# included in all copies or substantial portions of the Software.

# THE SOFTWARE IS PROVIDED “AS IS”, WITHOUT WARRANTY OF ANY KIND,
# EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
# MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
# NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS
# BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN
# ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
# CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

SHELL := /bin/sh

# compile options
CC := dmd
CCFLAGS := -w -O
LIB :=

# Add flags from the goals except
ifneq (,$(findstring debug,$(MAKECMDGOALS)))
override CCFLAGS := -g -debug $(CCFLAGS)
endif

ifneq (,$(findstring verbose,$(MAKECMDGOALS)))
override CCFLAGS := -g - $(CCFLAGS)
endif

ifneq (,$(findstring tests,$(MAKECMDGOALS)))
override CCFLAGS := -unittest $(CCFLAGS)
endif

D := $(CC) $(CCFLAGS)

# gitignore file
GITIGNORE := .gitignore

# sources and objects folders
SRC_DIR := src
BUILD_DIR := build

# source files
MAIN_SRC := $(wildcard $(SRC_DIR)/*.d)
AUX_SRC := $(shell find $(SRC_DIR)/*/ -name *.d 2> /dev/null)

# transform functions
d_to_o = $(patsubst %.d,%.o,$(patsubst $(SRC_DIR)/%,$(BUILD_DIR)/%,$(1)))
o_to_d = $(patsubst %.o,%.d,$(patsubst $(BUILD_DIR)/%,$(SRC_DIR)/%,$(1)))
d_to_exec = $(patsubst %.d,%,$(patsubst $(SRC_DIR)/%,%,$(1)))

# object files
MAIN_OBJS := $(call d_to_o,$(MAIN_SRC))

# executables
EXEC := $(call d_to_exec,$(MAIN_SRC))

.PHONY: build debug tests verbose clean run

build: $(EXEC)
debug: $(EXEC)
verbose: $(EXEC)
tests: $(EXEC)

# Link object files
$(EXEC): %: $(BUILD_DIR)/%.o
	@echo 'Linking $@'
	@(cat $(GITIGNORE) | grep -xq $@) || echo "$@" >> $(GITIGNORE)
	$(D) -of$@ $+ $(LIB)

# Build object files from sources
$(MAIN_OBJS): $(BUILD_DIR)/%.o: $(SRC_DIR)/%.d $(AUX_SRC)
	@echo 'Compiling $@ with dependencies: $+'
	$(D) -c -of$@ -I$(SRC_DIR) $+

# Remove all Emacs temporary files, objects and executable
clean:
	@rm -rf $(EXEC) $(BUILD_DIR)/*
	@find . -name '*~' -print0 | xargs -0 rm -f

# Run one executable
run: build
	@./$(lastword $(EXEC))
