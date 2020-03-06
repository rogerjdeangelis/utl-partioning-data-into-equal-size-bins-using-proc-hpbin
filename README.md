# utl-partioning-data-into-equal-size-bins-using-proc-hpbin
Partioning data into 5 equal size bins
    Partioning data into 5 equal size bins

    Bin realizations from the Beta(1.5,1.5) into 5 equal size bins.

    github
    https://tinyurl.com/upcx6ot
    https://github.com/rogerjdeangelis/utl-partioning-data-into-equal-size-bins-using-proc-hpbin

    SAS Forum
    https://communities.sas.com/t5/SAS-Procedures/creating-equal-size-bins/m-p/627647

    Rick_SAS Profile
    https://communities.sas.com/t5/user/viewprofilepage/user-id/13684

    HPBIN is a more robust and efficient method for bining variables.
    Yeilds better breakpoints?

    *_                   _
    (_)_ __  _ __  _   _| |_
    | | '_ \| '_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    ;
    data have;
    call streaminit(1);
    do idnum = 1 to 1000;
      x = rand("Beta",1.5, 1.5);
      output;
    end;
    run;quit;
    WORK.HAVE total obs=1,000

    Obs    IDNUM       X

      1       1     0.50619
      2       2     0.65721
      3       3     0.89060
      4       4     0.73747
      5       5     0.55468
      6       6     0.77856
      7       7     0.74693
      8       8     0.84709
      9       9     0.31715
     10      10     0.59368
     ...

    *            _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| '_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    ;


    WORK.WANT total obs=1,000

     Obs    IDNUM    BIN_X

       1       1       3    ==> we asked for 5 bins so bin_x is the bin number
       2       2       4
       3       3       5
       4       4       4
       5       5       3
       6       6       5
       7       7       4
       8       8       5
       9       9       2
      10      10       4

    he HPBIN Procedure

                                      Mapping

             Binned
    ariable  Variable  Range                                Frequency  Proportion

             BIN_X     X < 0.261698812                            200  0.20000000
                       0.261698812 <= X < 0.4309590723            200  0.20000000
                       0.4309590723 <= X < 0.5843573408           200  0.20000000
                       0.5843573408 <= X < 0.752041254            200  0.20000000
                       0.752041254 <= X                           200  0.20000000

    data havEqu;
     do x=0 to 1 by .01;
        val=pdf('BETA', x, 1.5, 1.5);
        output;
     end;
    run;quit;

    options ps=32 ls=64;
    proc plot data=havEqu;
    plot val*x / href= 0 0.261698812 0.4309590723 0.5843573408 0.752041254 1 box;
    run;quit;

                            PDF Beta (1.5,1.5)

          0.00         0.26     0.43    0.58    0.75        1.00
         ---+---------+--^------+-^------^+-------^-+---------+---
     VAL |  |            |        |      |        |           |  | VAL
     1.5 +  |            |        |      |        |           |  + 1.5
         |  |            | PDF Beta (1.5,1.5)     |           |  |
         |  |            |        |      |        |           |  |
         |  |            |       BBBBBBBBBA       |           |  |
         |  |            |  ABBBB |      |ABBBB   |           |  |
         |  |            ABBA     |      |     BBB|           |  |
         |  |          BBA        |      |        BBA         |  |
     1.0 +  |        AB  |        |      |        | AB        |  + 1.0
         |  |       BA   |        |      |        |   BA      |  |
         |  |     AB     |        |      |        |    AB     |  |
         |  |    AA      |        |      |        |      B    |  |
         |  |   AA       |        |      |        |       B   |  |
         |  |   A        |        |      |        |        A  |  |
         |  |  B         |        |      |        |        AA |  |
     0.5 +  | A          |        |      |        |         A |  + 0.5
         |  | A          |        |      |        |          A|  |
         |  |A           |        |      |        |          A|  |
         |  |A count     |count   |count | count  | count     A  |
         |  |   200      | 200    | 200  |  200   |  200      |  |
         |  |            |        |      |        |           |  |
         |  |            |        |      |        |           |  |
     0.0 + 0.00         0.26    0.43    0.58    0.75        1.00 + 0.0
         ---+---------+---------+---------+---------+---------+---
           0.0       0.2       0.4       0.6       0.8       1.0

                                     X

    *
     _ __  _ __ ___   ___ ___  ___ ___
    | '_ \| '__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    ;


    options ls=80 ps=65;
    proc hpbin data=have output=want numbin=5 PSEUDO_QUANTILE;
       input x;
       ID idnum;
       ods select Mapping;
    run;


