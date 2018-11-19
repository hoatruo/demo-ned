
JAVA_PACKAGE = com.tailf.packages.ned.jvx
JDIR = $(shell echo $(JAVA_PACKAGE) | sed 's/\./\//g')

NS  = namespaces

JFLAGS = --java-disable-prefix \
         --exclude-enums \
         --fail-on-warnings \
         --java-package $(JAVA_PACKAGE).$(NS) \
         --emit-java java/src/$(JDIR)/namespaces

all:	create-dirs fxs javac
	if [ -d "../netsim" ] ; then \
		cd ../netsim && $(MAKE) all ; \
	fi

# Include standard NCS examples build definitions and rules
include $(NCS_DIR)/src/ncs/build/include.ncs.mk

YANG = yang/tailf-ned-GRX.yang \
       yang/tailf-ned-GRX-id.yang \
       yang/tailf-ned-GRX-stats.yang

SUBMODULES = $(shell grep -l belongs-to yang/*.yang)
YANG_MODULES = $(filter-out $(SUBMODULES),$(YANG))

FXS = $(YANG_MODULES:yang/%.yang=ncsc-out/modules/fxs/%.fxs) ../load-dir/tailf-ned-GRX-meta.fxs

JAVA_NAMESPACES = \
	$(shell echo $(YANG:yang/%.yang=java/src/$(JDIR)/namespaces/-%.java) | \
		perl -pne 's/-([a-zA-Z])/\U\1/g' | \
		perl -pne 's/TailfNed//g')

create-dirs:
	mkdir -p ncsc-out/modules
	mkdir -p java/src/$(JDIR)/namespaces
	mkdir -p ../load-dir
	mkdir -p ../private-jar
	mkdir -p ../shared-jar

fxs:	$(FXS)

ncsc-out/modules/fxs/tailf-ned-GRX-stats.fxs:	yang/tailf-ned-GRX-stats.yang
	$(NCSC) --ncs-compile-module $< \
	        --ncs-device-dir ncsc-out \
	        --ncs-skip-config \
	        --ncs-skip-template \
	        --ncs-device-type generic-ned && \
	  cp $@ ../load-dir
	$(NCSC) $(JFLAGS)/tailfJVXNedGenericStats.java $@

../load-dir/tailf-ned-GRX-meta.fxs:	yang/tailf-ned-GRX-meta.yang
	$(NCSC) --yangpath yang -c $< -o $@

ncsc-out/modules/fxs/tailf-ned-GRX.fxs:	yang/tailf-ned-GRX.yang
	$(NCSC) --ncs-compile-module $< \
	        --ncs-device-dir ncsc-out \
	        --ncs-skip-statistics \
	        --ncs-device-type generic-ned && \
	  cp $@ ../load-dir
	$(NCSC) $(JFLAGS)/tailfJVXNedGeneric.java $@

ncsc-out/modules/fxs/tailf-ned-GRX-id.fxs:	yang/tailf-ned-GRX-id.yang
	$(NCSC) --ncs-compile-module $< \
	        --ncs-device-dir ncsc-out \
	        --ncs-skip-statistics \
	        --ncs-device-type generic-ned && \
	  cp $@ ../load-dir
	$(NCSC) $(JFLAGS)/tailfJVXNedGenericId.java $@

javac:
	cd java && ant -q all

clean:
	rm -rf ncsc-out ../load-dir/*
	rm -f java/src/$(JDIR)/$(NS)/*.java
	cd java && ant clean
	if [ -d "../netsim" ] ; then \
		cd ../netsim && $(MAKE) clean ; \
	fi
