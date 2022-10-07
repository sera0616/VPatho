# Software, tool, and database for VPatho
---------------------------------------------------------------------------------
# 1. Ensembl VEP v104 installation

Introduction of Ensembl VEP, refer to ： https://anaconda.org/bioconda/ensembl-vep

## 1.1  Create a new virtual environment and conda install：
>conda  create –n vep

>source activate vep

>conda install -c bioconda ensembl-vep


## 1.2 Check detailed command information
> vep –help
```
 Versions:
    ensembl              : 104.1af1dce
    ensembl-funcgen      : 104.59ae779
    ensembl-io           : 104.1d3bb6e
    ensembl-variation    : 104.6154f8b
    ensembl-vep          : 104.3
  Help: dev@ensembl.org , helpdesk@ensembl.org
  Twitter: @ensembl
  http://www.ensembl.org/info/docs/tools/vep/script/index.html
  Usage:
  ./vep [--cache|--offline|--database] [arguments]
  Basic options
  =============
  --help                 Display this message and quit
  -i | --input_file      Input file
  -o | --output_file     Output file
  --force_overwrite      Force overwriting of output file
  --species [species]    Species to use [default: "human"]                       
  --everything           Shortcut switch to turn on commonly used options. See web
                         documentation for details [default: off]                       
  --fork [num_forks]     Use forking to improve script runtime
  For full option documentation see:
  http://www.ensembl.org/info/docs/tools/vep/script/vep_options.html
```
If use Ensembl VEP, please cite:
McLaren, William, Laurent Gil, Sarah E. Hunt, Harpreet Singh Riat, Graham RS Ritchie, Anja Thormann, Paul Flicek, and Fiona Cunningham. "The ensembl variant effect predictor." Genome biology 17, no. 1 (2016): 1-14.

## 1.3 Download and convert genome-build vep_104_GRCh37
> vep_install -a cf -s homo_sapiens -y GRCh37 -c /data1/gfang/data/vep –CONVERT
```
- getting list of available cache files
- downloading ftp://ftp.ensembl.org/pub/release-104/variation/indexed_vep_cache/homo_sapiens_vep_104_GRCh37.tar.gz
- unpacking homo_sapiens_vep_104_GRCh37.tar.gz
- converting cache, this may take some time but will allow VEP to look up variants and frequency data much faster
- use CTRL-C to cancel if you do not wish to convert this cache now (you may run convert_cache.pl later)
- Processing homo_sapiens
- Processing version 104_GRCh37
- No unprocessed types remaining, skipping
- All done!
```

## 1.4 Install plugins
User can install the required some plugins at the same time
>vep_install -a p --PLUGINS AncestralAllele, Blosum62, CADD, CSN, Carol, Condel, Conservation, DisGeNET, Downstream, Draw, ExAC, ExACpLI, FATHMM, FATHMM_MKL, FlagLRG, FunMotifs, G2P, GO, GeneSplicer, Gwava, HGVSIntronOffset, LD, LOVD, LoF, LoFtool, LocalID, MPC, MTR, Mastermind, MaxEntScan, NearestExonJB, NearestGene, PON_P2, Phenotypes, PostGAP, ProteinSeqs, REVEL, ReferenceQuality, SameCodon, SingleLetterAA, SpliceAI, SpliceRegion, StructuralVariantOverlap, SubsetVCF, TSSDistance, dbNSFP, dbscSNV, gnomADc, miRNA, neXtProt, satMutMPRA

OR User can install the required plugin one by one

>vep_install -a p --PLUGINS ExAC

>vep_install -a p --PLUGINS Blosum62

>vep_install -a p --PLUGINS GO
etc

# 2. dbNSF v4.1a installation
For detailed information, please refer to:https://sites.google.com/site/jpopgen/dbNSFP
(Note: unzip “dbNSF v4.1a” database need about 28.42GB disk space)

>source activate vep

>mkdir /filepath/dbNSFP4.1/

>cd /filepath/dbNSFP4.1/

For release 4.1a with GRCh37/hg19 data:
> wget ftp://dbnsfp:dbnsfp@dbnsfp.softgenetics.com/dbNSFP4.1a.zip

> unzip dbNSFP4.1a.zip

> zcat dbNSFP4.1a_variant.chr1.gz | head -n1 > h

> zgrep -h -v ^#chr dbNSFP4.1a_variant.chr* | awk '$8 != "." ' | sort -T /home/gefang1/data/vep/dbNSFP/temp/ -k8,8 -k9,9n - | cat h - | bgzip -c > dbNSFP4.1a_grch37.gz

> tabix -s 8 -b 9 -e 9 dbNSFP4.1a_grch37.gz

If use dbNSFP4.1, please cite:
Liu, Xiaoming, Chang Li, Chengcheng Mou, Yibo Dong, and Yicheng Tu. "dbNSFP v4: a comprehensive database of transcript-specific functional predictions and annotations for human nonsynonymous and splice-site SNVs." Genome medicine 12, no. 1 (2020): 1-8.

# 3. Example of Ensembl VEP usage
> vep -i variant_test.vcf --dir_cache  /vep_path/vep  --total_length  --fork 25 --offline --force_overwrite --everything  --minimal  --allele_number  1  --nearest symbol --tab  --pick --pick_order ccds, ensembl havana, translated, canonical --plugin Blosum62, --plugin  Downstream, --plugin GO, --plugin HGVSIntronOffset,  --plugin SingleLetterAA, --plugin TSSDistance, --plugin miRNA, --plugin neXtProt, --plugin CSN  --fasta  /file_path/104_GRCh37/Homo_sapiens.GRCh37.75.dna.primary_assembly.fa.gz  --dir_plugins /dbNSFP_path/dbNSFP4.1/ --plugin dbNSFP,'consequence=ALL',dbNSFP4.1a_grch37.gz, pep_match=0, aapos, genename, HGVSc_snpEff, HGVSp_snpEff,HGVSc_VEP, HGVSp_VEP, APPRIS, GENCODE_basic, TSL, VEP_canonical, cds_strand, refcodon, codonpos, codon_degeneracy, Ancestral_allele, AltaiNeandertal, Denisova, VindijiaNeandertal, SIFT_score, SIFT_pred, SIFT4G_score, SIFT4G_pred, Polyphen2_HDIV_score, Polyphen2_HDIV_rankscore, Polyphen2_HDIV_pred, Polyphen2_HVAR_score, Polyphen2_HVAR_rankscore, Polyphen2_HVAR_pred, LRT_score, LRT_converted_rankscore, LRT_pred, LRT_Omega, MutationTaster_score, MutationTaster_converted_rankscore, MutationTaster_pred, MutationTaster_model, MutationTaster_AAE, MutationAssessor_score, MutationAssessor_rankscore, MutationAssessor_pred, FATHMM_score, FATHMM_converted_rankscore, FATHMM_pred, PROVEAN_score, PROVEAN_converted_rankscore, PROVEAN_pred, VEST4_score, VEST4_rankscore, MetaSVM_score, MetaSVM_rankscore, MetaSVM_pred, MetaLR_score, MetaLR_rankscore, MetaLR_pred, Reliability_index, gnomAD_genomes_NFE_nhomalt, gnomAD_genomes_AMI_AC, -o variant_test.out --force_overwrite

# 4. Ensembl API installation

## 4.1 install bioperl-1.2.3
>source activate vep

>perl –v

>cd ~

>mkdir src

>cd src

>wget https://github.com/bioperl/bioperl-live/archive/bioperl-release-1-2-3.zip

>unzip bioperl-release-1-2-3.zip

>mv bioperl-live-bioperl-release-1-2-3/  bioperl-1.2.3

>ls

## 4.2 Install ensembl API

>git –version

>git clone https://github.com/Ensembl/ensembl.git

>git clone https://github.com/Ensembl/ensembl-variation.git

>git clone https://github.com/Ensembl/ensembl-compara.git

>git clone https://github.com/Ensembl/ensembl-funcgen.git

>git clone https://github.com/Ensembl/ensembl-tools.git

>echo $SHELL

>PERL5LIB=${PERL5LIB}:${HOME}/src/bioperl-1.2.3

>PERL5LIB=${PERL5LIB}:${HOME}/src/ensembl/modules

>PERL5LIB=${PERL5LIB}:${HOME}/src/ensembl-compara/modules

>PERL5LIB=${PERL5LIB}:${HOME}/src/ensembl-variation/modules

>PERL5LIB=${PERL5LIB}:${HOME}/src/ensembl-funcgen/modules

>PERL5LIB=${PERL5LIB}:${HOME}/src/ensembl-tools/modules

>echo $PERL5LIB

>cd ensembl

>cd misc-scripts/

>./ping_ensembl.pl

## 4.3 Usage of Ensembl API
User could find some examples at https://rest.ensembl.org/documentation/info/sequence_id and make some changes for their own ENSP*. Herein, we list two typical examples as below:
### Example-1:
```
import requests, sys
server = "https://rest.ensembl.org"
ext = "/sequence/id/ENSP00000368496?"
r = requests.get(server+ext, headers={ "Content-Type" : "application/json"})
if not r.ok:
  r.raise_for_status()
  sys.exit() 
decoded = r.json()
print('--repr(decoded)--',repr(decoded)) #type(repr(decoded)): str
print('--decoded.sequence--',decoded['seq']) #type(decoded):dict
```
### Example-2:
```
import requests, sys
server = "https://rest.ensembl.org"
mylist=['ENSP00000368496','ENSP00000263741','ENSP00000327214','ENSG00000176022','ENSG00000162551']
for i in range(len(mylist)):
  temp=mylist[i]
  ext="/sequence/id/"+str(temp)+"?"
  r = requests.get(server+ext, headers={ "Content-Type" : "application/json"})
  if not r.ok:
    r.raise_for_status()
    sys.exit()
  decoded = r.json()  
  print('-------'+mylist[i]+'---------')
  print('--repr(decoded)--',repr(decoded)) #type(repr(decoded)): str
  print('--decoded.sequence--',decoded['seq']) #type(decoded):dict
```

# 5. IUPred2A

## 5.1 write form to get the download link
User must fill the form (https://iupred2a.hu/download_new). Then, the program package will be sent to the email address users provide. Please note that this automated service is only available to academic users.

## 5.2 Usage
>cd /file_path/iupred2a/

```
Uncomment the following codes:
if '-a' in sys.argv:
    print("# POS\tRES\tIUPRED2\tANCHOR2")
else:
    print("# POS\tRES\tIUPRED2")
```
>python iupred2a.py -a P53_HUMAN.seq long

If use IUPred2A, please cite:
Bálint Mészáros, Gábor Erdős, Zsuzsanna Dosztányi
IUPred2A: context-dependent prediction of protein disorder as a function of redox state and protein binding
Nucleic Acids Research 2018;46(W1):W329-W337.

# 6. Position Specific Score Matrix (PSSM)
We adopted the PSI-BLAST (Position-Specific Iterated Basic Local Alignment Search Tool) to generate PSSM information. For more detail information, please refer to: 
Schaffer A A, Aravind L, Madden T L, et al. Improving the accuracy of PSI-BLAST protein database searches with composition-based statistics and other refinements [J]. Nucleic Acids Research, 2001, 29(14): 2994-3005.  
Software website: https://www.ebi.ac.uk/seqdb/confluence/display/THD/PSI-BLAST.

### Multiple sequence alignment database:
Swiss-port protein sequence database. For detailed information, please refer to: Bairoch, Amos, and Rolf Apweiler. "The SWISS-PROT protein sequence database and its supplement TrEMBL in 2000." Nucleic acids research 28, no. 1 (2000): 45-48.


## Contact 
If you are interested in our work, OR, if you have any suggestions/questions about our work, PLEASE contact with us. 
E-mail: gfang0616@njust.edu.cn
