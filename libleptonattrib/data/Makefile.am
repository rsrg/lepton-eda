theme=hicolor
xdgdatadir=$(XDGDATADIR)
themedir=$(xdgdatadir)/icons/$(theme)

app_icon = lepton-attrib

app_icon_files = \
	$(app_icon:%=%-48.png) \
	$(app_icon:%=%.svg)
#	$(app_icon:%=%-16.png)
#	$(app_icon:%=%-22.png)
#	$(app_icon:%=%-24.png)
#	$(app_icon:%=%-32.png)
#
theme_icons = \
	$(app_icon_files:%=apps,%)


desktopdir=$(xdgdatadir)/applications
desktop_in_files = lepton-attrib.desktop.in
desktop_DATA = $(desktop_in_files:.desktop.in=.desktop)

DOMAIN=libleptonattrib

@DESKTOP_I18N_LANGS_RULE@
@DESKTOP_I18N_DESKTOP_RULE@

install_icon_exec = \
	$(ICON_THEME_INSTALLER) \
	-t $(theme) \
	-s $(srcdir) \
	-d "x$(DESTDIR)" \
	-b $(themedir) \
	-m "$(mkinstalldirs)" \
	-x "$(INSTALL_DATA)"

install-data-local:
	$(install_icon_exec) -i $(theme_icons)


install-data-hook:
if ENABLE_UPDATE_XDG_DATABASE
	$(UPDATE_DESKTOP_DATABASE) "$(DESTDIR)$(desktopdir)"
endif

uninstall-hook:
if ENABLE_UPDATE_XDG_DATABASE
	$(UPDATE_DESKTOP_DATABASE) "$(DESTDIR)$(desktopdir)"
endif
	$(install_icon_exec) -u $(theme_icons)

CLEANFILES = $(desktop_DATA)
EXTRA_DIST = $(app_icon_files) \
	     $(desktop_in_files) $(desktop_DATA)
