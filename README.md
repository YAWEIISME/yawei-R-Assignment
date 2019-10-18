---
title: "R_assignment"
output: html_document
---

## R Markdown
This is yawei's R assignment.
My R assignment will consist of three parts:
Inspect and analysis two data files. 
Additional analysis and visualization.
Reviewing two assignments from my two peers in class.
#loading fang_et_al_genotypes&snp_position
```{r}
library(tidyverse)
fang_et_al_genotypes <- read_tsv ("/Users/yaweili/yawei-R-Assignment/fang_et_al_genotypes.txt")

snp_position <- read_tsv ("/Users/yaweili/yawei-R-Assignment/snp_position.txt")
```
#Data inspection
#file.size
#the structure of the dataset
#number of columns, number of rows
```{r}
file.size("fang_et_al_genotypes")
file.size("snp_position")
dim(fang_et_al_genotypes)
dim(snp_position)
colnames(fang_et_al_genotypes)
colnames(snp_position)
rownames(fang_et_al_genotypes)
rownames(snp_position)
```
#Data processing

#for the fang

#filter the group into both maize and teosinte
```{r}
maize_fang_et_al_genotypes <- filter(fang_et_al_genotypes, Group == "ZMMLR" | Group == "ZMMIL" | Group == "ZMMMR")

teosinte_fang_et_al_genotypes <- filter(fang_et_al_genotypes, Group == "ZMPBA" | Group == "ZMPIL" | Group == "ZMPJA")
```
#we need to remove the first three columnes of the two groups.
#we need to extract the columnes of (1,3,4) from the data of snp
```{r}
fang_et_al_genotypes <- read_tsv ("/Users/yaweili/yawei-R-Assignment/fang_et_al_genotypes.txt")
snp_position <- read_tsv ("/Users/yaweili/yawei-R-Assignment/snp_position.txt")
maize_fang_et_al_genotypes <- filter(fang_et_al_genotypes, Group == "ZMMLR" | Group == "ZMMIL" | Group == "ZMMMR")
teosinte_fang_et_al_genotypes <- filter(fang_et_al_genotypes, Group == "ZMPBA" | Group == "ZMPIL" | Group == "ZMPJA")
maize_fang_et_al_genotypes <- maize_fang_et_al_genotypes[,-c(1,2,3)]#cut head
maize_fang_et_al_genotypes <- t (maize_fang_et_al_genotypes)
teosinte_fang_et_al_genotypes <- teosinte_fang_et_al_genotypes[,-c(1,2,3)]#Guillotine
teosinte_fang_et_al_genotypes <- t (teosinte_fang_et_al_genotypes)
snp_position <- snp_position[,-c(2,5:15)]#Guillotine
```


#remove the first row of SNP 


#Then we can combine both fang and snp files.
```{r}

maize_fang_et_al_genotypes <- cbind(snp_position, maize_fang_et_al_genotypes)

teosinte_fang_et_al_genotypes <- cbind(snp_position, teosinte_fang_et_al_genotypes)
```
#replace "?" to "-"
```{r}
maize_ded <- data.frame(lapply(maize_fang_et_al_genotypes, as.character), stringsAsFactors=FALSE)
maize_ded <- data.frame(sapply(maize_ded,function(x) {x <- gsub("?","-",x,fixed=TRUE)}))
teosinte_ded <- data.frame(lapply(teosinte_fang_et_al_genotypes, as.character), stringsAsFactors=FALSE)
teosinte_ded <- data.frame(sapply(teosinte_ded,function(x) {x <- gsub("?","-",x,fixed=TRUE)}))
rm(fang_et_al_genotypes)#clear up
rm(snp_position)
```



#Now we can start to creat files we want.
#For maize (Group = ZMMIL, ZMMLR, and ZMMMR in the third column of the fang_et_al_genotypes.txt file) we want 20 files in total:

#10 files (1 for each chromosome) with SNPs ordered based on increasing position values and with missing data encoded by this symbol: ?
```{r}
for (i in 1:10) {
  maize <- filter(maize_fang_et_al_genotypes, Chromosome == i)
  maize <- rename( maize,NA,"?")
  maize <- arrange(maize, Position)
  outpath <- "/Users/yaweili/yawei-R-Assignment/maize_increasing_data"
  nam <- sapply(
    names(maize),function(x){
                    paste("maize_in", i, ".csv", sep='')
    })
  out_filePath <- sapply(nam, function(x){
                     paste(outpath, x, sep='/')})
  write.csv(maize, file=out_filePath[i])
}
```


#10 files (1 for each chromosome) with SNPs ordered based on decreasing position values and with missing data encoded by this symbol: -


#1 file with all SNPs with unknown positions in the genome (these need not be ordered in any particular way)
```{r}
for (i in 1:10) {
  maize <- filter(maize_ded, Chromosome == i)
  maize <- arrange(maize, desc(Position))
  outpath <- "/Users/yaweili/yawei-R-Assignment/maize_decreasing_data/"
  nam <- sapply(
    names(maize),function(x){
                    paste("maize_de", i, ".csv", sep='')
    })
  out_filePath <- sapply(nam, function(x){
                     paste(outpath, x, sep='/')})
  write.csv(maize, file=out_filePath[i])
}
```

#1 file with all SNPs with multiple positions in the genome (these need not be ordered in any particular way)

#For teosinte (Group = ZMPBA, ZMPIL, and ZMPJA in the third column of the fang_et_al_genotypes.txt file) we want 20 files in total:

#10 files (1 for each chromosome) with SNPs ordered based on increasing position values and with missing data encoded by this symbol: ?
```{r}
for (i in 1:10) {
  teosinte <- filter(teosinte_fang_et_al_genotypes, Chromosome == i)
  teosinte <- rename(teosinte,NA,"?")
  teosinte <- arrange(teosinte, Position)
  outpath <- "/Users/yaweili/yawei-R-Assignment/teosinte_increasing_data/"
  nam <- sapply(
    names(teosinte),function(x){
                    paste("teosinte_in", i, ".csv", sep='')
    })
  out_filePath <- sapply(nam, function(x){
                     paste(outpath, x, sep='/')})
  write.csv(teosinte, file=out_filePath[i])
}
```


#10 files (1 for each chromosome) with SNPs ordered based on decreasing position values and with missing data encoded by this symbol: -
```{r}
for (i in 1:10) {
  teosinte <- filter(teosinte_ded, Chromosome == i)
  teosinte <- arrange(teosinte, desc(Position))
  outpath <- "Users/yaweili/yawei-R-Assignment/teosinte_decreasing_data/"
  nam <- sapply(
    names(teosinte),function(x){
                    paste("teosinte_dd", i, ".csv", sep='')
    })
  out_filePath <- sapply(nam, function(x){
                     paste(outpath, x, sep='/')})
  write.csv(teosinte, file=out_filePath[i])
}
```

#In conclusion, there are 40 files produced.
##Part II

#I need to use ggplot to visualize my data in this part. 
#But first I need to reshape the original data (make it tidy) using the melt command in the reshape2 package before attempting this part.
```{r}
if (!require("tidyverse")) install.packages("tidyverse")
library(tidyverse)
if (!require("reshape2")) install.packages("reshape2")
library(reshape2)
fang_et_al_genotypes <- read_tsv ("/Users/yaweili/yawei-R-Assignment/fang_et_al_genotypes.txt")
snp_position <- read_tsv ("/Users/yaweili/yawei-R-Assignment/snp_position.txt")
maize_fang_et_al_genotypes <- filter(fang_et_al_genotypes, Group == "ZMMLR" | Group == "ZMMIL" | Group == "ZMMMR")
teosinte_fang_et_al_genotypes <- filter(fang_et_al_genotypes, Group == "ZMPBA" | Group == "ZMPIL" | Group == "ZMPJA")
maize_fang_et_al_genotypes <- maize_fang_et_al_genotypes[,-c(1,2,3)]#cut head
maize_fang_et_al_genotypes <- t (maize_fang_et_al_genotypes)
teosinte_fang_et_al_genotypes <- teosinte_fang_et_al_genotypes[,-c(1,2,3)]#Guillotine
teosinte_fang_et_al_genotypes <- t (teosinte_fang_et_al_genotypes)
snp_position <- snp_position[,-c(2,5:15)]#Guillotine
arrange(snp_position, SNP_ID)
maize_fang_et_al_genotypes <- cbind(snp_position, maize_fang_et_al_genotypes)
teosinte_fang_et_al_genotypes <- cbind(snp_position, teosinte_fang_et_al_genotypes)
rm(fang_et_al_genotypes)#clear up
rm(snp_position)#clear up
setwd("/Users/yaweili/yawei-R-Assignment/")
```

#SNPs per chromosome

Plot the total number of SNPs in our dataset on each chromosome. Also plot the distribution of SNPs on chromosomes.
```{r}
for(i in 1:2){
  if(i==1){
  pp=ggplot(data = maize_fang_et_al_genotypes) + 
  geom_bar(mapping = aes(x = Chromosome))
print(pp)
ggsave(pp,filename = "maize_p.pdf",width = 3,height = 3)
dev.off()
getwd()
  }else{
  pp=ggplot(data = teosinte_fang_et_al_genotypes) + 
  geom_bar(mapping = aes(x = Chromosome))
print(pp)
ggsave(pp,filename = "teosinte_p.pdf",width = 3,height = 3)
dev.off()
getwd()
  }}
rm(i)
rm(pp)
for(i in 1:2){
  maize_fang_et_al_genotypes$Position <- as.numeric(maize_fang_et_al_genotypes$Position)
  teosinte_fang_et_al_genotypes$Position <- as.numeric(teosinte_fang_et_al_genotypes$Position)
  if(i==1){
  pp=ggplot(data = maize_fang_et_al_genotypes, mapping=aes(x=Chromosome, y=Position))+
      geom_point(alpha=0.1)
  print(pp)
  ggsave(pp,filename = "maize_distribution.pdf",width = 3,height = 3)
  dev.off()
  getwd()
          }else{
  pp=ggplot(data = teosinte_fang_et_al_genotypes, mapping=aes(x=Chromosome, y=Position))+
     geom_point(alpha=0.1)
  print(pp)
  ggsave(pp,filename = "teosinte_distribution.pdf",width = 3,height = 3)
  dev.off()
  getwd()
               }
             }
rm(pp)
rm(i)
```

#Missing data and amount of heterozygosity

Create a new column to indicate whether a particular site is homozygous (has the same nucleotide on both chromosomes (i.e., A/A, C/C, G/G, T/T) or heterozygous (otherwise)). Make a graph that shows the proportion of homozygous and heterozygous sites as well as missing data in each sample (you won't be able to see the sample names). Make another graph that shows the same data for each group. Normalize the height of individual bars using one of the ggplot "position adjustments" options.
```{r}
for(i in 1:2){
  if(i==1){
    maize_melt <-melt(maize_fang_et_al_genotypes,id.vars=c("SNP_ID","Chromosome", "Position"),variable.name = "Sample", value.name="Nucleotide")
    rm(maize_fang_et_al_genotypes)
nu <- data.frame(lapply(maize_melt, as.character), stringsAsFactors=FALSE)
nu <- data.frame(lapply(nu,function(x) {x <- gsub("A","1",x,fixed=TRUE)}))
nu <- data.frame(lapply(nu,function(x) {x <- gsub("C","2",x,fixed=TRUE)}))
nu <- data.frame(lapply(nu,function(x) {x <- gsub("G","3",x,fixed=TRUE)}))
nu <- data.frame(lapply(nu,function(x) {x <- gsub("T","4",x,fixed=TRUE)}))
nu <- data.frame(lapply(nu,function(x) {x <- gsub("?","0",x,fixed=TRUE)}))
nu$Nucleotide <- c(nu$Nucleotide)
maize_melt <- mutate(maize_melt, allele = case_when(nu$Nucleotide==2~ "homozygous",
                                                    nu$Nucleotide==11~ "homozygous",
                                                    nu$Nucleotide==9~ "homozygous",
                                                    nu$Nucleotide==6~ "homozygous",
                                                    nu$Nucleotide==1~ "miss",
                                                    nu$Nucleotide!=(1|2|11|9|6)~ "heterozygous")
                    )
           }else{ 
teosinte_melt<-melt(teosinte_fang_et_al_genotypes,id.vars=c("SNP_ID","Chromosome", "Position"),variable.name = "Sample", value.name="Nucleotide")
rm(teosinte_fang_et_al_genotypes)
nu <- data.frame(lapply(teosinte_melt, as.character), stringsAsFactors=FALSE)
nu <- data.frame(lapply(nu,function(x) {x <- gsub("A","1",x,fixed=TRUE)}))
nu <- data.frame(lapply(nu,function(x) {x <- gsub("C","2",x,fixed=TRUE)}))
nu <- data.frame(lapply(nu,function(x) {x <- gsub("G","3",x,fixed=TRUE)}))
nu <- data.frame(lapply(nu,function(x) {x <- gsub("T","4",x,fixed=TRUE)}))
nu <- data.frame(lapply(nu,function(x) {x <- gsub("?","0",x,fixed=TRUE)}))
nu$Nucleotide <- c(nu$Nucleotide)
teosinte_melt <- mutate(teosinte_melt, allele = case_when(nu$Nucleotide==2~ "homozygous",
                                                          nu$Nucleotide==11~ "homozygous",
                                                          nu$Nucleotide==9~ "homozygous",
                                                          nu$Nucleotide==6~ "homozygous",
                                                          nu$Nucleotide==1~ "miss",
                                                          nu$Nucleotide!=(1|2|11|9|6)~ "heterozygous")
                        )
                 }
       }
rm(nu)
rm(i)
```

```{r}
for(i in 1:2){
  if(i==1){
pp=ggplot(data = maize_melt) + 
  geom_bar(mapping = aes(x =Sample, fill=allele))
print(pp)
ggsave(pp,filename = "maize_proportion.pdf",width = 3,height = 3)
dev.off()
getwd()
          }else{
            pp=ggplot(data = teosinte_melt) + 
            geom_bar(mapping = aes(x =Sample, fill=allele))
            print(pp)
            ggsave(pp,filename = "teosinte_proportion.pdf",width = 3,height = 3)
            dev.off()
            getwd()
                }
              }
  rm(pp)
  rm(i)
```


```{r}
#proportion of homozygous and heterozygous sites as well as missing data in each Chromosome group
setwd("c:/Users/46554/github/R-Assignment/Tianqili-R-Assignment/submit/")
for(i in 1:2){
  if(i==1){
pp=ggplot(data = maize_melt) + 
  geom_bar(mapping = aes(x =Chromosome, fill=allele), position = 'fill')#Normalize the height of individual bars
print(pp)
ggsave(pp,filename = "maize_gro_proportion.pdf",width = 3,height = 3)
dev.off()
getwd()
          }else{
            pp=ggplot(data = teosinte_melt) + 
            geom_bar(mapping = aes(x =Chromosome, fill=allele), position = 'fill')#Normalize the height of individual bars
            print(pp)
            ggsave(pp,filename = "teosinte_gro_proportion.pdf",width = 3,height = 3)
            dev.off()
            getwd()
                }
              }
  rm(pp)
  rm(i)
```


#Your own visualization

Visualize one other feature of the dataset. The choice is up to you!
```{r}
for(i in 1:2){
  if(i==1){
pp=ggplot(data = maize_melt) + 
  geom_bar(mapping = aes(x =allele))
print(pp)
ggsave(pp,filename = "maize_allele.pdf",width = 3,height = 3)
dev.off()
getwd()
          }else{
            pp=ggplot(data = teosinte_melt) + 
            geom_bar(mapping = aes(x =allele))
            print(pp)
            ggsave(pp,filename = "teosinte_allele.pdf",width = 3,height = 3)
            dev.off()
            getwd()
                }
}
rm(list = ls())
```

