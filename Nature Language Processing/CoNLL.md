# CoNLL

SyntaxNet을 사용하면서 알게된 CoNLL Format.

---------------------------------

## Overview

There are many different CoNLL formats since CoNLL is a different shared task each year. The format for CoNLL 2009 is described here. Each line represents a single word with a series of tab-separated fields. _s indicate empty values. Mate-Parser's manual says that it uses the first 12 columns of CoNLL 2009:  

	ID FORM LEMMA PLEMMA POS PPOS FEAT PFEAT HEAD PHEAD DEPREL PDEPREL

The definition of some of these columns come from earlier shared tasks (the CoNLL-X format used in 2006 and 2007):

- ID (index in sentence, starting at 1)
- FORM (word form itself)
- LEMMA (word's lemma or stem)
- POS (part of speech)
- FEAT (list of morphological features separated by |)
- HEAD (index of syntactic parent, 0 for ROOT)
- DEPREL (syntactic relationship between HEAD and this word)

There are variants of those columns (e.g., PPOS but not POS) that start with P indicate that the value was automatically predicted rather a gold standard value.
Update: There is now a CoNLL-U data format as well which extends the CoNLL-X format.

---------------------------------

## Pos Tagging

### Universal POS Tags

Alphabetical listing

- ADJ: adjective
- ADP: adposition
- ADV: adverb
- AUX: auxiliary verb
- CONJ: coordinating conjunction
- DET: determiner
- INTJ: interjection
- NOUN: noun
- NUM: numeral
- PART: particle
- PRON: pronoun
- PROPN: proper noun
- PUNCT: punctuation
- SCONJ: subordinating conjunction
- SYM: symbol
- VERB: verb
- X: other

---------------------------------

### Penn Treebank POS Tags

Here are the POS tags used in the Penn Treebank:


POS Tag | Description | Example
--- | --- | --- | ---
CC | coordinating | conjunction | and
CD | cardinal number | 1, third
DT | determiner | the
EX | existential there | there is
FW | foreign word | d’hoevre
IN | preposition/subordinating conjunction | in, of, like
JJ | adjective | big
JJR	 | adjective, comparative | bigger
JJS	 | adjective, superlative | biggest
LS | list marker | 1)
MD | modal | could, will
NN | noun, singular or mass | door
NNS	 | noun plural | doors
NNP	 | proper noun, singular | John
NNPS | proper noun, plural | 	Vikings
PDT	 | predeterminer	 | both the boys
POS	 | possessive ending | friend‘s
PRP	 | personal pronoun | I, he, it
PRP$ | possessive pronoun | my, his
RB | adverb | however, usually, naturally, here, good
RBR	 | adverb, comparative | better
RBS	 | adverb, superlative | best
RP | particle | 	give up
TO | to | to go, to him
UH | interjection | uhhuhhuhh
VB | verb, base form | take
VBD	 | verb, past tense | took
VBG	 | verb, gerund/present participle | taking
VBN	 | verb, past participle | taken
VBP	 | verb, sing. present, non-3d | take
VBZ	 | verb, 3rd person sing. present | 	takes
WDT	 | wh-determiner | which
WP | wh-pronoun | who, whatg
WP$	 | possessive wh-pronoun | whose
WRB	 | wh-abverb | where, when

---------------------------------

## Universal Dependencies

- acl: clausal modifier of noun (adjectival clause)
- advcl: adverbial clause modifier
- advmod: adverbial modifier
- amod: adjectival modifier
- appos: appositional modifier
- aux: auxiliary
- auxpass: passive auxiliary
- case: case marking
- cc: coordinating conjunction
- ccomp: clausal complement
- compound: compound
- conj: conjunct
- cop: copula
- csubj: clausal subject
- csubjpass: clausal passive subject
- dep: unspecified dependency
- det: determiner
- discourse: discourse element
- dislocated: dislocated elements
- dobj: direct object
- expl: expletive
- foreign: foreign words
- goeswith: goes with
- iobj: indirect object
- list: list
- mark: marker
- mwe: multi-word expression
- name: name
- neg: negation modifier
- nmod: nominal modifier
- nsubj: nominal subject
- nsubjpass: passive nominal subject
- nummod: numeric modifier
- parataxis: parataxis
- punct: punctuation
- remnant: remnant in ellipsis
- reparandum: overridden disfluency
- root: root
- vocative: vocative
- xcomp: open clausal complement
