# yawei-R-Assignment
## R Markdown
This is yawei's R assignment.
My R assignment will consist of three parts:
Inspect and analysis two data files. 
Additional analysis and visualization.
Reviewing two assignments from my two peers in class.
#loading fang_et_al_genotypes&snp_position
```{r}
library(tidyverse)
fang_et_al_genotypes <- read_tsv ("https://raw.githubusercontent.com/Kakashi-sensei/Tianqili-R-Assignment/master/fang_et_al_genotypes.txt")
snp_position <- read_tsv ("https://raw.githubusercontent.com/Kakashi-sensei/Tianqili-R-Assignment/master/snp_position.txt")
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
```{r}
maize_fang_et_al_genotypes <- maize_fang_et_al_genotypes[,-c(1,2,3)]
teosinte_fang_et_al_genotypes <- teosinte_fang_et_al_genotypes[,-c(1,2,3)]
```

#Transpose the dataset.
```{r}
maize_fang_et_al_genotypes <- t (maize_fang_et_al_genotypes)
teosinte_fang_et_al_genotypes <- t (teosinte_fang_et_al_genotypes)
```

#for the snp
#we need to extract the columnes of (1,3,4) from the data of snp
```{r}
snp_position <- snp_position[,-c(2,5:15)]
```


#remove the first row of SNP 


#Then we can combine both fang and snp files.
```{r}
maize_fang_et_al_genotypes <- cbind(snp_position, maize_fang_et_al_genotypes)
teosinte_fang_et_al_genotypes <- cbind(snp_position, teosinte_fang_et_al_genotypes)
```



#Now we can start to creat files we want.
#For maize (Group = ZMMIL, ZMMLR, and ZMMMR in the third column of the fang_et_al_genotypes.txt file) we want 20 files in total:

#10 files (1 for each chromosome) with SNPs ordered based on increasing position values and with missing data encoded by this symbol: ?
```{r}
for (i in 1:10) {
  maize <- filter(maize_fang_et_al_genotypes, Chromosome == i)
  maize <- rename( maize,NA,"?")
  maize <- arrange(maize, Position)
  outpath <- "c:/Users/46554/github/R-Assignment/Tianqili-R-Assignment/submit"
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

#1 file with all SNPs with multiple positions in the genome (these need not be ordered in any particular way)

#For teosinte (Group = ZMPBA, ZMPIL, and ZMPJA in the third column of the fang_et_al_genotypes.txt file) we want 20 files in total:

#10 files (1 for each chromosome) with SNPs ordered based on increasing position values and with missing data encoded by this symbol: ?
```{r}
for (i in 1:10) {
  teosinte <- filter(teosinte_fang_et_al_genotypes, Chromosome == i)
  teosinte <- rename(teosinte,NA,"?")
  teosinte <- arrange(teosinte, Position)
  outpath <- "c:/Users/46554/github/R-Assignment/Tianqili-R-Assignment/submit"
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

#1 file with all SNPs with unknown positions in the genome (these need not be ordered in any particular way)

#1 file with all SNPs with multiple positions in the genome (these need not be ordered in any particular way)

#In conclusion, there are 44 files produced.
