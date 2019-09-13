# User guide to refolding functions
## Background

These functions were built to analyse data generated in _in vivo_ luciferase refolding experiments.
Briefly, during these experiments, luciferase is expressed in cells. Cells are subjected to a heat-shock which inactivates luciferase, then allowed to recover on 37°C for different periods of time (e.g. for 1, 2 and 3 hours). During this time, luciferase can refold, something that can be enhanced by molecular chaperones.
Cells are then lysed, and luciferase activity of differencially treated cells are measured.

 ## How to prepare your input file

Copy+paste the data of your independent experiments and different cell types/conditions in one file. 
From one experiment, the amount of data lines generated depends on the amount of different cells lines and conditions, and the number of multiplicates used.
E.g. If you have one WT and one KO cell lines, and you do your measurements in triplicates, 2x3=6 lines of data will be generated.
The next 6 lines that you produce the next time you perform the experiments can be pasted directly under the lines corresponding to the first experiments.
The first colunm is there to indicate which experiment that particular line corresponds to.

Your data can containg as many multiplicates, refolding time points, cell types/conditions or independent experiments, as you wish.

Your data structure should be the following: 
Columns containing background information: 

__- Column1: "Date" to identify independent experiments. The content of this column does not have to be actual dates, it can be anything that uniquely identifies independent experiments.__

__- Column2: "Celltype" to identify the cell types, or different conditions, such as the addition of drugs.__

__Then columns containing measurments:__

__- Column3: "Reference" containing the non-heat-shocked control measurements__

__- The rest of the columns contain the different time points of recovery, each column representing one time point. As many time point can be included as needed. The name of the column should be a number followed by the unit of measurement, without any characters in between (e.g. "2hours"). All your columns should indicate the same time unit (e.g. Do not give the 1st column in minutes and the rest in hours).__

Please see the uploaded input file that serves as an example.

Input files should then be saved in any wished format for R to read (e.g. txt, csv, tsv).
The input file can be read, e.g. by setting the working directory to the directory where the input file can be found, then reading the file with the function `read.table()` as described [here](http://www.r-tutor.com/r-introduction/data-frame/data-import).

 ## How to read input file

Example on how to read a file:
`getwd()` to ask R what is the current working directory (wd). The output can be used as a template to input wd:
`setwd("H:/inputfiles")`

Then the file can be read:

`mydata <- read.table("HeLa_minputfile.csv", sep=",", header=TRUE)` 

From a csv input file, where values are separated with a comma, this code generates a dataframe called mydata.

 ## How to use these functions and save figures

To use the functions, one has to first install the packages `reshape2`, `ggplot2` and `ggpubr` using the function `install.packages()`.
The imported dataframe "mydata" can be processed with the function `reshape_dataframe()`. The output file of this function will be the input file of all other functions. The output files of all other functions are figures, that can be saved to the working directory e.g. with the following code line:
`ggsave(filename = "myfigure.pdf", plot = Figure1, scale=0.5, useDingbats=FALSE)`
where useDingbats=FALSE is required correct opening in Adobe Illustrator when the wished output is pdf.

Each of the figures can be further modified as required according to the [ggplot](https://uc-r.github.io/ggplot_intro) syntax. 
