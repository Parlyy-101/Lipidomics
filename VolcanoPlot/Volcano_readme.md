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
  '````sql 
CREATE TABLE volcano_plot 
(name text,
fc numeric,
log2fc numeric,
padj numeric,
log10p numeric);
  '````

