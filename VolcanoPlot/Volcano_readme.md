Creating and customizing volcano plot using MetaboAnalyst, Postgresql, and RStudio

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
- Open the table and then go to "Download" and download the "volcano.csv" file available. This file should contain the fold change, log2FoldChange, adjusted p value and log10p values needed to recreate the volcano plot in R.
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

```
