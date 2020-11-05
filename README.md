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
    
   ### Agora para o Airflow, vamos precisar criar um arquivo chamado dataops.py
       from datetime import timedelta
    from airflow import DAG
    from airflow.operators.bash_operator import BashOperator
    from airflow.utils.dates import days_ago

    default_args = {
     'owner': 'airflow',
     'depends_on_past': False,
     'start_date': days_ago(2),
     'email': ['alisson.copyleft@gmail.com'],
     'email_on_failure': False,
     'email_on_retry': False,
     'retries': 1,
     'retry_delay': timedelta(minutes=5),
      }
     dag = DAG(
    'dataops',
    default_args=default_args,
    description='Retrieving apache logs',
    schedule_interval=timedelta(days=1),
     )

    landing_folder = BashOperator(
     task_id='landing_folder',
    bash_command='rm -rf /tmp/$(date +%Y%m%d) ; mkdir /tmp/$( date +%Y%m%d )',
    dag=dag,
      )

    copying_logs = BashOperator(
    task_id='copying_logs',
    bash_command='scp -i /root/chave.pem alisson@alissonmachado.com.br:/var/log/apache2/* /tmp/$(date +%Y%m%d)/ ',
    dag=dag,
     )

     uncompress = BashOperator(
     task_id='uncompress',
     depends_on_past=False,
    bash_command='cd /tmp/$(date +%Y%m%d) ; ls -1 *.gz  | xargs -i gunzip {}',
    retries=3,
    dag=dag,
 )

    send_to_datalake = BashOperator(
    task_id='send_to_datalake',
    depends_on_past=False,
    bash_command='/opt/hadoop/bin/hdfs dfs  -fs hdfs://192.168.33.100:9000/  -put /tmp/$(date +%Y%m%d)/access* /raw',
    retries=3,
    dag=dag,
      )

      landing_folder >> copying_logs >> uncompress >> send_to_datalake
     
   ### Agora que você já tem todos os pré requisitos, vamos subir o ambiente utilizando o comando vagrant up –provision.
       PS C:\Users\1511 MXTI\DataOps> vagrant up --provision
       Bringing machine 'datalake' up with 'virtualbox' provider...
       Bringing machine 'airflow' up with 'virtualbox' provider...
        ==> datalake: Importing base box 'ubuntu/bionic64'...
  ### comando vagrant status pra saber se elas estão no ar.
     S C:\Users\1511 MXTI\DataOps> vagrant status
     Current machine states:

     datalake                  running (virtualbox)
     airflow                   running (virtualbox)
   
  ### navegador: http://192.168.33.100:9870/
      Isso mostra que o seu Hadoop já está em execução.
      
