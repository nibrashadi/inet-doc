#PERL=$(TOOLS_DIR)\perl\bin\perl.exe
PERL=perl

INKSCAPE=inkscape
DOT=dot
TEMPLATE=omnetpp

TITLE=User Manual

# --- generating pdf ---

# FIXME
TEXCHAPTERS = \
	 ch-introduction.tex \
	 ch-overview.tex \
	 ch-ned-lang.tex \
	 ch-simple-modules.tex \
	 ch-messages.tex \
	 ch-sim-lib.tex \
	 ch-build-sim-progs.tex \
	 ch-config-sim.tex \
	 ch-run-sim.tex \
	 ch-graphics.tex \
	 ch-ana-sim.tex \
	 ch-eventlog.tex \
	 ch-neddoc.tex \
	 ch-parallel-exec.tex \
	 ch-plugin-exts.tex \
	 ch-embedding.tex \
	 appendix-ned-ref.tex \
	 appendix-ned-grammar.tex \
	 appendix-ned-dtd.tex \
	 appendix-ned-functions.tex \
	 appendix-msg-grammar.tex \
	 appendix-display-strings.tex \
	 appendix-config-options.tex \
	 appendix-result-file-formats.tex \
	 appendix-eventlog-file-format.tex
ALLTEX = inet-manual.tex title.tex $(TEXCHAPTERS)
HTMCHAPTERS = $(TEXCHAPTERS:.tex=.htm)

DOTPICS = $(wildcard figures/*.dot)
SVGPICS = $(wildcard figures/*.svg)
PDFPICS = $(SVGPICS:.svg=.pdf) $(DOTPICS:.dot=.pdf)
PNGPICS = $(SVGPICS:.svg=.png) $(DOTPICS:.dot=.png)

# an imperfect attempt to filter out less important warnings...
# LOGFILTER = | sed '/Underfull/{N;N;d;};/Overfull/{N;N;d;};/Token not allowed/{N;N;d;}'
LOGFILTER =

.PHONY : pdf html eclipse

default: pdf html eclipse

include ../cover/cover.makefrag

pdf: $(ALLTEX) $(PDFPICS) figures/*.png cover
	pdflatex inet-manual.tex $(LOGFILTER)
	bibtex inet-manual
	makeindex inet-manual
	pdflatex inet-manual.tex $(LOGFILTER)
	pdflatex inet-manual.tex $(LOGFILTER)
	pdftk C=cover.pdf B=../cover/copyright.pdf D=inet-manual.pdf cat C B D3-end output ../../Manual.pdf
	rm -f *.aux *.idx *.ilg *.ind *.log *.out *.toc *.blg

%.pdf: %.svg
	$(INKSCAPE) $< -A $@

%.svg: %.dot
	$(DOT) -Tsvg $< >$@

# --- generating html ---

.SUFFIXES : .tex .bbl .htm .svg .dot

#inet-manual-bibl.bbl : inet-manual.bbl
#       copy inet-manual.bbl inet-manual-bibl.bbl

%.png: %.svg
	$(INKSCAPE) $< -e $@

%.png: %.dot
	$(DOT) -Tpng $< >$@

.bbl.htm:
	cp $< `echo $< | sed 's|\.bbl|.tex|'`
	$(PERL) ltoh.pl `echo $< | sed 's|\.bbl|.tex|'`
	mv $@l $@
	rm  `echo $< | sed 's|\.bbl|.tex|'`

.tex.htm:
	$(PERL) ltoh.pl $<

eclipse : $(HTMCHAPTERS) $(PNGPICS) eclipse.thtml prephtml2 ltoh.specs ltoh.pl
	cat $(HTMCHAPTERS) > inet-manual.tmp
	$(PERL) prephtml2 --split --template eclipse.thtml inet-manual.tmp
	rm inet-manual.tmp
	rm -rf eclipse
	mkdir eclipse
	mv *.html toc.xml eclipse
	cp *.png eclipse
	cp figures/*.png eclipse

html : $(HTMCHAPTERS) $(PNGPICS) omnest.thtml omnetpp.thtml prephtml2 ltoh.specs ltoh.pl
	cat $(HTMCHAPTERS) > inet-manual.tmp
	$(PERL) prephtml2 --template $(TEMPLATE).thtml inet-manual.tmp
	rm inet-manual.tmp
	rm -rf ../../manual
	mkdir ../../manual
	cp inet-manual.html ../../manual
	cp *.png ../../manual
	cp figures/*.png ../../manual

clean:
	rm -f inet-manual.dvi inet-manual.pdf *.aux *.idx *.ilg *.ind *.log *.out *.toc *.bbl *.blg *.htm inet-manual.tmp inet-manual.html cover.svg cover.pdf
	rm -rf ../../manual
	rm -rf eclipse
	rm -f figures/*.pdf
	rm -f `ls figures/*.svg | sed 's/svg$$/png/'`
	rm -f `ls figures/*.dot | sed 's/dot$$/svg/'`
