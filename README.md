HW2_aengstrom28
===============
```{r}
# Getting data
> source("http://bioconductor.org/biocLite.R")
> library(Biobase)
> library(data.table)
> library(GEOquery)
> library(limma)
> gse_new <- getGEO(filename = "/Users/jjengstrom/Biostat-578/Data/GSE40812_series_matrix.txt.gz")
> setwd("~/aengstrom28")

#Cleaning-up data
> gds <- gse_new
> pd <- pData(gds)
> colnames(pd)
#Using monocyte-derived macrophage data from pData
> mm_pd <- pd[pd$source_name_ch1=="Monocyte-derived Macrophage",]
> mm_eset <- gds[,rownames(mm_pd)]
#Change "characteristics_ch1" to HCV status (viral load + or -)
> mm_pd$HCV <- gsub(".*: ", "", mm_pd$characteristics_ch1)
> mm_pd$HCV <- ifelse(mm_gds_pd$HCV=="Neg", "-", "+")
#Change "characteristics_ch1.2" to treatment (Mock or Poly IC) and characteristics_ch1.1 to cell (cell-type is macrophage)
> mm_pd$treatment <- gsub(".*: ", "", mm_pd$characteristics_ch1.2)
> mm_pd$cell <- gsub(".*:", "", mm_pd$characteristics_ch1.1)

#Set-up design matrix
> mm_matrix <-model.matrix(~0+HCV,mm_pd)
> fit_mm_matrix <- lmFit(mm_eset, mm_matrix)
> ebay_mm_matrix <- eBayes(fit_mm_matrix)
> TopHCV <- topTable(ebay_mm_matrix,adjust="BH")
> colnames(TopHCV)
> colnames(fit_mm_matrix$coef)
> TopHCV[,35]

#Set-up contrasts
> colnames(mm_matrix) <- c("Neg", "Pos")
> cont.matrix <- makeContrasts(Neg-Pos,levels=mm_matrix)
> rownames(cont_matrix)
> colnames(fit_mm_matrix$coef)
> colnames(fit_mm_matrix$coef) <- c("Neg", "Pos")
> fit2 <- contrasts.fit(fit_mm_matrix, cont_matrix)