# Software, tool, database for VPatho
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

## 1.3 Install vep_104_GRCh37
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

# 3. Example of Ensembl VEP usage
>vep -i example.vcf --fork 4 -o homo_sapiens_GRCh37.out.vcf --assembly GRCh37 --cache --cache_version 104 --dir /plugins_path/vep --offline --fasta /file_path/vep/homo_sapiens/104_GRCh37/Homo_sapiens.GRCh37.75.dna.primary_assembly.fa.gz  --force_overwrite

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
>python iupred2a.py -a P53_HUMAN.seq long
```

# 6. Position Specific Score Matrix (PSSM)
You could use PSI-BLAST software searing swiss-port database. Software address :https://www.ebi.ac.uk/seqdb/confluence/display/THD/PSI-BLAST.


