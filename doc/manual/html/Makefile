FILES=foreword.tex biblio.tex 
RELEASE=$$HOME/release/$${RELEASENAME}

vpath %.etex ..

all: $(FILES) FORCE
	@$(MAKE) -f Makefile.refman
	@$(MAKE) -f Makefile.library
	@$(MAKE) -f Makefile.cmds
	../../tools/texexpand manual.tex \
        | ../../tools/htmlgen > manual.html
	cd man-caml; \
        ../../../tools/htmlcut -3 -4 -14 -15 ../manual.html > index.html
	cd man-caml; ../../../tools/htmlthread node*.html

FORCE:

clean:
	rm -f $(FILES) manual.html
	@$(MAKE) -f Makefile.refman clean
	@$(MAKE) -f Makefile.library clean
	@$(MAKE) -f Makefile.cmds clean
	cd man-caml; rm -f index.html node*.html

release:
	tar cf - man-caml/*.* | gzip > $(RELEASE)refman.html.tar.gz

.SUFFIXES:
.SUFFIXES: .tex .etex

.etex.tex:
	cp $< $*.tex
