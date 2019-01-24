Test statistical assignment
================
Alicia Rey-Herme (660004693)
22 January 2019

Introduction
------------

Please change the author and date fields above as appropriate. Do not change the output format. Once you have completed the assignment you want to knit your document into a markdown document in the "github\_document" format and then commit both the .Rmd and .md files (and all the associated files with graphs) to your private assignment repository on Github.

Reading data (40 points)
------------------------

1.  The Understanding Society Waves 1-8 User Guide: <https://www.understandingsociety.ac.uk/sites/default/files/downloads/documentation/mainstage/user-guides/mainstage-user-guide.pdf>
2.  The youth self-completion questionnaire from Wave 8: <https://www.understandingsociety.ac.uk/sites/default/files/downloads/documentation/mainstage/questionnaire/wave-8/W8-youth-questionnaire.pdf>
3.  The codebook for the file: <https://www.understandingsociety.ac.uk/documentation/mainstage/dataset-documentation/wave/8/datafile/h_youth>

``` r
# Install tidyverse
library(tidyverse)
```

    ## Warning: package 'tidyverse' was built under R version 3.5.2

    ## -- Attaching packages -------------------------------------------------------------------------------------------------------------------------------- tidyverse 1.2.1 --

    ## v ggplot2 3.1.0     v purrr   0.2.5
    ## v tibble  1.4.2     v dplyr   0.7.8
    ## v tidyr   0.8.2     v stringr 1.3.1
    ## v readr   1.3.0     v forcats 0.3.0

    ## Warning: package 'tidyr' was built under R version 3.5.2

    ## Warning: package 'forcats' was built under R version 3.5.2

    ## -- Conflicts ----------------------------------------------------------------------------------------------------------------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
# Loading in data. Data folder was renamed to SN6614 for efficiency
Data <- read_tsv("C:/Users/alici/Documents/DA3/data/SN6614/tab/ukhls_w8/h_youth.tab")
```

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_double()
    ## )

    ## See spec(...) for full column specifications.

Tabulate variables (10 points)
------------------------------

In the survey children were asked the following question: "Do you have a social media profile or account on any sites or apps?". In this assignment we want to explore how the probability of having an account on social media depends on children's age and gender.

Tabulate three variables: children's gender, age (please use derived variables) and having an account on social media.

``` r
# Column Names:
# h_sex_dv <- Respondent's Gender (Derived Variables) | Male (1), Female (2), NA (-9)
# h_age_dv <- Respondent's Age (Derived Variables) | Ages 10-16
# h_ypsocweb <- Respondent's answer to "Do you have a social media profile or account on any sites or apps? | Yes (1), No (2), NA (-9)


#Individual Freq. Tables
T1 <- table(Data$h_sex_dv)
View(T1)
T2 <- table(Data$h_age_dv)
View(T2)
T3 <- table(Data$h_ypsocweb)
View(T3)

#Compiled Freq. table
Table <- table(Data$h_sex_dv, Data$h_age_dv, Data$h_ypsocweb)
View(Table)

#Data frame
Tadat <- data.frame(Data$h_sex_dv, Data$h_age_dv, Data$h_ypsocweb)
View(Tadat)
```

Recode variables (10 points)
----------------------------

We want to create a new binary variable for having an account on social media so that 1 means "yes", 0 means "no", and all missing values are coded as NA. We also want to recode gender into a new variable with the values "male" and "female" (this can be a character vector or a factor).

``` r
# Create a new binary variable (SocialMedia) by recoding variable h_ypsocweb so that 1 means "yes", 0 means "no", missing values as NA
Data$SocialMedia[Data$h_ypsocweb=="1"] <- 1
```

    ## Warning: Unknown or uninitialised column: 'SocialMedia'.

``` r
Data$SocialMedia[Data$h_ypsocweb=="2"] <- 0
Data$SocialMedia[Data$h_ypsocweb=="-9"] <- NA

# Ensure variable SocialMedia is numeric 
class(Data$SocialMedia) <- "numeric"

# Create a new variable (Gender) by recoding variable h_sex_dv with the values "male" and "female"
Data$Gender[Data$h_sex_dv==1] <- "male"
```

    ## Warning: Unknown or uninitialised column: 'Gender'.

``` r
Data$Gender[Data$h_sex_dv==2] <- "female"

# Rename h_age_dv to Age for clarity
Data$Age <- Data$h_age_dv
```

Calculate means (10 points)
---------------------------

Produce code that calculates probabilities of having an account on social media (i.e. the mean of your new binary variable produced in the previous problem) by age and gender (please filter out children who are aged 16).

``` r
# Data is grouped by age and then sex, mean of h_ypsocweb is calculated for each pairing of the variables.
   Data %>%
   group_by(Age, Gender) %>%
   summarise(Probability=mean(SocialMedia, na.rm = TRUE))
```

    ## # A tibble: 14 x 3
    ## # Groups:   Age [?]
    ##      Age Gender Probability
    ##    <dbl> <chr>        <dbl>
    ##  1    10 female       0.542
    ##  2    10 male         0.482
    ##  3    11 female       0.720
    ##  4    11 male         0.658
    ##  5    12 female       0.885
    ##  6    12 male         0.809
    ##  7    13 female       0.917
    ##  8    13 male         0.879
    ##  9    14 female       0.972
    ## 10    14 male         0.912
    ## 11    15 female       0.970
    ## 12    15 male         0.929
    ## 13    16 female       1    
    ## 14    16 male         0.667

Write short interpretation (10 points)
--------------------------------------

For both male and female respondents, the probability of having a social media account increases with age. At every age value, females have a higher probability of having an account of social media than males. Aside from 10 year old males, who have a probability of 0.48%, all categories of respondents had over a 50% likelihood of owning a social media account, suggesting that the majority of respondents own an account.

Visualise results (20 points)
-----------------------------

``` r
# Plot a bar graph using aforementioned function
Data %>%
   group_by(Age, Gender) %>%
   summarise(Probability=mean(SocialMedia, na.rm = TRUE)) %>%
    ggplot(aes(fill=Gender, y=Probability, x=Age)) +
     geom_bar(position="dodge", stat="identity")
```

![](Test_Assignment_files/figure-markdown_github/unnamed-chunk-5-1.png)

A bar plot would be most appropriate for visualising the data as it can compare probability values between female and male respondents as well as track the change over age.

Conclusion
----------

This is a test formative assignment and the mark will not count towards your final mark. If you cannot answer any of the questions above this is fine -- we are just starting this module! However, please do submit this assignment in any case to make sure you understand the procedure, it works correctly and we do not have any problems with summative assignments later.