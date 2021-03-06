---
title: "Additional Figures for Thesis Chapter 'Compliance with Reporting Standards in Metabolomics'"
author: "Analysis by Rachel Spicer, github:RASpicer"
date: "03/09/2018"
output: 
  html_document:
    code_folding: hide
    number_sections: yes
    theme: cerulean
    keep_md: true
---



# Distribution of species across metabolomics data repositories

Load species data

```r
# Read csv of frequency of species per database
Species <- read.csv("../data/species.csv", check.names = FALSE)

# Replace all spaces with line graps for a cleaner plot
levels(Species$Database) <- gsub(" ", "\n", levels(Species$Database))
```

## The frequency of studies including different species in the metabolomics data repositories
For a species to be plotted as an individual band it must have been found in a minimum of ten studies across the repositories. Species found in <10 studies across all repositories are reported as “Other Species”.

```r
ggplot(Species, aes(x=Database, y=Frequency, fill=Species))  +
  geom_bar(colour="black", stat="identity", width = 0.7) +
  scale_y_continuous(expand = c(0, 0), limits = c(0, 600)) +
  scale_fill_manual(values = c('#CC6677','#AA4499','#44AA99','#117733', '#882255',"#88CCEE", '#332288'),
                   labels = c(expression(italic("Arabidopsis thaliana")), 
                               expression(italic("Escherichia coli")), 
                               expression(italic("Homo sapiens")),
                               expression(italic("Mus musculus")),
                               expression(italic("Oryza sativas")),
                               "Other Species",
                               expression(italic("Rattus norvegicus")))) +
  ylab("Number of Studies") +
  xlab("Repository") +
  theme_bw() +
  theme(axis.text = element_text(colour = "black"),
        legend.text.align = 0,
        #legend.text = element_text(face = "italic"),
        axis.line.x = element_line(color="black", size = 0.5),
        axis.line.y = element_line(color="black", size = 0.5),
        panel.grid.major = element_blank(),
        panel.grid.minor = element_blank(),
        panel.border = element_blank(),
        panel.background = element_blank()) 
```

<img src="figs/speciesfreq-1.png" style="display: block; margin: auto;" />

## The percentage of studies including different species in the metabolomics data repositories
For a species to be plotted as an individual band it must have been found in a minimum of ten studies across the repositories. Species found in <10 studies across all repositories are reported as “Other Species”.

```r
ggplot(Species, aes(x=Database, y=Percentage, fill=Species))  +
  geom_bar(colour="black", stat="identity", width = 0.7) +
  scale_y_continuous(expand = c(0, 0), limits = c(0, 105)) +
  scale_fill_manual(values = c('#CC6677','#AA4499','#44AA99','#117733', '#882255',"#88CCEE", '#332288'),
                    labels = c(expression(italic("Arabidopsis thaliana")), 
                               expression(italic("Escherichia coli")), 
                               expression(italic("Homo sapiens")),
                               expression(italic("Mus musculus")),
                               expression(italic("Oryza sativas")),
                               "Other Species",
                               expression(italic("Rattus norvegicus")))) +
  ylab("Studies (%)") +
  xlab("Repository") +
  theme_bw() +
  theme(axis.text = element_text(colour = "black"),
        legend.text.align = 0,
        #legend.text = element_text(face = "italic"),
        axis.line.x = element_line(color="black", size = 0.5),
        axis.line.y = element_line(color="black", size = 0.5),
        panel.grid.major = element_blank(),
        panel.grid.minor = element_blank(),
        panel.border = element_blank(),
        panel.background = element_blank()) 
```

<img src="figs/speciesper-1.png" style="display: block; margin: auto;" />

# Distribution of analytical techniques across metabolomics data repositories

The four repositories MeRy-B, MetaboLights, Metabolomics Workbench and MetaPhen are plotted. GNPS is not included as it has no requirements for reporting type of chromatography used, although it is exclusively a repository for MS data


```r
# Read csv of frequency of instruments per database
Instrument <- read.csv("../data/instruments.csv", check.names = FALSE)

# Replace all spaces with line graps for a cleaner plot
levels(Instrument$Repository) <- gsub(" ", "\n", levels(Instrument$Repository))
```

## The frequency of studies including different analytical techniques in the metabolomics data repositories

```r
ggplot(Instrument, aes(x = Repository, y = Frequency, fill = Instrument)) + 
    geom_bar(colour = "black", stat = "identity", width = 0.7) + scale_y_continuous(expand = c(0, 
    0), limits = c(0, 400)) + scale_fill_manual(values = c("#fb8072", "#fdb462", 
    "#bebada", "#80b1d3", "#8dd3c7", "#ffffb3")) + ylab("Number of Studies") + 
    theme_bw() + theme(axis.text = element_text(colour = "black"), axis.line.x = element_line(color = "black", 
    size = 0.5), axis.line.y = element_line(color = "black", size = 0.5), panel.grid.major = element_blank(), 
    panel.grid.minor = element_blank(), panel.border = element_blank(), panel.background = element_blank())
```

<img src="figs/instrumentsfreq-1.png" style="display: block; margin: auto;" />

## The percentage of studies including different analytical techniques in the metabolomics data repositories


```r
ggplot(Instrument, aes(x = Repository, y = Percentage, fill = Instrument)) + 
    geom_bar(colour = "black", stat = "identity", width = 0.7) + scale_y_continuous(expand = c(0, 
    0), limits = c(0, 105)) + scale_fill_manual(values = c("#fb8072", "#fdb462", 
    "#bebada", "#80b1d3", "#8dd3c7", "#ffffb3")) + ylab("Studies (%)") + theme_bw() + 
    theme(axis.text = element_text(colour = "black"), axis.line.x = element_line(color = "black", 
        size = 0.5), axis.line.y = element_line(color = "black", size = 0.5), 
        panel.grid.major = element_blank(), panel.grid.minor = element_blank(), 
        panel.border = element_blank(), panel.background = element_blank())
```

<img src="figs/instrumentsper-1.png" style="display: block; margin: auto;" />

# GNPS and MSI Standards
GNPS does not attempt to comply with the MSI standards. For the paper <b>Compliance with Minimum Information Guidelines in Public Metabolomics Repositories</b>, only repositories that complied with the standards were examined. However for extended work in <b>Fit for purpose? A metascientific analysis of metabolomics data in public repositories</b>, it was included.

## Functions

Code for extracting only the minimal or best practice/ optional reporting standards.


```r
# Calculate the percentage compliance with the minimal reporting standards for clinical data
MinPerClin <- function(x) {
  # Calculate totals
  Totals = as.data.frame(colSums(x, na.rm = TRUE))
  # Extract only minimal reporting standards
  MinRep = c(Totals[1:7,], Totals[14:22,], Totals[42:44,], Totals[46,], Totals[48,], Totals[55,])
  # Calculate percentage
  Per = MinRep/(nrow(x)) * 100
  Per = round(Per, digits = 2)
  # Get column names
  Names = c(colnames(x[,1:7]), colnames(x[,14:22]), colnames(x[,42:44]), colnames(x[46]), colnames(x[48]), colnames(x[55]))
  MinMeta = as.data.frame(cbind(Names, Per))
  colnames(MinMeta) = c("Metadata", "Percentage")
  MinMeta$Percentage = as.numeric(as.character(MinMeta$Percentage))
  # Return percentage
  return(MinMeta)
}

# Calculate the percentage compliance with the minimal reporting standards for preclinical data
MinPerPreClin <- function(x,y) {
  # Calculate totals
  Totals = as.data.frame(colSums(x, na.rm = TRUE))
  # Extract only minimal reporting standards
  MinRep = c(Totals[1,], Totals[3:7,], Totals[14:17,], Totals[22:23,], Totals[25:30,], Totals[32:34,], Totals[36:37,], Totals[39:45,])
  # Calculate percentage
  Per = MinRep/(nrow(x)) * 100
  Per = round(Per, digits = 2)
  # Get column names
  Names = c(colnames(x[1]), colnames(x[,3:7]), colnames(x[,14:17]), colnames(x[,22:23]), colnames(x[,25:30]), colnames(x[,32:34]), colnames(x[,36:37]), colnames(x[,39:45]))
  MinMeta = as.data.frame(cbind(Names, Per))
  colnames(MinMeta) = c("Metadata", "Percentage")
  MinMeta$Percentage = as.numeric(as.character(MinMeta$Percentage))
  # Return percentage
  return(MinMeta)
}

# Calculate the percentage compliance with the minimal reporting standards for microbial/cell line data
MinPerCellLine <- function(x) {
  # Calculate totals
  Totals = as.data.frame(colSums(x, na.rm = TRUE))
  # Extract only minimal reporting standards
  MinRepSt = Totals[1:15,]
  # Calculate percentage
  Per = MinRepSt/(nrow(x)) * 100
  Per = round(Per, digits = 2)
  # Get column names
  Names = c(colnames(x[,1:15]))
  MinMeta = as.data.frame(cbind(Names, Per))
  colnames(MinMeta) = c("Metadata", "Percentage")
  MinMeta$Percentage = as.numeric(as.character(MinMeta$Percentage))
  return(MinMeta)
}

# Calculate the percentage compliance with the minimal reporting standards for plant studies
MinPerPlant <- function(x) {
  # Calculate totals
  Totals = as.data.frame(colSums(x, na.rm = TRUE))
  # Calculate percentage
  Per = Totals/(nrow(x)) * 100
  Per = round(Per, digits = 2)
  # Get column names
  Names = colnames(x)
  MinMeta = as.data.frame(cbind(Names, Per))
  colnames(MinMeta) = c("Metadata", "Percentage")
  MinMeta$Percentage = as.numeric(as.character(MinMeta$Percentage))
  # Return percentage
  return(MinMeta)
}

# Calculate the percentage compliance with the optional reporting standards for microbial/cell line data
OpPerCellLine <- function(x) {
  # Calculate totals
  Totals = as.data.frame(colSums(x, na.rm = TRUE))
  # Extract only optional reporting standards
  OpRep = Totals[16:54,]
  # Calculate percentage
  Per = OpRep/(nrow(x)) * 100
  Per = round(Per, digits = 2)
  # Get column names
  Names = c(colnames(x[,16:54]))
  OpMeta = as.data.frame(cbind(Names, Per))
  colnames(OpMeta) = c("Metadata", "Percentage")
  OpMeta$Percentage = as.numeric(as.character(OpMeta$Percentage))
  # Return percentage
  return(OpMeta)
}

# Calculate the percentage compliance with the optional reporting standards for clinical data
OpPerClin <- function(x) {
  # Calculate totals
  Totals = as.data.frame(colSums(x, na.rm = TRUE))
  OpRep = c(Totals[8:13,], Totals[23:41,], Totals[45,], Totals[47,], Totals[49:54,])
  # Calculate percentage
  Per = OpRep/(nrow(x)) * 100
  Per = round(Per, digits = 2)
  # Get column names
  Names = c(colnames(x[,8:13]), colnames(x[,23:41]), colnames(x[45]), colnames(x[47]), colnames(x[,49:54]))
  OpMeta = as.data.frame(cbind(Names, Per))
  colnames(OpMeta) = c("Metadata", "Percentage")
  OpMeta$Percentage = as.numeric(as.character(OpMeta$Percentage))
  # Return percentage
  return(OpMeta)
}

# Calculate the percentage compliance with the optional reporting standards for preclinical data
OpPerPreClin <- function(x) {
  # Calculate totals
  Totals = as.data.frame(colSums(x, na.rm = TRUE))
  # Extract only minimal reporting standards
  OpRep = c(Totals[2,], Totals[8:13,], Totals[18:21,], Totals[24,], Totals[31,], Totals[35,], Totals[38,], Totals[46,])
  # Calculate percentage
  Per = OpRep/(nrow(x)) * 100
    Per = round(Per, digits = 2)
  # Get column names
  Names = c(colnames(x[2]), colnames(x[,8:13]), colnames(x[,18:21]),colnames(x[24]), colnames(x[31]), colnames(x[35]), colnames(x[38]), colnames(x[46]))
  OpMeta = as.data.frame(cbind(Names, Per))
  colnames(OpMeta) = c("Metadata", "Percentage")
  OpMeta$Percentage = as.numeric(as.character(OpMeta$Percentage))
  # Return percentage
  return(OpMeta)
}

# ggplot theme
theme_adjbw <- function(base_size = 12, base_family = ""){
  theme_bw() +
    theme(
      axis.text = element_text(colour = "black"),
      axis.line.x = element_line(color="black", size = 0.5),
      axis.line.y = element_line(color="black", size = 0.5),
      # Remove gridlines and borders
      panel.grid.major = element_blank(),
      panel.grid.minor = element_blank(),
      panel.border = element_blank(),
      panel.background = element_blank()
    )
}
```

## Data Processing

Data for the compliance to one of the four sets of reporting standards is first split by repository. The percentage of studies that comply with each reporting standard is then calculated. Reporting standards are then divided into minimal and best practice/optional.


```r
# read compliance data for each study type
Mouse <- read.csv("../data/MousePreclinicalRawData4Analysis.csv", check.names = FALSE)
Plant <- read.csv("../data/ArabidopsisPlantRawData4Analysis.csv", check.names = FALSE)
HumanClin <- read.csv("../data/HumanClinicalRawData4Analysis.csv", check.names = FALSE)
HumanCell <- read.csv("../data/HumanCellRawData4Analysis.csv", check.names = FALSE)

# Extract only MetaboLights studies
MLMouse <- Mouse[grep("MetaboLights", Mouse$`Study Repository`), ]
MLPlant <- Plant[grep("MetaboLights", Plant$`Study Repository`), ]
MLHumanClin <- HumanClin[grep("MetaboLights", HumanClin$`Study Repository`), 
    ]
MLHumanCell <- HumanCell[grep("MetaboLights", HumanCell$`Study Repository`), 
    ]

# Extract only Metabolomics Workbench studies
MWMouse <- Mouse[grep("Metabolomics Workbench", Mouse$`Study Repository`), ]
MWPlant <- Plant[grep("Metabolomics Workbench", Plant$`Study Repository`), ]
MWHumanClin <- HumanClin[grep("Metabolomics Workbench", HumanClin$`Study Repository`), 
    ]
MWHumanCell <- HumanCell[grep("Metabolomics Workbench", HumanCell$`Study Repository`), 
    ]

# Extract only MetaPhen studies
MPPlant <- Plant[grep("MetaPhen", Plant$`Study Repository`), ]

# Extract only MeRy-B studies
MeRPlant <- Plant[grep("MeRy-B", Plant$`Study Repository`), ]

# Extract only GNPS studies
GNPSMouse <- Mouse[grep("GNPS", Mouse$`Study Repository`), ]
GNPSHumanClin <- HumanClin[grep("GNPS", HumanClin$`Study Repository`), ]
GNPSHumanCell <- HumanCell[grep("GNPS", HumanCell$`Study Repository`), ]

# Minimal - MetaboLights
MLHumanCellP <- MinPerCellLine(MLHumanCell[, -(1:2)])
MLHumanCellP$Order <- rep(1)
MLHumanClinP <- MinPerClin(MLHumanClin[, -(1:2)])
MLHumanClinP$Order <- rep(2)
MLMouseP <- MinPerPreClin(MLMouse[, -(1:2)])
MLMouseP$Order <- rep(3)
MLPlantP <- MinPerPlant(MLPlant[, -(1:2)])
MLPlantP$Order <- rep(4)

# Minimal - Metabolomics Workbench
MWHumanCellP <- MinPerCellLine(MWHumanCell[, -(1:2)])
MWHumanCellP$Order <- rep(1)
MWHumanClinP <- MinPerClin(MWHumanClin[, -(1:2)])
MWHumanClinP$Order <- rep(2)
MWMouseP <- MinPerPreClin(MWMouse[, -(1:2)])
MWMouseP$Order <- rep(3)
MWPlantP <- MinPerPlant(MWPlant[, -(1:2)])
MWPlantP$Order <- rep(4)

# Minimal - MetaPhen
MPPlantP <- MinPerPlant(MPPlant[, -(1:2)])
MPPlantP$Order <- rep(1)

# Minimal - MeRy-B
MeRPlantP <- MinPerPlant(MeRPlant[, -(1:2)])
MeRPlantP$Order <- rep(4)

# Minimal - GNPS
GNPSHumanCellP <- MinPerCellLine(GNPSHumanCell[, -(1:2)])
GNPSHumanCellP$Order <- rep(1)
GNPSHumanClinP <- MinPerClin(GNPSHumanClin[, -(1:2)])
GNPSHumanClinP$Order <- rep(2)
GNPSMouseP <- MinPerPreClin(GNPSMouse[, -(1:2)])
GNPSMouseP$Order <- rep(3)

# Best Practice - MetaboLights
MLHumanCellOP <- OpPerCellLine(MLHumanCell[, -(1:2)])
MLHumanCellOP$Order <- rep(1)
MLHumanClinOP <- OpPerClin(MLHumanClin[, -(1:2)])
MLHumanClinOP$Order <- rep(2)
MLMouseOP <- OpPerPreClin(MLMouse[, -(1:2)])
MLMouseOP$Order <- rep(3)

# Best Practice - Metabolomics Workbench
MWHumanCellOP <- OpPerCellLine(MWHumanCell[, -(1:2)])
MWHumanCellOP$Order <- rep(1)
MWHumanClinOP <- OpPerClin(MWHumanClin[, -(1:2)])
MWHumanClinOP$Order <- rep(2)
MWMouseOP <- OpPerPreClin(MWMouse[, -(1:2)])
MWMouseOP$Order <- rep(3)

# Best Practice - GNPS
GNPSHumanCellOP <- OpPerCellLine(GNPSHumanCell[, -(1:2)])
GNPSHumanCellOP$Order <- rep(1)
GNPSHumanClinOP <- OpPerClin(GNPSHumanClin[, -(1:2)])
GNPSHumanClinOP$Order <- rep(2)
GNPSMouseOP <- OpPerPreClin(GNPSMouse[, -(1:2)])
GNPSMouseOP$Order <- rep(3)
```

## Compliance to the MSI minimum reporting standards in MetaboLights.

Combined violin and dot plots showing the percentage compliance with the MSI minimum reporting standards within the MetaboLights repository. Red ‘+’ indicate outliers; each ‘dot’ represents compliance to a single reporting standard. Figure was adapted from Spicer *et al.* (2017).


```r
ML <- rbind(MLHumanCellP, MLHumanClinP, MLMouseP, MLPlantP)
ML$Order <- as.factor(ML$Order)
ggplot(ML, aes(x = Order, y = Percentage)) + geom_violin(fill = "#7fc97f", outlier.shape = 3, 
    outlier.color = "red") + geom_boxplot(width = 0.1, fill = "#7fc97f") + geom_jitter() + 
    scale_x_discrete(labels = c(expression(italic("In vitro")), "Clinical", 
        "Pre-clinical", "Plant")) + scale_y_continuous(expand = c(0, 0), limits = c(-1, 
    101)) + theme_adjbw() + xlab("MSI Guideline") + ylab("Compliance (%)")
```

<img src="figs/MLMin-1.png" style="display: block; margin: auto;" />

## Compliance to the MSI minimum reporting standards in Metabolomics Workbench.

Combined violin and dot plots showing the percentage compliance with the MSI minimum reporting standards within the Metabolomics Workbench repository. Red ‘+’ indicate outliers; each ‘dot’ represents compliance to a single reporting standard. Figure was adapted from Spicer *et al.* (2017).


```r
MW <- rbind(MWHumanCellP, MWHumanClinP, MWMouseP, MWPlantP)
MW$Order <- as.factor(MW$Order)
ggplot(MW, aes(x = Order, y = Percentage)) + geom_violin(fill = "#beaed4") + 
    geom_boxplot(width = 0.1, fill = "#beaed4", outlier.shape = 3, outlier.color = "red") + 
    geom_jitter() + scale_x_discrete(labels = c(expression(italic("In vitro")), 
    "Clinical", "Pre-clinical", "Plant")) + theme_adjbw() + scale_y_continuous(expand = c(0, 
    0), limits = c(-1, 101)) + xlab("MSI Guideline") + ylab("Compliance (%)")
```

<img src="figs/MWMin-1.png" style="display: block; margin: auto;" />

## Compliance to the MSI minimum reporting standards in GNPS.

Combined violin and dot plots showing the percentage compliance with the MSI minimum reporting standards within the GNPS repository. Red ‘+’ indicate outliers; each ‘dot’ represents compliance to a single reporting standard.


```r
GNPS <- rbind(GNPSHumanCellP, GNPSHumanClinP, GNPSMouseP)
GNPS$Order <- as.factor(GNPS$Order)
ggplot(GNPS, aes(x = Order, y = Percentage)) + geom_violin(fill = "#386cb0") + 
    geom_boxplot(width = 0.1, fill = "#386cb0", outlier.shape = 3, outlier.color = "red") + 
    geom_jitter() + scale_x_discrete(labels = c(expression(italic("In vitro")), 
    "Clinical", "Pre-clinical")) + theme_adjbw() + scale_y_continuous(expand = c(0, 
    0), limits = c(-1, 101)) + xlab("MSI Guideline") + ylab("Compliance (%)")
```

<img src="figs/GNPSMin-1.png" style="display: block; margin: auto;" />

## Compliance to Best Practice MSI Reporting Standards in MetaboLights
Combined box-and-whisker and dot plot showing the percentage compliance with the MSI best practice/optional reporting standards within the MetaboLights repository. Red `+' indicate outliers. Figure was adapted from Spicer *et al.* (2017).


```r
MLO <- rbind(MLHumanCellOP, MLHumanClinOP, MLMouseOP)
MLO$Order <- as.factor(MLO$Order)
ggplot(MLO, aes(x = Order, y = Percentage)) + geom_violin(fill = "#7fc97f") + 
    geom_boxplot(width = 0.1, fill = "#7fc97f", outlier.shape = 3, outlier.color = "red") + 
    geom_jitter() + scale_x_discrete(labels = c(expression(italic("In vitro")), 
    "Clinical", "Pre-clinical", "Plant")) + scale_y_continuous(expand = c(0, 
    0), limits = c(-1, 101)) + theme_adjbw() + xlab("MSI Guideline") + ylab("Compliance (%)")
```

<img src="figs/MLOp-1.png" style="display: block; margin: auto;" />

## Compliance to Best Practice MSI Reporting Standards in Metabolomics Workbench
Combined box-and-whisker and dot plot showing the percentage compliance with the MSI best practice/optional reporting standards within the Metabolomics Workbench Repository. Red `+' indicate outliers. Figure was adapted from Spicer *et al.* (2017).


```r
MWO <- rbind(MWHumanCellOP, MWHumanClinOP, MWMouseOP)
MWO$Order <- as.factor(MWO$Order)
ggplot(MWO, aes(x = Order, y = Percentage)) + geom_violin(fill = "#beaed4") + 
    geom_boxplot(width = 0.1, fill = "#beaed4", outlier.shape = 3, outlier.color = "red") + 
    geom_jitter() + scale_x_discrete(labels = c(expression(italic("In vitro")), 
    "Clinical", "Pre-clinical", "Plant")) + theme_adjbw() + scale_y_continuous(expand = c(0, 
    0), limits = c(-1, 101)) + xlab("MSI Guideline") + ylab("Compliance (%)")
```

<img src="figs/MWOp-1.png" style="display: block; margin: auto;" />


## Compliance to the MSI best practice or optional reporting standards in GNPS.
Compliance to the MSI reporting standards in the GNPS Repository. Combined violin and dot plots showing the percentage compliance with the MSI best practice or optional standards. Red ‘+’ indicate outliers; each ‘dot’ represents compliance to a single reporting standard. 

```r
GNPSO <- rbind(GNPSHumanCellOP, GNPSHumanClinOP, GNPSMouseOP)
GNPSO$Order <- as.factor(GNPSO$Order)
ggplot(GNPSO, aes(x = Order, y = Percentage)) + geom_violin(fill = "#386cb0") + 
    geom_boxplot(width = 0.1, fill = "#386cb0", outlier.shape = 3, outlier.color = "red") + 
    geom_jitter() + scale_x_discrete(labels = c(expression(italic("In vitro")), 
    "Clinical", "Pre-clinical")) + theme_adjbw() + scale_y_continuous(expand = c(0, 
    0), limits = c(-1, 101)) + xlab("MSI Guideline") + ylab("Compliance (%)")
```

<img src="figs/GNPSOp-1.png" style="display: block; margin: auto;" />

## Comparison of Compliance to Minimal and Best Practice Reporting Standards in GNPS
Box-and-whisker plot showing the percentage compliance to the minimal and optional/best practice reporting standards inGNPS. Minimal reporting standards are indicated in blue, optional/best practice reporting standards are pink and red ‘+’ indicate outliers.


```r
# Compare minimal and optional reporting standards within databases
# MetaboLights
GNPS$Req <- rep("Minimal")
GNPSO$Req <- rep("Optional")
GNPSComp <- rbind(GNPS, GNPSO)
GNPSComp$Order <- as.factor(GNPSComp$Order)
# Plot box and Whisker diagram
ggplot(GNPSComp, aes(x=Order, y=Percentage, fill = Req))  + 
  geom_boxplot(outlier.shape = 3, outlier.color = "red")  +
  scale_x_discrete(labels = c(expression(italic("In vitro")),
                              "Clinical",
                              "Pre-clinical",
                              "Plant")) +
  theme_bw() +
  theme(
    axis.text = element_text(colour = "black"),
    axis.line.x = element_line(color="black", size = 0.5),
    axis.line.y = element_line(color="black", size = 0.5),
    # Remove gridlines and borders
    panel.grid.major = element_blank(),
    panel.grid.minor = element_blank(),
    panel.border = element_blank(),
    panel.background = element_blank(),
    legend.title = element_blank(),
    legend.position="top") +
  scale_y_continuous(expand = c(0, 0), limits = c(-1, 100)) +
  xlab("MSI Guideline") +
  ylab("Compliance (%)") +
  scale_fill_manual(values = c("#386cb0", "#fb9a99"))
```

<img src="figs/GNPSCom-1.png" style="display: block; margin: auto;" />


## Compliance to Plant MSI Reporting Standards. 
Combined box-and-whisker and dot plots showing the percentage compliance of *A. thaliana* studies to the plant MSI minimum reporting standards within the MeRy-B, MetaboLights, Metabolomics Workbench and MetaPhen repositories. Red ‘+’ indicate outliers; each “dot” represents compliance to a single reporting standard. Figure was adapted from Spicer *et al.* (2017).

```r
MLPlantP$Repository <- rep("MetaboLights")
MWPlantP$Repository <- rep("Metabolomics Workbench")
MPPlantP$Repository <- rep("MetaPhen")
MeRPlantP$Repository <- rep("MeRy-B")
Plant <- rbind(MLPlantP, MWPlantP, MPPlantP, MeRPlantP)
Plant$Repository <- as.factor(Plant$Repository)
levels(Plant$Repository) <- gsub(" ", "\n", levels(Plant$Repository))
ggplot(Plant, aes(x=Repository, y=Percentage, fill=Repository))  + 
  geom_violin() +
  geom_boxplot(width=0.1, outlier.shape = 3, outlier.color = "red")  +
  geom_jitter() +
  theme_bw() +
  theme(
    axis.text = element_text(colour = "black"),
    axis.line.x = element_line(color="black", size = 0.5),
    axis.line.y = element_line(color="black", size = 0.5),
    # Remove gridlines and borders
    panel.grid.major = element_blank(),
    panel.grid.minor = element_blank(),
    panel.border = element_blank(),
    panel.background = element_blank(),
    legend.position="none") +
  scale_y_continuous(expand = c(0, 0), limits = c(-1, 101)) +
  ylab("Compliance (%)") +
  scale_fill_manual(values = c("#fdc086","#7fc97f", "#beaed4", "#ffff99"))
```

<img src="figs/plantfig-1.png" style="display: block; margin: auto;" />
