
AM_CPPFLAGS = -I$(top_srcdir)/compat -I$(top_srcdir)/lib @OPENSSL_INCLUDES@

pam_libdir = @PAMDIR@
pam_lib_LTLIBRARIES = pam_duo.la

pam_duo_la_DEFS = -DDEBUG_PAM -DPAM_DEBUG @DEFS@
pam_duo_la_SOURCES = pam_duo.c pam_extra.h pam_extra.c
pam_duo_la_LIBADD = $(top_builddir)/lib/libduo.la $(top_builddir)/compat/libcompat.la @OPENSSL_LDFLAGS@ @OPENSSL_LIBS@ -lpam
pam_duo_la_LDFLAGS = -module -no-undefined -avoid-version -shared -export-symbols-regex '^pam_sm_'

notrans_dist_man8_MANS = pam_duo.8

semodule_name = authlogin_duo
semodule:
	-checkmodule -M -m -o $(semodule_name).mod $(semodule_name).te
	-semodule_package -o $(semodule_name).pp -m $(semodule_name).mod

semodule-install:
	-bzip2 -9 $(semodule_name).pp
	-install -d $(DESTDIR)/usr/share/selinux/packages/
	-install -m 0644 $(semodule_name).pp.bz2 $(DESTDIR)/usr/share/selinux/packages/
	-semodule -i $(DESTDIR)/usr/share/selinux/packages/$(semodule_name).pp.bz2

semodule-remove:
	-semodule -r $(semodule_name)
	-rm -f $(DESTDIR)/usr/share/selinux/packages/$(semodule_name).pp.bz2

clean-local: clean-local-semodule

clean-local-semodule:
	-rm -f $(semodule_name).{mod,pp,pp.bz2}

.PHONY: semodule semodule-enable semodule-install semodule-remove clean-local-semodule


install-data-local:
	$(MKDIR_P) $(DESTDIR)$(sysconfdir)
	-@if [ ! -f $(DESTDIR)$(sysconfdir)/pam_duo.conf ]; then \
		cp pam_duo.conf $(DESTDIR)$(sysconfdir)/pam_duo.conf; \
		echo "Created ${DESTDIR}$(sysconfdir)/pam_duo.conf"; \
		echo "Please edit it to add your Duo integration and secret keys"; \
	else \
		echo "Found existing ${DESTDIR}$(sysconfdir)/pam_duo.conf - updating permissions"; \
	fi
	-chmod 600 $(DESTDIR)$(sysconfdir)/pam_duo.conf

EXTRA_DIST = pam_duo.conf $(semodule_name).te
