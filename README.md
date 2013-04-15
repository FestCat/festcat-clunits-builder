# Festcat clunits template

This template allows building a clunits Catalan voice.

## Dependencies:

The dependencies are splitted in two groups. The *downloadable dependencies*
are dependencies that can be downloaded from the Internet easily. On the other
hand, you should provide some recordings and the prompts in festival format.

### Downloadable dependencies

1. [Speech tools 2.1](http://www.cstr.ed.ac.uk/downloads/festival/2.1/speech_tools-2.1-release.tar.gz)
2. [Festival 2.1](http://www.cstr.ed.ac.uk/downloads/festival/2.1/festival-2.1-release.tar.gz)
3. [Festvox 2.4](http://www.speech.cs.cmu.edu/15-492/assignments/tts/packed2010/festvox-2.4-current.tar.gz)
4. [upc_ca_base](http://festcat.talp.cat/download/upc_ca_base-3.0.2.tgz)
5. A voice similar to your recordings (i.e: [pep](http://festcat.talp.cat/download/upc_ca_pep_clunits-1.1.tgz)):

### Prompts and recordings

1. Prompts in festival format (you may check some 
   in [upc_ca_prompts](http://festcat.talp.cat/download/data/upc_ca_prompts-1.0.tar.bz2))
2. Recordings (currently this has been tested with 16kHZ, 16 bit wav recordings)


### Downloading and compiling dependencies:

If you have not downloaded this package, you may want to run:

        mkdir workdir
        cd workdir
        wget https://github.com/FestCat/festcat-clunits-builder/archive/master.zip
        unzip master.zip || exit 1
        cd festcat-clunits-builder-master

Download and install the festival suite:

        ./train_voice "festival_suite"

The basic Catalan package:

        ./train_voice "festcat_base"

Install a similar voice to your already existing recordings. This voice is used as a reference
for the phonetic labelling.

        ./train_voice "festcat_closest_voice" "upc_ca_pep_clunits"


## Setting the template parameters for your voice:

Now the system needs to know information from your speaker. 
This includes a name for the new voice, its gender and some path settings.
By now you should decide/know:

  - The new name for your voice (i.e. `pol`)
  - The gender of your speaker (i.e. `male`)
  - The dialect of your speaker (i.e. `central`)
  - A similar similar voice to the one you recorded (i.e. `upc_ca_pep_clunits`)
  - The path to your prompt file in festival format
  - The path to your recordings in 16kHz and 16bit wav format.

Use the following syntax (I know the `--enable-festivalpath` is not 
consistent with the rest of the arguments, that might be changed in the future):

        INST="upc" VOX="pol" GENDER="male" DIALECT="central" \
        CLOSESTVOICE="upc_ca_pep_clunits" \
        PROMPTS="/promptsdirectory/pol.data" \
        WAVDIR="/actualrecordings/wav" \
        ./train_voice "configure_template"


## Creating the synthetic clunits voice:

### If the "Closest voice" has the same dialect than your speaker

If you type `make` everything will be done. But it's better if you run it 
step by step understanding what is happening:

        make setup 
        make prompts
        make labs
        make utts
        make pm
        make mcep
        make clunits

### If the "Closest voice" has a different dialect than your speaker

**WORK IN PROGRESS: How things work**

The label files should relate the phonemes to the second they appear
in the recording.

In a setup where both speaker and closest voice dialects are equal, 
label files are generated for the closest voice during the `make prompts` step
and then the timings are used as an initial estimation to align 
the phonemes to your recordings. The alignment takes place on the
`make labs` step.

In a cross-dialect setup, we have two dialects:

 - Speaker dialect
 - Closest voice dialect.

For the speaker dialect we know the phoneme transcription (i.e. computed 
automatically with previously trained LTS rules) and we want to predict
the time at which each phoneme is pronounced in the recordings.

For the closest voice dialect, we know the phoneme transcription and we know
the timings.

As both dialects are different, both phoneme transcriptions are different and
we cannot use the timings from the closest voice as an initial estimation
of the recordings.

We need to build a map which converts one phoneme transcription in the other.
(Manual work is also possible)

Here is an outline of the required steps:

        make setup 
        make prompts
        make prompts-dialect
        SOME MAGIC_TO_MERGE_prompt-lab_AND_prompt-lab-dialect (Not yet implemented)
        make labs
        REPLACE prompt-utt with prompt-utt-dialect (trivial to do, but not implemented yet as can't be tested)
        make utts
        make pm
        make mcep
        make clunits

### Setup

This step copies the necessary files from festvox, the recordings 
and the text prompts (in festival format) to the template directory.

### Prompts

This step uses the reference voice to synthesize the text prompts.
A phonetic labelling reference will be generated in prompt-lab directory.

### Labs

This step fits the new recordings (in `wav` directory) to the synthesized 
labels (in `prompt-lab` directory).
As the alignment algorithm is not very robust to dialectal and other changes,
you may want to check the labelling result (available at `lab` directory) 
with `wavesurfer`.

#### Checking labels with wavesurfer:

1. Install wavesurfer (in Debian/Ubuntu `sudo apt-get install wavesurfer`)
2. Open wavesurfer (in Debian/Ubuntu you will probably need `padsp wavesurfer`)
3. Open Wave file
4. Right click -> Create Pane -> Transcription
5. Right click on transcription pane -> Load transcription -> Choose lab
file.

Alternatively you can just put lab and wav files together in a single
directory. Then wavesurfer will find label file automatically.

### Utts

This step merges the segmentation information from the aligned labels 
with the prompt-utt files to generate the final utt files which can be used
by festival. These .utt files are available at the `festival/utts` subdirectory
and are required for building HTS voices.

### pm

This step extracts the pitchmark from the audio recordings. Using a 
laryngograph can improve this step (TODO: the Makefile should be adapted
for that, pull requests are welcome).

### mcep

This step extracts the mel cepstral coefficients from the audio recordings.

### clunits

This final step builds the cluster units.

## Packaging and installation:

To use the new voice you will need the following files and directories:

  - `festvox`
  - `festival/clunits/upc_ca_pep.catalogue`
  - `festival/trees/upc_ca_pep.tree`
  - `wav`
  - `mcep`

Place these files and directories in a directory named `$INST_ca_$VOX_clunits`
(replace `$INST` and `$VOX` by your institution and your voice name 
respectively).

It is highly recommended to add a `doc` directory with some documentation,
copyright information and a suitable license. Given that the clunits voice
is mainly data, [CC-BY-SA](http://creativecommons.org/licenses/by-sa/3.0/) 
seems a sensible choice. Release your recordings under a free license if you 
have the chance, so all the community can benefit from them.

The `$INST_ca_$VOX_clunits` directory has to be copied to 
`festival/lib/voices/catalan/` or to the equivalent directory for your 
particular distribution (i.e. in Debian would be 
`/usr/share/festival/voices/catalan/`.

The voice can be used from festival with the command 
`(voice_$INST_ca_$VOX_clunits)` before the `tts` command.


