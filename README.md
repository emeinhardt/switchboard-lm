# switchboard-lm

# Processing swbd2003 for use with a language model

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
