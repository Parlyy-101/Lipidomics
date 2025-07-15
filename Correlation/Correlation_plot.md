## Creating correlation plot using corrplot and hmisc in R
- Prepare your input file which should have all the lipids that you want to analyse for correlation. It should look like [this](https://github.com/Parlyy-101/Lipidomics/blob/main/Correlation/correlation_input.csv).
  ![Correlation/correlation_input.png](https://github.com/Parlyy-101/Lipidomics/blob/main/Correlation/correlation_input.png)

- Install and call the packages "data.table", "corrplot", and "hmisc"
```r
library(data.table)
library(corrplot)
library(Hmisc)
```
- Run the following commands to obtain the plot:
```r
W <- fread ("correlation_input.csv")
head(W)
col <- colorRampPalette(c( "#4477AA", "#77AADD", "#FFFFFF", "#EE9988","#BB4444")) #to change color range
WH = cor(W, use = "complete.obs",
         method = "pearson") #to define the type of correlation test
corrplot(WH, type = "lower", order= "hclust",col=col(200),
         tl.col = "black", tl.srt = 45,
         tl.cex=0.5) #text size
```
