# utl-tail-and-tailh-command-macros-list-last-eleven-observations
Tail, tailh and tailfh command macros list last 11 observations

    Tail, tailh and tailfh command macros list last 11 observations

    Adding unix tail to your classic editor performance package(utl_perpac.sas)

    github
    https://tinyurl.com/y79gsu8v
    https://github.com/rogerjdeangelis/utl-tail-and-tailh-command-macros-list-last-eleven-observations

    Functions (applicable to function keys, mouse action or classic editor clean command line)

        1. TAIL: Type tail on the command line and the last eleven obs(or all obs if less than 11) from the latest
           SAS table will appear in the output window and the paste buffer

        2. TAILH: Highlight the table name in and classic editor pgm. log, output or note window and
           last 11 obs will appear in the output window and in the paste buffer.

        3. TAILFH: Highlight the table name in and classic editor pgm. log, output or note window and
           last 11 FORMATTED obs will appear in the output window and in the paste buffer.


    For performance macros
    https://tinyurl.com/y9nfugth
    https://github.com/rogerjdeangelis/utl-macros-used-in-many-of-rogerjdeangelis-repositories
    utl_perpac.sas

    INPUT
    =====

    Command ===>

    000001  * make data;
    000002  data have ;
    000003    format date date9.;
    000004    do date=today() to today()+250;
    000005       age=int(50*uniform(1234));
    000006       wgt=100+age;
    000007       output;
    000008    end;
    000009    stop;
    000001  run;quit;
    000001

    WORK.HAVE total obs=251

    Obs      DATE       AGE    WGT

      1    08JAN2019     12    112
      2    09JAN2019      4    104
      3    10JAN2019     19    119
    ....
    249    13SEP2019     14    114
    250    14SEP2019     14    114
    251    15SEP2019     27    127



    EXAMPLE OUTPUTS (last 11 obs formatted and unformatted)
    --------------------------------------------------------

    FORMATTED (TAILFH)

    Last 11 obs from WORK.HAVE total obs=251

    Obs      DATE       AGE    WGT

    241    05SEP2019     15    115
    242    06SEP2019      6    106
    243    07SEP2019      2    102
    244    08SEP2019      5    105
    245    09SEP2019      6    106
    246    10SEP2019     30    130
    247    11SEP2019     21    121
    248    12SEP2019     12    112
    249    13SEP2019     14    114
    250    14SEP2019     14    114
    251    15SEP2019     27    127


    UNFORMATTED (TAIL(if last created or TAILH if hilighted)

    Last 11 obs from WORK.HAVE total obs=251

    Obs     DATE    AGE    WGT

    241    21797     15    115
    242    21798      6    106
    243    21799      2    102
    244    21800      5    105
    245    21801      6    106
    246    21802     30    130
    247    21803     21    121
    248    21804     12    112
    249    21805     14    114
    250    21806     14    114
    251    21807     27    127


    PROCESS (put the in three macros in your performance package autocall library)
    ===============================================================================

      Cut and paste all the macros below into c:/oto/utl_perpac.sas and include in your autoexec.

      Could also put them in separate macros

      c:/oto/tail.sas
      c:/oto/tailh.sas
      c:/oto/tailfh.sas

    Submit this code
    ----------------

    data have ;
      format date date9.;
      do date=today() to today()+250;
         age=int(50*uniform(1234));
         wgt=100+age;
         output;
      end;
      stop;
    run;quit;

    * then just type tail on classic editor command line or
    * hilight HAVE and type tailh or tailfh

    Command ===> tail

    000001  * make data;
    000002  data have ;
    000003    format date date9.;
    000004    do date=today() to today()+250;
    000005       age=int(50*uniform(1234));
    000006       wgt=100+age;
    000007       output;
    000008    end;
    000009    stop;
    000001  run;quit;
    000001


    *
     _ __ ___   __ _  ___ _ __ ___  ___
    | '_ ` _ \ / _` |/ __| '__/ _ \/ __|
    | | | | | | (_| | (__| | | (_) \__ \
    |_| |_| |_|\__,_|\___|_|  \___/|___/

    ;

    %macro tail / cmd
       des="last 10 obs from the last dataset";
       note;notesubmit '%taila;';
       run;
    %mend tail;

    %macro taila /cmd des="last 10 obs from table";
      %utlfkil("%sysfunc(pathname(work))/__dm.txt");
      footnote;
      options nocenter;
      proc printto print="%sysfunc(pathname(work))/__dm.txt";
      proc sql noprint;
            select
               put(count(*),comma18.)
              ,count(*)
            into
              :tob  trimmed
             ,:tobs  trimmed
          from _last_;quit;
      title "last 11 obs %upcase(%sysfunc(getoption(_last_))) total obs=&tob";
      %let pobs=%sysfunc(ifn(&tobs > 10, %eval(&tobs - 10),1));
      proc print data=_last_ ( firstObs= &pobs ) width=min uniform  heading=horizontal;
         format _all_;
      run;quit;
      proc printto;
      run;quit;
      title;
      filename __dm clipbrd ;
      data _null_;file _dm; put "";run;quit;
      data _null_;
         infile "%sysfunc(pathname(work))/__dm.txt" end=dne;
         input;
         file __dm ;
         put _infile_;
         file print;
         put _infile_;
      run;quit;
      filename __dm clear;
      run;quit;
      title;
      dm "out;";
    %mend taila;


    %macro tailh /cmd des="lat 10 obs from highlighted dataset";
       store;note;notesubmit '%tailha;';
    %mend tailh;


    %macro tailha /cmd des="last 10 obs highlight dataset and type tailha for a list of 10 obs";
      FILENAME clp clipbrd ;
      DATA _NULL_;
        INFILE clp;
        INPUT;
        put _infile_;
        call symputx('argx',_infile_);
      RUN;
      footnote;
      options nocenter;
      %utlfkil("%sysfunc(pathname(work))/__dm.txt");
      proc sql noprint;
            select
               put(count(*),comma18.)
              ,count(*)
            into
              :tob  trimmed
             ,:tobs  trimmed
      from &argx;quit;
      title "last 11 obs from %upcase(&argx) total obs=&tob";
      proc printto print="%sysfunc(pathname(work))/__dm.txt";
      %let pobs=%sysfunc(ifn(&tobs > 10, %eval(&tobs - 10),1));
      proc print data=_last_ ( firstObs= &pobs ) width=min uniform  heading=horizontal;
      format _all_;
      run;
      proc printto;
      run;quit;
      title;
      filename __dm clipbrd ;
      data _null_;file _dm; put "";run;quit;
      data _null_;
         infile "%sysfunc(pathname(work))/__dm.txt" end=dne;
         input;
         file __dm ;
         put _infile_;
         file print;
         put _infile_;
      run;quit;
      filename __dm clear;
      run;quit;
      title;
      dm "out;";
    %mend tailha;


    %macro tailfh /cmd des="lat 10 obs from highlighted dataset";
       store;note;notesubmit '%tailfha;';
    %mend tailfh;


    %macro tailfha /cmd des="last 10 obs highlight dataset and type tailfha for a list of 10 obs";
      FILENAME clp clipbrd ;
      DATA _NULL_;
        INFILE clp;
        INPUT;
        put _infile_;
        call symputx('argx',_infile_);
      RUN;
      footnote;
      options nocenter;
      %utlfkil("%sysfunc(pathname(work))/__dm.txt");
      proc sql noprint;
            select
               put(count(*),comma18.)
              ,count(*)
            into
              :tob  trimmed
             ,:tobs  trimmed
      from &argx;quit;
      title "last 11 obs from %upcase(&argx) total obs=&tob";
      proc printto print="%sysfunc(pathname(work))/__dm.txt";
      %let pobs=%sysfunc(ifn(&tobs > 10, %eval(&tobs - 10),1));
      proc print data=_last_ ( firstObs= &pobs ) width=min uniform  heading=horizontal;
      run;
      proc printto;
      run;quit;
      title;
      filename __dm clipbrd ;
      data _null_;file _dm; put "";run;quit;
      data _null_;
         infile "%sysfunc(pathname(work))/__dm.txt" end=dne;
         input;
         file __dm ;
         put _infile_;
         file print;
         put _infile_;
      run;quit;
      filename __dm clear;
      run;quit;
      title;
      dm "out;";
    %mend tailfha;

