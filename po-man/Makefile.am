
PO_FILES = $(wildcard *.po)
PO_LANGS = $(PO_FILES:.po=)

PO_STAMPS = po4a-gen.stamp
MAN_SECTIONS = 1 3 5 8
CLEANFILES = $(PO_STAMPS)

EXTRA_DIST = README.md $(PO_FILES) po4a.cfg util-linux-man.pot

po4a-gen.stamp: $(PO_FILES)
	@echo 'po4a: generate man-pages translations'
	$(AM_V_GEN) $(PO4A) po4a.cfg
	@touch po4a-gen.stamp

gen-trans: $(PO_STAMPS)

asciidoc_man_cmd = $(ASCIIDOCTOR) \
	-b manpage \
	-a 'VERSION=$(VERSION)' \
	-a 'ADJTIME_PATH=$(ADJTIME_PATH)'

gen-mans: gen-trans
	@for l in $(PO_LANGS); do \
	  gendir="$(abs_builddir)/$$l"; \
	  genfiles=`echo $${gendir}/*.adoc`; \
	  if test "$$genfiles" != '$${gendir}/*.adoc'; then \
	    for file in $${genfiles}; do \
	      manname=`echo $$file | sed -e 's|^.*/||' -e 's|\.adoc||'`;  \
	      test -f $${gendir}/$${manname} || { \
		   echo "  GEN     " $$l ": " $$manname && \
	           $(asciidoc_man_cmd) \
		      --base-dir=$${gendir} \
		      --destination-dir $${gendir} $${file}; \
	      }; \
	    done; \
	  fi; \
	done

all: gen-mans

clean-local:
	rm -f *~ *.bak

distclean-local:
	rm -rf $(PO_LANGS) *.stamp

install-data-local: gen-mans
	@for l in $(PO_LANGS); do \
	  mansrcdir="$(abs_builddir)/$$l"; \
	  for s in $(MAN_SECTIONS); do \
	    installfiles=`echo $${mansrcdir}/*.$$s`; \
	    if test "$$installfiles" != '$${mansrcdir}/*.$$s'; then \
	      installdir="$(DESTDIR)$(mandir)/$$l/man$$s"; \
	      $(MKDIR_P) "$${installdir}" || exit 1; \
	      for file in $$installfiles; do \
	        manname=`echo $$file | sed -e 's|^.*/||'`;  \
	        echo "  Installing " $$l ": " $$manname ; \
	        $(INSTALL_DATA) $$file $${installdir}; \
	      done; \
	    fi; \
	  done; \
	done

uninstall-local:
	@for l in $(PO_LANGS); do \
	  mansrcdir="$(abs_builddir)/$$l"; \
	  for s in $(MAN_SECTIONS); do \
	    installfiles=`echo $${mansrcdir}/*.$$s`; \
	    if test "$$installfiles" != '$${mansrcdir}/*.$$s'; then \
	      installdir="$(DESTDIR)$(mandir)/$$l/man$$s"; \
	      for file in $$installfiles; do \
	        manname=`echo $$file | sed -e 's|^.*/||'`;  \
	        echo "  Uninstalling " $$l ": " $$manname ; \
	        rm -f $${installdir}/$${manname}; \
	      done; \
	    fi; \
	  done; \
	done
