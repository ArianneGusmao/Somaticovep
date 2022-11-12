  :grinning: # Somaticovep_Colab  :grinning:
 :grinning:
Pipeline somático para anotação de variantes utilizando VCF e ensembl 105.0 no Google Colab

Você deverá estar logado em uma conta gmail para criar seu documento no Google Colab. Para mais informações sobre o Colab, acessar o link abaixo 
https://colab.research.google.com/



# A primeira etapa é montar o drive com acesso aos arquivos que serão utilizados na anotação. Para isso podemos utilizar o código abaixo conectando ao drive.Mas não se esqueça que você deve estar logado à uma conta gmail e acessar o link acima.
from google.colab import drive
drive.mount('/content/drive')

# Depois de conectar ao drive, podemos instalar os programas necessários. Não esquecer de fazer download da mesma versão do vep e referências.

'''
%%bash

sudo apt install unzip curl git libmodule-build-perl libdbi-perl libdbd-mysql-perl build-essential zlib1g-dev
wget -c https://github.com/Ensembl/ensembl-vep/archive/refs/tags/105.0.tar.gz
tar -zxvf 105.0.tar.gz
cd ensembl-vep-105.0
./INSTALL.pl --NO_UPDATE
'''

# Após instalados, podemos testar o vep através do seguionte link
%%bash

cd ensembl-vep-105.0
./vep

# Com o vep funcionando, podemos utilizar o comando abaixo para anotação. 
O primeiro passo é indicar que serão utilizados 3 processadores. Após isso, no campo (-i) indicar o arquivo de imput (o VCF que precisa ser anotado).
A linha indicando (-o) é o output, local em que sua anotação será salva.
Para baixar os arquivos de interesse que serão utilizados como referência, inserir a informação da linha com a função --cache
Para escolher as informações ou bloco de dados que você deseja utilizar em futuras análises, como classe de variantes, alelos, utilizar a linha com a função --pick 
Já em fields nós conseguimos dizer quais informações eu quero trabalhar naquele momento, ou seja, quais informações esse arquivo que será gerado vai mostrar. 
OBS: Posso fazer download de várias bases em pick, mas posso escolher quais delas eu quero utilizar naquele momento ou análise. É preferível fazer download das principais se você tiver a intenção de análisar outros conjuntos de informações posteriormente.
Caso tenha dúvidas sobre quais são as principais, descritas com o termo "everything" acessar o link(https://www.ensembl.org/info/docs/tools/vep/script/vep_options.html#opt_sift).
Segue o modelo de escrita do código:

%%bash

./ensembl-vep-105.0/vep  \
  --fork 3 \
 	-i /COPIAR O CAMINHO QUE ESTÁ SALVO O VCF/INDICAR O NOME DO VCF \
 	-o /INDICAR O CAMINHO QUE VOCÊ DESEJA SALVAR/INDICAR O NOME DO ARQUIVO DE ANOTAÇÃO.tsv \
  --dir_cache /PASTA GLOBAL, OU SEJA, CONSTA TODOS OS ARQUIVOS/ \
  --fasta /INDICAR O CAMINHO QUE ESTÁ SALVO O ARQUIVO EM FASTA/homo_sapiens_refseq/Homo_sapiens_assembly19.fasta \
  --cache --offline --assembly GRCh37 --refseq  \
	--pick --pick_allele --force_overwrite --tab --symbol --check_existing --variant_class --everything --filter_common \
  --fields "Uploaded_variation,Location,Allele,Existing_variation,HGVSc,HGVSp,SYMBOL,Consequence,IND,ZYG,Amino_acids,CLIN_SIG,PolyPhen,SIFT,VARIANT_CLASS,FREQS" \
  --individual all
  
  # Após realizar a anotação e gerar seu arquivo .tsv, você pode verificar quantas variantes o arquivo anotou utilizado o grep
  
  %%bash
  
grep -vc "##" /INSERIR O CAMINHO DO ARQUIVO GERADO

# Importar os pacotes que serão utilizados. Nesse caso vamos utilizar o pandas e csv

import pandas as pd
import csv
tabela = pd.read_csv('INSERIR O CAMINHO DO SEU ARQUIVO', sep='\t', skiprows=38)
df = pd.DataFrame(tabela)     DEFINIR O DATAFRAME (Estrutura de dados tabular bidimensional potencialmente heterogênea e de tamanho variável com eixos rotulados (linhas e colunas
df
