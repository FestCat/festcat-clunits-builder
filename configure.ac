AC_PREREQ([2.68])
AC_INIT([festcat-voice-template], [0.1], [https://github.com/zeehio/festival-ca/issues],[festcat-voice-template], [http://festcat.talp.cat])

AC_ARG_VAR(INST, [The first part of the voice name, typically "upc" in FestCat voices. ])
AC_ARG_VAR(VOX, [ A nice speaker name, such as "pau", "ona" or "bet".])
AC_ARG_VAR(GENDER, [ Either "male" or "female".])
AC_ARG_VAR(PROMPTS, [ The prompts.data file ])
AC_ARG_VAR(CLOSESTVOICE, [ A similar existing voice ("upc_ca_ona_hts") ])
AC_ARG_VAR(FESTVOXDIR, [ Festvox directory ])

if test "x$INST" = x; then
   AC_MSG_ERROR(INST was not set.)
fi

if test "x$VOX" = x; then
   AC_MSG_ERROR(VOX was not set.)
fi

if test "x$GENDER" = x; then
   AC_MSG_ERROR(GENDER was not set.)
   exit -1
fi

if test "x$PROMPTS" = x; then
   AC_MSG_ERROR(PROMPTS file path was not set)
   exit -1
fi

LANG="ca"
TYPE="clunits"


if test "x$CLOSESTVOICE" = x; then
   AC_MSG_ERROR(CLOSESTVOICE was not set.)
   exit -1
fi

if test "x$FESTVOXDIR" = x; then
   AC_MSG_ERROR(FESTVOXDIR was not set.)
   exit -1
fi

# Checks for programs.

# Checks for Festival: Required to build and to install.
AC_ARG_ENABLE(festivalpath,AS_HELP_STRING([--enable-festivalpath],[--enable-festivalpath=/path/to/festival (specify a custom festival path)]),[festivalpath=$enableval])

if test -d "${festivalpath}"; then 
    echo "using custom festival path: $festivalpath"
else
    festivalpath="$PATH"
fi

# FESTIVALBIN
AC_PATH_PROG([FESTIVALBIN], [festival], [:], [${festivalpath}])

if test ! -f "${FESTIVALBIN}"; then
        AC_MSG_ERROR(Can't find festival)
fi


# Checks for libdir and datadir
${FESTIVALBIN} --pipe > /dev/null 2>&1 << EOF
(set! fp (fopen "datadir.txt" "w"))
(if (boundp 'datadir)
(format fp datadir)
(format fp libdir)
)
(fclose fp)
EOF

FESTDATADIR=`cat "datadir.txt"`
rm "datadir.txt"

if test ! -d "${FESTDATADIR}"; then
        AC_MSG_ERROR(Can't find neither datadir or libdir)
fi
echo "Festival data directory: ${FESTDATADIR} found."

# WAGON
  # Checks for using Wagon:
  AC_ARG_ENABLE(wagonpath,AS_HELP_STRING([--enable-wagonpath],[--enable-wagonpath=/path/to/wagon (specify a custom wagon path)]),[wagonpath=$enableval])

  if test -d "${wagonpath}"; then 
      echo "using custom wagon path: $wagonpath"
  else
      wagonpath="$PATH"
  fi

AC_PATH_PROG([WAGON], [wagon], [:], [${wagonpath}])

if test ! -f "${WAGON}"; then
  AC_MSG_ERROR(Can't find wagon)
fi



AC_PROG_MKDIR_P
AC_PROG_INSTALL

AC_SUBST(INST)
AC_SUBST(LANG)
AC_SUBST(VOX)
AC_SUBST(TYPE)
AC_SUBST(GENDER)
AC_SUBST(PROMPTS)
AC_SUBST(FESTIVALBIN)
AC_SUBST(FESTDATADIR)
AC_SUBST(FESTVOXDIR)
AC_SUBST(WAGON)


AC_CONFIG_FILES([festvox/CLUNITS_clunits.scm festvox/build_clunits.scm etc/voice.defs Makefile])
AC_OUTPUT
