---
title: "Popularity vs. Ability: Exploring the Legitimacy of NBA All-star Voting Process"
author: "Jiehwan Yang (����ȯ)"
date: "2016�� 9�� 14��"
output: html_document
---



# Popularity vs. Ability: Exploring the Legitimacy of NBA All-star Voting Process


## Intro
NBA All-star had repeatedly come under strong criticism from many players, fans, and other important stakeholders alike. There are players like Kobe Bryant, who benefits from his long-standing popularity and reputation and was voted as one of the 2015 NBA All-star players despite of the mediocre season battling with constant injuries. On the other hand, there are players like Damian Lillard and Demarcus Cousins, who do not receive as much attention from media and recognition from fans and did not make it to the 2015 NBA All-star team despite of superb and record-breaking season. Damian Lillard famously tweeted, ��I��m definitely going to take it personal. I said I��d be pissed off about it, and I am.�� 

This controversy around NBA All-star voting stems from its voting process, which selects NBA All-star players solely relying on the Internet and text message ballot from the fans. By utilizing fan voting, the current voting process suffers from couple systematic problems: 
1. Only a very small percentage of fan votes is from NBA followers or experts. It automatically leads to uninformed popularity contest rather than basing it on ability.  
2. Celebrities, or anyone who are opinion leaders, also influence the voting by endorsing a player of the same demographic, such as nationality.
Due to these reasons, many expressed their dissatisfaction toward and questioned the legitimacy of NBA All-star events in general. Dallas Mavericks owner, Mark Cuban, has claimed that the NBA should get rid of the fan ballot entirely.

With the NBA All-star games always being the the source of contention, it leads to the important question: does popularity trump ability when it comes to the NBA All-star? In other words, are basketball statistics/high performance good predictors to determine if a player will make it into the NBA All-star team? The purpose of this project is to explore the predictability of various basketball statistics on whether a player will make it into the NBA All-star team or not. The results from this project will help better evaluate the strength and the legitimacy of the current voting process and will appeal to various stakeholders of the NBA, ranging from the fans and the players to the NBA commission and the team owners. The next section, methodology, will cover different steps we took to collect, cleanse, and analyze the data and the thought-process behind each step. Then the results of the data mining will be presented and discussed at the end of the paper. 

Methodology
Data Collection
We gathered two different NBA basketball datasets from KDNuggets.com. The first dataset (D1), basketballplayers, is the detailed basketball stats records of every single NBA player in each year from 1950 to 2009. Each observation in D1 is a player in one season, and the attributes include typical basketball stats measurements such as points, rebounds, assists, blocks, etc. The second dataset (D2), ��basketball_allstar,�� is the similar to the first dataset but the data are limited to only all-star players. Just as it was described in D1, D2��s observations are all-star players in each season, and the attributes characterizing these observations are the same as D1. 
Data Cleansing and Preprocessing
The first step of data preprocessing was merging D1 and D2 together, so that there is one consolidated dataset (D3) with every single NBA player in each year with the additional attribute that indicates if respective player was in the All-star team or not in that given year. This merging was done in the Excel spreadsheet.

The next step involved working with missing data and dirty data. GS (games started) column was removed since all the values were 0 in all observations. We dropped all observations before the year of 1980, since the merger between NBA and ABA happened in 1976, because before that, the way basketball statistics were measured and recorded was different between those two leagues. For the sake of easier data analysis, we dropped everything before 1980. Furthermore, after data exploration, we found that the list of All-star players in 1998 is empty, so we decided to exclude the year 1998 as well.

The final dataset that had been ��cleaned�� up has a total of 36 variables. Only 32 out of those 36 variables will be utilized as predictors for the analysis. There was a total of 685 All-star player appearances between 1980 to 2009, except 1998. Summary statistics on those 32 predictors are shown below:


```
##    playerID year tmID lgID GP minutes points rebounds assists steals
## 1 abdulka01 1980  LAL  NBA 80    2976   2095      821     272     59
## 2 abernto01 1980  GSW  NBA 10      39      4        8       1      1
## 3 abernto01 1980  IND  NBA 29     259     59       40      18      6
## 4 adamsal01 1980  PHO  NBA 75    2054   1115      546     344    106
## 5 allumda01 1980  DAL  NBA 22     276     59       65      25      5
## 6 archina01 1980  BOS  NBA 80    2820   1106      176     618     75
##   blocks turnovers  PF fgAttempted fgMade ftAttempted ftMade
## 1    228       249 244        1457    836         552    423
## 2      0         2   5           3      1           3      2
## 3      3         6  29          56     24          19     11
## 4     69       226 226         870    458         259    199
## 5      8        23  51          67     23          22     13
## 6     18       265 201         766    382         419    342
##   threeAttempted threeMade PostGP PostGS PostMinutes PostPoints
## 1              1         0      3      0         134         80
## 2              0         0      0      0           0          0
## 3              1         0      0      0           0          0
## 4              0         0      7      0         218         74
## 5              1         0      0      0           0          0
## 6              9         0     17      0         630        266
##   PostRebounds PostAssists PostSteals PostBlocks PostTurnovers PostPF
## 1           50          12          3          8            11     14
## 2            0           0          0          0             0      0
## 3            0           0          0          0             0      0
## 4           41          26          4          1            19     20
## 5            0           0          0          0             0      0
## 6           28         107         13          0            50     39
##   PostfgAttempted PostfgMade PostftAttempted PostftMade PostthreeAttempted
## 1              65         30              28         20                  0
## 2               0          0               0          0                  0
## 3               0          0               0          0                  0
## 4              60         27              28         20                  0
## 5               0          0               0          0                  0
## 6             211         95              94         76                  5
##   PostthreeMade Allstar
## 1             0       1
## 2             0       0
## 3             0       0
## 4             0       0
## 5             0       0
## 6             0       1
```

## Classification Analysis


```r
#Select a random sample of our data to use as our training data set. 
#Put 50% of the data in training and 50% in test.
ind = sample(1:nrow(dat),floor(nrow(dat)*0.50)) 
dat_train = dat[ind,] 
dat_test = dat[-ind,]
ind = sample(1:nrow(dat),floor(nrow(dat)*0.50)) 
dat_train = dat[ind,] 
dat_test = dat[-ind,]
```


```r
#Load rpart and rpart.plot library in order to perform prediction analysis by using regression tree.
library(rpart)
library(rpart.plot)
```

### Decision Tree
We explored the strength of decision trees by changing the minsplit parameters between 10, 20, and 60. Confusion matrices were utilized to calculate accuracy, precisions, and recalls. It is important to note that we prioritize the predictability of someone making into All-star team than not making into one. Furthermore, we believe that FN is more costly than FP, because most of the dissatisfactions from stakeholders are caused by well-deserved players not making to the team.


```r
#First, try split the data at minimum split value of 20.
# Minsplit says don't split if you have fewer than 20 observations left in the branch from the training data.
tree_1<-rpart(Allstar ~ GP+ minutes+ points+ rebounds+ assists+ steals+ blocks+ turnovers
              + PF+ fgAttempted+ fgMade+ ftAttempted+ ftMade+ threeAttempted+ threeMade+ PostGP
              + PostMinutes+ PostPoints+ PostRebounds+ PostAssists+ PostSteals+ PostBlocks
              + PostTurnovers+ PostPF+ PostfgAttempted+ PostfgMade+ PostftAttempted+ PostftMade
              + PostthreeAttempted+ PostthreeMade,
              data=dat_train,method = "class",control = rpart.control(minsplit = 20))

#Draw regression tree using rpart.plot
rpart.plot(tree_1,type = 1,extra=2,under=TRUE)
title(sub="Classifcation Tree with minsplit =20 ")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png)


```r
#Try minsplit=10
tree_2<-rpart(Allstar ~ GP+ minutes+ points+ rebounds+ assists+ steals+ blocks+ turnovers
              + PF+ fgAttempted+ fgMade+ ftAttempted+ ftMade+ threeAttempted+ threeMade+ PostGP
              + PostMinutes+ PostPoints+ PostRebounds+ PostAssists+ PostSteals+ PostBlocks
              + PostTurnovers+ PostPF+ PostfgAttempted+ PostfgMade+ PostftAttempted+ PostftMade
              + PostthreeAttempted+ PostthreeMade,
              data=dat_train,method = "class",control = rpart.control(minsplit = 10))
rpart.plot(tree_2,type = 1,extra=2,under=TRUE)
title(sub="Classifcation Tree with minsplit =10 ")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png)



```r
#Try minsplit=60
tree_3<-rpart(Allstar ~ GP+ minutes+ points+ rebounds+ assists+ steals+ blocks+ turnovers
              + PF+ fgAttempted+ fgMade+ ftAttempted+ ftMade+ threeAttempted+ threeMade+ PostGP
              + PostMinutes+ PostPoints+ PostRebounds+ PostAssists+ PostSteals+ PostBlocks
              + PostTurnovers+ PostPF+ PostfgAttempted+ PostfgMade+ PostftAttempted+ PostftMade
              + PostthreeAttempted+ PostthreeMade,
              data=dat_train,method = "class",control = rpart.control(minsplit = 60))
rpart.plot(tree_3,type = 1,extra=2,under=TRUE)
title(sub ="Classifcation Tree with minsplit =60 ")
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png)



```r
#Predict the classification of each observation in the test data, using the three trees that we built.
dat_test1<- dat_test
dat_test2<- dat_test
dat_test3<- dat_test

dat_test1$pred <- predict(tree_1,dat_test, type="class")
dat_test2$pred <- predict(tree_2,dat_test, type="class")
dat_test3$pred <- predict(tree_3,dat_test, type="class")

#Now let's check out our confusion matrix to see how the tree did. 
confusionmatrix1<-table(dat_test1$Allstar,dat_test1$pred,dnn = c("Actual","Predicted"))
confusionmatrix2<-table(dat_test2$Allstar,dat_test2$pred,dnn = c("Actual","Predicted"))
confusionmatrix3<-table(dat_test3$Allstar,dat_test3$pred,dnn = c("Actual","Predicted"))

confusionmatrix1
```

```
##       Predicted
## Actual    0    1
##      0 5955   76
##      1  161  171
```

```r
confusionmatrix2
```

```
##       Predicted
## Actual    0    1
##      0 5957   74
##      1  164  168
```

```r
confusionmatrix3
```

```
##       Predicted
## Actual    0    1
##      0 5965   66
##      1  170  162
```


```r
Accuracy<-(confusionmatrix1[1,1]+confusionmatrix1[2,2])/sum(confusionmatrix1)
Accuracy
```

```
## [1] 0.9627534
```

```r
#precisions
Precision_N<-confusionmatrix1[1,1]/(confusionmatrix1[1,1]+confusionmatrix1[2,1])
Precision_P<-confusionmatrix1[2,2]/(confusionmatrix1[2,2]+confusionmatrix1[1,2])
Precision_N
```

```
## [1] 0.9736756
```

```r
Precision_P
```

```
## [1] 0.6923077
```

```r
#recalls
Recall_N<-confusionmatrix1[1,1]/(confusionmatrix1[1,1]+confusionmatrix1[1,2])
Recall_P<-confusionmatrix1[2,2]/(confusionmatrix1[2,2]+confusionmatrix1[2,1])
Recall_N
```

```
## [1] 0.9873984
```

```r
Recall_P
```

```
## [1] 0.5150602
```

## Conclusion

It is not surprising to discover that All-star players in general have better basketball statistics than non-All-star players, because it is only intuitive to conclude that the popularity of a given player is positively correlated with his ability to play basketball. It was interesting to find the high discrepancy in the number of All-star players throughout the team. We did not take the establishment of each team into consideration, but it is safe to assume that the probability of making into the All-star team is different from one team to another due to their popularity, fan base, and ability. 

Through the decision tree and the K-NN model, we found that basketball statistics are relatively reliable information to predict whether a player will make into the All-star team or not. In terms of predicting if someone will NOT make the team, both techniques were highly accurate and viable (precision and recall both over 97%). This, as it was mentioned, is not very meaningful, since there are simply more players who do not get selected into the All-star team thus much higher chance of not making it.  They had more difficulty in predicting if someone will make the team, as the decision tree had precision and recall of 62% and 55% and K-nn had 69% and 49%. The fact the recalls are so low would be quite costly if these models were to be deployed since false negative in this case would cause a lot of dissatisfaction among stakeholders, more so that false positive. 

All in all, the fact that basketball statistics are somewhat reliable information to predict someone��s chance at making the All-star team indicates that the current NBA All-star voting process is not as bad as what people think. This is based on the widely accepted notion that popularity is positively correlated to one��s ability to play basketball. There always will be outliers, such as Kobe Bryant making it to the All-star team, but we can safely conclude that in general players who are fan voted to join the All-star team are definitely talented, superior to, and perform far better than non-All-star players.

