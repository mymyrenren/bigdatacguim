---
title: "乳癌預測模型"
output: github_document
---
##資料前處理

####資料讀取
 資料來源由博士 威廉H. Wolberg得到是在威斯康星大學醫院( 威斯康星州麥迪遜市)，由其中的參數依序有叢厚度、細胞均勻大小、細胞形狀、細胞邊際附著力、單上皮細胞尺寸、裸核、布蘭德染色質、普通核仁、有絲分裂情形。 主要由這些參數，對照紀錄，來判斷是否會得到乳癌的可能。


```{r echo=T}
install.packages("mlbench")
library(mlbench)
data(BreastCancer)
str(BreastCancer)

```


####處理資料
```{r}
PimaIndiansDiabetesC<-

PimaIndiansDiabetes[complete.cases(PimaIndiansDiabetes),]

c(nrow(PimaIndiansDiabetes),nrow(PimaIndiansDiabetesC))
```


####分成訓練組跟測試組,並紀錄各組人數 


```{r}
BreastCancerC$Test<-F
BreastCancerC[
sample(1:nrow(BreastCancerC),nrow(BreastCancerC)/3),]$Test<-T
c(sum(BreastCancerC$Test==F),sum(BreastCancerC$Test==T))

```
隨機將2/3的資料分到訓練組（Test==F），剩下1/3為測試組（Test==T〕。
可得訓練組案例數為456，測試組案例數為227。



##預測模型建立

####模型建立
```{r}

install.packages("rpart")
library(rpart)

BreastCancerC$Class<-factor(BreastCancerC$Class,levels=c("malignant","benign"))

set.seed(1000)          
fit<-rpart(Class~.,data=BreastCancerC[BreastCancerC$Test==F,]) 

install.packages("rpart.plot")
library(rpart.plot)

summary(fit)

prp(fit)
```
因為變數過多，這裡使用決測術演算法來建立模型。


##模型說明
由上述參數可知，以決策樹建立模型預測乳房腫瘤是否為陰性或良性，經最佳化後，所用到的參數為上圖的決策樹所示
使用病患資料來預測乳房腫瘤是否為陰性或良性，以決策樹模型預測是否為陰性，可得：
```{r}
install.packages("caret")
library(caret)
MinePred<-predict(fit,newdata = BreastCancerC[BreastCancerC$Test==T,],type = "class")
sensitivity(MinePred,BreastCancerC[BreastCancerC$Test==T,]$Class)#敏感度

specificity(MinePred,BreastCancerC[BreastCancerC$Test==T,]$Class)#特異性

posPredValue(MinePred,BreastCancerC[BreastCancerC$Test==T,]$Class)#陽性預測率

negPredValue(MinePred,BreastCancerC[BreastCancerC$Test==T,]$Class)#陰性預測率

```

