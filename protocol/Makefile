SHELL=/bin/bash -eo pipefail

# Experimental; options are pdflatex, lualatex, or xelatex.
# On Debian, LuaLaTeX needs the texlive-luatex package, and XeLaTeX needs the texlive-xetex package.
# Make sure to read <https://github.com/zcash/zips/issues/249>.
ENGINE=pdflatex

LATEXMKOPT_pdflatex=
LATEXMKOPT_xelatex=-pdflatex=xelatex -dvi- -ps-
LATEXMKOPT_lualatex=-pdflatex=lualatex -dvi- -ps-

LATEXMK=max_print_line=10000 latexmk $(LATEXMKOPT_$(ENGINE)) --halt-on-error --file-line-error -bibtex -pdf -logfilewarnings- -e '$$max_repeat=8'
LATEX=$(ENGINE) --halt-on-error --file-line-error
NOCRUFT?=|awk '{gsub(/[({<][/][^ ]* ?/,"")}1' |sed '/name{index:.*} has been referenced but does not exist, replaced by a fixed one/,+1 d'

# Use EXTRAOPT=-pvc for "continuous preview" mode. For example, "make auxblossom EXTRAOPT=-pvc".
# In this case the updated .pdf will be in the aux/ directory.

.PHONY: all-specs all
all-specs: .Makefile.uptodate
	$(MAKE) nufour.pdf heartwood.pdf blossom.pdf sapling.pdf sprout.pdf

all: .Makefile.uptodate
	$(MAKE) nufour heartwood blossom sapling sprout

.Makefile.uptodate: Makefile
	$(MAKE) clean
	touch .Makefile.uptodate

sprout.pdf: protocol.tex zcash.bib incremental_merkle.png key_components.png
	$(MAKE) sprout

sapling.pdf: protocol.tex zcash.bib incremental_merkle.png key_components_sapling.png
	$(MAKE) sapling

blossom.pdf: protocol.tex zcash.bib incremental_merkle.png key_components_sapling.png
	$(MAKE) blossom

heartwood.pdf: protocol.tex zcash.bib incremental_merkle.png key_components_sapling.png
	$(MAKE) heartwood

nufour.pdf: protocol.tex zcash.bib incremental_merkle.png key_components_sapling.png
	$(MAKE) nufour

.PHONY: auxsprout
auxsprout:
	printf '\\renewcommand{\\docversion}{Version %s [\\SproutSpec]}' "$$(git describe --tags --abbrev=6)" |tee protocol.ver
	mkdir -p aux
	rm -f aux/sprout.* aux/protocol.*
	$(LATEXMK) -jobname=sprout -auxdir=aux -outdir=aux $(EXTRAOPT) protocol $(NOCRUFT)

.PHONY: sprout
sprout:
	$(MAKE) auxsprout
	mv -f aux/sprout.pdf .

.PHONY: auxsapling
auxsapling:
	printf '\\toggletrue{issapling}\n\\renewcommand{\\docversion}{Version %s [\\SaplingSpec]}' "$$(git describe --tags --abbrev=6)" |tee protocol.ver
	mkdir -p aux
	rm -f aux/sapling.*
	$(LATEXMK) -jobname=sapling -auxdir=aux -outdir=aux $(EXTRAOPT) protocol $(NOCRUFT)

.PHONY: sapling
sapling:
	$(MAKE) auxsapling
	mv -f aux/sapling.pdf .

.PHONY: auxblossom
auxblossom:
	printf '\\toggletrue{isblossom}\n\\renewcommand{\\docversion}{Version %s [\\BlossomSpec]}' "$$(git describe --tags --abbrev=6)" |tee protocol.ver
	mkdir -p aux
	rm -f aux/blossom.*
	$(LATEXMK) -jobname=blossom -auxdir=aux -outdir=aux $(EXTRAOPT) protocol $(NOCRUFT)

.PHONY: blossom
blossom:
	$(MAKE) auxblossom
	mv -f aux/blossom.pdf .

.PHONY: auxheartwood
auxheartwood:
	printf '\\toggletrue{isheartwood}\n\\renewcommand{\\docversion}{Version %s [\\HeartwoodSpec]}' "$$(git describe --tags --abbrev=6)" |tee protocol.ver
	mkdir -p aux
	rm -f aux/heartwood.*
	$(LATEXMK) -jobname=heartwood -auxdir=aux -outdir=aux $(EXTRAOPT) protocol $(NOCRUFT)

.PHONY: heartwood
heartwood:
	$(MAKE) auxheartwood
	mv -f aux/heartwood.pdf .
	cp -f heartwood.pdf protocol.pdf

.PHONY: auxnufour
auxnufour:
	printf '\\toggletrue{isnufour}\n\\renewcommand{\\docversion}{Version %s [\\NufourSpec]}' "$$(git describe --tags --abbrev=6)" |tee protocol.ver
	mkdir -p aux
	rm -f aux/nufour.*
	$(LATEXMK) -jobname=nufour -auxdir=aux -outdir=aux $(EXTRAOPT) protocol $(NOCRUFT)

.PHONY: nufour
nufour:
	$(MAKE) auxnufour
	mv -f aux/nufour.pdf .

.PHONY: nolatexmk-sprout
nolatexmk-sprout:
	printf '\\renewcommand{\\docversion}{Version %s [\\SproutSpec]}' "$$(git describe --tags --abbrev=6)" |tee protocol.ver
	# If $(LATEX) fails, touch an input so that 'make' won't think it is up-to-date next time.
	rm -f sprout.aux sprout.bbl sprout.blg sprout.brf sprout.bcf
	$(LATEX) -jobname=sprout protocol.tex || { touch incremental_merkle.png; exit 1; }
	biber sprout
	$(LATEX) -jobname=sprout protocol.tex || { touch incremental_merkle.png; exit 1; }
	$(LATEX) -jobname=sprout protocol.tex || { touch incremental_merkle.png; exit 1; }
	sh mymakeindex.sh -o sprout.ind sprout.idx
	$(LATEX) -jobname=sprout protocol.tex || { touch incremental_merkle.png; exit 1; }

.PHONY: nolatexmk-sapling
nolatexmk-sapling:
	printf '\\toggletrue{issapling}\n\\renewcommand{\\docversion}{Version %s [\\SaplingSpec]}' "$$(git describe --tags --abbrev=6)" |tee protocol.ver
	# If $(LATEX) fails, touch an input so that 'make' won't think it is up-to-date next time.
	rm -f sapling.aux sapling.bbl sapling.blg sapling.brf sapling.bcf
	$(LATEX) -jobname=sapling protocol.tex || { touch incremental_merkle.png; exit 1; }
	biber sapling
	$(LATEX) -jobname=sapling protocol.tex || { touch incremental_merkle.png; exit 1; }
	$(LATEX) -jobname=sapling protocol.tex || { touch incremental_merkle.png; exit 1; }
	sh mymakeindex.sh -o sapling.ind sapling.idx
	$(LATEX) -jobname=sapling protocol.tex || { touch incremental_merkle.png; exit 1; }

.PHONY: nolatexmk-blossom
nolatexmk-blossom:
	printf '\\toggletrue{isblossom}\n\\renewcommand{\\docversion}{Version %s [\\BlossomSpec]}' "$$(git describe --tags --abbrev=6)" |tee protocol.ver
	# If $(LATEX) fails, touch an input so that 'make' won't think it is up-to-date next time.
	rm -f blossom.aux blossom.bbl blossom.blg blossom.brf blossom.bcf
	$(LATEX) -jobname=blossom protocol.tex || { touch incremental_merkle.png; exit 1; }
	biber blossom
	$(LATEX) -jobname=blossom protocol.tex || { touch incremental_merkle.png; exit 1; }
	$(LATEX) -jobname=blossom protocol.tex || { touch incremental_merkle.png; exit 1; }
	sh mymakeindex.sh -o blossom.ind blossom.idx
	$(LATEX) -jobname=blossom protocol.tex || { touch incremental_merkle.png; exit 1; }
	cp -f blossom.pdf protocol.pdf

.PHONY: nolatexmk-heartwood
nolatexmk-heartwood:
	printf '\\toggletrue{isheartwood}\n\\renewcommand{\\docversion}{Version %s [\\HeartwoodSpec]}' "$$(git describe --tags --abbrev=6)" |tee protocol.ver
	# If $(LATEX) fails, touch an input so that 'make' won't think it is up-to-date next time.
	rm -f heartwood.aux heartwood.bbl heartwood.blg heartwood.brf heartwood.bcf
	$(LATEX) -jobname=heartwood protocol.tex || { touch incremental_merkle.png; exit 1; }
	biber heartwood
	$(LATEX) -jobname=heartwood protocol.tex || { touch incremental_merkle.png; exit 1; }
	$(LATEX) -jobname=heartwood protocol.tex || { touch incremental_merkle.png; exit 1; }
	sh mymakeindex.sh -o heartwood.ind heartwood.idx
	$(LATEX) -jobname=heartwood protocol.tex || { touch incremental_merkle.png; exit 1; }

.PHONY: nolatexmk-nufour
nolatexmk-nufour:
	printf '\\toggletrue{isnufour}\n\\renewcommand{\\docversion}{Version %s [\\NufourSpec]}' "$$(git describe --tags --abbrev=6)" |tee protocol.ver
	# If $(LATEX) fails, touch an input so that 'make' won't think it is up-to-date next time.
	rm -f nufour.aux nufour.bbl nufour.blg nufour.brf nufour.bcf
	$(LATEX) -jobname=nufour protocol.tex || { touch incremental_merkle.png; exit 1; }
	biber nufour
	$(LATEX) -jobname=nufour protocol.tex || { touch incremental_merkle.png; exit 1; }
	$(LATEX) -jobname=nufour protocol.tex || { touch incremental_merkle.png; exit 1; }
	sh mymakeindex.sh -o nufour.ind nufour.idx
	$(LATEX) -jobname=nufour protocol.tex || { touch incremental_merkle.png; exit 1; }

.PHONY: clean
clean:
	rm -f aux/* html/* protocol.ver protocol.pdf
