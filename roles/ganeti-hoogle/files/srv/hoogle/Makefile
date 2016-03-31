HOOGLE_VER = 4.2.43
PACKAGES_FILE = packages.lst
PACKAGES = $(shell awk '{ sub(/-[-.0-9]*$$/,""); print }' < $(PACKAGES_FILE) )

GANETI_TXT=ganeti-unpatched.txt
GANETI_URL=http://docs.ganeti.org/ganeti/master/api/hs/
CABAL = /usr/local/bin/cabal
DBDIR = $(CURDIR)/web/datadir/databases
DATABASE = $(DBDIR)/default.hoo

# -----------

TXT_FILES = $(addprefix $(DBDIR)/,$(addsuffix .txt,$(PACKAGES) ganeti))
HOO_FILES = $(patsubst %.txt,%.hoo,$(TXT_FILES))

.PHONY: update
update:
	wget --timestamping -O$(GANETI_TXT) \
	  http://docs.ganeti.org/ganeti/master/api/hs/ganeti.txt
	$(MAKE) all

all: db web/hoogle web/log.txt
.PHONY: all

.SECONDARY: $(TXT_FILES) $(HOO_FILES)

web/hoogle:
	mkdir -p web
	$(CABAL) sandbox init
	# utf8-string-1.0.1 provides major performance optimizations
	# aeson 0.7.0.0 is broken, request a fixed version
	# warp-3.1 is broken, request a fixed version
	$(CABAL) install warp-3.2.2 utf8-string-1.0.1 aeson-0.6.2.1
	$(CABAL) install --datadir=$(CURDIR)/web --datasubdir=datadir -O2 hoogle-$(HOOGLE_VER)
	install -t web .cabal-sandbox/bin/hoogle

.PHONY: db
db: $(DATABASE) $(HOO_FILES)

$(DATABASE): web/hoogle $(HOO_FILES)
	mkdir -p $(dir $@)
	web/hoogle combine -o $@ $(filter %.hoo,$^)

$(DBDIR)/ganeti.txt: $(GANETI_TXT)
	sed 's#^@package #@url $(GANETI_URL)\n&#' '$<' >'$@'

PKG_FULL_NAME  = $(shell grep '^$(basename $(notdir $(1)))-' $(PACKAGES_FILE))
%.txt:
	mkdir -p $(DBDIR)
	cd '$(dir $@)' ; wget --timestamping \
		'http://hackage.haskell.org/package/$(call PKG_FULL_NAME,$*)/docs/$(notdir $@)'

%.hoo: %.txt web/hoogle
	web/hoogle convert $< $@

web/log.txt::
	touch $@
	-chown www-data:www-data $@
