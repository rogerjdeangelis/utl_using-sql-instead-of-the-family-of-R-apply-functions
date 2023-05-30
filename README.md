# utl_using-sql-instead-of-the-family-of-R-apply-functions
Substituting sql for the family of r apply functions 
    %let pgm=utl_using-sql-instead-of-the-family-of-R-apply-functions;

    Substituting sql for the family of r apply functions

    In SAS 'proc sql' can be faster than datstep code.
    It can often be competitive with datastep code.
    Not sure why it seems so slow in R, maybe there is an easy way to speed it up.

    github
    https://tinyurl.com/33duvkes
    https://github.com/rogerjdeangelis/utl_using-sql-instead-of-the-family-of-R-apply-functions

    Although the SQL code may be longer it is much more powerfull than the
    falmily of 'apply' function. However some odering functions
    may be difficult in SQL. SQLites does provide a roeid primary key.
    Qunatiled and ranfing functions can get complicated in SQL.


    NOTE:
      I am only going to present SQL eqivalents that output dataframes,
      The applyfamily inputs can dataframes, matricies and lists.

      Mutidimensional matrices are not covered. SQL always outputs a dataframe,
      I have restricted the lists to a collection of congruent dataframes.
      I restricted the list structure just to miminize this post.
      Vecotors are represented by signle row dataframes.

      Family of apply functions, do we need all of these?

         Big four?
           aggreagte
           mapply
           apply
           lapply

           dapply  No value added?
           tapply  Use aggregate
           sapply  Supports iteration
           eapply  ?
           vapply  ? vectors (use 1 row dataframe?
           rapply  ?

    https://stackoverflow.com/questions/3505701/grouping-functions-tapply-by-aggregate-and-the-apply-family


    /*XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX*/
    /*                                                   |                                       |                            */
    /*   _                      ____                     |   ___              _ _ _              |               _            */
    /*  | |__   __ _ ___  ___  |  _ \                    | |  _ \   ___  __ _| (_) |_ ___        |    _ __ _   _| | ___  ___  */
    /*  | `_ \ / _` / __|/ _ \ | |_) |                   | | |_) | / __|/ _` | | | __/ _ \       |   | `__| | | | |/ _ \/ __| */
    /*  | |_) | (_| \__ \  __/ |  _ <                    | |  _ <  \__ \ (_| | | | ||  __/       |   | |  | |_| | |  __/\__ \ */
    /*  |_.__/ \__,_|___/\___| |_| \_\                   | |_| \_\ |___/\__, |_|_|\__\___|       |   |_|   \__,_|_|\___||___/ */
    /*                                                   |                 |_|                   |                            */
    /*XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX*/
    /*                                          _                                                |                            */
    /*    __ _  __ _  __ _ _ __ ___  __ _  __ _| |_ ___                                          |                            */
    /*   / _` |/ _` |/ _` | `__/ _ \/ _` |/ _` | __/ _ \                                         |                            */
    /*  | (_| | (_| | (_| | | |  __/ (_| | (_| | ||  __/                                         |                            */
    /*   \__,_|\__, |\__, |_|  \___|\__, |\__,_|\__\___|                                         |                            */
    /*         |___/ |___/          |___/                                                        |                            */
    /*                                                                                           |                            */
    /*                                                   |                                       |                            */
    /*   The SQL code should work in SAS, Python, R      |                                       |                            */
    /*   .... and it outputs dataframs which are lists   |                                       |                            */
    /*                                                   |                                       |                            */
    /*                                                   |                                       |                            */
    /*   AGGEGATE                                        |  SQL (returns list(dataframe)         |      RULES                 */
    /* ===================                               | ============================          |    =======                 */
    /*                                                   |                                       |                            */
    /*  with(                                            |                                       |    Statistic               */
    /*   have                                            | SELECT                                |    Grouped                 */
    /*    ,aggregate(WEIGHT                              |    age                                |                            */
    /*    ,list(AGE, SEX)                                |   ,sex                                |                            */
    /*    ,function(x) { c(MEAN=mean(x), SD=sd(x) )}));  |   ,avg(weight)    \'avgWgt\'          |                            */
    /*                                                   |   ,stdev(weight)  \'stdWgt\'          |                            */
    /*                                                   | FROM                                  |                            */
    /*                                                   |    have                               |                            */
    /*                                                   | GROUP                                 |                            */
    /*                                                   |    by age, sex                        |                            */
    /*                                                   |                                       |                            */
    /*      Group.1 Group.2    x.MEAN      x.SD          |     AGE    SEX avgWgt    stdWgt       |                            */
    /*                                                   |                                       |                            */
    /*    1   adult  female 88.500000 16.263456          | 1 adult female   88.5 16.263456       |                            */
    /*    2   young  female 84.000000  5.656854          | 2 adult   male   67.0 32.526912       |                            */
    /*    3   adult    male 67.000000 32.526912          | 3 young female   84.0  5.656854       |                            */
    /*    4   young    male 65.000000 14.142136          | 4 young   male   65.0 14.142136       |                            */
    /*                                                   |                                       |                            */
    /*XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX*/
    /*                               _                                                                                        */
    /*   _ __ ___   __ _ _ __  _ __ | |_   _                                                     |                            */
    /*  | `_ ` _ \ / _` | `_ \| `_ \| | | | |                                                                                 */
    /*  | | | | | | (_| | |_) | |_) | | |_| |                                                                                 */
    /*  |_| |_| |_|\__,_| .__/| .__/|_|\__, |                                                                                 */
    /*                  |_|   |_|      |___/                                                                                  */
    /*                                                                                           |                            */
    /*                                                                                           |                            */
    /* COLUMN SUMS                                       |                                       |     INPUT                  */
    /* mapply(sum,have)                                  |  SELECT                               |      A B                   */
    /*                                                   |    sum(a) as a                        |      1 3                   */
    /* A B                                               |   ,sum(b) as b                        |      2 4                   */
    /* 3 7                                               |  FROM                                 |                            */
    /*                                                   |    have                               |                            */
    /*                                                   |                                       |                            */
    /*                                                   |    A B                                |     A= 1+2                 */
    /*                                                   |  1 3 7                                |     B= 3+4                 */
    /*                                                   |                                       |                            */
    /*------------------------------------------------------------------------------------------------------------------------*/
    /*                                                   |                                       |                            */
    /* SUM A*B by ROW                                    |                                       |                            */
    /* myfun <- function(a, b) {                         | SELECT                                |                            */
    /*  a*b                                              |   A*B as AxB                          |                            */
    /* };                                                | FROM                                  |                            */
    /*                                                   |   have                                |                            */
    /* [1] "mapply(myfun, A, B)"                         |                                       |                            */
    /* [1] 3 8                                           |   AxB                                 |                            */
    /*                                                   | 1   3                                 |     Row 1 1*3              */
    /*                                                   | 2   8                                 |     Row2  2*4              */
    /*------------------------------------------------------------------------------------------------------------------------*/
    /*                                                   |                                       |                            */
    /* COLUMN SUMS STACK DATASETS                        |                                       |                            */
    /* [1] "mapply(sum,have,have)"                       | SELECT                                |                            */
    /*  A  B                                             |   sum(a) as a                         |                            */
    /*  6 14                                             |  ,sum(b) as b                         |                            */
    /*                                                   | FROM (                                |                            */
    /*                                                   |  select * from have                   |                            */
    /*                                                   | UNION ALL                             +    A=1+2+1+2               */
    /*                                                   |  select * from have)                  |    B=3+4+3+4               */
    /*                                                   |                                       |                            */
    /*   A  B                                            |   A  B                                |                            */
    /* 1 6 14                                            | 1 6 14                                |                            */
    /*                                                   |                                       |                            */
    /*------------------------------------------------------------------------------------------------------------------------*/
    /*                                                   |                                       |                            */
    /* ELEMENT+ELEMENT IST ROWS                          |                                       |                            */
    /* [1] "mapply(sum,have{1,],have[1,]"                | SELECT                                |                            */
    /*   a b                                             |    l.a+r.a as a                       |                            */
    /* 1 2 6                                             |   ,l.b+r.b as b                       |                            */
    /*                                                   | FROM                                  |                            */
    /*                                                   |    have as l, have as r               |                            */
    /*                                                   | ON                                    |                            */
    /*                                                   |        l.rowid = 1                    |                            */
    /*                                                   |    and r.rowid = 1                    |                            */
    /*                                                   |                                       |                            */
    /*                                                   |   A B                                 |    A=1+1                   */
    /*                                                   | 1 2 6                                 |    B=3+3                   */
    /*                                                   |                                       |                            */
    /*                                                   |                                       |                            */
    /*------------------------------------------------------------------------------------------------------------------------*/
    /*                                                   |                                       |                            */
    /*                                                   |                                       |                            */
    /* GRAND TOTAL OF BOTH 1st ROWS                      | SELECT                                |                            */
    /* Could not figure out how to do                    |    sum(a+b) as ab                     |                            */
    /*                                                   | FROM (                                |                            */
    /*                                                   |    SELECT                             |                            */
    /*                                                   |       l.a+r.a as a                    |                            */
    /*                                                   |      ,l.b+r.b as b                    |                            */
    /*                                                   |    FROM                               |                            */
    /*                                                   |       have as l, have as r            |                            */
    /*                                                   |    ON                                 |                            */
    /*                                                   |           l.rowid = 1                 |                            */
    /*                                                   |       and r.rowid = 1 )               |                            */
    /*                                                   |                                       |    AB=1+3 + 1+3            */
    /*                                                   |     ab                                |                            */
    /*                                                   |   1  8                                |                            */
    /*                                                   |                                       |                            */
    /*------------------------------------------------------------------------------------------------------------------------*/
    /*                                                   |                                       |                            */
    /*                                                   |                                       |                            */
    /* GRAND TOTAL ALL ELEMENTS                          | SELECT                                |                            */
    /* Could not figure out how to do                    |      sum(a+b) as ab                   |                            */
    /*                                                   | FROM (                                |                            */
    /*                                                   |    SELECT                             |                            */
    /*                                                   |       l.a+r.a as a                    |                            */
    /*                                                   |      ,l.b+r.b as b                    |                            */
    /*                                                   |    FROM                               |                            */
    /*                                                   |       have as l, have as r            |                            */
    /*                                                   |    ON                                 |       6     +  14          */
    /*                                                   |       l.rowid = r.rowid )             |    A=1+2+1+2 +3+4+3+4      */
    /*                                                   |                                       |    AxB=20                  */
    /*                                                   |  GRAND_TOTAL                          |                            */
    /*                                                   |  1 20                                 |                            */
    /*                                                   |                                       |                            */
    /*XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX*/
    /*                    _                                                                                                   */
    /*   __ _ _ __  _ __ | |_   _                                                                                             */
    /*  / _` | `_ \| `_ \| | | | |                                                                                            */
    /* | (_| | |_) | |_) | | |_| |                                                                                            */
    /*  \__,_| .__/| .__/|_|\__, |                                                                                            */
    /*       |_|   |_|      |___/                                                                                             */
    /*                                                   |                                       |                            */
    /*    MAPPLY                                         | SQL (returns list(dataframe)          |     RULES                  */
    /* ===================                               | ============================          |    =======                 */
    /*                                                   |                                       |                            */
    /* COLUMN SUMS                                       |                                       |     INPUT                  */
    /* apply(have,2,sum)                                 |  SELECT                               |      A B                   */
    /*                                                   |    sum(a) as a                        |      1 3                   */
    /* A B                                               |   ,sum(b) as b                        |      2 4                   */
    /* 3 7                                               |  FROM                                 |                            */
    /*                                                   |    have                               |                            */
    /*                                                   |                                       |                            */
    /*                                                   |    A B                                |     A= 1+2                 */
    /*                                                   |  1 3 7                                |     B= 3+4                 */
    /*                                                   |                                       |                            */
    /*------------------------------------------------------------------------------------------------------------------------*/
    /*                                                   |                                       |                            */
    /*                                                   |                                       |                            */
    /* ROW SUMS                                          |                                       |                            */
    /* apply(have,1,sum)                                 |  SELECT                               |                            */
    /*                                                   |    sum(a) as a                        |                            */
    /* A B                                               |   ,sum(b) as b                        |                            */
    /* 4 6                                               |  FROM                                 |                            */
    /*                                                   |    have                               |                            */
    /*                                                   |                                       |                            */
    /*                                                   |    rowsum                             |                            */
    /*                                                   |  1      4                             |    A=1+3                   */
    /*                                                   |  2      6                             |    B=2+4                   */
    /*                                                   |                                       |                            */
    /*------------------------------------------------------------------------------------------------------------------------*/
    /*                                                   |                                       |                            */
    /*                                                   |                                       |                            */
    /* SQUARE EACH ELEMENT                               |                                       |                            */
    /* apply(have,1:2, function(x) x*x)                  | SELECT                                |     A    B                 */
    /*                                                   |   a*a as A                            |    1*1  3*3                */
    /*      A  B                                         |  ,b*b as B                            |    2*2  4*4                */
    /* [1,] 1  9                                         | FROM                                  |                            */
    /* [2,] 4 16                                         |   have                                |                            */
    /*                                                   |                                       |                            */
    /*                                                   |   A  B                                |                            */
    /*                                                   | 1 1  9                                |                            */
    /*                                                   | 2 4 16                                |                            */
    /*                                                   |                                       |                            */
    /*------------------------------------------------------------------------------------------------------------------------*/
    /*                                                   |                                       |                            */
    /* GRAND TOTAL                                       | SELECT                                |                            */
    /* Could not figure out how to do this               |   sum(rowsum) as total                |                            */
    /*                                                   | FROM (                                |                            */
    /*                                                   |   SELECT                              |                            */
    /*                                                   |      sum(a+b) as rowsum               |                            */
    /*                                                   |   FROM                                |                            */
    /*                                                   |      have )                           |                            */
    /*                                                   |                                       |    A    B                  */
    /*                                                   |     TOTAL                             |    1 +  3 +                */
    /*                                                   |   1    10                             |    2 +  4 = 10             */
    /*                                                   |                                       |                            */
    /*                                                   |                                       |                            */
    /*XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX*/
    /*  _                   _                                                                                                 */
    /* | | __ _ _ __  _ __ | |_   _                                                                                           */
    /* | |/ _` | `_ \| `_ \| | | | |                                                                                          */
    /* | | (_| | |_) | |_) | | |_| |                                                                                          */
    /* |_|\__,_| .__/| .__/|_|\__, |                                                                                          */
    /*         |_|   |_|      |___/                                                                                           */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /* Two times every element                           |                                                                    */
    /* twox <- function(x) 2*x;                          | Write R list to SQLite                |                            */
    /* lapply(lst,twox);                                 | rws_write(lst,exists= F, conn = mydb);|                            */
    /*                                                   |                                       |                            */
    /*  $have1                                           |   SELECT                              |                            */
    /*    A B                                            |      \"have1\"    as datraframe       |                            */
    /*  1 2 6                                            |      ,2*a as a                        |                            */
    /*  2 4 8                                            |      ,2*b as b                        |                            */
    /*  $have2                                           |   FROM                                |                            */
    /*    A B                                            |      have1                            |                            */
    /*  1 2 6                                            |     UNION ALL                         |                            */
    /*  2 4 8                                            |       SELECT                          |                            */
    /*                                                   |    \"have2\"    as datraframe         |                            */
    /*                                                   |    ,2*a as a                          |                            */
    /*                                                   |    ,2*b as b                          |                            */
    /*                                                   |  FROM                                 |                            */
    /*                                                   |    have2                              |                            */
    /*                                                   |                                       |    RWICE                   */
    /*                                                   |    datraframe a b                     |    A=2*1 B=2*3  Row1       */
    /*                                                   |  1      have1 2 6                     |    A=2*2 B=4*4  Row2       */
    /*                                                   |  2      have1 4 8                     |                            */
    /*                                                   |  3      have2 2 6                     |                            */
    /*                                                   |  4      have2 4 8                     |                            */
    /*                                                   |                                       |                            */
    /*------------------------------------------------------------------------------------------------------------------------*/
    /*                                                   |                                       |                            */
    /*                                                   |                                       |                            */
    /*  COLUMN SUMS                                      |SELECT                                 |                            */
    /*  Could could not do this                          |   \"have1\"    as datraframe          |                            */
    /*                                                   |   ,sum(a) as a                        |                            */
    /*                                                   |   ,sum(b) as b                        |                            */
    /*                                                   |FROM                                   |                            */
    /*                                                   |   have1                               |                            */
    /*                                                   |UNION ALL                              |                            */
    /*                                                   |SELECT                                 |                            */
    /*                                                   |   \"have2\"    as datraframe          |                            */
    /*                                                   |   ,sum(a) as a                        |                            */
    /*                                                   |   ,sum(b) as b                        |                            */
    /*                                                   |FROM                                   |                            */
    /*                                                   |   have2                               |                            */
    /*                                                   |                                       |    TWICE                   */
    /*                                                   |  datraframe a b                       |    A=1+2 B=3+4             */
    /*                                                   |1      have1 3 7                       |                            */
    /*                                                   |2      have2 3 7                       |                            */
    /*                                                   |                                       |                            */
    /*------------------------------------------------------------------------------------------------------------------------*/
    /*                                                   |                                       |                            */
    /*                                                   |                                       |                            */
    /*  Grand total both dataframes                      | Write R list to SQLite                |                            */
    /*  lapply(lst,sum);                                 | rws_write(lst,exists=F,conn = mydb);  |                            */
    /*                                                   |                                       |                            */
    /*  $have1                                           | SELECT                                |                            */
    /*  [1] 10                                           |  \"have1\"    as dataframe            |                            */
    /*  $have2                                           |  ,sum(rowsum) as total                |                            */
    /*  [1] 10                                           | FROM (                                |                            */
    /*                                                   | SELECT                                |                            */
    /*                                                   |   sum(a+b) as rowsum                  |                            */
    /*                                                   | FROM                                  |                            */
    /*                                                   |   have1 )                             |                            */
    /*                                                   | UNION ALL                             |                            */
    /*                                                   | SELECT                                |                            */
    /*                                                   |  \"have2\"    as datraframe           |                            */
    /*                                                   |  ,sum(rowsum) as total                |                            */
    /*                                                   | FROM (                                |                            */
    /*                                                   |   SELECT                              |                            */
    /*                                                   |       sum(a+b) as rowsum              |                            */
    /*                                                   |   FROM                                |                            */
    /*                                                   |      have2 )                          |                            */
    /*                                                   |                                       |    TWICE                   */
    /*                                                   |    dataframe total                    |    A    B                  */
    /*                                                   |  1     have1    10                    |    1 +  3 +                */
    /*                                                   |  2     have2    10                    |    2 +  4 = 10             */
    /*                                                   |  2     have2    10                    |                            */
    /*                                                   |                                       |                            */
    /*XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX*/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */
    data have;
     input a b;
    cards4;
     1 3
     2 4
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  R DATA STRUCTURES                             SAS DATASET                                                             */
    /*  =================                             ===========                                                             */
    /*                                                                                                                        */
    /*  DATAFRAME                                                                                                             */
    /*                                                Up to 40 obs from HAVE total obs=2                                      */
    /*  data.frame 2 obs. of  2 variable                                                                                      */
    /*                                                Obs    COL1    COL2                                                     */
    /*  $ COL1: num  1 2                                                                                                      */
    /*  $ COL2: num  3 4                               1       1       3                                                      */
    /*                                                 2       2       4                                                      */
    /*  Create match dataframe for apply functions                                                                            */
    /*                                                                                                                        */
    /*  MATRIX                                                                                                                */
    /*                                                                                                                        */
    /*  mtx <- as.matrix(have);                                                                                               */
    /*                                                                                                                        */
    /*  num [1:2, 1:2] 1 2 3 4                                                                                                */
    /*                                                                                                                        */
    /*  - attr(*, "dimnames")=List of 2                                                                                       */
    /*   ..$ : NULL                                                                                                           */
    /*   ..$ : chr [1:2] "COL1" "COL2"                                                                                        */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*  LIST                                                                                                                  */
    /*                                                                                                                        */
    /*  List of 2                                                                                                             */
    /*   $: dataframe                                                                                                         */
    /*    ..$ COL1: num [1:2] 1 2                                                                                             */
    /*    ..$ COL2: num [1:2] 3 4                                                                                             */
    /*   $: dataframe                                                                                                         */
    /*    ..$ COL1: num [1:2] 1 2                                                                                             */
    /*    ..$ COL2: num [1:2] 3 4                                                                                             */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                           _
     _ __ ___   __ _ _ __  _ __ | |_   _
    | `_ ` _ \ / _` | `_ \| `_ \| | | | |
    | | | | | | (_| | |_) | |_) | | |_| |
    |_| |_| |_|\__,_| .__/| .__/|_|\__, |
                    |_|   |_|      |___/
    */


    %let _pth=%sysfunc(pathname(work));

    libname wrk "&_pth";

    %utl_submit_wps64('
     libname wrk "&_pth";
     proc r;
     export data=wrk.have r=have;
     submit;
     library(RSQLite);
     library(DBI);
     library(sqldf);
     mydb <- dbConnect(RSQLite::SQLite(), "file::memory:");
     dbWriteTable(mydb, "have", have);

     print("1. mapply(sum,have)");
     mapply(sum,have);
     dbGetQuery(mydb,"
       SELECT
         sum(a) as a
        ,sum(b) as b
       FROM
         have
       ");

     myfunction <- function(a, b) {
      a*b
     };
     attach(have);
     print("2. mapply(myfunction, A, B)");
     mapply(myfunction, A, B);
     dbGetQuery(mydb,"
       SELECT
         A*B as AxB
       FROM
         have
     ");
    print("3. mapply(sum,have,have)");
     mapply(sum,have,have);
     dbGetQuery(mydb,"
       SELECT
         sum(a) as a
        ,sum(b) as b
       FROM (
        select * from have
       UNION ALL
        select * from have)
       ");
    print("4. mapply(sum,have{1,],have[1,]");
     mapply(sum,have[1,],have[1,]);
     dbGetQuery(mydb,"
       SELECT
          l.a+r.a as a
         ,l.b+r.b as b
       FROM
          have as l, have as r
       ON
              l.rowid = 1
          and r.rowid = 1
       ");
     print("5. Could not figure out how to do");
     dbGetQuery(mydb,"
          SELECT
             l.a+r.a+l.b+r.b  as ab
          FROM
             have as l, have as r
          ON
                 l.rowid = 1
             and r.rowid = 1
       ");
     print("6. Could not figure out how to do");
     dbGetQuery(mydb,"
       SELECT
          sum(a+b) as ab
       FROM (
          SELECT
             l.a+r.a as a
            ,l.b+r.b as b
          FROM
             have as l, have as r
          ON
             l.rowid = r.rowid )
       ");
     endsubmit;
    ');


    /*                 _
      __ _ _ __  _ __ | |_   _
     / _` | `_ \| `_ \| | | | |
    | (_| | |_) | |_) | | |_| |
     \__,_| .__/| .__/|_|\__, |
          |_|   |_|      |___/
    */

    %let _pth=%sysfunc(pathname(work));

    libname wrk "&_pth";

    %utl_submit_wps64('
     libname wrk "&_pth";
     proc r;
     export data=wrk.have r=have;
     submit;
     library(RSQLite);
     library(DBI);
     library(sqldf);

     mydb <- dbConnect(RSQLite::SQLite(), "file::memory:");
     dbWriteTable(mydb, "have", have);

     print("1, apply(have,1:2, function(x) x*x)");
     apply(have,1:2,function(x) x*x);
     dbGetQuery(mydb,"select a*a as a, b*b as b from have");

     print("apply(have,1,sum)");
     apply(have,1,sum);
     dbGetQuery(mydb,"select (a+b) as rowsum from have");

     print("2. apply(have,2,sum)");
     apply(have,2,sum);
     dbGetQuery(mydb,"select sum(a) as a, sum(b) as b from have");

     print("3. Do not know how to do this with apply");
     dbGetQuery(mydb,"select sum(rowsum) as total from (select sum(a+b) as rowsum from have)");

    endsubmit;
    ');

    /*                   _
    | | __ _ _ __  _ __ | |_   _
    | |/ _` | `_ \| `_ \| | | | |
    | | (_| | |_) | |_) | | |_| |
    |_|\__,_| .__/| .__/|_|\__, |
            |_|   |_|      |___/
    */

    %let _pth=%sysfunc(pathname(work));

    libname wrk "&_pth";

    options noxsync noxwait;
    %utl_submit_wps64('
     libname wrk "&_pth";
     proc r;
     export data=wrk.have r=have;
     submit;
     library(RSQLite);
     library(DBI);
     library(sqldf);
     library(readwritesqlite);
     mydb <- dbConnect(RSQLite::SQLite(), "file::memory:");

     lst <- list(have,have);
     names(lst) <- c("have1", "have2");

     rws_write(lst, exists = FALSE, conn = mydb);
     dbGetQuery(mydb,"SELECT name FROM sqlite_master");

     twox <- function(x) 2*x;
     lapply(lst,twox);

     dbGetQuery(mydb,"
            SELECT
               \"have1\"    as datraframe
               ,2*a as a
               ,2*b as b
            FROM
               have1
          UNION ALL
            SELECT
               \"have2\"    as datraframe
               ,2*a as a
               ,2*b as b
            FROM
               have2
          ");
     dbGetQuery(mydb,"
          SELECT
           \"have1\"    as dataframe
           ,sum(rowsum) as total
          FROM (
            SELECT
                sum(a+b) as rowsum
            FROM
               have1 )
          UNION ALL
          SELECT
           \"have2\"    as datraframe
           ,sum(rowsum) as total
          FROM (
            SELECT
                sum(a+b) as rowsum
            FROM
               have2 )
          ");

     dbGetQuery(mydb,"
            SELECT
               \"have1\"    as datraframe
               ,sum(a) as a
               ,sum(b) as b
            FROM
               have1
            UNION ALL
            SELECT
               \"have2\"    as datraframe
               ,sum(a) as a
               ,sum(b) as b
            FROM
               have2
          ");
    endsubmit;
    ');


    /*                                      _
      __ _  __ _  __ _ _ __ ___  __ _  __ _| |_ ___
     / _` |/ _` |/ _` | `__/ _ \/ _` |/ _` | __/ _ \
    | (_| | (_| | (_| | | |  __/ (_| | (_| | ||  __/
     \__,_|\__, |\__, |_|  \___|\__, |\__,_|\__\___|
           |___/ |___/          |___/
    */


    options validvarname=upcase;
    data have;
    informat age sex $6.;
    input animal    age     sex  weight;
    cards;
    1 adult female 100
    2 adult female 77
    3 adult male 90
    4 adult male 44
    5 young female 80
    6 young female 88
    7 young male 75
    8 young male 55
    ;;;;
    run;quit;

    %let _pth=%sysfunc(pathname(work));

    options noxsync noxwait;
    %utl_submit_wps64("
     libname wrk '&_pth';
     proc r;
     export data=wrk.have r=have;
     submit;
     library(sqldf);
     with(
        have
         ,aggregate(WEIGHT
         ,list(AGE, SEX)
         ,function(x) { c(MEAN=mean(x), SD=sd(x) )}));
     sqldf('
        SELECT
           age
          ,sex
          ,avg(weight)    \'avgWgt\'
          ,stdev(weight)  \'stdWgt\'
        FROM
           have
        GROUP
           by age, sex');
     endsubmit;
     import data=wrk.have r=have;
    ");


    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
