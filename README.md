
<!-- README.md is generated from README.Rmd. Please edit the README.Rmd file -->

# Lab report \#3 - instructions

Follow the instructions posted at
<https://ds202-at-isu.github.io/labs.html> for the lab assignment. The
work is meant to be finished during the lab time, but you have time
until Monday evening to polish things.

Include your answers in this document (Rmd file). Make sure that it
knits properly (into the md file). Upload both the Rmd and the md file
to your repository.

All submissions to the github repo will be automatically uploaded for
grading once the due date is passed. Submit a link to your repository on
Canvas (only one submission per team) to signal to the instructors that
you are done with your submission.

# Lab 3: Avenger’s Peril

## As a team

Extract from the data below two data sets in long form `deaths` and
`returns`

``` r
av <- read.csv("https://raw.githubusercontent.com/fivethirtyeight/data/master/avengers/avengers.csv", stringsAsFactors = FALSE)
head(av)
```

    ##                                                       URL
    ## 1           http://marvel.wikia.com/Henry_Pym_(Earth-616)
    ## 2      http://marvel.wikia.com/Janet_van_Dyne_(Earth-616)
    ## 3       http://marvel.wikia.com/Anthony_Stark_(Earth-616)
    ## 4 http://marvel.wikia.com/Robert_Bruce_Banner_(Earth-616)
    ## 5        http://marvel.wikia.com/Thor_Odinson_(Earth-616)
    ## 6       http://marvel.wikia.com/Richard_Jones_(Earth-616)
    ##                    Name.Alias Appearances Current. Gender Probationary.Introl
    ## 1   Henry Jonathan "Hank" Pym        1269      YES   MALE                    
    ## 2              Janet van Dyne        1165      YES FEMALE                    
    ## 3 Anthony Edward "Tony" Stark        3068      YES   MALE                    
    ## 4         Robert Bruce Banner        2089      YES   MALE                    
    ## 5                Thor Odinson        2402      YES   MALE                    
    ## 6      Richard Milhouse Jones         612      YES   MALE                    
    ##   Full.Reserve.Avengers.Intro Year Years.since.joining Honorary Death1 Return1
    ## 1                      Sep-63 1963                  52     Full    YES      NO
    ## 2                      Sep-63 1963                  52     Full    YES     YES
    ## 3                      Sep-63 1963                  52     Full    YES     YES
    ## 4                      Sep-63 1963                  52     Full    YES     YES
    ## 5                      Sep-63 1963                  52     Full    YES     YES
    ## 6                      Sep-63 1963                  52 Honorary     NO        
    ##   Death2 Return2 Death3 Return3 Death4 Return4 Death5 Return5
    ## 1                                                            
    ## 2                                                            
    ## 3                                                            
    ## 4                                                            
    ## 5    YES      NO                                             
    ## 6                                                            
    ##                                                                                                                                                                              Notes
    ## 1                                                                                                                Merged with Ultron in Rage of Ultron Vol. 1. A funeral was held. 
    ## 2                                                                                                  Dies in Secret Invasion V1:I8. Actually was sent tto Microverse later recovered
    ## 3 Death: "Later while under the influence of Immortus Stark committed a number of horrible acts and was killed.'  This set up young Tony. Franklin Richards later brought him back
    ## 4                                                                               Dies in Ghosts of the Future arc. However "he had actually used a hidden Pantheon base to survive"
    ## 5                                                      Dies in Fear Itself brought back because that's kind of the whole point. Second death in Time Runs Out has not yet returned
    ## 6                                                                                                                                                                             <NA>

Get the data into a format where the five columns for Death\[1-5\] are
replaced by two columns: Time, and Death. Time should be a number
between 1 and 5 (look into the function `parse_number`); Death is a
categorical variables with values “yes”, “no” and ““. Call the resulting
data set `deaths`.

    Completed by Dhairya Kachalia

``` r
library(dplyr)
```

    ## Warning: package 'dplyr' was built under R version 4.3.3

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
#get sum of deaths in Time column
death_cols <- av[c("Death1","Death2","Death3","Death4","Death5")]
return_cols <-av[c("Return1","Return2","Return3","Return4","Return5")]
#create Time Column
av$Time <-rowSums(death_cols == "YES",na.rm = TRUE)
#create Return_Time Column
av$Return_Time <- rowSums(return_cols == "YES",na.rm = TRUE)

#remove Death(2-5) and Return(2-5)
av <- av[,-(13:20)]

#rename Death1 to Death and Return1 to Return
av<-av %>% rename("Death"="Death1")
av<-av %>% rename("Return"="Return1")

rm(death_cols)
```

Similarly, deal with the returns of characters.

Based on these datasets calculate the average number of deaths an
Avenger suffers.

``` r
cat("The average deaths an avenger get is ", mean(av$Time),"\n")
```

    ## The average deaths an avenger get is  0.5144509

## Individually

For each team member, copy this part of the report.

Each team member picks one of the statements in the FiveThirtyEight
[analysis](https://fivethirtyeight.com/features/avengers-death-comics-age-of-ultron/)
and fact checks it based on the data. Use dplyr functionality whenever
possible.

### FiveThirtyEight Statement

> Quote the statement you are planning to fact-check.

    Dhairya Kachalia analysis statement:
    Out of 173 listed Avengers, my analysis found that 69 had died at least one time after they joined the team. That’s about 40 percent of all people who have ever signed on to the team. 

``` r
#get rows where Time >=1
filter_row <- av[av$Time >=1,]
cat("the total number of avengers that died atleast once is", nrow(filter_row), "\n")
```

    ## the total number of avengers that died atleast once is 69

    statement2: Of the nine Avengers we see on screen — Iron Man, Hulk, Captain America, Thor, Hawkeye, Black Widow, Scarlet Witch, Quicksilver and The Vision — every single one of them has died at least once in the course of their time Avenging in the comics. In fact, Hawkeye died twice!

``` r
# i will use the names Anthony Edward "Tony" Stark, Robert Bruce Banner, Thor Odinson, Steven Rogers, Pietro Maximoff, Wanda Maximoff, Natalia Alianovna Romanova, Clinton Francis Barton instead of Iron Man, Hulk, Hawkeye, etc. as these are the names in the database

#gets the rows where name alias are as above and Time >=1
tv_avengers <- av %>% select(Name.Alias,Time) %>%  filter(Name.Alias %in% c("Anthony Edward \"Tony\" Stark", "Robert Bruce Banner", "Thor Odinson", "Steven Rogers", "Pietro Maximoff", "Wanda Maximoff", "Natalia Alianovna Romanova", "Clinton Francis Barton"),av$Time >=1)

tv_avengers
```

    ##                    Name.Alias Time
    ## 1 Anthony Edward "Tony" Stark    1
    ## 2         Robert Bruce Banner    1
    ## 3                Thor Odinson    2
    ## 4               Steven Rogers    1
    ## 5      Clinton Francis Barton    2
    ## 6             Pietro Maximoff    1
    ## 7              Wanda Maximoff    1
    ## 8  Natalia Alianovna Romanova    1

``` r
# the tv_anvegers table shows that the avengers in this analysis died atleast once and Hawkeye i.e Clinton Francis Barton died twice
```

    statement 3: I counted 89 total deaths — some unlucky Avengers7 are basically Meat Loaf with an E-ZPass — and on 57 occasions the individual made a comeback. But you can only tempt death so many times. There’s a 2-in-3 chance that a member of the Avengers returned from their first stint in the afterlife, but only a 50 percent chance they recovered from a second or third death.

``` r
#gives the sum of the total number of deaths in av. Also adds multiple deaths of same character
sum_deaths <- sum(av$Time, na.rm = TRUE)

#gives the sum of the total number of returns in av. Also adds multiple deaths of same character
sum_returns <- sum(av$Return_Time, na.rm = TRUE)

cat("Total occasions of deaths is",sum_deaths,"which matches the statement.\n")
```

    ## Total occasions of deaths is 89 which matches the statement.

``` r
cat("Total ocassions of returns is", sum_returns,"which matches the statement\n")
```

    ## Total ocassions of returns is 57 which matches the statement

``` r
#gives the sum of total characters who have died atleast once
sum_died_atleast_once <- nrow(av %>% filter(av$Time >=1))

#gives the sum of total characters who have made a comeback atleast once
sum_returns_atleast_once <- nrow(av %>% filter(av$Return_Time>=1))

cat("The percentage of characters who made a comeback atleast once is",(sum_returns_atleast_once/sum_died_atleast_once)*100,"% which matches the statement that the comeback rate is 2 in 3 \n")
```

    ## The percentage of characters who made a comeback atleast once is 66.66667 % which matches the statement that the comeback rate is 2 in 3

``` r
#characters who made more than 1 return
gg <- av %>% filter(av$Return_Time>1)
#sum of ocassions where the character made more than one time
sum_returns_more_than_once <-sum(gg$Return_Time, na.rm = TRUE)
```

### Include the code

Make sure to include the code to derive the (numeric) fact for the
statement

### Include your answer

Include at least one sentence discussing the result of your
fact-checking endeavor.

Upload your changes to the repository. Discuss and refine answers as a
team.
