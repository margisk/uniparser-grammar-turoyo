# Turoyo morphological analyzer

This is a rule-based morphological analyzer for Ṭuroyo (``tru``, Afro-Asiatic > Central Neo-Aramaic). It is based on a formalized description of Turoyo morphology and uses [uniparser-morph](https://github.com/timarkh/uniparser-morph) for parsing. It performs full morphological analysis of Turoyo words (lemmatization, POS tagging, grammatical tagging). The text to be analyzed should be written in a version of Latin Turoyo alphabet which is somewhat closer to IPA: it uses *ʔ* instead of *'*, *ʕ* instead of *c*, *ə* insteadt of *ë* etc.

## How to use
### Python package
The analyzer is available as a Python package. If you want to analyze Turoyo texts in Python, install the module:

```
pip3 install uniparser-turoyo
```

Import the module and create an instance of ``TuroyoAnalyzer`` class. Set ``mode='strict'`` if you are going to process text in standard Latin Turoyo alphabet, or ``mode='nodiacritics'`` if you expect some words to lack the diacritics (e.g. *t* instead of *ṭ*). After that, you can either parse tokens or lists of tokens with ``analyze_words()``, or parse a frequency list with ``analyze_wordlist()``. Here is a simple example:

```python
from uniparser_turoyo import TuroyoAnalyzer
a = TuroyoAnalyzer(mode='strict')

analyses = a.analyze_words('koroḥamnux')
# The parser is initialized before first use, so expect
# some delay here (usually several seconds)

# You will get a list of Wordform objects
# The analysis attributes are stored in its properties
# as string values, e.g.:
for ana in analyses:
        print(ana.wf, ana.lemma, ana.gramm)

# You can also pass lists (even nested lists) and specify
# output format ('xml', 'json' or 'conll')
# If you pass a list, you will get a list of analyses
# with the same structure
analyses = a.analyze_words([['koroḥamnux'], ['ʕəbarwo', 'lab', 'bote', '.']],
	                       format='xml')
analyses = a.analyze_words([['koroḥamnux'], ['ʕəbarwo', 'lab', 'bote', '.']],
	                       format='conll')
analyses = a.analyze_words(['koroḥamnux', [['laḥmawo'], ['ʕəbarwo', 'lab', 'bote', '.']]],
	                       format='json')
```

Refer to the [uniparser-morph documentation](https://uniparser-morph.readthedocs.io/en/latest/) for the full list of options.

If you want to quickly check an analysis for one particular word, you can also use the command-line interface. Here is an example for the word *koroḥamnux*:

```
python3 -m uniparser_turoyo koroḥamnux
```

<!---
### Disambiguation
Apart from the analyzer, this repository contains a set of [Constraint Grammar](https://visl.sdu.dk/constraint_grammar.html) rules that can be used for partial disambiguation of analyzed Turoyo texts. If you want to use them, set ``disambiguation=True`` when calling ``analyze_words``:

```python
analyses = a.analyze_words(['ʕəbarwo', 'lab', 'bote', '.'], disambiguate=True)
```

In order for this to work, you have to install the ``cg3`` executable separately. On Ubuntu/Debian, you can use ``apt-get``:

```
sudo apt-get install cg3
```

On Windows, download the binary and add the path to the ``PATH`` environment variable. See [the documentation](https://visl.sdu.dk/cg3/single/#installation) for other options.

Note that each time you call ``analyze_words()`` with ``disambiguate=True``, the CG grammar is loaded and compiled from scratch, which makes the analysis even slower. If you are analyzing a large text, it would make sense to pass the entire text contents in a single function call rather than do it sentence-by-sentence, for optimal performance.
-->

### Word lists
Alternatively, you can use a preprocessed word list. The ``wordlists`` directory contains a list of words from a 600-thousand-word [Ṭuroyo corpus](https://neo-aramaic.web-corpora.net/index_en.html) (``wordlist.csv``) with 53,000 unique tokens, list of analyzed tokens (``wordlist_analyzed.txt``; each line contains all possible analyses for one word in an XML format), and list of tokens the parser could not analyze (``wordlist_unanalyzed.txt``). The recall of the analyzer on the corpus texts is about 90%. (This number is somewhat low due to orthographic variability in the texts.)

## Description format
The description is carried out in the ``uniparser-morph`` format and involves a description of the inflection (paradigms/paradigms_XXX.txt) and a grammatical dictionary (lexemes/lexemes-XXX.txt files). The dictionary contains descriptions of individual lexemes, each of which is accompanied by information about its stem, its part-of-speech tag and some other grammatical information, its consonant root, its inflectional type (paradigm), and English and/or German translations. See more about the format [in the uniparser-morph documentation](https://uniparser-morph.readthedocs.io/en/latest/format.html).
