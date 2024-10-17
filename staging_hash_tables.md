making pyspark read local direcotries directly instead of fetching from sftp

comment the following methods :
# def connect_sftp():
# def check_and_download_file(sftp):
# def process_selected_file

#ignore the following
# Define the inbound_spark_management dictionary to store statuses
inbound_spark_management = {}

#create a new method to process the lattest file
def get_latest_file(directory, prefix):
    files = [f for f in os.listdir(directory) if f.startswith(prefix) and f.endswith('.csv')]
    print(files)
    if not files:
        logger.warning(f"No files found with prefix {prefix} in directory {directory}.")
        return None
    latest_file = max(files, key=lambda x: os.path.getmtime(os.path.join(directory, x)))
    return os.path.join(directory, latest_file)



#replacte the "if __name__ == '__main__':" witht the following and 

if __name__ == '__main__':
    # Define your CSV directory and other necessary parameters
    csv_directory = csv_directory  # Update with your actual directory
    AUSALTPART = "AUSALTPART"
    # Get the latest CSV file path
    local_csv_path = get_latest_file(csv_directory, AUSALTPART)
    # Initialize Spark session
    spark = SparkSession.builder \
        .appName(spark_app_name) \
        .config("spark.jars", spark_jars) \
        .config("spark.driver.memory", spark_driver_memory) \
        .getOrCreate()

    # Define your JDBC properties
    properties = {
        "driver": db_driver,
        "user": db_user,
        "password": db_password,
    }
    # URL for your database connection
    url = db_url  # Replace with your actual database URL

    # Check if the latest CSV file was found
    if local_csv_path:
        # Call the main function with required parameters
        main(local_csv_path, spark, properties, url)
        alternate_parts_table_step_2_insert_into_main(spark,properties,db_url)
        alternate_parts_table_step_3_update_into_main()
    else:
        logger.error("No valid CSV files found.")






#####################################################################################################################################


creating staging and hash table

1.insert_staging


-- Table: public.us_llp

-- DROP TABLE IF EXISTS public.us_llp;

CREATE TABLE IF NOT EXISTS public.us_llp_insert_staging
(
    action character(1) COLLATE pg_catalog."default",
    custtype character(30) COLLATE pg_catalog."default",
    custid character(6) COLLATE pg_catalog."default",
    partno character(30) COLLATE pg_catalog."default",
    brk1qty numeric(8,0),
    brk1prc numeric(12,4),
    brk1pmnam character(20) COLLATE pg_catalog."default",
    brk1pmlvl character(10) COLLATE pg_catalog."default",
    brk1pmtyp character(10) COLLATE pg_catalog."default",
    brk2qty numeric(8,0),
    brk2prc numeric(12,4),
    brk2pmnam character(20) COLLATE pg_catalog."default",
    brk2pmlvl character(10) COLLATE pg_catalog."default",
    brk2pmtyp character(10) COLLATE pg_catalog."default",
    brk3qty numeric(8,0),
    brk3prc numeric(12,4),
    brk3pmnam character(20) COLLATE pg_catalog."default",
    brk3pmlvl character(10) COLLATE pg_catalog."default",
    brk3pmtyp character(10) COLLATE pg_catalog."default",
    brk4qty numeric(8,0),
    brk4prc numeric(12,4),
    brk4pmnam character(20) COLLATE pg_catalog."default",
    brk4pmlvl character(10) COLLATE pg_catalog."default",
    brk4pmtyp character(10) COLLATE pg_catalog."default",
    brk5qty numeric(8,0),
    brk5prc numeric(12,4),
    brk5pmnam character(20) COLLATE pg_catalog."default",
    brk5pmlvl character(10) COLLATE pg_catalog."default",
    brk5pmtyp character(10) COLLATE pg_catalog."default",
    brk6qty numeric(8,0),
    brk6prc numeric(12,4),
    brk6pmnam character(20) COLLATE pg_catalog."default",
    brk6pmlvl character(10) COLLATE pg_catalog."default",
    brk6pmtyp character(10) COLLATE pg_catalog."default",
    brk7qty numeric(8,0),
    brk7prc numeric(12,4),
    brk7pmnam character(20) COLLATE pg_catalog."default",
    brk7pmlvl character(10) COLLATE pg_catalog."default",
    brk7pmtyp character(10) COLLATE pg_catalog."default",
    lastupdateddate date,
    sendingstatus character varying(250) COLLATE pg_catalog."default",
    lastsentdate date
)

TABLESPACE pg_default;

ALTER TABLE IF EXISTS public.us_llp_insert_staging
    OWNER to postgres;

###################################################################################

2.update staging table

-- Table: public.us_llp

-- DROP TABLE IF EXISTS public.us_llp;

CREATE TABLE IF NOT EXISTS public.us_llp_update_staging
(
    action character(1) COLLATE pg_catalog."default",
    custtype character(30) COLLATE pg_catalog."default",
    custid character(6) COLLATE pg_catalog."default",
    partno character(30) COLLATE pg_catalog."default",
    brk1qty numeric(8,0),
    brk1prc numeric(12,4),
    brk1pmnam character(20) COLLATE pg_catalog."default",
    brk1pmlvl character(10) COLLATE pg_catalog."default",
    brk1pmtyp character(10) COLLATE pg_catalog."default",
    brk2qty numeric(8,0),
    brk2prc numeric(12,4),
    brk2pmnam character(20) COLLATE pg_catalog."default",
    brk2pmlvl character(10) COLLATE pg_catalog."default",
    brk2pmtyp character(10) COLLATE pg_catalog."default",
    brk3qty numeric(8,0),
    brk3prc numeric(12,4),
    brk3pmnam character(20) COLLATE pg_catalog."default",
    brk3pmlvl character(10) COLLATE pg_catalog."default",
    brk3pmtyp character(10) COLLATE pg_catalog."default",
    brk4qty numeric(8,0),
    brk4prc numeric(12,4),
    brk4pmnam character(20) COLLATE pg_catalog."default",
    brk4pmlvl character(10) COLLATE pg_catalog."default",
    brk4pmtyp character(10) COLLATE pg_catalog."default",
    brk5qty numeric(8,0),
    brk5prc numeric(12,4),
    brk5pmnam character(20) COLLATE pg_catalog."default",
    brk5pmlvl character(10) COLLATE pg_catalog."default",
    brk5pmtyp character(10) COLLATE pg_catalog."default",
    brk6qty numeric(8,0),
    brk6prc numeric(12,4),
    brk6pmnam character(20) COLLATE pg_catalog."default",
    brk6pmlvl character(10) COLLATE pg_catalog."default",
    brk6pmtyp character(10) COLLATE pg_catalog."default",
    brk7qty numeric(8,0),
    brk7prc numeric(12,4),
    brk7pmnam character(20) COLLATE pg_catalog."default",
    brk7pmlvl character(10) COLLATE pg_catalog."default",
    brk7pmtyp character(10) COLLATE pg_catalog."default",
    lastupdateddate date,
    sendingstatus character varying(250) COLLATE pg_catalog."default",
    lastsentdate date
)

TABLESPACE pg_default;

ALTER TABLE IF EXISTS public.us_llp_update_staging
    OWNER to postgres;

#####################################################################
3.hash table

CREATE TABLE IF NOT EXISTS public.us_llp_hash
(
id integer NOT NULL GENERATED BY DEFAULT AS IDENTITY ( INCREMENT 1 START 1
MINVALUE 1 MAXVALUE 2147483647 CACHE 1 ),
code character varying(255) COLLATE pg_catalog."default",
hash_value character varying(255) COLLATE pg_catalog."default",
check_sum character varying(255) COLLATE pg_catalog."default"
)
TABLESPACE pg_default;
ALTER TABLE IF EXISTS public.us_llp_hash
OWNER to postgres;

###################################################################
4.hash staging

CREATE TABLE IF NOT EXISTS public.us_llp_hash_staging
(
id integer NOT NULL GENERATED BY DEFAULT AS IDENTITY ( INCREMENT 1 START 1
MINVALUE 1 MAXVALUE 2147483647 CACHE 1 ),
code character varying(255) COLLATE pg_catalog."default",
hash_value character varying(255) COLLATE pg_catalog."default",
check_sum character varying(255) COLLATE pg_catalog."default"
)
TABLESPACE pg_default;
ALTER TABLE IF EXISTS public.us_llp_hash_staging
OWNER to postgres;
