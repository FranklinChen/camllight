CORE_INTF=int.tex float.tex eq.tex bool.tex exc.tex char.tex string.tex \
  vect.tex list.tex pair.tex ref.tex io.tex fchar.tex \
  fstring.tex fvect.tex stream.tex builtin.tex

STDLIB_INTF=sort.tex hashtbl.tex printf.tex arg.tex \
  filename.tex lexing.tex parsing.tex stack.tex queue.tex random.tex \
  printexc.tex sys.tex genlex.tex set.tex map.tex baltree.tex format.tex gc.tex

OTHER_INTF=graphics.tex unix.tex str.tex num.tex arithstatus.tex

FILES=core.tex core-all.tex $(CORE_INTF) \
      stdlib.tex stdlib-all.tex $(STDLIB_INTF) \
      graph.tex $(OTHER_INTF) libunix.tex libstr.tex libnum.tex

FORMAT=../../tools/format-intf

CLDIR=../../../cl

VPATH=$(CLDIR)/src/lib:$(CLDIR)/contrib/libgraph:$(CLDIR)/contrib/libunix:$(CLDIR)/contrib/libstr:$(CLDIR)/contrib/libnum

all: $(FILES)

clean:
	rm -f $(FILES)

$(CORE_INTF) $(STDLIB_INTF) $(OTHER_INTF): $(FORMAT)

core-all.tex: $(CORE_INTF)
	@ (for i in $(CORE_INTF); do echo "\\input $$i"; done) | sort > core-all.tex

stdlib-all.tex: $(STDLIB_INTF)
	@ (for i in $(STDLIB_INTF); do echo "\\input $$i"; done) | sort > stdlib-all.tex

.SUFFIXES:
.SUFFIXES: .tex .etex .mli

.etex.tex:
	../../tools/texquote2 < $*.etex > $*.tex

.mli.tex:
	$(FORMAT) $* $< > $*.tex

arithstatus.tex: $(CLDIR)/contrib/libnum/arith_status.mli
	$(FORMAT) arith_status $(CLDIR)/contrib/libnum/arith_status.mli > arithstatus.tex
