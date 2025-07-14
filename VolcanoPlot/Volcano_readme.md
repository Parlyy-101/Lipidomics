### Creating and customizing volcano plot using MetaboAnalyst, Postgresql, and RStudio

## Obtaining fold change, log2FoldChange, adjusted p value and log10p value Using MetaboAnalyst:


- Go to the home page of [MetaboAnalyst](https://dev.metaboanalyst.ca/home.xhtml).
- Click on "click to start".
- Click on "Statistical Analysis" which will take you to [this](https://dev.metaboanalyst.ca/MetaboAnalyst/upload/StatUploadView.xhtml) page.
  ![VolcanoPlot/Metaboanalyst1.png](https://github.com/Parlyy-101/Lipidomics/blob/main/VolcanoPlot/Metaboanalyst1.png)
- Upload the numerical matrix form of your data as a .csv file as seen [here](https://github.com/Parlyy-101/Lipidomics/blob/main/Input_files/PsA_lipids_BC.csv).
- Normalize  the data and then go to "Volcano plot" under "Statistics".
  ![img/Metaboanalyst3.png](https://github.com/Parlyy-101/Lipidomics/blob/main/img/Metaboanalyst3.png)
- Set the parameters as shown in this image:
  ![img/Metaboanalyst_volcano.png](https://github.com/Parlyy-101/Lipidomics/blob/main/img/Metaboanalyst_volcano.png)
- Open the table and then go to "Download" and download the "volcano.csv" file available. This file should contain the fold change, log2FoldChange, adjusted p value and log10p values needed to recreate the volcano plot in R. (Please make sure that the log2fc and -log10p values are correct by conducting a brief verification.)
- Make necessary changes to the headers to make the file more readable as shown [here](https://github.com/Parlyy-101/Lipidomics/blob/main/volcano.csv).


## Adding labels using SQL (Postgresql):


- To create a table use the following commands:
```sql 
CREATE TABLE volcano_plot 
(name text,
fc numeric,
log2fc numeric,
padj numeric,
log10p numeric);
```
- To import your data from the file named "volcano.csv", use these commands:
```sql
COPY volcano_plot(name, fc, log2fc, padj, log10p)
FROM 'path\name.csv'
DELIMITER ','
CSV HEADER;
```
 In my case, the code looks like this:
```sql
COPY volcano_plot(name, fc, log2fc, padj, log10p)
FROM 'D:\Work\GitHub\volcano.csv'
DELIMITER ','
CSV HEADER;
```
- We can add an additional column to this file that will define whether the lipid species is upregulated or downregulated. Additionally, we should also add a column that will be used in R to label the points in the volcano plot which are significantly deregulated.
```sql
SELECT *,
CASE WHEN log2fc >0.6 AND padj <0.05 THEN 'Upregulated'
     WHEN log2fc <-0.6 AND padj <0.05 THEN 'Downregulated'
	 ELSE 'Not_significant'
END as colour,
CASE WHEN log2fc >0.6 AND padj <0.05 THEN name
     WHEN log2fc <-0.6 AND padj <0.05 THEN name
	 ELSE null
END as label
FROM volcano_plot;
```
To download the output of this code, use the COPY command as follows:
```sql
Copy (SELECT *,
CASE WHEN log2fc >0.6 AND padj <0.05 THEN 'Upregulated'
     WHEN log2fc <-0.6 AND padj <0.05 THEN 'Downregulated'
	 ELSE 'Not_significant'
END as colour,
CASE WHEN log2fc >0.6 AND padj <0.05 THEN name
     WHEN log2fc <-0.6 AND padj <0.05 THEN name
	 ELSE null
END as label
FROM volcano_plot) To 'D:\Work\GitHub\volcano_labelled.csv' With CSV DELIMITER ',' HEADER;
```
- The file should look like [this](https://github.com/Parlyy-101/Lipidomics/blob/main/volcano_labelled.csv)
- Your file is now ready for RStudio!

## Creating volcano plot in RStudio using ggplot

- You will need to install the following packages in R:
```r
install.packages("tidyverse") 
install.packages("RColorBrewer") 
install.packages("ggrepel")
install.packages("tibble")
```
- Call the libraries as follows:
```r
library(tidyverse) # includes ggplot2, for data visualisation. dplyr, for data manipulation.
library(RColorBrewer) # for a colourful plot
library(ggrepel) # for annotations
library(data.table) # to read .csv files
library(tibble)
```
- Creating the plot:
```r
volcano <- fread("volcano_labelled.csv")
head(volcano)

ggplot(data = volcano, aes(x = log2fc, y = log10p, col = colour, label = label)) +
  geom_vline(xintercept = c(-0.6, 0.6), col = "gray", linetype = 'dashed') +
  geom_hline(yintercept = -log10(0.05), col = "gray", linetype = 'dashed') +
  geom_point(size = 2) + #to customize points
  scale_color_manual(values = c("#00AFBB", "grey","#bb0c00"))+ # to set the labels
  coord_cartesian(ylim = c(0, 15), xlim = c(-5,5)) + # to change scale of the axes
  labs(color = 'Key', #legend_title,
       x = expression("log"[2]*"FC"), y = expression("-log"[10]*"Adjusted p-value")) + # axis labels
  scale_x_continuous(breaks = seq(-5,5, 5)) + # to customise the breaks in the x axis
  ggtitle('Psoriasis vs Control') + # Plot title
  geom_text_repel(max.overlaps = Inf, size=3) +   theme_bw()
# the lines below can be used instead of theme_bw() to customize the figure further
# theme(panel.background = element_rect(fill = 'white', color = 'white'),
# panel.grid.major = element_line(color = 'white', linetype = 'solid'), 
# axis.line.x = element_line(size = 0.5),
# axis.line.y = element_line(size = 0.5),
# axis.text.x = element_text(face="bold"),
# axis.text.y = element_text(face="bold"),
# axis.text=element_text(color="black"),
# axis.ticks=element_line(size=1),
# axis.ticks.length=unit(.25, "cm"),
# text= element_text(size=20),                                             
# panel.border = element_rect(colour = "black", fill=NA, size=1))
```
Image should look something like this: 
![VolcanoPlot/volcano_plot.png](https://github.com/Parlyy-101/Lipidomics/blob/main/VolcanoPlot/volcano_plot.png)

- You can now customise it according to your needs.
