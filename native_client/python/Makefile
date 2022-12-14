.PHONY: bindings

include ../definitions.mk

bindings-clean:
	rm -rf dist temp_build stt.egg-info MANIFEST.in temp_lib
	rm -f impl_wrap.cpp impl.py

# Enforce PATH here because swig calls from build_ext looses track of some
# variables over several runs
bindings-build: ds-swig
	pip3 install --quiet $(PYTHON_PACKAGES) wheel setuptools
	DISTUTILS_USE_SDK=1 PATH=$(TOOLCHAIN_DIR):$(DS_SWIG_BIN_PATH):$$PATH SWIG_LIB="$(SWIG_LIB)" AS=$(AS) CC=$(CC) CXX=$(CXX) LD=$(LD) CFLAGS="$(CFLAGS)" LDFLAGS="$(LDFLAGS_NEEDED) $(RPATH_PYTHON)" MODEL_LDFLAGS="$(LDFLAGS_DIRS)" MODEL_LIBS="$(LIBS)" $(PYTHON_PATH) $(PYTHON_SYSCONFIGDATA) $(NUMPY_INCLUDE) python3 ./setup.py build_ext $(PYTHON_PLATFORM_NAME)

MANIFEST.in: bindings-build
	> $@
	$(call copy_missing_libs,temp_build/stt/*.so,temp_build/stt/lib,$@)

# On Unix, _wrap.o gets generated
# On Windows, _wrap.obj it is
bindings-package: MANIFEST.in
	cat MANIFEST.in
	rm -f temp_build/*_wrap.o temp_build/Release/*_wrap.obj
	DISTUTILS_USE_SDK=1 AS=$(AS) CC=$(CC) CXX=$(CXX) LD=$(LD) CFLAGS="$(CFLAGS)" LDFLAGS="$(LDFLAGS_NEEDED) $(RPATH_PYTHON)" MODEL_LDFLAGS="$(LDFLAGS_DIRS)" MODEL_LIBS="$(LIBS)" $(PYTHON_PATH) $(PYTHON_SYSCONFIGDATA) $(NUMPY_INCLUDE) python3 ./setup.py bdist_wheel $(PYTHON_PLATFORM_NAME) $(SETUP_FLAGS)

bindings: bindings-build bindings-package
