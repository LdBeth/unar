# Compilers and linkers

OBJCC = gcc
CC = gcc
CXX = g++
LD = gcc
AR = ar
SDK =

# Options for compilation

GCC_OPTS = 	-O2 \
		-Wno-import \
		-Wno-multichar \
		-mmacosx-version-min=10.3.9

OBJC_OPTS =	-std=gnu99 \
		$(SDK)

C_OPTS =	-std=gnu99 \
		$(SDK)

CXX_OPTS =	$(SDK)

AUTODEPENDENCY_CFLAGS=-MMD -MF$(@:.o=.d) -MT$@

ALL_OBJCFLAGS = $(OBJC_OPTS) $(GCC_OPTS) $(OBJCFLAGS)
ALL_CFLAGS = $(C_OPTS) $(GCC_OPTS) $(CFLAGS)
ALL_CXXFLAGS = $(CXX_OPTS) $(GCC_OPTS) $(CXXFLAGS)

# Options for linking

LIBS =	-framework Foundation \
	-lobjc \
	-lstdc++ \
	-lm

ALL_LDFLAGS = $(SDK) \
		$(LDFLAGS)

# Paths

SOURCE_DIR = .
BUILD_DIR = Build

# Files

include Makefile.common

# Framework

FRAMEWORK_NAME = UniversalDetector

FRAMEWORK_FILES = $(FRAMEWORK_NAME).framework

FRAMEWORK_HEADER_FILES = UniversalDetector.h

FRAMEWORK_LDFLAGS =	-dynamiclib \
			-install_name @executable_path/../Frameworks/$(FRAMEWORK_NAME).framework/$(FRAMEWORK_NAME) \
			$(SDK) \
			$(LDFLAGS)

# Compilation rules

all: $(FRAMEWORK_FILES) $(EXECUTABLE_FILES) $(LIBRARY_FILES)

$(FRAMEWORK_NAME).framework: \
$(FRAMEWORK_NAME).framework/Versions/A/$(FRAMEWORK_NAME) \
$(FRAMEWORK_NAME).framework/Versions/A/Resources/Info.plist \
$(FRAMEWORK_NAME).framework/Versions/A/Resources/en.lproj/InfoPlist.strings \
$(FRAMEWORK_HEADER_FILES:%=$(FRAMEWORK_NAME).framework/Versions/A/Headers/%)
	ln -shf A $(FRAMEWORK_NAME).framework/Versions/Current
	ln -shf Versions/Current/$(FRAMEWORK_NAME) $(FRAMEWORK_NAME).framework/$(FRAMEWORK_NAME)
	ln -shf Versions/Current/Resources $(FRAMEWORK_NAME).framework/Resources
	ln -shf Versions/Current/Headers $(FRAMEWORK_NAME).framework/Headers

$(FRAMEWORK_NAME).framework/Versions/A/$(FRAMEWORK_NAME): $(LIBRARY_OBJS)
	@mkdir -p $(dir $@)
	$(LD) $(FRAMEWORK_LDFLAGS) -o $@ $^ $(LIBS)

$(FRAMEWORK_NAME).framework/Versions/A/Resources/Info.plist: Info.plist
	@mkdir -p $(dir $@)
	cat $^ |\
	perl -pe 's/\$$\{EXECUTABLE_NAME\}/$(FRAMEWORK_NAME)/sge' |\
	perl -pe 's/\$$\{PRODUCT_NAME\}/$(FRAMEWORK_NAME)/sge' |\
	cat >$@

$(FRAMEWORK_NAME).framework/Versions/A/Resources/en.lproj/InfoPlist.strings: English.lproj/InfoPlist.strings
	@mkdir -p $(dir $@)
	cp $^ $@

$(FRAMEWORK_NAME).framework/Versions/A/Headers/%: %
	@mkdir -p $(dir $@)
	cp $^ $@

libUniversalDetector.a: $(LIBRARY_OBJS)
	rm -f $@
	$(AR) rcs $@ $^

DetectorTest: $(DETECTORTEST_OBJS) libUniversalDetector.a
	$(LD) $(ALL_LDFLAGS) -o $@ $^ $(LIBS)

clean:
	rm -rf $(BUILD_DIR) $(FRAMEWORK_FILES) $(EXECUTABLE_FILES) $(LIBRARY_FILES)

# Suffix rules

.SUFFIXES: .o .c .m .cpp

$(BUILD_DIR)/%.o: $(SOURCE_DIR)/%.c
	@mkdir -p $(dir $@)
	$(CC) $(ALL_CFLAGS) $(AUTODEPENDENCY_CFLAGS) -c $< -o $@

$(BUILD_DIR)/%.o: $(SOURCE_DIR)/%.m
	@mkdir -p $(dir $@)
	$(OBJCC) $(ALL_OBJCFLAGS) $(AUTODEPENDENCY_CFLAGS) -c $< -o $@

$(BUILD_DIR)/%.o: $(SOURCE_DIR)/%.cpp
	@mkdir -p $(dir $@)
	$(CXX) $(ALL_CXXFLAGS) $(AUTODEPENDENCY_CFLAGS) -c $< -o $@

-include $(OBJS:.o=.d)
