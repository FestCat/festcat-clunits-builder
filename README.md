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
4. [upc_ca_base](http://festcat.talp.cat/download/upc_ca_base-2.1.5.tgz)
5. A voice similar to your recordings (i.e: [pep](http://festcat.talp.cat/download/upc_ca_pep_clunits-1.0.tgz)):

### Prompts and recordings

1. Prompts in festival format (you may check some 
   in [upc_ca_prompts](http://festcat.talp.cat/download/data/upc_ca_prompts-1.0.tar.bz2))
2. Recordings (currently this has been tested with 16kHZ, 16 bit wav recordings)


### Downloading and compiling dependencies:

The festival suite:

        mkdir workdir
        wget https://github.com/FestCat/festcat-clunits-builder/archive/master.zip
        unzip master.zip || exit 1
        cd festcat-clunits-builder-master
        mkdir festival_suite || exit 1
        cd festival_suite
        wget http://www.cstr.ed.ac.uk/downloads/festival/2.1/speech_tools-2.1-release.tar.gz
        wget http://www.cstr.ed.ac.uk/downloads/festival/2.1/festival-2.1-release.tar.gz
        wget http://www.speech.cs.cmu.edu/15-492/assignments/tts/packed2010/festvox-2.4-current.tar.gz
        tar xzf speech_tools-2.1-release.tar.gz || exit 1
        tar xzf festival-2.1-release.tar.gz || exit 1
        tar xzf festvox-2.4-current.tar.gz || exit 1
        cd speech_tools
        ./configure || exit 1
        make || exit 1
        cd ../festival
        ./configure || exit 1
        make || exit 1
        cd ../festvox
        patch -p1 < ../../festvox-patch.diff || exit 1
        ./configure || exit 1
        make || exit 1
        cd ..

The basic Catalan package:

        wget http://festcat.talp.cat/download/upc_ca_base-2.1.5.tgz
        tar xzf upc_ca_base-2.1.5.tgz || exit 1
        cd upc_ca_base-2.1.5
        ./configure --enable-onlyinstall \
        --enable-festivalpath="/home/sergio/Escriptori/tmptest/festival_suite/festival/bin" 
        make

Install a similar voice to your already existing recordings. This voice is used as a reference
for the phonetic labelling.

        wget http://festcat.talp.cat/download/upc_ca_pep_clunits-1.0.tgz # PLEASE REPLACE THIS
        tar xzf upc_ca_pep_clunits-1.0.tgz
        mkdir -p /home/sergio/Escriptori/tmptest/festival_suite/festival/lib/voices/catalan
        cp -r upc_ca_pep_clunits /home/sergio/Escriptori/tmptest/festival_suite/festival/lib/voices/catalan/

## Setting the template parameters for your voice:

Now the system needs to know information from your speaker. 
This includes a name for the new voice, its gender and some path settings.
By now you should decide/know:

  - The new name for your voice (i.e. `"pol"`)
  - The gender of your speaker (i.e. `"male"`)
  - A similar similar voice to the one you recorded (i.e. `"upc_ca_pep_clunits"`)
  - The path to your prompt file in festival format
  - The path to your recordings in 16kHz and 16bit wav format.
  - The path to speech-tools, festival and festvox.

Use the following syntax (I know the `--enable-festivalpath` is not 
consistent with the rest of the arguments, that might be changed in the future):

        ./configure \
        INST="upc" VOX="pol" GENDER="male" \
        CLOSESTVOICE="upc_ca_pep_clunits" \
        PROMPTS="/home/sergio/Escriptori/tmptest/upc_ca_prompts-1.0/etc/pol.data" \
        WAVDIR="/home/sergio/Escriptori/tmptest/upc_ca_pol_raw/wav" \
        ESTDIR="/home/sergio/Escriptori/tmptest/festival_suite/speech_tools" \
        FESTVOXDIR="/home/sergio/Escriptori/tmptest/festival_suite/festvox" \
        --enable-festivalpath="/home/sergio/Escriptori/tmptest/festival_suite/festival/bin"

## Creating the synthetic clunits voice:

If you type `make` everything will be done. But it's better if you run it 
step by step understanding what is happening:

        make setup 
        make prompts
        make labs
        make utts
        make pm
        make mcep
        make clunits

### Setup

This step copies the necessary files from festvox, and the recordings 
and text prompts in festival format to the template directory.

### Prompts

This step uses the reference voice to synthesize the text prompts.
A phonetic labelling reference will be generated in prompt-lab directory.

### Labs

This step fits the new recordings (in `wav` directory) to the synthesized 
labels (in `prompt-lab` directory).
You may want to check the labelling result (available at `lab` directory) 
with `wavesurfer`.

#### Checking labels with wavesurfer:

1. Open Wave file
2. Right click -> Create Pane -> Transcription
3. Right click on transcription pane -> Load transcription -> Choose lab
file.

Alternatively you can just put lab and wav files together in a single
directory. Then wavesurfer will find label file automatically.

### Utts

This step merges the segmentation information from the aligned labels 
with the prompt-utt files to generate the final utt files which can be used
by festival.

### pm

This step extracts the pitchmark from the audio recordings.

### mcep

This step extracts the mel cepstral coefficients from the audio recordings.

### clunits

This final step builds the cluster units combining all the previous information.

## Installation and testing:

To use the new voice you will need the following files and directories:

  - `festvox`
  - `festival/clunits/upc_ca_pep.catalogue`
  - `festival/trees/upc_ca_pep.tree`
  - `wav`
  - `mcep`

