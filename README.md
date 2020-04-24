# utl-formatting-aggregated-across-cells-as-character-in-proc-report-not-possible-in-tabulate
Formatting aggregated across cells as character in proc report not possible in tabulate
    Formatting aggregated across cells as a character in proc report not possible in tabulate

           Two Solutions

                a. Without across
                b. With across

    github
    https://tinyurl.com/y87zs6j7
    https://github.com/rogerjdeangelis/utl-formatting-aggregated-across-cells-as-character-in-proc-report-not-possible-in-tabulate

    I am producing output 'ODS" like tables from proc report not static printer ouptu.

    Generally, proc report wants aggregated sums and percents in across cells to be numeric.
    I know of no other base SAS REPORTING procedure that can produce the following outputs.

    Tabulate does allow combined statistics in one cell?

    macros
    https://tinyurl.com/y9nfugth
    https://github.com/rogerjdeangelis/utl-macros-used-in-many-of-rogerjdeangelis-repositories

    inspred by
    https://tinyurl.com/yd7hsy3o
    https://communities.sas.com/t5/SAS-Programming/Create-cross-table-by-PROC-REPORT/m-p/642405

    Cynthia_sas
    https://communities.sas.com/t5/user/viewprofilepage/user-id/13549
    I have learned much from Cynthia

    *            _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| '_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    ;
    I want 'ODS' like tables from proc report

    Up to 40 obs from WORK.WANT total obs=5

                        CNT_      CNT_     NPCT_      NPCT_
      AGE    WEIGHT    MALES    FEMALES    MALES     FEMALES

       11     135.5      1         1       1(50%)    1(50%)
       12     472.0      2         3       2(40%)    3(60%)
       13     266.0      2         1       2(67%)    1(33%)
       14     407.5      2         2       2(50%)    2(50%)
       15     469.5      2         2       2(50%)    2(50%)


    or without the across

    up to 40 obs from WANT_ONECOL total obs=6

      STUDENT_    STUDENT_    COUNT_
         AGE       WEIGHT     PERCENT

         11           68      2(11%)
         12           94      5(26%)
         13           89      3(16%)
         14          102      4(21%)
         15          117      4(21%)
         16          150      1(5.3%)

    *_                   _
    (_)_ __  _ __  _   _| |_
    | | '_ \| '_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    ;

    Up to 40 obs from SASHELP.CLASS total obs=19

       NAME       SEX    AGE    HEIGHT    WEIGHT

       Joyce       F      11     51.3       50.5
       Louise      F      12     56.3       77.0
       Alice       F      13     56.5       84.0
       James       M      12     57.3       83.0
       Thomas      M      11     57.5       85.0
    ...

    *
     _ __  _ __ ___   ___ ___  ___ ___
    | '_ \| '__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
                        _ _   _                 _
      __ _    __      _(_) |_| |__   ___  _   _| |_    __ _  ___ _ __ ___  ___ ___
     / _` |   \ \ /\ / / | __| '_ \ / _ \| | | | __|  / _` |/ __| '__/ _ \/ __/ __|
    | (_| |_   \ V  V /| | |_| | | | (_) | |_| | |_  | (_| | (__| | | (_) \__ \__ \
     \__,_(_)   \_/\_/ |_|\__|_| |_|\___/ \__,_|\__|  \__,_|\___|_|  \___/|___/___/

    ;

    %utl_odsrpt(setup);
    proc report data=sashelp.class nowd missing box formchar="|" noheader;
    title "|student_age|student_weight|Count_Percent|";
    cols age weight n pctn npctn;
    define age / group;
    define weight / mean format=3.;
    define n / "count" noprint;
    define pctn / "Pct" noprint;
    define npctn / computed;
    compute npctn / character length=15;
      npctn=cats(put(n,2.),'(',put(pctn,percent6.2),')');
    endcomp;
    run;quit;
    %utl_odsrpt(outdsn=want_onecol);

    *_                 _ _   _
    | |__    __      _(_) |_| |__     __ _  ___ _ __ ___  ___ ___
    | '_ \   \ \ /\ / / | __| '_ \   / _` |/ __| '__/ _ \/ __/ __|
    | |_) |   \ V  V /| | |_| | | | | (_| | (__| | | (_) \__ \__ \
    |_.__(_)   \_/\_/ |_|\__|_| |_|  \__,_|\___|_|  \___/|___/___/

    ;

    %utl_odsrpt(setup);
    proc report data=have nowd missing box out=tst formchar='|' noheader;
    title  "|Age|Weight| nPct_Males|nPct_Females|cnt_Males|cnt_Females|";
    cols age weight  sex, (n m f) ;
    define age / group;
    define sex / across;
    define n /  "count" noprint;
    define m /  "M" computed;
    define f /  "F" computed;
    compute f / character length=15;
      _c4_=cats(put(_c3_,2.),'(',put(_c3_/(_c3_+_c6_),percent6.2),')');
      _c5_=cats(put(_c6_,2.),'(',put(_c6_/(_c3_+_c6_),percent6.2),')');
      _c7_=_c3_;
      _c8_=_c6_;
    endcomp;
    compute m / character length=15;
    endcomp;
    run;quit;
    %utl_odsrpt(outdsn=want);

    *_
    | | ___   __ _
    | |/ _ \ / _` |
    | | (_) | (_| |
    |_|\___/ \__, |
             |___/
    ;


    MLOGIC(UTL_ODSRPT):  Beginning execution.
    MLOGIC(UTL_ODSRPT):  This macro was compiled from the autocall file c:\oto\utl_odsrpt.sas
    MLOGIC(UTL_ODSRPT):  Parameter OUTDSN has value setup
    SYMBOLGEN:  Macro variable OUTDSN resolves to setup
    MLOGIC(UTL_ODSRPT):  %IF condition %qupcase(&outdsn)=SETUP is TRUE
    MLOGIC(UTL_ODSRPT):  %PUT @@@@ &=sysindex.
    SYMBOLGEN:  Macro variable SYSINDEX resolves to 224
    @@@@ SYSINDEX=224
    MLOGIC(UTL_ODSRPT):  %LET (variable name is _TMP1_)
    SYMBOLGEN:  Macro variable SYSINDEX resolves to 224
    MLOGIC(UTL_ODSRPT):  %PUT xxxx &=_tmp1_
    SYMBOLGEN:  Macro variable _TMP1_ resolves to a224
    xxxx _TMP1_=a224
    SYMBOLGEN:  Macro variable _TMP1_ resolves to a224
    MPRINT(UTL_ODSRPT):   filename a224 clear;
    WARNING: No logical assign for filename A224.
    MPRINT(UTL_ODSRPT):   * just in case;
    MLOGIC(UTLFKIL):  Beginning execution.
    SYMBOLGEN:  Macro variable _TMP1_ resolves to a224
    MLOGIC(UTLFKIL):  Parameter UTLFKIL has value d:\wrk\_TD7784_E6420_/a224.txt
    MLOGIC(UTLFKIL):  %LOCAL  URC
    MLOGIC(UTLFKIL):  %LET (variable name is URC)
    SYMBOLGEN:  Macro variable UTLFKIL resolves to d:\wrk\_TD7784_E6420_/a224.txt
    SYMBOLGEN:  Macro variable URC resolves to 0
    SYMBOLGEN:  Macro variable FNAME resolves to #LN00565
    MLOGIC(UTLFKIL):  %IF condition &urc = 0 and %sysfunc(fexist(&fname)) is FALSE
    MLOGIC(UTLFKIL):  %LET (variable name is URC)
    MPRINT(UTLFKIL):   run;
    MLOGIC(UTLFKIL):  Ending execution.
    MPRINT(UTL_ODSRPT):  ;
    SYMBOLGEN:  Macro variable _TMP1_ resolves to a224
    SYMBOLGEN:  Macro variable _TMP1_ resolves to a224
    MPRINT(UTL_ODSRPT):   filename a224 "d:\wrk\_TD7784_E6420_/a224.txt";
    MLOGIC(UTL_ODSRPT):  %LET (variable name is _PS_)
    MLOGIC(UTL_ODSRPT):  %LET (variable name is _FC_)
    MPRINT(UTL_ODSRPT):   OPTIONS ls=max ps=32756 FORMCHAR='|' nodate nocenter;
    MPRINT(UTL_ODSRPT):   title;
    MPRINT(UTL_ODSRPT):   footnote;
    SYMBOLGEN:  Macro variable _TMP1_ resolves to a224
    MPRINT(UTL_ODSRPT):   proc printto print=a224;
    MPRINT(UTL_ODSRPT):   run;

    NOTE: PROCEDURE PRINTTO used (Total process time):
          real time           0.00 seconds
          user cpu time       0.00 seconds
          system cpu time     0.00 seconds
          memory              132.71k
          OS Memory           21248.00k
          Timestamp           04/24/2020 01:22:29 PM
          Step Count                        721  Switch Count  0


    MPRINT(UTL_ODSRPT):  quit;
    MLOGIC(UTL_ODSRPT):  Ending execution.
    2179  proc report data=have nowd missing box out=tst formchar='|' noheader;
    2180  title  "|Age|Weight| nPct_Males|nPct_Females|cnt_Males|cnt_Females|";
    2181  cols age weight  sex, (n m f) ;
    2182  define age / group;
    2183  define sex / across;
    2184  define n /  "count" noprint;
    2185  define m /  "M" computed;
    2186  define f /  "F" computed;
    2187  compute f / character length=15;
    2188    _c4_=cats(put(_c3_,2.),'(',put(_c3_/(_c3_+_c6_),percent6.2),')');
    2189    _c5_=cats(put(_c6_,2.),'(',put(_c6_/(_c3_+_c6_),percent6.2),')');
    2190    _c7_=_c3_;
    2191    _c8_=_c6_;
    2192  endcomp;
    2193  compute m / character length=15;
    2194  endcomp;
    2195  run;

    NOTE: Multiple concurrent threads will be used to summarize data.
    2195!     quit;
    NOTE: Numeric values have been converted to character values at the places given by: (Line):(Column).
          3:7   4:7
    NOTE: Missing values were generated as a result of performing an operation on missing values.
          Each place is given by: (Number of times) at (Line):(Column).
          10 at 1:37   10 at 1:43   10 at 2:37   10 at 2:43
    NOTE: There were 18 observations read from the data set WORK.HAVE.
    NOTE: The data set WORK.TST has 5 observations and 9 variables.
    NOTE: At least one W.D format was too small for the number to be printed. The decimal may be shifted by the "BEST" format.
    NOTE: PROCEDURE REPORT used (Total process time):
          real time           0.03 seconds
          user cpu time       0.03 seconds
          system cpu time     0.00 seconds
          memory              13874.43k
          OS Memory           35604.00k
          Timestamp           04/24/2020 01:22:29 PM
          Step Count                        722  Switch Count  0


    2196  %utl_odsrpt(outdsn=want);
    MLOGIC(UTL_ODSRPT):  Beginning execution.
    MLOGIC(UTL_ODSRPT):  This macro was compiled from the autocall file c:\oto\utl_odsrpt.sas
    MLOGIC(UTL_ODSRPT):  Parameter OUTDSN has value want
    SYMBOLGEN:  Macro variable OUTDSN resolves to want
    MLOGIC(UTL_ODSRPT):  %IF condition %qupcase(&outdsn)=SETUP is FALSE
    MLOGIC(UTL_ODSRPT):  %PUT @@@ &=sysindex.
    SYMBOLGEN:  Macro variable SYSINDEX resolves to 226
    @@@ SYSINDEX=226
    MLOGIC(UTL_ODSRPT):  %LET (variable name is _TMP2_)
    SYMBOLGEN:  Macro variable SYSINDEX resolves to 226
    MLOGIC(UTL_ODSRPT):  %LET (variable name is _TMP1_)
    SYMBOLGEN:  Macro variable SYSINDEX resolves to 226
    MLOGIC(UTL_ODSRPT):  %PUT xxxx  &=_tmp1_
    SYMBOLGEN:  Macro variable _TMP1_ resolves to a224
    xxxx  _TMP1_=a224
    MLOGIC(UTL_ODSRPT):  %PUT xxxx  &=_tmp2_
    SYMBOLGEN:  Macro variable _TMP2_ resolves to b226
    xxxx  _TMP2_=b226
    MPRINT(UTL_ODSRPT):   proc printto;
    MPRINT(UTL_ODSRPT):   run;

    NOTE: PROCEDURE PRINTTO used (Total process time):
          real time           0.01 seconds
          user cpu time       0.01 seconds
          system cpu time     0.00 seconds
          memory              6.68k
          OS Memory           21248.00k
          Timestamp           04/24/2020 01:22:29 PM
          Step Count                        723  Switch Count  0


    MPRINT(UTL_ODSRPT):  quit;
    SYMBOLGEN:  Macro variable _TMP2_ resolves to b226
    MPRINT(UTL_ODSRPT):   filename b226 clear;
    WARNING: No logical assign for filename B226.
    MLOGIC(UTLFKIL):  Beginning execution.
    SYMBOLGEN:  Macro variable _TMP2_ resolves to b226
    MLOGIC(UTLFKIL):  Parameter UTLFKIL has value d:\wrk\_TD7784_E6420_/b226txt
    MLOGIC(UTLFKIL):  %LOCAL  URC
    MLOGIC(UTLFKIL):  %LET (variable name is URC)
    SYMBOLGEN:  Macro variable UTLFKIL resolves to d:\wrk\_TD7784_E6420_/b226txt
    SYMBOLGEN:  Macro variable URC resolves to 0
    SYMBOLGEN:  Macro variable FNAME resolves to #LN00566
    MLOGIC(UTLFKIL):  %IF condition &urc = 0 and %sysfunc(fexist(&fname)) is FALSE
    MLOGIC(UTLFKIL):  %LET (variable name is URC)
    MPRINT(UTLFKIL):   run;
    MLOGIC(UTLFKIL):  Ending execution.
    MPRINT(UTL_ODSRPT):  ;
    MPRINT(UTL_ODSRPT):   proc datasets lib=work nolist;
    MPRINT(UTL_ODSRPT):   *just in case;
    SYMBOLGEN:  Macro variable OUTDSN resolves to want
    MPRINT(UTL_ODSRPT):   delete want;
    MPRINT(UTL_ODSRPT):   run;

    NOTE: Deleting WORK.WANT (memtype=DATA).
    MPRINT(UTL_ODSRPT):  quit;
    NOTE: PROCEDURE DATASETS used (Total process time):
          real time           0.01 seconds
          user cpu time       0.00 seconds
          system cpu time     0.00 seconds
          memory              243.43k
          OS Memory           21248.00k
          Timestamp           04/24/2020 01:22:29 PM
          Step Count                        724  Switch Count  0


    SYMBOLGEN:  Macro variable _TMP2_ resolves to b226
    SYMBOLGEN:  Macro variable _TMP2_ resolves to b226
    MPRINT(UTL_ODSRPT):   filename b226 "d:\wrk\_TD7784_E6420_/b226txt";
    MPRINT(UTL_ODSRPT):   data _null_;
    SYMBOLGEN:  Macro variable _TMP1_ resolves to a224
    MPRINT(UTL_ODSRPT):   infile a224 length=l;
    MPRINT(UTL_ODSRPT):   input lyn $varying32756. l;
    MPRINT(UTL_ODSRPT):   if countc(lyn,'|')>1;
    MPRINT(UTL_ODSRPT):   lyn=compress(lyn);
    MPRINT(UTL_ODSRPT):   putlog lyn;
    SYMBOLGEN:  Macro variable _TMP2_ resolves to b226
    MPRINT(UTL_ODSRPT):   file b226;
    MPRINT(UTL_ODSRPT):   put lyn;
    MPRINT(UTL_ODSRPT):   run;

    NOTE: The infile A224 is:
          Filename=d:\wrk\_TD7784_E6420_\a224.txt,
          RECFM=V,LRECL=384,File Size (bytes)=1117,
          Last Modified=24Apr2020:13:22:29,
          Create Time=24Apr2020:13:22:29

    NOTE: The file B226 is:
          Filename=d:\wrk\_TD7784_E6420_\b226txt,
          RECFM=V,LRECL=384,File Size (bytes)=0,
          Last Modified=24Apr2020:13:22:29,
          Create Time=24Apr2020:13:22:29




    |Age|Weight|nPct_Males|nPct_Females|cnt_Males|cnt_Females|
    |11|135.5|1(50%)|1(50%)|1|1|
    |12|472|2(40%)|3(60%)|2|3|
    |13|266|2(67%)|1(33%)|2|1|
    |14|407.5|2(50%)|2(50%)|2|2|
    |15|469.5|2(50%)|2(50%)|2|2|



    NOTE: 13 records were read from the infile A224.
          The minimum record length was 0.
          The maximum record length was 94.
    NOTE: 6 records were written to the file B226.
          The minimum record length was 26.
          The maximum record length was 58.
    NOTE: DATA statement used (Total process time):
          real time           0.04 seconds
          user cpu time       0.01 seconds
          system cpu time     0.01 seconds
          memory              486.12k
          OS Memory           21248.00k
          Timestamp           04/24/2020 01:22:29 PM
          Step Count                        725  Switch Count  0


    MPRINT(UTL_ODSRPT):  quit;
    SYMBOLGEN:  Macro variable _TMP2_ resolves to b226
    SYMBOLGEN:  Macro variable OUTDSN resolves to want
    MPRINT(UTL_ODSRPT):   proc import datafile=b226 dbms=dlm out=want(drop=VAR:) replace;
    MPRINT(UTL_ODSRPT):   ADLM;
    MPRINT(UTL_ODSRPT):   delimiter='|';
    MPRINT(UTL_ODSRPT):   getnames=yes;
    MPRINT(UTL_ODSRPT):   run;

    Number of names found is less than number of variables found.
    Name  is not a valid SAS name.
    Problems were detected with provided names.  See LOG.
    2197   /**********************************************************************
    2198   *   PRODUCT:   SAS
    2199   *   VERSION:   9.4
    2200   *   CREATOR:   External File Interface
    2201   *   DATE:      24APR20
    2202   *   DESC:      Generated SAS Datastep Code
    2203   *   TEMPLATE SOURCE:  (None Specified.)
    2204   ***********************************************************************/
    2205      data WORK.WANT (drop=VAR:)   ;
    MPRINT(UTL_ODSRPT):   data WORK.WANT (drop=VAR:) ;
    2206      %let _EFIERR_ = 0; /* set the ERROR detection macro variable */
    2207      infile B226 delimiter = '|' MISSOVER DSD lrecl=384 firstobs=2 ;
    MPRINT(UTL_ODSRPT):   infile B226 delimiter = '|' MISSOVER DSD lrecl=384 firstobs=2 ;
    2208         informat VAR1 $1. ;
    MPRINT(UTL_ODSRPT):   informat VAR1 $1. ;
    2209         informat Age best32. ;
    MPRINT(UTL_ODSRPT):   informat Age best32. ;
    2210         informat Weight best32. ;
    MPRINT(UTL_ODSRPT):   informat Weight best32. ;
    2211         informat nPct_Males $6. ;
    MPRINT(UTL_ODSRPT):   informat nPct_Males $6. ;
    2212         informat nPct_Females $6. ;
    MPRINT(UTL_ODSRPT):   informat nPct_Females $6. ;
    2213         informat cnt_Males best32. ;
    MPRINT(UTL_ODSRPT):   informat cnt_Males best32. ;
    2214         informat cnt_Females best32. ;
    MPRINT(UTL_ODSRPT):   informat cnt_Females best32. ;
    2215         informat VAR8 $1. ;
    MPRINT(UTL_ODSRPT):   informat VAR8 $1. ;
    2216         format VAR1 $1. ;
    MPRINT(UTL_ODSRPT):   format VAR1 $1. ;
    2217         format Age best12. ;
    MPRINT(UTL_ODSRPT):   format Age best12. ;
    2218         format Weight best12. ;
    MPRINT(UTL_ODSRPT):   format Weight best12. ;
    2219         format nPct_Males $6. ;
    MPRINT(UTL_ODSRPT):   format nPct_Males $6. ;
    2220         format nPct_Females $6. ;
    MPRINT(UTL_ODSRPT):   format nPct_Females $6. ;
    2221         format cnt_Males best12. ;
    MPRINT(UTL_ODSRPT):   format cnt_Males best12. ;
    2222         format cnt_Females best12. ;
    MPRINT(UTL_ODSRPT):   format cnt_Females best12. ;
    2223         format VAR8 $1. ;
    MPRINT(UTL_ODSRPT):   format VAR8 $1. ;
    2224      input
    2225                  VAR1  $
    2226                  Age
    2227                  Weight
    2228                  nPct_Males  $
    2229                  nPct_Females  $
    2230                  cnt_Males
    2231                  cnt_Females
    2232                  VAR8  $
    2233      ;
    MPRINT(UTL_ODSRPT):   input VAR1 $ Age Weight nPct_Males $ nPct_Females $ cnt_Males cnt_Females VAR8 $ ;
    2234      if _ERROR_ then call symputx('_EFIERR_',1);  /* set ERROR detection macro variable */
    MPRINT(UTL_ODSRPT):   if _ERROR_ then call symputx('_EFIERR_',1);
    2235      run;
    MPRINT(UTL_ODSRPT):   run;

    NOTE: The infile B226 is:
          Filename=d:\wrk\_TD7784_E6420_\b226txt,
          RECFM=V,LRECL=384,File Size (bytes)=206,
          Last Modified=24Apr2020:13:22:29,
          Create Time=24Apr2020:13:22:29

    NOTE: 5 records were read from the infile B226.
          The minimum record length was 26.
          The maximum record length was 28.
    NOTE: The data set WORK.WANT has 5 observations and 6 variables.
    NOTE: DATA statement used (Total process time):
          real time           0.06 seconds
          user cpu time       0.01 seconds
          system cpu time     0.04 seconds
          memory              9578.90k
          OS Memory           27040.00k
          Timestamp           04/24/2020 01:22:29 PM
          Step Count                        726  Switch Count  0


    5 rows created in WORK.WANT from B226.



    NOTE: WORK.WANT data set was successfully created.
    NOTE: The data set WORK.WANT has 5 observations and 6 variables.
    NOTE: PROCEDURE IMPORT used (Total process time):
          real time           0.17 seconds
          user cpu time       0.06 seconds
          system cpu time     0.10 seconds
          memory              9578.90k
          OS Memory           27040.00k
          Timestamp           04/24/2020 01:22:29 PM
          Step Count                        726  Switch Count  10


    MPRINT(UTL_ODSRPT):    ;
    MPRINT(UTL_ODSRPT):   quit;
    SYMBOLGEN:  Macro variable _TMP1_ resolves to a224
    MPRINT(UTL_ODSRPT):   filename a224 clear;
    NOTE: Fileref A224 has been deassigned.
    SYMBOLGEN:  Macro variable _TMP2_ resolves to b226
    MPRINT(UTL_ODSRPT):   filename b226 clear;
    NOTE: Fileref B226 has been deassigned.
    MLOGIC(UTLFKIL):  Beginning execution.
    SYMBOLGEN:  Macro variable _TMP1_ resolves to a224
    MLOGIC(UTLFKIL):  Parameter UTLFKIL has value d:\wrk\_TD7784_E6420_/a224txt
    MLOGIC(UTLFKIL):  %LOCAL  URC
    MLOGIC(UTLFKIL):  %LET (variable name is URC)
    SYMBOLGEN:  Macro variable UTLFKIL resolves to d:\wrk\_TD7784_E6420_/a224txt
    SYMBOLGEN:  Macro variable URC resolves to 0
    SYMBOLGEN:  Macro variable FNAME resolves to #LN00567
    MLOGIC(UTLFKIL):  %IF condition &urc = 0 and %sysfunc(fexist(&fname)) is FALSE
    MLOGIC(UTLFKIL):  %LET (variable name is URC)
    MPRINT(UTLFKIL):   run;
    MLOGIC(UTLFKIL):  Ending execution.
    MPRINT(UTL_ODSRPT):  ;
    MLOGIC(UTLFKIL):  Beginning execution.
    SYMBOLGEN:  Macro variable _TMP2_ resolves to b226
    MLOGIC(UTLFKIL):  Parameter UTLFKIL has value d:\wrk\_TD7784_E6420_/b226txt
    MLOGIC(UTLFKIL):  %LOCAL  URC
    MLOGIC(UTLFKIL):  %LET (variable name is URC)
    SYMBOLGEN:  Macro variable UTLFKIL resolves to d:\wrk\_TD7784_E6420_/b226txt
    SYMBOLGEN:  Macro variable URC resolves to 0
    SYMBOLGEN:  Macro variable FNAME resolves to #LN00568
    MLOGIC(UTLFKIL):  %IF condition &urc = 0 and %sysfunc(fexist(&fname)) is TRUE
    MLOGIC(UTLFKIL):  %LET (variable name is URC)
    SYMBOLGEN:  Macro variable FNAME resolves to #LN00568
    MLOGIC(UTLFKIL):  %LET (variable name is URC)
    MPRINT(UTLFKIL):   run;
    MLOGIC(UTLFKIL):  Ending execution.
    MPRINT(UTL_ODSRPT):  ;
    MLOGIC(UTL_ODSRPT):  Ending execution.
    2236  %conha;
    MLOGIC(CONHA):  Beginning execution.
    MPRINT(CONHA):   FILENAME clp clipbrd ;
    MPRINT(CONHA):   DATA _NULL_;
    MPRINT(CONHA):   INFILE clp;
    MPRINT(CONHA):   INPUT;
    MPRINT(CONHA):   put _infile_;
    MPRINT(CONHA):   call symputx('argx',_infile_);
    MPRINT(CONHA):   RUN;

    NOTE: The infile CLP is:
          (no system-specific pathname available),
          (no system-specific file attributes available)

    WANT
    NOTE: 1 record was read from the infile CLP.
          The minimum record length was 4.
          The maximum record length was 4.
    NOTE: DATA statement used (Total process time):
          real time           0.01 seconds
          user cpu time       0.00 seconds
          system cpu time     0.01 seconds
          memory              315.34k
          OS Memory           21248.00k
          Timestamp           04/24/2020 01:22:56 PM
          Step Count                        727  Switch Count  0


    MPRINT(CONHA):   dm "out;clear;";
    SYMBOLGEN:  Macro variable ARGX resolves to WANT
    MPRINT(CONHA):   title "Contents of WANT";
    MPRINT(CONHA):   options nocenter;
    SYMBOLGEN:  Macro variable ARGX resolves to WANT
    MPRINT(CONHA):   proc contents data=WANT position;
    MPRINT(CONHA):   run;

    NOTE: PROCEDURE CONTENTS used (Total process time):
          real time           0.04 seconds
          user cpu time       0.00 seconds
          system cpu time     0.04 seconds
          memory              580.28k
          OS Memory           21248.00k
          Timestamp           04/24/2020 01:22:56 PM
          Step Count                        728  Switch Count  0


    MPRINT(CONHA):   title;
    MPRINT(CONHA):   dm "out;top";
    MLOGIC(CONHA):  Ending execution.



