This application will allow you to create a sharded MongoDB database and import data from a .csv file into the database. 
The database will be created using docker-compose to containerize and host each service. You will then be able to import data using python.

1) Install all dependencies needed.
```
sudo apt install docker
sudo apt install docker-compose
sudo apt install python
```
   If you are running this on a clean OS, you may run the setup.sh script.
```
. /new-install/setup.sh
```

2) Download this repository and browse to the directory it was placed in.
```
git clone https://github.com/jakeHebert/mongo-csv-import
```

3) Start the application with:
```
docker-compose up
```

4) Configure the dbs to allow them to communicate with each other by running these commands one-by-one:
```
docker exec -it mongocfg1 bash -c "echo 'rs.initiate({_id: \"mongors1conf\",configsvr: true, members: [{ _id : 0, host : \"mongocfg1\" },{ _id : 1, host : \"mongocfg2\" }, { _id : 2, host : \"mongocfg3\" }]})' | mongo"
docker exec -it mongors1n1 bash -c "echo 'rs.initiate({_id : \"mongors1\", members: [{ _id : 0, host : \"mongors1n1\" },{ _id : 1, host : \"mongors1n2\" },{ _id : 2, host : \"mongors1n3\" }]})' | mongo"
docker exec -it mongos1 bash -c "echo 'sh.addShard(\"mongors1/mongors1n1\")' | mongo "
```

5) Move a csv file into the app-data for easier access.
```
mv <filename> mongo-csv-import/app-data
```
  Or if the file is on a host machine that needs to be moved to a virtual machine, run this first, then move the file:
```
scp <filename> <username>@<vm ip address>:~/
```

6) Run import.py from the app-data directory:
```
python import.py <"csvfilename.csv">
```
