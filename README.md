# utl_forecast_next_thee_months_sales
Forecast next thee months sales. Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.
    Forecast next thee months sales

    see
    https://github.com/rogerjdeangelis/utl_forecast_next_thee_months_sales

    Not exactly the answer, but this might get the op started.
    The op could just sum the 6 forecasted values?

    WPS/PROC/R or IML/R Solution

    I am a little over my head here, it has be a long time since I used time series.
    Data looks stationary(no seasonality, trend or drift), but lets let R pick the differencing;

    I assume the data series is monthly with missing months?

    see
    https://goo.gl/GW3mqq
    https://communities.sas.com/t5/Base-SAS-Programming/Compute-cumulative-return-over-future-6-months/m-p/437380

    INPUT
    =====

     I like to use the numeric vale of a SAS/WPS data;

     Datre appears to be last non holiday workday;

     SD1.HAVE total obs=168

       MONTH     DATE    SALES

          1     13937      187
          2     13969        .
          3     13999       51
          4     14028        .
          5     14060      -78
          6     14091        .
          7     14122        .
          8     14152     -206
          9     14182       96
         10     14213      192
         .....................
         165    18931       75
         166    18961        .
         167    18991      477
         168    19023        5


    SALES |
      750 +
          |
          |
          |        *
      500 +
          |
          |          *    *      *
          |        *   **                              *       *   *
      250 +      *       *  *   *          *   *        *
          |*  * *             * *    *         *  *               *
          |   *  * *  *   *   **    * *  **   *  *** *  *  **
          | *  *     * **  * *   **    * *  *   ** **    * * *    *
        0 +         ** * * ** *     *       * ** *       * *  *  **
          |  *  * * * *      * *  *  * **  *  * *    **     *  * *
          |       *   * *   **    ** *    *  *      * **  *  *  *
          |   * *        *         *              **  *
     -250 +               *                            * *
          |      *
          |
          |                         *
     -500 +
          -+-------+-------+-------+-------+-------+-------+-------+
           0      24      48      72      96      120     144    168

                                     MONTHS


    PROCESS (working code)
    =======================

      * linearly interpret
      filmis<-na.approx(have$SALES, 1:nrow(have));

      * convert to ts data structure;
      data = ts(filmis);

      * let R auto select model order;
      model = auto.arima(data);

      * forecast six months ahead;
      want<-forecast(model,h=6);

    OUTPUT
    ======

      d:/txt/utl_forecast_next_thee_months_sales.txt

       Series: data
       ARIMA(0,0,0) with non-zero mean

       Coefficients:
                mean
             28.3929
       s.e.  11.8854

       sigma^2 estimated as 23874:  log likelihood=-1084.65
       AIC=2173.29   AICc=2173.37   BIC=2179.54

       Training set error measures:
                              ME     RMSE      MAE      MPE     MAPE      MASE       ACF1
       Training set 3.248488e-14 154.0525 118.8542 73.03261 168.7907 0.7621197 0.08506171

       Since the time series is stationary we get constant result with 95% CI.
       Note R can handle a rich array of models.

         POINT_
        FORECAST      LO_80      HI_80       LO_95      HI_95

         28.3929    -169.624    226.409    -274.447    331.233
         28.3929    -169.624    226.409    -274.447    331.233
         28.3929    -169.624    226.409    -274.447    331.233
         28.3929    -169.624    226.409    -274.447    331.233
         28.3929    -169.624    226.409    -274.447    331.233
         28.3929    -169.624    226.409    -274.447    331.233


               Plot starts with the 11th year with 6 month predition
     SALES |
       600 +
           |
           |                                              *         FORECAST
           |                                                        ========
       400 +
           |                                                ------ Upper 95% bound
           |          *    *                    *
           |
       200 +
           |*
           |  *  *       **         **                  *
           |                *     *      *                  ++++++ Mean Constant due to
         0 +                  *    *        * *     * **   *       Stationary series
           |    *   *                 *        * *   *
           | *                 *
           |       *   *        *         *        *
      -200 +         *
           |            *    *                              ------ Lower 95% bound
           |
           |
      -400 +
           -+----------+----------+----------+----------+----------+-
           121        132        143        154        165        176


    *                _               _       _
     _ __ ___   __ _| | _____     __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \   / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/  | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|   \__,_|\__,_|\__\__,_|

    ;

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
    retain date month 0;
    input month date sales @@;
    cards4;
    1 13937 187 2 13969 . 3 13999 51 4 14028 . 5 14060 -78 6 14091 . 7 14122 . 8 14152 -206
    9 14182 96 10 14213 192 11 14244 . 12 14273 58 13 14301 . 14 14334 -70 15 14364 -182 16 14392 171
    17 14425 258 18 14455 -285 19 14487 150 20 14517 -130 21 14546 -100 22 14578 -83 23 14609 333 24 14640 113
    25 14669 561 26 14700 -6 27 14728 . 28 14761 -60 29 14791 63 30 14822 400 31 14853 14 32 14882 126
    33 14914 -50 34 14944 -144 35 14973 46 36 15006 338 37 15034 -10 38 15064 -122 39 15095 32 40 15126 333
    41 15155 -29 42 15187 272 43 15218 -214 44 15246 -246 45 15279 96 46 15309 405 47 15340 23 48 15371 52
    49 15399 -19 50 15427 22 51 15460 -146 52 15491 239 53 15519 -143 54 15552 -52 55 15582 42 56 15613 -13
    57 15644 95 58 15673 186 59 15705 -52 60 15736 -43 61 15764 133 62 15795 173 63 15825 . 64 15855 221
    65 15886 . 66 15917 369 67 15946 60 68 15978 -136 69 16009 -33 70 16037 87 71 16070 -212 72 16100 -200
    73 16128 -95 74 16161 -25 75 16191 120 76 16219 -437 77 16252 194 78 16282 -145 79 16314 -84 80 16344 136
    81 16373 105 82 16405 121 83 16436 -87 84 16467 -52 85 16495 50 86 16526 -72 87 16555 . 88 16587 .
    89 16617 43 90 16646 115 91 16679 44 92 16709 155 93 16740 . 94 16770 -96 95 16800 232 96 16832 .
    97 16860 -65 98 16891 84 99 16919 -27 100 16952 -21 101 16982 . 102 17013 -98 103 17044 -94 104 17073 -93
    105 17105 15 106 17135 117 107 17164 206 108 17197 241 109 17225 8 110 17255 85 111 17286 -51 112 17317 88
    113 17346 31 114 17378 90 115 17409 141 116 17437 202 117 17470 107 118 17500 -212 119 17531 39 120 17562 -196
    121 17591 126 122 17622 -113 123 17652 75 124 17682 . 125 17713 -38 126 17744 96 127 17773 . 128 17805 -142
    129 17836 -70 130 17864 -181 131 17897 309 132 17927 -130 133 17955 -233 134 17987 108 135 18017 96 136 18046 279
    137 18078 69 138 18109 -256 139 18140 18 140 18170 -122 141 18200 -126 142 18231 . 143 18262 61 144 18291 -13
    145 18319 99 146 18352 109 147 18382 -47 148 18410 . 149 18443 . 150 18473 65 151 18505 -140 152 18535 .
    153 18564 3 154 18596 . 155 18627 -2 156 18658 -69 157 18686 317 158 18717 -29 159 18746 . 160 18778 -139
    161 18808 -7 162 18837 -43 163 18870 22 164 18900 -20 165 18931 75 166 18961 . 167 18991 477 168 19023 5
    ;;;;
    run;quit;

    options ls=64 ps=30;
    proc plot data=sd1.have;
    plot sales*month='*';
    run;quit;

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;

    %utl_submit_wps64('
    libname sd1 "d:/sd1";
    options set=R_HOME "C:/Program Files/R/R-3.3.1";
    libname wrk "%sysfunc(pathname(work))";
    proc r;
    submit;
    source("C:/Program Files/R/R-3.3.1/etc/Rprofile.site", echo=T);
    library(haven);
    library(forecast);
    library(zoo);
    have<-read_sas("d:/sd1/have.sas7bdat")[,-1];
    head(have);
    filmis<-na.approx(have$SALES, 1:nrow(have));
    data = ts(filmis);
    model = auto.arima(data);
    want<-forecast(model,h=6);
    capture.output(summary(model), file ="d:/txt/utl_forecast_next_thee_months_sales.txt");
    head(data);
    endsubmit;
    import r=want data=wrk.wantwps;
    run;quit;
    ');

    data want(keep=typ mth sales mrk);
      retain mth 168;
      set wantwps(in=w) sd1.have;
      if w then do;
         mth=mth+1;
         typ="ESTIMATED";
         sales=point_forecast;
         mrk='+';
         output;
         mrk='-';
         typ="LO_95";
         sales=lo_95;
         output;
         typ="HI_95";
         sales=hi_95;
         output;
      end;
      else do;
       mth=month;
       mrk='*';
       typ="INPUT";
       output;
     end;
    run;quit;


    options ls=64 ps=30;
    proc plot data=want(where=(mth>120));
    plot sales*mth=mrk;
    run;quit;


