---
title: "1928-1969間，小兒麻痺在美國各州的發生率變化"
output: github_document
---

##資料前處理
把資料讀進來
```{r}
polio<-read.csv("POLIO_Incidence.csv",stringsAsFactors = F)
head(polio)
```

將寬表格轉為長表格
```{r}
library(ggplot2)
library(reshape)
polio.m<-melt(polio,id.vars = c('YEAR','WEEK'))
head(polio.m)
```

處理缺值
```{r}
polio.m[polio.m$value=="-",]$value<-NA #處理缺值,將"-"轉為NA
polio.m$value<-as.numeric(as.character(polio.m$value)) #將value欄位轉為數字
```

計算年度發生率
```{r}
polio.sumYear<- #各州各年度加總，計算該年度的總發生率
    aggregate(value~YEAR+variable,data=polio.m,FUN=sum,na.rm=F)
head(polio.sumYear)
```


##視覺畫呈現
題目要求為1928-1969間小兒麻痺在美國各洲的發生率變化,因此我選擇使用Heatmap來畫圖，表示需要用三維的圖來展現各洲每年的發生率，x軸為年度，y軸則為各州的名字，每個點則放小兒麻痺的發生率，藉此就能清楚比較每個洲每年的發生率之間的關係，也可以清楚看到在1955年之後，發生率都有變小的趨勢。
```{r}

library(ggplot2)
ggplot(polio.sumYear, aes(YEAR, variable)) + #aes(x,y)
    geom_tile(aes(fill = value),colour = "white")+
  geom_vline(xintercept = 1955)+ #geom_tile: 區塊著色
    scale_fill_gradient(low = "white",high = "black") #數值低：白色

```

圖形說明:
 上圖為美國在1928-1969年間，小兒麻痺在各州的發生率變化。可以發現發生率每年都有慢慢成長的趨勢，但是自從1955年開始有減少的趨勢是因為小兒麻痺的疫苗發行而帶來減少的趨勢,甚至有達到0的部分，可見疫苗是有抑制的效果。
