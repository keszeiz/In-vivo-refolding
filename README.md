# In-vivo-refolding
In vivo luciferase assay analysis

############### Reshape data frame ###############
reshape_dataframe <- function(dataframe) {
  library(reshape2)
  melted <- melt(data=dataframe, measure.vars=3:ncol(dataframe))
  timelevels <- levels(melted$variable)
  #retrieve the time unit and store in "timeunit"
  melted$timeunit <- (sub("(X)([0-9])(.*)", "\\3", timelevels))[2]
  #Rename the new variable
  colnames(melted)[which( colnames(melted)=="variable")] <- "TimePoint"
  #eliminate the X from before the number (X1 -> 1)
  timelevels <- gsub("(X)([0-9])(.*)", "\\2", timelevels)
  levels(melted[,3]) <- timelevels
  return(melted)
}


############### Data quality plot ###############
data_quality <- function(dataframe) {
  library(ggplot2)
  p <- ggplot(data=dataframe, aes(TimePoint, value, colour=Celltype)) + 
    geom_point(size=3)+ facet_wrap( ~ Date) + theme_light()
  
  return(p)
}

############### Refolding dotplot ###############
refolding_dotplot <- function(dataframe) {
  #take the unit of time from the last column
  timeunit <- dataframe[1, ncol(dataframe)]
  #Take the mean of multiplicates
  meandata <- aggregate(data=dataframe, value ~ Date + Celltype + TimePoint, mean)
  #make an independent df out of only the references
  refdata <- meandata[meandata$TimePoint == "Reference", -(which( colnames(meandata)=="TimePoint"))] #the 3rd column "TimePoint" not needed here (it only states "Referece")
  colnames(refdata)[(which(colnames(refdata)=="value"))] <- "RefValue"
  #add the corresponding ref values in my meandata file, next to every value
  mergedata <- merge(meandata, refdata)
  #calculating normalized values
  mergedata$normvalue <- mergedata$value / mergedata$RefValue
  #plot
  library(ggplot2)
  xlab <- paste("Time of recovery ", "(", timeunit, ")", sep="")
  p <- ggplot(data=mergedata[mergedata$TimePoint != "Reference", ], 
              aes(TimePoint, normvalue*100, colour=Celltype, shape=Celltype)) + 
    geom_point(size=3) + geom_smooth(aes(group=Celltype), se=F) +
    labs(x=xlab, y="% Recovery of luciferase") +
    theme_light()
  return(p)
}

############## Fold change plot ###############
refolding_fold_dotplot <- function(dataframe) {
  #take the unit of time from the last column
  timeunit <- dataframe[1, ncol(dataframe)]
  #Take the mean of multiplicates
  meandata <- aggregate(data=dataframe, value ~ Date + Celltype + TimePoint, mean)
  #remove references from our dataframe
  smalldata <- meandata[meandata$TimePoint != "Reference", ]
  #make an independent dataframe out of only the 0h values
  refdata <- smalldata[smalldata$TimePoint == "0", -(which( colnames(meandata)=="TimePoint"))] #the 3rd column "TimePoint" not needed here (it only states "Referece")
  colnames(refdata)[(which(colnames(refdata)=="value"))] <- "RefValue"
  #add the corresponding ref values in my meandata file, next to every value
  mergedata <- merge(smalldata, refdata)
  #calculating normalized values
  mergedata$normvalue <- mergedata$value / mergedata$RefValue
  #plot
  library(ggplot2)
  xlab <- paste("Time of recovery ", "(", timeunit, ")", sep="")
  p <- ggplot(data=mergedata[mergedata$TimePoint != "0", ], 
              aes(TimePoint, normvalue, colour=Celltype, shape=Celltype)) + 
    geom_point(size=3) + geom_smooth(aes(group=Celltype), se=F) +
    labs(x=xlab, y="Fold recovery of luciferase") +
    theme_light()
  
  return(p)
}


############## Traditional refolding plot ###############
refolding_lineplot <- function(dataframe) {
  #take the unit of time from the last column
  timeunit <- dataframe[1, ncol(dataframe)]
  #Take the mean of multiplicates
  meandata <- aggregate(data=dataframe, value ~ Date + Celltype + TimePoint, mean)
  #make an independent df out of only the references
  refdata <- meandata[meandata$TimePoint == "Reference", -(which( colnames(meandata)=="TimePoint"))] #the 3rd column "TimePoint" not needed here (it only states "Referece")
  colnames(refdata)[(which(colnames(refdata)=="value"))] <- "RefValue"
  #add the corresponding ref values in my meandata file, next to every value
  mergedata <- merge(meandata, refdata)
  #calculating normalized values
  mergedata$normvalue <- mergedata$value / mergedata$RefValue
  #plot
  library(ggplot2)
  xlab <- paste("Time of recovery ", "(", timeunit, ")", sep="")
  library(ggpubr)
  p <- ggline(mergedata[mergedata$TimePoint != "Reference", ], x="TimePoint", y="normvalue", 
              add="mean_sd", color="Celltype", shape = "Celltype", size=0.75, point.size = 2)+
    stat_compare_means(aes(group = Celltype), hide.ns = T, label = "p.signif") +
    labs(x=xlab, y="% Recovery of luciferase")
  
  return(p)
}

