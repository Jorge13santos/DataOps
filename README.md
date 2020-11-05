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
        
  ### No Vagrantfile que eu criei, vamos subir duas VMs, uma chamada Datalake e a outra chamada Airflow:

     Datalake: 192.168.33.100
     Ariflow: 192.168.33.111

  ### Agora dentro do mesmo diretório também crie alguns arquivos que serão necessários para fazer o provisionamento do HDFS.

  ### Arquivo: core-site.xml
      <?xml version="1.0" encoding="UTF-8"?>
      <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
      <configuration>
      <property>
      <name>fs.defaultFS</name>
      <value>hdfs://192.168.33.100:9000</value>
      </property>
      <property>
     <name>dfs.data.dir</name>
     <value>/</value>
     </property>
     </configuration>
     
  ### Arquivo: hdfs-site.xml
      <?xml version="1.0" encoding="UTF-8"?>
    <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
     <configuration>
    <property>
    <name>dfs.namenode.rpc-bind-host</name>
    <value>0.0.0.0</value>
    <description>        
    </description>
    </property>
     <property>
     <name>dfs.namenode.servicerpc-bind-host</name>
     <value>0.0.0.0</value>
     <description>        
     </description>
     </property>
     <property>
     <name>dfs.namenode.http-bind-host</name>
     <value>0.0.0.0</value>
    <description>       
     </description>
    </property>
    <property>
    <name>dfs.datanode.use.datanode.hostname</name>
    <value>false</value>
    <description>
    </description>
    </property>
    <property>
    <name>dfs.namenode.https-bind-host</name>
    <value>0.0.0.0</value>
    <description>        
    </description>
      </property>
    <property>
      <name>dfs.replication</name>
      <value>1</value>
    </property>
    <property>
    <name>dfs.permissions</name>
    <value>false</value>
     </property>
    </configuration>
     
  
