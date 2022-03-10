# utl-computing-cohens-d-by-dropping-down-to-r-from-sas
Computing Cohens D by dropping down to R from sas   
    %let pgm=utl-computing-cohens-d-by-dropping-down-to-r-from-sas;

    Computing Cohens D by dropping down to R from sas

    r Package effectsize has many options

    github
    https://tinyurl.com/2p8rp4pw
    https://github.com/rogerjdeangelis/utl-computing-cohens-d-by-dropping-down-to-r-from-sas

    sas communities
    https://tinyurl.com/bhduxtfs
    https://communities.sas.com/t5/Statistical-Procedures/Easy-way-to-calculate-Cohen-s-d/m-p/801120

    stackoverflow
    https://tinyurl.com/bde88f88
    https://stackoverflow.com/questions/15436702/estimate-cohens-d-for-effect-size

    Patil Indrajeet
    https://stackoverflow.com/users/7973626/indrajeet-patil

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */
    options validvarname=upcase;

    libname sd1 "d:/sd1";

     data sd1.have;
       call streaminit(1234);
       do rec=1 to 10;
         x = rand ('normal',10.10,1);
         y = rand ('normal',10.5,5);
         output;
       end;
       drop rec;
    run;quit;

    /***************************************************************/
    /*                                                             */
    /* Up to 40 obs from SD1.HAVE total obs=10 09MAR2022:18:18:08  */
    /*                                                             */
    /* Obs       X          Y                                      */
    /*                                                             */
    /*   1    10.9650    14.5559                                   */
    /*   2     9.0556     5.2176                                   */
    /*   3     8.3510    13.3328                                   */
    /*   4     8.6356    11.8015                                   */
    /*   5     9.4462    12.5857                                   */
    /*   6    10.0394     1.3543                                   */
    /*   7    10.0253    12.7695                                   */
    /*   8     9.4954     9.0111                                   */
    /*   9    10.4277    13.4602                                   */
    /*  10    11.0364     7.7248                                   */
    /*                                                             */
    /***************************************************************/

    /*           _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| `_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    */

    /**************************************************************/
    /*                                                            */
    /*  Obs     LABEL          COHENSD_95PCT_CI                   */
    /*                                                            */
    /*    1    unpaired    -0.14     | [-1.02, 0.74]              */
    /*    2    paired      -0.10     | [-0.76, 0.55]              */
    /*                                                            */
    /*                                                            */
    /*                                                            */
    /**************************************************************/

    /*
     _ __  _ __ ___   ___ ___  ___ ___
    | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    */

    * as usual the hardest part is getting data out of R;
    * see the cohens_d data structure beloe;

    %utl_submit_r64('
    library(effectsize);
    library(haven);
    library(SASxport);
    have<-read_sas("d:/sd1/have.sas7bdat");
    str(cohens_d(have$X, have$Y));
    unpaired<-capture.output(cohens_d(have$X, have$Y))[3];
    paired<-capture.output(cohens_d(have$X, have$Y, paired=TRUE))[3];
    want<-as.data.frame(rbind(unpaired,paired));
    want$label<-rbind("unpaired","paired");
    str(want);
    want;
    write.xport(want,file="d:/xpt/want.xpt");
    ');

    libname xpt xport "d:/xpt/want.xpt";

    proc print data=xpt.want LABEL;
    label v1='COHENSD_95PCT_CI';
    var label V1;
    run;quit;

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */

    * this is the cohens D data structure

    Probably could pick it apart. However it may not be obvious;

    Classes 'effectsize_difference', 'effectsize_table', 'see_effectsize_table' and 'data.frame':	1 obs. of  4 variables:
     $ Cohens_d: num -0.14
     $ CI      : num 0.95
     $ CI_low  : num -1.02
     $ CI_high : num 0.74
     - attr(*, "paired")= logi FALSE
     - attr(*, "correction")= logi FALSE
     - attr(*, "pooled_sd")= logi TRUE
     - attr(*, "mu")= num 0
     - attr(*, "ci")= num 0.95
     - attr(*, "ci_method")=List of 2
      ..$ method      : chr "ncp"
      ..$ distribution: chr "t"
     - attr(*, "approximate")= logi FALSE
     - attr(*, "alternative")= chr "two.sided"
