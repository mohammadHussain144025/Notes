>> create meta_data.json from sourceData.csv and hussain_MetaDataCreator.py    

>> edit sequence.csv as per your need {the domain based entities/table should be listed first here}    
e.g  
json_name,Sequence of Ingestion  
x_brand_code,1  
x_brand_description,2  
  
>> edit related.csv ,register which column refers to which table  
 e.g   
    entity_name,columns,linked_to  
    part,ecc_code,ecc    
  
## copy bearer token and place it in bearer segment   
>> now use the above files in hussain_jsonBuilder.py {run >  python .\hussain_jsonBuilder.py } Note: run this script in windows comand prompt    

## script hits:   
>> local : http://localhost:9001/api/v1/definition/entities  
>> dev : https://spriced.dev.meritor.simadvisory.com/api/v1/definition/entities  

#######################################################################################################################################################

>> DATA INGESTION MERITOR SERVER
  --------------------------------

## PGADMIN
• ssh meritor-be aur connect putty
• force delete the spriced_meritor db
• create again spriced_meritor

## Re-run the definition api on server
• ssh meritor-be
• cd deployments (in this directory check the latest date directory e.g 18032024)
>>stop the definition api
• since definiton api runs on port 8080
  -> sudo lsof -i :8080
    (get/copy the PID)
  -> sudo kill -9 PID
(e.g sudo kill -9 27848)

>>start definition api again
-> sudo nohup java -jar sim-spriced-defnition-api-0.0.1.jar &
(press enter and you'll be prompted as nohup: ignoring input and appending output to 'nohup.out' ,press enter again then you come out of the prompt and give the following commannd 
-> sudo tail -f nohup.out
)
now you should be able to create model on the server UI


/Work/projects/infra/vms/meritor_dev$ vagrant ssh


>>changing the definition of an entity(table) in server , e.g datatype,autogen etc and you dont want to disturb your current data in the table
first of all , download the table data by ,pg admin 
>> select * from list_price   
>>             {entity_name} ,and above in the output you can see the download optiion download the data

## 1 update and set your requirements in dataSource csv and create metadata.json file
## 2 delete the role permission to delete the entity from the database and then delete the entity(table) from the 'entity' table
    consider the following steps to do so
          ## 1. get the entitiy id first
>>      select * from entity where name = 'promos_additions'

          ## 2. To check the entity permission
>>      select * from role_entity_permission_mapping where entity_id = 103

          ## 2. delete the entity permission
>>      delete from role_entity_permission_mapping where entity_id = 103

          ## 3. delete entity
>>      delete from entity where id = 103
## 3 now delete the table either open UI https://spriced.dev.meritor.simadvisory.com/ and find and delete or open pgadmin and delete that table manually

## 4 now you can generate table json and hit to the api and create the table and then ingest the downloaded data

>> to check number of files in a directory
find -type f | wc -l

>> if the directory is at different location
find "your_directory_path" -type f | wc -l

>>total size of the directory
du -sh /path/to/directory

>>if you are under the directory then simply
du -sh

>>to check the size of all files present in a directory
ls -lh

>> to kill a process ongoing at port in windows
netstat -ano|findstr :8080
  you will get PID from this ,for instance 3940 is PID here
     TCP    0.0.0.0:8080           0.0.0.0:0              LISTENING       3940
     TCP    [::]:8080              [::]:0                 LISTENING       3940
then to kill the process
taskkill /f /pid 3940 

>> to find non-utf8 charachers in a file
grep -axv '.*' technical_part_details.csv

>> to find how many files are there in a directory
ls -1 | wc -l

>>unziped files
unzip ACAPARTREG_202407260210_389614.csv.zip
gunzip OCAPARTREG_202407260257_129411.csv.gz

>> to check which file is missing in one directory if two directories are having same files in it and one or some files is missing from one directory
# List files in _local_view_scripts
ls _local_view_scripts | sort > /tmp/local_files_sorted.txt

# List files in dev_view_scripts
ls dev_view_scripts | sort > /tmp/dev_files_sorted.txt

# Compare the files
comm -3 /tmp/local_files_sorted.txt /tmp/dev_files_sorted.txt
