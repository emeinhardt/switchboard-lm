# switchboard-lm

# Preprocessing NXT Switchboard for use with a language model and predicting reduction

The goal of the notebook `Preprocessing NXT Switchboard corpus transcriptions for ease of processing and use with kenlm` is to produce a (relational) representation of the NXT Switchboard corpus whose dialogue turns have been segmented into utterances (per Seyfarth 2014, "Word informativity influences acoustic duration"), whose wordform (`phonword`) tokens have been annotated for various criteria relevant for a model of reduction, and whose orthographic representations have been normalized with respect to the Fisher corpus so that a language model (based on the Fisher corpus) can be applied to these tokens.

## Utterance segmentation

Turns are segmented into 'utterances' per Seyfarth 2014's criteria, as explicated in the text and in [his `buckeye` package](https://github.com/scjs/buckeye).

## Phonword annotation

To create utterance and phonword representations of interest (i.e. with enough information to apply exclusion criteria comparable to those in Seyfarth 2014), the following annotation layers are 'linked' together: `turn`, `syntax`, `terminal`, `phonword`, `syllable`, and `phone`, ultimately associating each `turn` with a sequence of (POS-tagged) `terminal`s, and thereby with a sequence of `phonword`s, and thereby with information about syllable and segmental information. 

## Preprocessing of orthographic representations

Motivated by intended use with a language model based on the Fisher corpus (documented in (this repository)[https://github.com/emeinhardt/fisher-lm]),

 1. Interrupted or broken-off wordforms - wordforms that speakers didn't finish production of or that they resumed production somewhere in the middle of have been replaced with `<rem>`.
 2. Words where transcribers were indecisive between multiple transcriptions have been replaced with `<rem>`.
 3. Non-speech noises (e.g. `[laughter]` or `[silence]`) are removed. Note that 'utterances' that in the original contained only things like this are not included in any post-processed data meant to be input to a language model.
 4. Words that transcribers weren't sure of(?) or that seemed like (whole words but) production errors or new coinages are apparently transcribed in curly braces. All such curly braces have been removed.
 5. Some orthographic tokens appear in the corpus with an underscore, e.g.
  - `{'<b_aside>', '<e_aside>', 'about_1', 'because_1', 'depends_1', 'especially_1', 'okay_1', "them's_1", 'them_1', "them_1's"}`
  - `_aside` tokens have been removed altogether and the trailing `_1` has been removed from all others.
 6. All characters are lower-cased.

## Dependencies
 - `more_itertools`, `funcy`, `joblib` and Unix shell command cell/line magics are used throughout, though none have essential functionality that couldn't easily be replaced by something else if necessary. Less essential are `pandas` and `plotline` used for plotting, sanity-checking, and calculating some statistics relevant to exclusion criteria. 
 
 
## A note on hardware and performance
*This 👏 notebook 👏 will 👏 not 👏 run 👏 on 👏 your 👏 personal 👏 laptop 👏 as 👏 is.* (This is also true of the other notebooks in this repository.)

Using `joblib` and running on a 32-(logical-)core AMD Ryzen Threadripper 2950X CPU, the notebook as is takes about 40m to run from end to end, with peak memory usage somewhere between 70-90GB. (Most of those 40m are spent linking and correcting layers of NXT annotation.) 

Between not using `joblib` and being rewritten to use lazy iterators or perform computations in batches, the notebook could certainly be refactored to have lower peak memory usage. Getting memory usage below 60GB by the end of the notebook will require aggressive deletion of variables once they're no longer needed (blunting the exploratory, stateful, and interactive properties of Jupyter notebook usage), throwing away some redundant information from relations, and writing/reading things from disk when they're not immediately necessary. Using `dask`'s out-of-core data structures is probably the path of least resistance, most portable direction (as opposed to e.g. arbitrarily machine-specific batch sizes); aggressive deletion is a distant second option.

## Outputs
If run successfully, this notebook will create the following files as outputs:
 1. A text file containing all the (normalized, orthographically represented) utterances containing at least one unexcluded wordform, one per line.
 2. A text file containing the full vocabulary of these utterances.
 3. A text file containing only the vocabulary of unexcluded ('target') wordforms.
 4. Text files containing the {1,2,3,4} {preceding, following} wordforms of each target wordform, plus a JSON file containing the full bidirectional context of each target wordform.
 5. A TSV file relating each non-disfluent, un-interrupted orthographic wordform in the corpus with a non-unk and non-empty-string processed orthographic representation to its unique transcription. (A handful of wordform types are thrown out to make a deterministic orthography⟶transcription mapping.)
 6. A JSON file containing fully annotated representations of each utterance whose normalized orthographic representation is in #1.
 7. A JSON file containing fully annotated representations of each target wordform token.
 8. A JSON file containing information (viz. including speech rate statistics) about speakers in the corpus.
 9. A JSON file containing information (viz. including duration statistics) about word types in the corpus.


# Preprocessing swbd2003 for use with a language model

The goal of the notebook `Preprocessing Switchboard (2003 release) corpus transcriptions for ease of processing and use with kenlm` is to produce a version of the 2003 release of the Switchboard corpus whose vocabulary has been normalized with respect to the Fisher corpus. The motivation for doing this is applying a language model trained on (a slightly processed version of) the Fisher corpus to Switchboard.

## Preprocessing steps

To that end, 
 1. Interrupted or broken-off wordforms - wordforms that speakers didn't finish production of or that they resumed production somewhere in the middle of have been replaced with `<rem>`.
 2. Non-speech noises (e.g. `[laughter]` or `[silence]`) are removed. Note that 'utterances' that in the original contained only things like this are not included in any post-processed data meant to be input to a language model.
 3. Words that transcribers weren't sure of(?) or that seemed like (whole words but) production errors or new coinages are apparently transcribed in curly braces. All such curly braces have been removed.
 4. The following tokens appear in the corpus with an underscore:
  - `{'<b_aside>', '<e_aside>', 'about_1', 'because_1', 'depends_1', 'especially_1', 'okay_1', "them's_1", 'them_1', "them_1's"}`
  - `_aside` tokens have been removed altogether and the trailing `_1` has been removed from all others.
 5. All characters are lower-cased.
 
## Dependencies
 - `more_itertools`, `funcy`, `joblib` and Unix shell command cell/line magics are used throughout, though none have essential functionality that couldn't relatively easily be replaced by something else if necessary.
 
## Outputs
If run successfully, this notebook will create five files as outputs:
 1. A .json file containing a list of objects (Python dictionaries), where each object is a finitary relation describing an utterance (and associated metadata) in the Switchboard corpus.
 2. A .json file containing a list of objects (Python dictionaries), where each object is a finitary relation describing a wordform token (and associated metadata) in the Switchboard corpus.
 3. A .txt file containing one utterance from Switchboard per line.
 4. A .txt file containing the vocabulary (one wordform per line) of the previous file.
 5. A .json file containing a further-annotated version of the word relation (#2 above) for other research purposes.


# Annotation Exploration

The notebook `Switchboard annotation exploration` is included as a moderately organized documentation of my exploration of the annotation structure of three different Switchboard corpora.