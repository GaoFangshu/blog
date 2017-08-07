---
title: Learning SAS by Examples
date: 2016-12-18 16:59:32
tags: SAS
---

When learning SAS programming, only reading basic SAS introductions can be a comfortable way but not efficient one. Struggling with these questions given by my professor takes much time, but it's worth it!

---

*Every question begins with the sentence:
Using table SASHELP.CLASS write a code that ...*

<!--more-->

## Data processing in SAS:
1.	creates a library engine v9 pointing to directory c:\ sas \
```
libname libdata v9 'c:\sas\';
```
 `libdata` is name of library.
2.	creates a library engine v8 pointing to directory c:\ sas \
```
libname libdata v8 'c:\sas\';
```
3.	creates a library engine odbc indicating the ODBC driver called CRM
```
libname odbc odbc datasrc='CRM';
libname libdata odbc dsn='CRM'; /*Solution*/
```
4.	reads it using a SET instruction in a single datastep pass
```
data result;
    set sashelp.class;
run;
```
 Another solution:
 ```
data result;
    do i=1 to nobs;
        set sashelp.class nobs=nobs;
        output;
    end;
run;
```
5.	reads it and creates table RESULT with a new variable called TWO, which contains the first two letters of the name
```
data result;
    set sashelp.class;
    TWO=substr(name,1,2);
run;
```
6.	selects the rows with age greater than 12 years
```
data result;
    set sashelp.class;
    if (age>12) then output;
run;
proc print;
run;
```
 Another solution:
 ```
data result;
    set sashelp.class;
    where age>12;
run;
```
 `where` is faster than `if`.
7.	selects the rows with names starting with the letter A
```
data result;
    set sashelp.class;
    if (substr(name,1,1)='A') then output;
run;
proc print;
run;
```
 Another solution:
 ```
data result;
    set sashelp.class;
    where upcase(name) like 'A%';
run;
```
 `where` is faster than `if`.
8.	creates a new column DATE_OF_BIRTH on the basis of age and today's date in the system
```
data result;
    set sashelp.class;
    today=date();
    format today yymmdd10.;
    DATE_OF_BIRTH = intnx('Year',today,-age,'s');
    format DATE_OF_BIRTH yymmdd10.;
    drop today;
run;
proc print;
run;
```
 Another solution:
 ```
data result;
    set sashelp.class;
    DATE_OF_BIRTH1=today()-age*365; /*Not accurate*/
    DATE_OF_BIRTH2=mdy(month(today()),day(today()),year(today()-age));
    format DATE_OF_BIRTH: yymmdd10.;
run;
```
9.	creates a new column and counts number of letters in the name
```
data result;
    set sashelp.class;
    num_name = length(name);
run;
proc print;
run;
```
10.	creates a new column: binary flag with 1 when the name contains letter a
```
data result;
    set sashelp.class;
    if (indexc(name,'a','A')>0) then name_flag = 1;
        else name_flag = 0;
run;
proc print;
run;
```
11.	creates a new column concatenating: name, age and gender, for example. Alfred-14-M
```
data result;
    set sashelp.class;
    n_a_g=catx("-",name,age,sex);
run;
proc print;
run;
```
 Another solution:
 ```
data result;
    length longtext1 longtext2 $20;
    set sashelp.class;
    longtext1=trim(name)||'-'||trim(put(age,best12.-L))||'-'||trim(sex);
    longtext2=catx("-",name,put(age,best12.-L),sex);
run;
```
12.	randomly multiplies each observation from 1 to 10 times
```
data result;
    set sashelp.class;
    do i=1 to int(ranuni(0)*10+1);
        output;
    end;
run;
proc print;
run;
```
 Another solution:
 ```
libname v9 'c:\sas\';
data v9.RESULT;
    set sashelp.class;
    do i=1 to 10*ranuni(1)+0.5;
        output;
    end;
run;
proc print;
run;
```
13.	creates a RESULT table containing every second observation
```
data result;
    set sashelp.class;
    if mod(_n_,2)=0 then output;
run;
proc print;
run;
```
14.	sorts it by sex increasingly and age decreasingly
```
data result;
    set sashelp.class;
run;
proc sort;
    by sex descending age;
run;
proc print;
run;
```
 Another solution:
 ```
proc sort data=sashelp.class out=result;
    by sex descending age;
run;
```
15.	based on the processing in groups mechanism calculates the number of women and men
```
/*Solution 1*/
data a;
    set sashelp.class;
run;
proc sort data=a out=b;
    by sex;
run;
data result(keep = sex count);
    set b;
    by sex;
    if first.sex then count=0;
    count+1;
    if last.sex then output;
run;
proc print data=result;
run;
```
 ```
/*Solution 2*/
data result;
    set sashelp.class;
proc freq;
    tables sex;
run;
```
16.	based on the processing in groups mechanism calculates minimum and maximum age for each gender
```
data result;
    set sashelp.class;
run;
proc sort data=a out=b;
    by sex descending age;
run;
data result (keep = sex age);
    set b;
    by sex;
    if first.sex then output;
    if last.sex then output;
run;
proc print data=result;
run;
```
17.	based on the processing in groups mechanism calculates average for each gender
```
data a;
    set sashelp.class;
run;
proc sort data=a out=b;
    by sex;
run;
proc means data=b;
    var age;
    by sex;
run;
```
18.	using table SASHELP.AIR calculates the cumulative number of thousands of passengers, date by date - ascending, based on column AIR
```
data a;
    set sashelp.air;
run;
proc sort;
    by DATE;
run;
data result;
    set a;
    if first.date then cum_air=0;
    cum_air+AIR;
run;
proc print data=result;
run;
```
19.	using LAG function crates table RESULT containing only these rows for which the previous line was a woman
```
data result;
    set sashelp.class;
    lag=lag1(sex);
    if lag='F' then output;
run;
proc print data=result;
    title 'RESULT';
run;
```
20.	creates a new column as the sum of the ages of the previous two lines
```
data result;
    set sashelp.class;
    lag_1=lag1(age);
    lag_2=lag2(age);
    pre_2_age=lag_1+lag_2; /*sum of the ages of the previous two lines*/
run;
proc print data=result;
run;
```
 Another solution:
 ```
data result;
    set sashelp.class;
    last2age=sum(age,lag(age));
run;
```
21.	creates table RESULT containing only observations with numbers from 5 to 7
```
data result;
    set sashelp.class(firstobs=5 obs=7);
run;
proc print data=result;
    title 'RESULT';
run;
```
22.	creates table RESULT containing only the first observation
```
data result;
    set sashelp.class;
    if _N_=1 then output;
run;
proc print data=result;
    title 'RESULT';
run;
```
23.	creates table RESULT containing only the last observation
```
/*Solution 1*/
data result;
    set sashelp.class end=end;
    if end then output;
run;
proc print data=result;
    title 'RESULT';
run;
```
 ```
/*Solution 2*/
data result;
    set sashelp.class nobs=nobs;
    if _N_=nobs then output;
run;
proc print data=result;
    title 'RESULT';
run;
```
24.	creates new variables age1 to age20 and fills them the following way: eg. for age equal to 10 column age10 has a value of 1 and the other ones have 0

 *Refer to [Is there a quick way to create dummy variables?](http://www.ats.ucla.edu/stat/sas/faq/dumvars.htm)*
```
/*Solution 1*/
data result;
    set sashelp.class;
    if age=1 then age1=1;
        else age1=0;
    /* . . . */
    /*use lots of 'if then else' statements*/
    /* . . . */
    if age=20 then age20=1;
        else age20=0;
run;
proc print data=result;
run;
```
 ```
/*Solution 2*/
data result;
    set sashelp.class;
    array  dummy{*} 3. age1 - age20;
        do i=1 to 20;
            dummy{i}=0;
        end;
    dummy{age}=1;
run;
proc print data=result;
run;
```
25.	using SASHELP.ZHC select only those observations whose PCMS code is in table SASHELP.ZTC

 > The data sets that are listed in the MERGE statement must be sorted in order of the values of the variables that are listed in the BY statement, or they must have an appropriate index.

 *[Match merging data files in SAS](http://www.ats.ucla.edu/stat/sas/modules/merge.htm)*

 > When you merge files that have the same variable, SAS will use the values from the file that appears last on the merge statement.

 ```
proc sort data=sashelp.zhc out=zhc;
    by pcms;
run;
proc sort data=sashelp.ztc out=ztc;
    by pcms;
run;
data result;
    merge ztc(in=a) zhc(in=b);
    by pcms;
    if a&b then do;
    /*WARNING: Apparent symbolic reference B not resolved.*/
        keep unicode ibm pcms;
        output result;
    end;
run;
proc print data=result;
run;
```
 Another solution:
 ```
proc sql;
    create table result as
        select * from sashelp.zhc where pcms
        in (select distinct pcms from sashelp.ztc);
quit;
```
 ```
proc sql noprint;
    select distinct quote(pcms)
        into :allvalues separated by ',' from sashelp.ztc;
quit;
%put &allvalues;
data result;
    set sashelp.zhc;
    where pcms in (&allvalues);
run;
```
 ```
proc sql noprint;
    select distinct quote(pcms)
        into :value1-:value99999 from sashelp.ztc;
quit;
%let nvalues=&sqlobs;
%put &nvalues***&value1***&&value&nvalues;
%macro dodo;
    data result3;
        set sashelp.zhc;
        where pcms in (
            %do i=1 %to &nvalues;
                &&value&i,
            %end;
        );
    run;
%mend;
%dodo;
```
26.	adds column EUC to table SASHELP.ZHC from table SASHELP.ZTC using PCMS as joining key

 **SASHELP.ZHC merging SASHELP.ZTC is a many-to-many merge?!**

 ```
proc sort data=sashelp.zhc out=zhc;
    by pcms;
run;
data ztc0;
    set sashelp.ztc;
    keep pcms euc;
run;
proc sort data=ztc0 nodup out=ztc;
    by pcms;
run;
data result;
    merge zhc(in=a) ztc;
    by pcms;
    if a then output result;
run;
proc print data=result;
run;
```
 Another solution:
 ```
proc sql;
    create table result as
        select z1.*,z2.euc from sashelp.zhc as z1 left join sashelp.ztc as z2
        on z1.pcms=z2.pcms;
quit;
```
27.	tests joining tables SASHELP.ZHC and SASHELP.ZTC by PCMS, how many observations joined, how many are not joined in first and second tables

 *Refer to [Using DATA Step MERGE and PROC SQL JOIN to Combine SAS Datasets](http://www.lexjansen.com/nesug/nesug08/ff/ff03.pdf)*

 Solution:
 ```
proc sort data=sashelp.zhc out=zhc;
    by pcms;
proc sort data=sashelp.ztc out=ztc;
    by pcms;
run;
data joined left right;
    merge zhc(in=z1) ztc(in=z2);
    by pcms;
    if z1 and z2 then output joined;
    if z1 and not z2 then output left;
    if not z1 and z2 then output right;
run;
```
28.	creates table RESULT containing columns from age11 to age16 and name. For example, if a student is 11 years old the variable age11 contains the student’s gender and the remaining variables are empty
```
data result(keep=name age11-age16);
    set sashelp.class;
    array dummy{*} $ age11 - age16;
    do i=1 to 6;
        dummy{i}=' ';
    end;
    dummy{age-10}=sex;
run;
proc print data=v9.RESULT;
    title 'RESULT';
run;
```
 Another solution:
 ```
data result;
    length age11-age16 $1;
    array ages(11:16)$ age11-age16;
    set sashelp.class;
    ages(age)=sex;
run;
```
 `$`organize the array of text variables.
29.	using table SASHELP.CARS creates table RESULT containing information about car models in multiple lines. For a given model, in rows, there are two additional columns named PROPERTY and VALUE. In the first column there is the name of the column from original table and its value in the second one.

 Solution:
 ```
proc sort data=sashelp.cars out=cars;
    by Model;
run;
proc transpose data=cars
    out=result
        (rename=(_name_=property coll=value) drop=col2 _label_);
    by model;
    var type--Length;
run;
```
30.	counts the Levenshtein distance between the name and the word Alfred
```
data result;
    set sashelp.class;
    Levenshtein=complev(name,'Alfred');
run;
```
31.	creates 19 tables from name1 to name19, each with one observation, the first with the first observation of the input table, and the last with the last

 *Refer to [SAS · Macro Programming for Beginners](http://www2.sas.com/proceedings/sugi29/243-29.pdf)*

 Solution:
```
%macro createtables;
%do i=1 %to 19;
    data name&i;
    set sashelp.class(firstobs=&i obs=&i);
    run;
%end;
%mend;
%createtables;
```
32.	creates table RESULT containing observations repeated as many times as is the value of the variable age
```
data result;
    set sashelp.class;
    do i=1 to age;
        output;
    end;
run;
```
33.	creates a two-dimensional array in the IML taking the first two observations and columns age and weight and calculates its determinant
 ```
proc iml;
    use sashelp.class;
    array={. ., . .};
    read point{1 2} var {age weight} into array;
    determinant=det(array);
    print array determinant;
quit;
```
 Another solution:
 ```
proc iml;
    use sashelp.class(obs=2);
    read all var {age weight} into A;
    print A;
    detA = det(A);
    print detA;
quit;
```

## Reporting in SAS:
1.	creates a format for age group: up to 13 years included and above, using the MEANS procedure determine the number of students and the average values of age
```
data a;
    set sashelp.class;
    if age>=13 then output;
run;
proc means data=a;
    var age;
    class age;
    types () age;
run;
```
2.	creates a two-dimensional table containing frequencies of age and sex
```
proc freq data=sashelp.class;
    table age*sex /NOPERCENT NOROW NOCOL;
run;
```
3.	creates a table containing absolute and relative frequencies for age, includes cumulated values for both
 ```
proc freq data=sashelp.class;
    table age;
run;
```
 Another solution:
 ```
proc freq data=sashelp.class noprint;
    table age /outcum out=table missing;
run;
```
4.	creates an HTML report with a table with names on the left hand side and headings of sex; the table should be filled with total values of age
 ```
data result;
    set sashelp.class;
    if sex='M' then M=age;
    if sex='F' then F=age;
run;
ods html body='result.htm';
proc print data=result(keep=name M F);
    ID name;
run;
ods html close;
```
 Another solution:
 ```
%let dir=C:\GaoFangshu\SGH\SAS\;
ods listing close;
ods html path="&dir" body="report.html" style=statistical;
title "Class report";
options missing=' ';
proc tabulate data=sashelp.class;
    class name sex;
    var age;
    table name='' all, (sex='' all)*mean=''*age=''*f=12.
    / box='Average age / Gender';
run;
ods html close;
ods listing;
```
5.	creates a report in PDF based on SASHELP.PRDSALE containing information about the current total sales with percentages by years and regions, adds summaries
 ```
proc sort data=sashelp.prdsale out=sort;
    by year region;
run;
proc summary data=sashelp.prdsale;
    freq actual;
    var actual;
    class year region;
    types () year region year*region;
    output out=out n=n;
run;
data result;
    set out;
    if _type_=0 then total=_freq_;
        else total=first.total;
run;
proc print data=result(drop=_type_ _freq_);
run;
```
 Solution:
 ```
%let dir=C:\GaoFangshu\SGH\SAS\;
ods listing close;
ods pdf file="&dir.report.pdf";
title 'Sale report';
options missing=' ';
proc tabulate data=sashelp.prdsale;
    class year region;
    var actual;
    table region='' all, (year='' all)*(sum='Sum' rowpctsum='Pct')*actual=''*f=12.1
    / box='Region / Year';
run;
ods pdf close;
ods listing;
```
6.	creates a serial report for each region, i.e. creates as many HTML reports, as there are regions and every of these reports contains total sales divided by years and products; adds summaries

 Solution:
 ```
proc sql noprint;
    select distinct region into :reg1-:reg99999 from sashelp.prdsale;
quit;
%let n_reg=&sqlobs;
%put &n_reg***&reg1***&&reg&n_reg;
%macro doreports;
    %let dir=C:\GaoFangshu\SGH\SAS\;
    %do i=1 %to &n_reg;
        %let region=&&reg&i;
        ods listing close;
        ods html path="&dir" body="report_&region..html" style=statistical;
        title "Sales report in &region";
        options missing=' ';
        proc tabulate data=sashelp.prdsale;
            class year product;
            var actual;
            table product='' all, (year='' all)*sum=''*actual=''*f=12.1
            / box='Product / Year';
            where region="&region";
        run;
        ods html close;
        ods listing;
    %end;
%mend;
%doreports;
```
7.	creates a list macrovariables name1 to name19 inserting names of the students in alphabetical order

 Solution:
 ```
proc sql noprint;
    select name into :name1-:name19 from sashelp.class
    order by name;
quit;
%put &name1***&name2***&name19;
```
8.	creates a list macrovariables with names of students inserting each student’s sex after his/her name

 Solution:
 ```
data _null_;
    set sashelp.class;
    call symput(name,trim(sex));
run;
%put &Alfred***&Alice***&William;
```
 Three kinds of macrovariable: `%let`, `select ... into :...`, `call symput()`
9.	creates one long macrovariable with list of names of students in alphabetical order, separated with a #

 Solution:
 ```
proc sql noprint;
    select distinct name into :names separated by '#' from sashelp.class
    order by name;
quit;
%put &names;
```
10.	creates a macro program with parameter sex which lists (PROC PRINT) observations for the selected gender

 Solution:
 ```
%macro report(gender);
    title "Report for &gender";
    proc print data=sashelp.class;
        where sex='&gender';
    run;
%mend;
%report(F);
%report(M);
```
11.	creates a bar graph of the average age for sex

 Solution:
 ```
proc gchart data=sashelp.class;
    vbar3d sex / sumvar=age type=mean;
run;
quit;
```
12.	creates a line graph of the time series containing number of passengers AIR from the set SASHELP.AIR

 Solution:
 ```
symbol i=join v=star;
proc gplot data=sashelp.air;
    plot air*date;
run;
quit;
```
13.	calculates percentiles for age: 5, 25, 50, 75 and 95

 *Refer to [How do I obtain percentiles not automatically calculated?](http://www.ats.ucla.edu/stat/sas/faq/percentiles.htm)*

 ```
proc univariate data=sashelp.class;
    var age;
    output pctlpre=P_ pctlpts= 5, 25 to 75 by 25, 95 out=result;
run;
proc print data=result;
run;
```
14.	calculates standard deviation, average and the inter-quartile range
```
proc univariate data=sashelp.class;
    var age;
run;
```
15.	exports numbers 3,4,5 to EXCEL cells A1, A2 and A3
```
options noxwait noxsync;
filename sas2xl dde 'excel|system';
data _null_;
    length fid rc start stop time 8;
    fid=fopen('sas2xl','s');
    if (fid le 0) then do;
        rc=system('start excel');
        start=datetime();
        stop=start+10;
        do while (fid le 0);
            fid=fopen('sas2xl','s');
            time=datetime();
            if (time ge stop) then fid=1;
        end;
    end;
    rc=fclose(fid);
run;
data w;
    filename ddedata dde
    /*	'excel|Sheet1!w1k1:w3k1';  */
    'excel|input!r4c2:r6c2';
    file ddedata;
    x=sleep(2);
    format i numx10.2;
    do i=3 to 5;
        put i;
    end;
run;
```
16.	creates a data set containing file names from the directory c: \
```
filename cf pipe 'dir c:\ /w /a:-d /b';
data result;
    infile cf;
    input;
    /*input tempc $ 1-200;*/
    /*put tempc=;*/
    a=_infile_;
run;
```
17.	creates text files with names as variable PRODUCT in table SASHELP.PRDSALE and inserts information about the current sales and year
```
%let dir=C:\GaoFangshu\SGH\SAS\;
proc sql noprint;
    select distinct product into :pr1-:pr99999 from sashelp.PRDSALE;
quit;
%let n_pr=&sqlobs;
%put &n_pr***&pr1***&&pr&n_pr;
%macro all_reports;
    %do i=1 %to &n_pr;
        %let product=&&pr&i;
        /*%let product=SOFA;*/
        proc means data=SASHELP.PRDSALE noprint nway;
            class year;
            var actual;
            output out=stat sum()=;
            where product="&product";
        run;
        data _null_;
            file "&dir.&product..txt" dsd dlm='#';
            set stat;
            put year actual;
        run;
    %end;
%mend;
%all_reports;
```
18.	creates a box plot for age by sex

 *Refer to [The BOXPLOT Procedure](https://support.sas.com/documentation/cdl/en/statug/63347/HTML/default/viewer.htm#boxplot_toc.htm)*
 ```
proc sort data=sashelp.class out=sorted;
    by sex;
run;
proc boxplot data=sorted;
    plot age*sex;
run;
```
19.	creates a scatter plot for age and weight

 *Refer to [SAS Learning Module: Graphing data in SAS](http://www.ats.ucla.edu/stat/sas/modules/graph.htm),
 [The CORR Procedure: Example 2.8 Creating Scatter Plots](http://support.sas.com/documentation/cdl/en/procstat/66703/HTML/default/viewer.htm#procstat_corr_examples11.htm)*

 > You must enable ODS graphics before requesting plots.

 ```
ods graphics on;
proc corr data=sashelp.class plots=scatter(nvar=2);
	var age weight;
run;
ods graphics off;
```
20.	calculates robust means for age: e.g. trimmed or winsorized mean

 *Refer to [The UNIVARIATE Procedure: Robust Estimators](http://support.sas.com/documentation/cdl/en/procstat/67528/HTML/default/viewer.htm#procstat_univariate_details22.htm)*

 > The ODS SELECT statement restricts the output to the "TrimmedMeans," "WinsorizedMeans," and "RobustScale" tables; see the section [ODS Table Names](http://support.sas.com/documentation/cdl/en/procstat/67528/HTML/default/viewer.htm#procstat_univariate_details85.htm). The TRIMMED= option computes two trimmed means, the first after removing one observation and the second after removing 10% of the observations. If the value of TRIMMED= is greater than or equal to one, it is interpreted as the number of observations to be trimmed. The WINSORIZED= option computes a Winsorized mean that replaces three observations from the tails with the next closest observations.

 ```
ods select TrimmedMeans WinsorizedMeans;
proc univariate data=sashelp.class trimmed=1 .1 winsorized=.1;
    var age;
run;
```
21.	creates a listing for LaTeX
```
ods listing close;
ods latex file='C:\GaoFangshu\SGH\SAS\report_latex.txt';
proc print data=sashelp.class;
run;
ods latex close;
ods listing;
```
 ```
ods listing close;
ods pdf file='C:\GaoFangshu\SGH\SAS\report_latex.pdf';
proc print data=sashelp.class;
run;
ods pdf close;
ods listing;
```
22.	creates a table CONVERGENCE containing statistics of fit of age to normal distribution, using procedure UNIVARIATE
```
ods trace on / listing;
ods trace off;
ods listing close;
ods output TestsForNormality=CONVERGENCE;
proc univariate data=sashelp.class normal;
    var age;
run;
ods output close;
ods listing;
```

## Statistics in SAS:
1.	creates a histogram of the age variable containing the gaussian curve
 ```
proc univatiate data=sashelp.class;
    var age;
    histogram age / normal;
run;
```
2.	creates the chi-square test for gender and age, treating both as a nominal variables, counts p-value

 *Refer to [Statistical Test in SAS](http://www.ats.ucla.edu/stat/sas/whatstat/whatstat.htm)*

 ```
proc freq data=sashelp.class;
    table sex*age / chisq;
run;
```
3.	calculates the V-Cramer statistic for age and sex
```
proc freq data=sashelp.class;
    table sex*age / chisq;
run;
```
4.	calculates the Gini coefficient for sex and age

 > The Somer's D statistic given in the logistic output is the same as the Gini index.

 ```
proc logistic data=sashelp.class;
    model sex=age;
run;
proc freq data=sashelp.class;
    tables sex*age;
    exact SMDCR;
run;
```
5.	calculates the Spearman correlation coefficient for age and weight
```
proc corr data=sashelp.class pearson spearman;
    var age weight;
run;
```
6.	calculates the Pearson correlation coefficient for age and weight
```
proc corr data=sashelp.class pearson spearman;
    var age weight;
run;
```
7.	calculates the Kolmogorov-Smirnov statistic to test the fit of empirical age distribution to the theoretical normal distribution

 *Refer to [The UNIVARIATE Procedure: Goodness-of-Fit Tests](http://support.sas.com/documentation/cdl/en/procstat/67528/HTML/default/viewer.htm#procstat_univariate_details52.htm)*

 ```
proc univariate data=sashelp.class normal;
    var age;
run;
```
8.	calculates the Shapiro-Wilk statistic to test the fit of empirical age distribution to the theoretical normal distribution

 *Refer to [The UNIVARIATE Procedure: Goodness-of-Fit Tests](http://support.sas.com/documentation/cdl/en/procstat/67528/HTML/default/viewer.htm#procstat_univariate_details52.htm)*
```
proc univariate data=sashelp.class normal;
    var age;
run;
```
9.	calculates the partial Pearson correlation coefficient for age and weight excluding the influence of height

 *Refer to [Correlation and Simple Regression](https://onlinecourses.science.psu.edu/stat482/book/export/html/7),
 [Computing Partial Correlation Via Regression](http://www.ats.ucla.edu/stat/sas/code/partreg.htm),
 [The CORR Procedure: Example 2.9 Computing Partial Correlations](http://support.sas.com/documentation/cdl/en/procstat/67528/HTML/default/viewer.htm#procstat_corr_examples12.htm)*

 ```
proc corr data=sashelp.class;
    var age weight;
	partial height;
run;
```
10.	calculates linear regression coefficients for age and weight
```
proc reg data=sashelp.class;
    model age=weight;
run;
quit;
```
11.	calculates determination coefficient of linear regression for age and weight
```
proc reg data=sashelp.class;
    model age=weight;
run;
quit;
```
12.	performs diagnostics of linear regression model for age and weight

 *Refer to [Regression Diagnostics](http://www.ats.ucla.edu/stat/sas/webbooks/reg/chapter2/sasreg2.htm)*
 ```
proc reg data=sashelp.class;
model age=weight;
run;
quit;
```
13.	examines homoscedasticity in linear regression model for age and weight
```
proc reg data=sashelp.class;
    model age=weight;
    plot r. * p.;
run;
quit;
```
14.	examines normality of residuals in linear regression model for age and weight
```
proc reg data=sashelp.class;
    model age=weight / spec;
    output out=rests r=r;
	/*
    plot r. * (p. age);
    plot student. * obs. / vref=3 2 -2 -3 vaxis=-4 to 4 by 1;
    plot nqq. * student.; /*rest normality test;*/
	*/
run;
quit;
proc univariate data=rests mu0=0 alpha=0.01 normal;
    var r;
    histogram r / normal;
run;
```
15.	performs stepwise variable selection in multiple linear regression model of age dependent on weight and height
```
proc reg data=sashelp.class;
    model age=weight height / selection=stepwise;
run;
quit;
```
16.	calculates collinearity measures in multiple linear regression model of age dependent on weight and height
```
proc reg data=sashelp.class;
    model age=weight height / collin vif;
run;
quit;
```
17.	calculates coefficients of determination for all models of multiple linear regression of age dependent on weight and height
```
proc reg data=sashelp.class;
    model age = weight height;
    model age = weight;
    model age = height;
run;
quit;
```
18.	determines outliers and/or influential observations in linear regression model of age and height
```
ods listing close;
ods output OutputStatistics.stat;
proc reg data=sashelp.class outest=e;
    model age=weight / influence vif collinoint r cli clm p;
run;
quit;
ods output close;
ods listing;
data untypical;
    set stat;
    if
        abs(RStudent)>3
        or HatDiagonal>(2*&p/&n)
        or CooksD > (4/&n)
        or abs(DIFFITS) > 2/sqrt(&n)
        or min(of DFB:) < 2/sqrt(&n)
        or max(of DFB:) < -2/sqrt(&n)
        or abs(CovRatio-1) > 3*&p/&n
        /*many other conditions*/
        ;
run;
```
19.	calculates the percent variance explained by principal components for the variables age, weight and height
```
proc princomp data=sashelp.class;
    var age weight height;
run;
```
20.	calculates a forecast of age with weight equal to 80 in the linear regression model of age dependent on weight
```
data dataSet;
    set sashelp.class end=e;
    output;
    if e then do;
        age=.; weight=80; output;
    end;
run;
proc reg data=dataSet;
    model age=weight;
    output out=regression p=pred r=reszty lcl=dol ucl=gora lclm=dolsr uclm=gorasr;
run;
quit;
```
21.	determines clusters (concentration) variables (PROC VARCLUS) for all numeric variables in table SASHELP.CARS and the explained variance (ratio) of 80%
```
proc varclus data=sashelp.cars proportion=0.6;
    var _numeric_;
run;
```
