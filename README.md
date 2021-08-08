### UCSC Genome Browser Docker Image

A minimal UCSC Genome Browser mirror.

http://genome.ucsc.edu/

### License
This is a Dockerized version of the UCSC Genome Browser source code. The license is the same as the UCSC Genome Browser itself. The source code and executables are freely available for academic, nonprofit and personal use. Commercial use requires purchase of a license with setup fee and annual payment. See https://genome-store.ucsc.edu/.

### Download
```shell
docker pull icebert/ucsc_genome_browser

docker pull icebert/ucsc_genome_browser_db
```

### Run with local data files
Assume local data is going to be stored in /my/data/path

First copy the basic database files into /my/data/path from docker

```shell
docker run -d --name gbdb -p 3338:3306 icebert/ucsc_genome_browser_db

cd /my/data/path && docker cp gbdb:/data ./ && mv data/* ./ && rm -rf data

docker stop gbdb

docker run -d --link gbdb:gbdb -p 8038:80 icebert/ucsc_genome_browser
cd /my/gbdb/path && docker cp [here is the id of docker container]:/gbdb ./ && mv gbdb/* ./ && rm -rf gbdb
docker stop [here is the id of docker container]
```

Then put database files into /my/data/path. For example, mirror all the tracks of hg38 from ucsc genome browser

```shell
rm -rf /my/data/path/hg38
rsync -avP --delete --max-delete=20 rsync://hgdownload.soe.ucsc.edu/mysql/hg38 /my/data/path/
rsync -avP --delete --max-delete=20 rsync://hgdownload.soe.ucsc.edu/gbdb/hg38/hg38.2bit /my/gbdb/path/

```

Finally start the database server and genome browser server

```shell
docker run -d --name gbdb -p 3338:3306 -v /my/data/path:/data icebert/ucsc_genome_browser_db

docker run -d --link gbdb:gbdb -p 8038:80 -v /my/gbdb/path:/gbdb icebert/ucsc_genome_browser
```

### MySQL Access
The mysql server listens on port 3338. The default username for mysql is 'admin' with password 'admin'.

```shell
mysql -h 127.0.0.1 -P 3338 -u admin -p
```


###usefull commands:
```shell
docker ps -a 
docker container prune 
docker container stop/start [container id]
docker exec
docker container ls
docker images
```

###how to using mysqlworkbench to add my tracks to browser
1.change the grp table in hg38/mm10 database
2.add tracks into trackDb table, for example:

bigDataUrl /gbdb/userData/mydata/*_norm.bw
longLabel my cuttag data
shortLabel my cuttag
visibility full
autoScale on
maxHeightPixels 50:32:8

see more at:https://genome-asia.ucsc.edu/goldenPath/help/trackDb/trackDbDoc.html#Common_Settings

download the new files according the reported error information:
rsync -avP --delete --max-delete=20 rsync://hgdownload.soe.ucsc.edu/mysql/hg38 /my/data/path/


