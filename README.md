# connecting-ADW-to-OCI-CLI-to-run-a-datapump

##This document shows how to connect ADW to the OCI CLI and carry out a datapump to import the hr schema

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Prerequisites
a. An Oracle database 19c linux on-prem
b. Exported data-pump HR Schema(Easier using SQl-developer--see document "Export HR schema to OCI-Bucket storage")
c. Always Free Autonomous Database
d. Generated token from OCI
--------------------------------------------------------------------------------------------------
-------------------------------------------------
1. Get the OCID of your Autonomous Database Instance----- Go to ADW AND COPY THE ocid

ocid1.autonomousdatabase.oc1.eu-frankfurt-1.-------------------------------------------------

2. Go to the OCI cli and run following commands

a. export ADBID=ocid1.autonomousdatabase.oc1.eu-frankfurt-1.-------------------------------------------------

b. export WNAME=wallet.zip

c. export WPWD=your password

d. oci db autonomous-database generate-wallet --autonomous-database-id $ADBID --file $WNAME --password $WPWD

e. If d. does not work then run the following in order 
	 oci db autonomous-database generate-wallet \
	 autonomous-database-id $ADBID \
	 file $WNAME \
	 password $WPWD

f. type ls and confirm if the wallet.zip file exists

g. unzip wallet.zip

h. type pwd 
result e.g /home/prince_onu

i. vi sqlnet.ora   ----- change directory to the result above..e.g /home/prince_onu

j. export TNS_ADMIN=/home/prince_onu

k. cat tnsnames.ora

L. sqlplus ADMIN@linuxconndatabase_medium

M. Allow your Autonomous Database to Access Object Storage. Generate a token to use as password for the the below command
BEGIN
  DBMS_CLOUD.CREATE_CREDENTIAL(
    credential_name => 'OBJECT_STORE_CRED',
    username => 'max.maaxiols@oracle.com',
    password => 'your generated token'
  );
END;
/

N. Go to the dumpfile in the buckets and create a pre authenticated request for the dump file and copy the url and paste in the below command

o. vi imp.par       the add the following to imp.par

directory=DATA_PUMP_DIR
credential=OBJECT_STORE_CRED
schemas=sampleuser
remap_tablespace=USERS:DATA
dumpfile=https://objectstorage.eu-frankfurt-1.-------------------------------------------------

p. Run the import command---   impdp userid=ADMIN@linuxconndatabase_medium parfile=imp.par




3. sql -cloudconfig $WNAME admin@linuxconndatabase_low

