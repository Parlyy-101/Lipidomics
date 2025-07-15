### 2D PCA plot using RStudio
- Autoscale the numerical matrix using  MetaboAnalyst normalization page.
- Make two .csv files that look like this:
  1. [input file (for pca generation)](https://github.com/Parlyy-101/Lipidomics/blob/main/PCA/pca_input.csv)
     ![PCA/input_file.png](https://github.com/Parlyy-101/Lipidomics/blob/main/PCA/input_file.png)
     *Please note that the first row consists of the renamed lipid species.
  3. [file to specify groups and labels](https://github.com/Parlyy-101/Lipidomics/blob/main/PCA/pca_input_groups.csv)
     
     ![PCA/input_groups.png](https://github.com/Parlyy-101/Lipidomics/blob/main/PCA/input_groups.png)

- In R, install and call ggfortify, ggplot, ggrepel and data.table.
```r
install.packages("ggfortify")
install.packages("ggplot2")
install.packages("data.table")
install.packages("ggrepel")
```
 setting up libraries:
```r
library(ggfortify)
library(ggplot2)
library(data.table)
library(ggrepel)
```

- Create the plot using the following commands:
```r
input <- fread ("PCA_input.csv") #the input file
head(input)
groups <- fread ("PCA_input_groups.csv") #The groups and label file
head(groups)

pca_res <- prcomp(input, scale. = TRUE)
autoplot(pca_res, data = groups,  colour = 'Groups', frame = TRUE, frame.type = 'norm', size=3)+ #frame is used to create the ellipses
scale_colour_manual(values= c("#b51963","#5ba300"))+ #to colour the data points
  coord_cartesian(ylim = c(-.4, .4), xlim = c(-.4, .4)) + #to set the scales
  scale_x_continuous(breaks = seq(-.4,.4, .1))+  #to set breaks in x axis
  scale_y_continuous(breaks = seq(-.4,.4, .1)) + #to set breaks in y axis
  theme_bw()
```
