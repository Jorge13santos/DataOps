   ### DataOps: Criando uma Pipeline de Dados com Vagrant, Hadoop e Airflow

       Então aqui vou mostrar como podemos subir um Data Lake utilizando como base o Vagrant,
       que seria o equivalente a nossa plataforma cloud,
       o HDFS que seria o equivalente ao Azure Data Lake e o Airflow que entraria no lugar do Azure DataFactory.
       Estou considerando que você já tem o Vagrant e o VirtualBox instalado
       
  ### 1 Crie uma pasta chamada DataOps, onde vamos criar os arquivos:
       PS C:\Users\1511 MXTI> mkdir DataOps
 ###  2 Dentro dela crie o arquivo do Vagrant.      
       C:\Users\1511 MXTI\DataOps> vagrant init
       A `Vagrantfile` has been placed in this directory. You are now
       ready to `vagrant up` your first virtual environment! Please read
       the comments in the Vagrantfile as well as documentation on
       `vagrantup.com` for more information on using Vagrant.
       PS C:\Users\1511 MXTI\DataOps>
   ### Substitúa o conteudo do arquivo por este abaixo
        Arquivo acima 
