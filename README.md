# Festcat clunits template


## Dependencies:

1. [Speech tools 2.1](http://www.cstr.ed.ac.uk/downloads/festival/2.1/speech_tools-2.1-release.tar.gz)
2. [Festival 2.1](http://www.cstr.ed.ac.uk/downloads/festival/2.1/festival-2.1-release.tar.gz)
3. [Festvox 2.4](http://www.speech.cs.cmu.edu/15-492/assignments/tts/packed2010/festvox-2.4-current.tar.gz)
4. [upc_ca_base](http://festcat.talp.cat/download/upc_ca_base-2.1.5.tgz)
5. A voice similar to your recordings (i.e: [pep](http://festcat.talp.cat/download/upc_ca_pep_clunits-1.0.tgz)):

### Compile them:

The festival suite:

        mkdir festival_suite
        cd festival_suite
        wget http://www.cstr.ed.ac.uk/downloads/festival/2.1/speech_tools-2.1-release.tar.gz
        wget http://www.cstr.ed.ac.uk/downloads/festival/2.1/festival-2.1-release.tar.gz
        wget http://www.speech.cs.cmu.edu/15-492/assignments/tts/packed2010/festvox-2.4-current.tar.gz
        tar xzf speech_tools-2.1-release.tar.gz
        tar xzf festival-2.1-release.tar.gz
        tar xzf festvox-2.4-current.tar.gz
        cd speech_tools
        ./configure
        make
        cd ../festival
        ./configure
        make
        cd ../festvox
        patch -p1 < ../../festvox-patch.diff
        ./configure
        make

The basic Catalan package:

        wget http://festcat.talp.cat/download/upc_ca_base-2.1.5.tgz
        tar xzf upc_ca_base-2.1.5.tgz
        cd upc_ca_base-2.1.5
        ./configure --enable-onlyinstall --enable-festivalpath="/home/sergio/Escriptori/tmptest/festival_suite/festival/bin"

A similar voice to your recordings:

        wget http://festcat.talp.cat/download/upc_ca_pep_clunits-1.0.tgz # PLEASE REPLACE THIS
        tar xzf upc_ca_pep_clunits-1.0.tgz
        mkdir -p /home/sergio/Escriptori/tmptest/festival_suite/festival/lib/voices/catalan
        cp -r upc_ca_pep_clunits /home/sergio/Escriptori/tmptest/festival_suite/festival/lib/voices/catalan/

## Configure:

I know the syntax may be unusual, but please stick to it. Simply change your paths, your "VOX" and "GENDER".

        ./configure ESTDIR="/home/sergio/Escriptori/tmptest/festival_suite/speech_tools" INST="upc" VOX="pol" GENDER="male" PROMPTS="/home/sergio/Escriptori/tmptest/upc_ca_prompts-1.0/etc/pol.data" WAVDIR="/home/sergio/Escriptori/tmptest/upc_ca_pol_raw/wav" CLOSESTVOICE="upc_ca_pep_clunits" FESTVOXDIR="/home/sergio/Escriptori/tmptest/festival_suite/festvox" --enable-festivalpath="/home/sergio/Escriptori/tmptest/festival_suite/festival/bin"


## Make:

If you type `make` everything will be done. But it's better if you run it step by step:

        make setup 
        make prompts
        make labs
        make utts
        make pm
        make mcep
        make clunits


## Installation and testing:

Please check any existing clunits voice to see what files are needed. 
