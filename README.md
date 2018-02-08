# How-to-create-a-table-to-hold-the-missing-value-count-for-multiple-columns
How to create a table to hold the missing value count for multiple columns. Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.
    How to create a table to hold the missing value count for multiple columns

    see
    https://goo.gl/isz6K5
    https://github.com/rogerjdeangelis/How-to-create-a-table-to-hold-the-missing-value-count-for-multiple-columns

    stole some code from here
    https://gist.github.com/statgeek/2de1faf1644dc8160fe721056202f111

    INPUT
    =====

      proc format ;
          value $ misfmt ' '="Missing" other="Not Missing";
          value   nmisfmt . ="Missing" other="Not Missing";
      run;


     WORK.HAVE total obs=19                          |
                                                    |
       NAME       SEX    AGE    HEIGHT    WEIGHT    |
                                                    |
       Alfred      M      14     69.0      112.5    |
       Alice       F      13     56.5       84.0    |
       Barbara     F      13     65.3       98.0    |
       Carol       F      14     62.8      102.5    |
       Henry       M      14     63.5      102.5    |
       James               .       .          .     |
       Jane                .       .          .     |
       Janet               .       .       112.5    |
       Jeffrey             .       .        84.0    |
       John                .       .          .     |
       Joyce               .       .        50.5    |
       Judy                .       .        90.0    |
       Louise             12       .          .     |
       Mary        F      15     66.5      112.0    | RULE for WEIGHT (we want)
       Philip      M      16     72.0      150.0    |
       Robert             12       .          .     |                         Non
       Ronald      M      15     67.0      133.0    |              Missing   Missing
       Thomas      M      11     57.5       85.0    |
       William     M      15     66.5      112.0    |  WEIGHT        5         14       19


    PROCESS  (All the code  could also do with proc report across but you need my rename macro)
    ===========================================================================================


       %utl_gather(have,var,val,,havxpo,valformat=$12.,withformats=Y);

       ods exclude all;
       ods output observed=want;
       proc corresp data=havXpo dim=1 observed;
       tables var, val;
       run;quit;
       ods select all;


    OUTPUT
    ======

       WORK.WANT total obs=6

                                 NOT_
          LABEL     MISSING    MISSING       SUM

          AGE           7         12          19
          HEIGHT        9         10          19
          NAME          0         19          19
          SEX           9         10          19
          WEIGHT        5         14          19

          Sum          30         65          95

    *                _              _       _
     _ __ ___   __ _| | _____    __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \  / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/ | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|  \__,_|\__,_|\__\__,_|

    ;

     data have;
          set sashelp.class;
          format _numeric_  nmisfmt11. _character_  misfmt11.;
          if age=12 then
                call missing(height, weight, sex);
          if name=:'J' then
                call missing(sex, age, height);
    run;quit;

    *create format for missing;

    proc format ;
          value $ misfmt ' '="Missing" other="Not Missing";
          value   nmisfmt .="Missing" other="Not Missing";
    run;
    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;
    %utl_gather(have,var,val,,havxpo,valformat=$12.,withformats=Y);

    /*
    WORK.HAVXPO total obs=95

      VAR       VAL            _COLFORMAT    _COLTYP

      NAME      Not Missing    $MISFMT11.       C
      SEX       Not Missing    $MISFMT11.       C
      AGE       Not Missing    NMISFMT11.       N
      HEIGHT    Not Missing    NMISFMT11.       N
      WEIGHT    Not Missing    NMISFMT11.       N
      NAME      Not Missing    $MISFMT11.       C
      SEX       Not Missing    $MISFMT11.       C
      AGE       Missing        NMISFMT11.       N
      HEIGHT    Missing        NMISFMT11.       N
      WEIGHT    Missing        NMISFMT11.       N
      NAME      Not Missing    $MISFMT11.       C
    ...
    */

    ods exclude all;
    ods output observed=want;
    proc corresp data=havXpo dim=1 observed;
    tables var, val;
    run;quit;
    ods select all;


