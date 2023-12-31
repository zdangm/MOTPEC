# MOTPEC
## A Multi-Omics-based framework bridges Transcriptome between PEripheral and Central tissues

MOTPEC trains a prediction model using lasso regression based on peripheral blood profiles to predict the gene expression of central tissues. The peripheral blood profiles include expression, eSNP, splicing, APA event, co-expression module and transcriptional factors. For each gene in each tissue, MOTPEC predicts its tissue-specific expression.The pearson's r between observed expression and predicted expression is employed to evaluate the accuracy of model. Integrating TWAS, compare DGE of observed expression and predicted expression to estimate the utility of prediction model.

## Data availability
Download data files from GTEx Portal and GENCODE https://www.gencodegenes.org/human/  
- gene expression
- genetics variants
- splicing profiles
- APA events
- phenotype (BMI)
- demography variables
- transcriptional factors list
- loci file: gencode.v32.GRCh37.txt


## Main script in experiment
### Workdir
workdir: ~/MOTPEC  
raw_data: ~/MOTPEC/data/raw_data  
input_data: ~/MOTPEC/data/input  
output_data: ~/MOTPEC/data/output 

### step1: get all input file
1. All these files should be kept in raw_data: ~/MOTPEC/data/raw_data:  
- gene expression(normal)  
  normal exp  
- partial whole blood profiles  
  blood.pca, blood.tf.pca, modules_pca  
  colors and modules:  
  annotation/co_exp_module.Rdata  
- Transcriptional factors list  
  annotation/TF.txt  
- splicing  
  Whole_Blood.v8.leafcutter_phenotypes.bed.gz  
- APA event  
  Whole_Blood_All_PDUIs.zip  
- samples attributes  
  GTEx_Analysis_v8_Annotations_SampleAttributesDS.txt  
- BMI phenotype  
  GTEx_Analysis_2017-06-05_v8_Annotations_SubjectPhenotypesDS.txt  
- demography variables  
  sample_ga.txt  
2. The loci file gencode.v32.GRCh37.txt should be kept in ~/MOTPEC/data/input/
3. The script get_input_data.R is used to get all formatted data, and data will be stored in "~/MOTPEC/data/input", for example:

  #### raw data
- normal expression:  
   ```file = fread("Adipose_Subcutaneous.v8.normalized_expression.bed")```  
   ```file[1:5, 1:10]```
  
|    |  chr  |  start  |  end  |  gene_id  |  GTEX-1117F  |  GTEX-111CU  |  GTEX-111FC  |  GTEX-111VG  |  GTEX-111YS  |  GTEX-1122O  |  
|  ---  |  ---  |  ----  |  ----  |  ----------------  |  -----------  |  -----------  |  -----------  |  -----------  |  -----------  |  -----------  |  
|  1  |  chr1  |  29552  |  29553  |  ENSG00000227232.5  |  1.31353292  |  -0.9007945  |  -0.2926896  |  -0.7324114  |  -0.2747541  |  -0.6990255  |  
|  2  |  chr1  |  135894  |  135895  |  ENSG00000268903.1  |  -0.39787592  |  0.5724601  |  -1.0918158  |  2.1157713  |  0.1035509  |  0.1295693  |  
|  3  |  chr1  |  137964  |  137965  |  ENSG00000269981.1  |  0.06033348  |  0.9953057   |  -0.8440787  |  2.3148972  |  0.6187479  |  0.7045350  |  
|  4  |  chr1  |  173861  |  173862  |  ENSG00000241860.6  |  0.22586574  |  -0.8197281  |  -0.2347111  |  1.0686590  |  0.2613606  |  1.1236339  |  
|  5  |  chr1  |  195410  |  195411  |  ENSG00000279457.4  |  0.29268956  |  -1.0023984  |  0.4025419  |  -0.5422753 |  -1.7767986  |  -0.5522808  |

- splicing:  
   ```file = fread('Whole_Blood.v8.leafcutter_phenotypes.bed.gz')```  
   ```file[1:5, 1:8]```
  
|  |  #Chr  |  start  |  end  |  ID  |  GTEX-1LVAO  |  GTEX-1AX9K  |  GTEX-1GN73  |  GTEX-RM2N   |
|  ----  |  ----  |  ----  |  ----  |  ----  |  ----  |  ----  |  ----  |  ----  |
|  1  |  chr1  |  29552  |  29553  |  chr1:14829:14970:clu_40978:ENSG00000227232.5  |  1.5627810  |   1.617492  |  1.8133847 |  0.003239502  |
|  2  |  chr1  |  29552  |  29553  |  chr1:15038:15796:clu_40978:ENSG00000227232.5  | -1.3877237  |  -1.650869  |  -1.7964716 | -0.004566534  |
|  3  |  chr1  |  29552  |  29553  |  chr1:15947:16607:clu_40980:ENSG00000227232.5  | -0.7163063  |  -2.124633  |  0.4795690 |  -0.005269084  |
|  4  |  chr1  |  29552  |  29553  |  chr1:16310:16607:clu_40980:ENSG00000227232.5  |  1.1117534  |   1.685548  |  -0.4241061 |  -0.014246458  |
|  5  |  chr1  |  29552  |  29553  |  chr1:17055:17233:clu_40981:ENSG00000227232.5  |  1.7357796  |   2.367227  |  0.6758256 |  0.156601906  |

- sample attributes:  
   ```file = fread('GTEx_Analysis_v8_Annotations_SampleAttributesDS.txt')```  
   ```file[1:5,1:9]```
  
|    |  SAMPID  |  SMATSSCR  |  SMCENTER  |  SMPTHNTS  |  SMRIN  |  SMTS  |  SMTSD  |  SMUBRID  |  SMTSISCH  |
|  ----  |  ----  |  ----  |  ----  |  ----  |  ----  |  ----  |  ----  |  ----  |  ----  |
|  1  |  GTEX-1111F-1111-SM-58Q7G  |  NA  |  B1  |    |  NA  |  Blood  |  Whole Blood  |  0011111  |  1111  |
|  2  |  GTEX-1111F-1111-SM-5DWSB  |  NA  |  B1  |    |  NA  |  Blood  |  Whole Blood  |  0011111  |  1111  |
|  3  |  GTEX-1111F-1111-SM-6WBT7  |  NA  |  B1  |    |  NA  |  Blood  |  Whole Blood  |  0011111  |  1111  |
|  4  |  GTEX-1111F-1111-R10a-SM-AHZ7F  |  NA  |  B1, A1  |    |  NA  |  Brain  |  Brain - Frontal Cortex (BA9)  |  0001111  |  1111  |
|  5  |  GTEX-1111F-1111-R10b-SM-CYKQ8  |  NA  |  B1, A1  |    |  7.2  |  Brain  |  Brain - Frontal Cortex (BA9)  |  0001111  |  1111  |

- APA event:  
   ```file = fread(unzip('Whole_Blood_All_PDUIs.zip', "Whole_Blood_All_PDUIs.txt"))```  
   ```file[1:5,1:10]```
  
|    |  Gene  |  GTEX-1111  |  GTEX-1112  |  GTEX-1113  |  GTEX-1114  |  GTEX-1115  |  GTEX-1116  |  GTEX-1117  |  GTEX-1118  |  GTEX-1119  |
|  ----  |  ----  |  ----  |  ----  |  ----  |  ----  |  ----  |  ----  |  ----  |  ----  |  ----  |
|  1  |  NM_020524  |  0.68  |  0.58  |  0.57  |  0.63  |  0.56  |  0.71  |  0.61  |  0.61  |  0.79  |
|  2  |  NM_053282  |  NA  |  NA  |  0.40  |  0.53  |  0.40  |  0.39  |  0.43  |  0.34  |  0.53  |
|  3  |  NM_020978  |  0.17  |  0.19  |  0.21  |  0.25  |  0.24  |  NA  |  0.46  |  0.32  |  NA  |
|  4  |  NM_001294339  |  0.67  |  0.55  |  0.56  |  0.65  |  0.57  |  0.80  |  0.58  |  0.68  |  0.53  |
|  5  |  NM_024602  |  1.00  |  1.00  |  1.00  |  1.00  |  1.00  |  1.00  |  0.99  |  1.00  |  1.00  |

- demography variables:  
   ```file = fread('sample_ga.txt')```  
   ```file[1:5,1:5]```
  
|    |  SUBJID  |  COHORT  |  SEX  |  AGE  |  RACE  |  
| --- | ---- | ---- | ---- | ---- | ---- |
|  1  |  GTEX-1111F  |  **********  |  2  |  66  |  2  |
|  2  |  GTEX-1111U  |  **********  |  1  |  57  |  3  |
|  3  |  GTEX-1111C  |  **********  |  1  |  61  |  3  |
|  4  |  GTEX-1111G  |  **********  |  1  |  63  |  3  |
|  5  |  GTEX-1111S  |  **********  |  1  |  62  |  3  |

- BMI:  
   ```file = fread('GTEx_Analysis_2017-06-05_v8_Annotations_SubjectPhenotypesDS.txt')```  
   ```file[1:5,1:10]```
  
|    |  SUBJID  |  COHORT  |  SEX  |  AGE  |  RACE  |  ETHNCTY  |  HGHT  |  HGHTU  |  WGHT  |  WGHTU  |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|  1  |  GTEX-1111F  |  **********  |  2  |  66  |  2  |  0  |  66  |  in  |  199  |  lb  |
|  2  |  GTEX-1111U  |  **********  |  1  |  57  |  3  |  0  |  70  |  in  |  234  |  lb  |
|  3  |  GTEX-1111C  |  **********  |  1  |  61  |  3  |  0  |  73  |  in  |  190  |  lb  |
|  4  |  GTEX-1111G  |  **********  |  1  |  63  |  3  |  0  |  69  |  in  |  200  |  lb  |
|  5  |  GTEX-1111S  |  **********  |  1  |  62  |  3  |  0  |  72  |  in  |  227  |  lb  |

- loci:  
   ```loci <- read.csv('gencode.v32.GRCh37.txt', sep = '\t')```  
   ```loci[1:5, 1:10]```
  
|    |  chr  |  left  |  right  |  strand  |  geneid_full  |  geneid  |  genetype  |  genename  |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|  1  |  chr1  |  11869  |  14409  |  +  |  ENSG00000223972.5_2  |  ENSG00000223972  |  transcribed_unprocessed_pseudogene  |  DDX11L1  |
|  2  |  chr1  |  14404  |  29570  |  -  |  ENSG00000227232.5_2  |  ENSG00000227232  |  unprocessed_pseudogene  |  WASH7P  |
|  3  |  chr1  |  29554  |  31109  |  +  |  ENSG00000243485.5_6  |  ENSG00000243485  |  lncRNA  |  MIR1302-2HG  |
|  4  |  chr1  |  34554  |  36081  |  -  |  ENSG00000237613.2_3  |  ENSG00000237613  |  lncRNA  |  FAM138A  |
|  5  |  chr1  |  52473  |  53312  |  +  |  ENSG00000268020.3_4  |  ENSG00000268020  |  unprocessed_pseudogene  |  OR4G4P  |

  #### data format after processing
- exp_matrix: a list of 49 tissues' expression(include Whole Blood), each element is a matrix  

   ```exp.matrix[['Whole_Blood']][1:5,1:5]```
  
|    |  ENSG00000227232  |  ENSG00000238009  |  ENSG00000233750  |  ENSG00000268903  |  ENSG00000269981  |
|  ----  |  ----  |  ----  |  ----  |  ----  |  ----  |
|  GTEX-1111S  |  -1.2250236  |  -0.6733178  |  0.3014908  |  0.4171043  |  0.8916543  |
|  GTEX-1111O  |  -0.8533908  |  0.1217088  |  0.8587798  |  0.3766841  |  0.3093189  |
|  GTEX-1111S  |  0.4293614  |  -0.6546895  |  -0.4375687  |  0.8163377  |  1.1788697  |
|  GTEX-1111C  |  0.8533908  |  -1.1788697  |  -1.5580346  |  -2.6142683  |  -2.6142683  |
|  GTEX-1111C  |  -0.7064943  |  -0.8373702   |  -0.7702820  |  -0.9312701  |  -0.8215617  |

- WB: a list include: blood.pca, blood.tf.pca, colors, modules_pca, modules  

 ```WB[['blood.pca']][1:5,1:5]```
  
|    |  PC1  |  PC2  |  PC3  |  PC4  |  PC5  |
|  ----  |  ----  |  ----  |  ----  |  ----  |  ----  |
|  GTEX-1111S  |  -68.56067  |  18.53914  |  -15.42782  |  -3.938970  |  20.411347  |
|  GTEX-1111O  |  -102.96515  |  -51.41632  |  29.38948  |  -40.438315  |  -13.367777  |
|  GTEX-1111S  |  62.51361  |  -22.31329  |  27.41144  |  -65.461205  |  10.169594  |
|  GTEX-1111C  |  16.41480  |  -83.65493  |  -104.79319  |  -5.910071  |  -1.902242  |
|  GTEX-1111C  |  38.33161  |  -12.52743  |  60.43982  |  -9.061731  |  5.730263  |

   ```WB[['blood.tf.pca']][1:5,1:5]```  
   
|    |  PC1  |  PC2  |  PC3  |  PC4  |  PC5  |
|  ----  |  ----  |  ----  |  ----  |  ----  |  ----  |
|  GTEX-1111S  |  15.956726  |  8.756085788  |  -4.649753  |  -3.299114  |  -4.446283  |
|  GTEX-1111O  |  25.759200  |  -5.709656529  |  11.203563  |  -9.575776  |  1.353987  |
|  GTEX-1111S  |  -15.255152  |  -7.904850477  |  1.738098  |  -14.940524  |  -5.970758  |
|  GTEX-1111C  |  9.744112  |  -36.092799242  |  -23.263840  |  2.306993  |  1.778211  |
|  GTEX-1111C  |  -9.658695  |  -0.004755886  |  12.989872  |  -2.667919  |  -2.465581  |

   ```WB[['colors']][1:5]```  
   
|  ENSG00000227232  |  ENSG00000238009  |  ENSG00000233750  |  ENSG00000268903  |  ENSG00000269981  |
|  ----  |  ----  |  ----  |  ----  |  ----  |
|  "grey"  |  "grey"  |  "turquoise"  |  "turquoise"  |  "turquoise"  |
   
   ```WB[['modules']][['grey']][1:5]```  
   
|  ENSG00000227232  |  ENSG00000238009  |  ENSG00000241860  |  ENSG00000279928  |  ENSG00000279457   |
|  ----  |  ----  |  ----  |  ----  |  ----  |
|  1  |  2  |  7  |  8  |  9  |
   
   ```WB[['modules_pca']][['grey']][1:5,1:5]```  
   
|    |  PC1  |  PC2  |  PC3  |  PC4  |  PC5  |
|  ----  |  ----  |  ----  |  ----  |  ----  |  ----  |
|  GTEX-1111S  |  -27.653046  |  3.697308  |  -8.865022  |  2.724629  |  -19.317787  |
|  GTEX-1111O  |  -32.551785  |  -12.059564  |  32.652657  |  6.593311  |  1.591776  |
|  GTEX-1111S  |  16.218895  |  -3.304558  |  21.695663  |  34.882897  |  -16.463806  |
|  GTEX-1111C  |  5.248274  |  70.203751  |  25.648423  |  -6.454238  |  -5.834333  |
|  GTEX-1111C  |  16.836411  |  -36.001744  |  10.849349  |  4.437152  |  -3.455231  |

- splicing: a matrix  

   ```splicing[1:5,1:10]```
  
|    |  #Chr  |  start  |  end  |  ID  |  GTEX-1LVAO  |  GTEX-1AX9K  |  GTEX-1GN73  |  GTEX-RM2N  |  GTEX-111YS  |  GTEX-1R9PN  |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |  ----  | ----  |
|  1  |  chr1  |  29552  |  29553  |  ENSG00000227232  |  1.5627810  |  1.617492  |  1.8133847  |  0.003239502  |  0.4766374  |  0.9620965  |
|  2  |  chr1  |  29552  |  29553  |  ENSG00000227232  |  -1.3877237  |  -1.650869  |  -1.7964716  |  -0.004566534  |  -0.3512171  |  -0.8436864  |
|  3  |  chr1  |  29552  |  29553  |  ENSG00000227232  |  -0.7163063  |  -2.124633  |  0.4795690  |  -0.005269084  |  1.7039249  |  -0.2537381  |
|  4  |  chr1  |  29552  |  29553  |  ENSG00000227232  |  1.1117534  |  1.685548  |  -0.4241061  |  -0.014246458  |  -1.8304159  |  0.1309287  |
|  5  |  chr1  |  29552  |  29553  |  ENSG00000227232  |  1.7357796  |  2.367227  |  0.6758256  |  0.156601906  |  0.2407400  |  1.1691889  |

- apa_pc_data: a matrix
   
   ```apa_pc_data[1:5,1:5]```
  
|    |  PC1  |  PC2  |  PC3  |  PC4  |  PC5  |
|  ----  |  ----  |  ----  |  ----  |  ----  |  ----  |
|  GTEX-1111S  |  -3.162204  |  0.365157  |  -1.5807869  |  -1.2272305  |  1.69888839   |
|  GTEX-1111O  |  -4.485739  |  2.712429  |  0.7774328  |  0.5957889  |  -0.01354537  |
|  GTEX-1111S  |   1.289923  |  1.588977  |  1.6681345  |  0.4693770  |  1.09205211  |
|  GTEX-1111C  |  -6.401152  |  1.260727  |  2.8176932  |  -0.8861736  |  -0.31765179  | 
|  GTEX-1111C  |  4.051234  |  2.184295  |  0.4758485  |  -0.8010198  |   0.58414172  |

- dummy.demo.info: a matrix
   
   ```dummy.demo.info[1:5,1:5]```
  
|    |  SEX  |  AGE  |  RACE.1  |  RACE.2  |  RACE.3  |
|  ----  |  ----  |  ----  |  ----  |  ----  |  ----  |
|  GTEX-1111F  |  1  |  66  |  0  |  1  |  0  |
|  GTEX-1111U  |  0  |  57  |  0  |  0  |  1  |
|  GTEX-1111C  |  0  |  61  |  0  |  0  |  1  |
|  GTEX-1111G  |  0  |  63  |  0  |  0  |  1  |
|  GTEX-1111S  |  0  |  62  |  0  |  0  |  1  |

- bmi: a matrix
   ```bmi[1:5,]```
  
|    |  SUBJID  |  BMI  |
|  ----  |  ----  |  ----  |
|  GTEX-1111F  |  GTEX-1117F  |  32.12  |
|  GTEX-1111U  |  GTEX-111CU  |  33.57  |
|  GTEX-1111C  |  GTEX-111FC  |  25.06  |
|  GTEX-1111G  |  GTEX-111VG  |  29.53  |
|  GTEX-1111S  |  GTEX-111YS  |  30.78  |

### step2: Model building and accuracy evaluation
MOTPEC uses Lasso regression model under a 5-fold cross-validation to train prediction model and get prediction. The prediction accuracy for each gene are
evaluated using Pearson correlation test between the predicted expression and the ground truth.  
The script prediction_model.R is used to realize above work, and functions.R saves some functions used in prediction_model.R. In prediction_model.R, gene expression is required. Splicing, APA event and genetic variants are optional.  
The results will be saved in ~/MOTPEC/data/output/prediction_model_rs/ by tissue, including baseline accuracy, MOTPEC's accuracy, MOTPEC's prediction, MOTPEC's beta.  

### step3: Formatting predicted results
The script format_prediction.R will format the prediction model results, and pcc_2models, exp_pred, all_model_beta, sam_gene_time will be stored in ~/MOTPEC/data/output/

### step4: Performing TWAS
PrediXcan (https://www.nature.com/articles/ng.3367) is employed to do TWAS in this study.  
The code is in folder do_TWAS(predixcan_r.r).  
The required data should be kept in ~/MOTPEC/data/raw_data/ beforehand.  
The TWAS results will be stored in ~/MOTPEC/data/output/twas_rs.  

### step5: Performing DGE
A linear regression equation after confonding factors adjusted is used to estimate the association between expression and BMI. MOTPEC does this on observed expression, predicted expression and baseline expression respectively. The script do_DGE.R is used to realize above work, and DGE results will be stored in ~/MOTPEC/data/output/b_beta.Rdata

## Tool: Predicting the tissue-specific expression of non-peripheral tissues
The script get_predicted_expression.R is designed to get other tissues' expression by coefficents trained by us, which is executed by command line.
### Input
- necessary
 1. Gene expression data (Note: expression matrix variable must be named "blood.exp" and be saved into "blood_exp.Rdata" and be placed into input dir!)
 2. all_model_beta.Rdata (Provided by us: data/raw_data/annotation/all_model_beta.Rdata)
 3. co_exp_net_and_blood_pc.Rdata (Provided by us: data/raw_data/annotation/co_exp_net_and_blood_pc.Rdata)

- optional
  Splicing, APA event, genetic variants and demography variables (refer to get_input_file.R).
  
Here introduce the format of blood expression after processing:  
```load(paste0(input_dir, '/blood_exp.Rdata'))```  
```blood.exp[1:5,1:5]```  

|    |  ENSG00000227232  |  ENSG00000238009  |  ENSG00000233750  |  ENSG00000268903  |  ENSG00000269981  |
|  ----  |  ----  |  ----  |  ----  |  ----  |  ----  |
|  GTEX-1111S  |  -1.2250236  |  -0.6733178  |  0.3014908  |  0.4171043  |  0.8916543  |
|  GTEX-1111O  |  -0.8533908  |  0.1217088  |  0.8587798  |  0.3766841  |  0.3093189  |
|  GTEX-1111S  |   0.4293614  |  -0.6546895  |  -0.4375687  |  0.8163377  | 1.1788697  |
|  GTEX-1111C  |   0.8533908  |  -1.1788697  |  -1.5580346  |  -2.6142683  |  -2.6142683  |  
|  GTEX-1111C  |  -0.7064943  |  -0.8373702  |  -0.7702820  |  -0.9312701  |  -0.8215617  |
  
### Command aruments:  
```--input_dir: the directory to place input files```  
```--output_dir: the directory to place output files```  
```--args: 1-48 represent 48 tissues```  

|  args  |  tissue  |
|  ----  |  ----  |
|  1  |  "Adipose_Subcutaneous"  |                 
|  2  |  "Adipose_Visceral_Omentum"  |             
|  3  |  "Adrenal_Gland"  |                        
|  4  |  "Artery_Aorta"  |                         
|  5  |  "Artery_Coronary"  |                      
|  6  |  "Artery_Tibial"  |                        
|  7  |  "Brain_Amygdala"  |                       
|  8  |  "Brain_Anterior_cingulate_cortex_BA24"  | 
|  9  |  "Brain_Caudate_basal_ganglia"  |          
|  10  |  "Brain_Cerebellar_Hemisphere"  |          
|  11  |  "Brain_Cerebellum"  |                     
|  12  |  "Brain_Cortex"  |                         
|  13  |  "Brain_Frontal_Cortex_BA9"  |             
|  14  |  "Brain_Hippocampus"  |                    
|  15  |  "Brain_Hypothalamus"  |                   
|  16  |  "Brain_Nucleus_accumbens_basal_ganglia"  |
|  17  |  "Brain_Putamen_basal_ganglia"  |          
|  18  |  "Brain_Spinal_cord_cervical_c-1"  |       
|  19  |  "Brain_Substantia_nigra"  |               
|  20  |  "Breast_Mammary_Tissue"  |                
|  21  |  "Cells_Cultured_fibroblasts"  |           
|  22  |  "Cells_EBV-transformed_lymphocytes"  |    
|  23  |  "Colon_Sigmoid"  |                        
|  24  |  "Colon_Transverse"  |                     
|  25  |  "Esophagus_Gastroesophageal_Junction"  |  
|  26  |  "Esophagus_Mucosa"  |                     
|  27  |  "Esophagus_Muscularis"  |                 
|  28  |  "Heart_Atrial_Appendage"  |               
|  29  |  "Heart_Left_Ventricle"  |                 
|  30  |  "Kidney_Cortex"  |                        
|  31  |  "Liver"  |                                
|  32  |  "Lung"  |                                 
|  33  |  "Minor_Salivary_Gland"  |                 
|  34  |  "Muscle_Skeletal"  |                      
|  35  |  "Nerve_Tibial"  |                         
|  36  |  "Ovary"  |                                
|  37  |  "Pancreas"  |                             
|  38  |  "Pituitary"  |                            
|  39  |  "Prostate"  |                             
|  40  |  "Skin_Not_Sun_Exposed_Suprapubic"  |      
|  41  |  "Skin_Sun_Exposed_Lower_leg"  |           
|  42  |  "Small_Intestine_Terminal_Ileum"  |       
|  43  |  "Spleen"  |                               
|  44  |  "Stomach"  |                              
|  45  |  "Testis"  |                               
|  46  |  "Thyroid"  |                              
|  47  |  "Uterus"  |                               
|  48  |  "Vagina"  |      

```--demo_index: whether add demography profiles, default F, while T represents add```  
```--APA_index: whether add APA event profiles, default F, while T represents add```  
```--eSNP_index: whether add genetic profiles, default F, while T represents add```  
```--splicing_index: whether add splicing profiles, default F, while T represents add```  
```--plink: the installed directory of plink, the version of which is 1.9```  
```--plink_wd: the working directory of plink, only support bed,fam,bim format, not support bgen```  
```--plink_all_sam_name: file names of all exported sample genetic profiles```  
### Output
For example, you will get such a file named by tissue name in output directory.  
```file = fread(paste0(output_dir, 'Artery_Aorta.txt'))```
```file[1:5, 1:5]```

|  V1  |  ENSG00000228794  |  ENSG00000142599  |  ENSG00000171608  |  ENSG00000130940  |
|  ----  |  ----  |  ----  |  ----  |  ----  |
|  GTEX-1111  |  -0.035103603  |  -0.04837770  |  -0.13777566  |  0.0006240904  |
|  GTEX-1111Y  |  -0.169903070  |  0.08400447  |  0.08639159  |  -0.0617938968  |
|  GTEX-1111Q  |  0.210433138  |  -0.03393886  |  -0.12010460  |  0.0914501497  |
|  GTEX-1111W  |  0.007376501  |  -0.37130859  |  -0.15527437  |  0.1009398230  |
|  GTEX-1111  |  -0.042160986  |  0.31247051  |  0.14350070  |  -0.0519353176  |

## Software and support
### Required Packages
For the code the following R-packages needs to be installed 
data.table, WGCNA, readr, ggplot2, glmnet, caret, foreach, doMC
### TWAS Tool
Predixcan(https://www.nature.com/articles/ng.3367, https://github.com/gamazonlab/MR-JTI/blob/master/model_training/predixcan/)
