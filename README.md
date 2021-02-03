
# Dublin bus

# 1st place - students final projects

[Presentation - Youtube](https://youtu.be/-PGyBLPF-IM "Presentation - Youtube")


![](https://i.imgur.com/fSJlKGa.png)


### Features
In this project we help the Dublin bus control center and citizines of the city by:
- Indicating about picks in air pollution (data by [BreezoMeter.com](http://BreezoMeter.com "BreezoMeter.com"))

- Recommendations for city closure - when the air pollution is extremely high, we want to reduce the emissions and advice to prevent the entrance of private cars to the city center. 

------------


[![](https://i.imgur.com/7KBRL38.png)](https://i.imgur.com/7KBRL38.png)


- Real time transit alternatives - car drivers are provided with an alternative transportation option with buses to the city center, based on real-time bus locations (Kafka stream)

------------


[![](https://i.imgur.com/oSbMGHu.png)](https://i.imgur.com/oSbMGHu.png)


### Prerequisites
               
1. Databricks machine with Databricks Runtime Version 6.4 (includes Apache Spark 2.4.5, Scala 2.11)
    * Python 3.7 or higher
    * spark-cassandra-connector v2.4 from:
      https://github.com/datastax/spark-cassandra-connector
    * packages from requirements.txt in this repo
<br>

2.  Another machine that will be our warehouse, together with Apache Cassandra cluster

	* This warehouse is used to log extreme air quality events
	* See instructions below for setup

### notebook.ipnyb
*  Contains the cells to run to reproduce the code
* Kafka stream with dublin-bus data is needed
* Air Quality data is needed but we will not supply

In this code we:
- Show the closure location that will be recommended upon high air pollution event.
- Calculate bus rides alternatives between every two bus stops in Dublin, based on kafka stram bus locations
- Process air quality data
- Read the Kafka data stream for dublin-bus data
- Impute missing data in air quality index values by windowed mean imputation [[1]](#1)
- Create and updated live map for the dashboard, deploy it to git to update the bus control center dashboard.
- Upload data to warehouse regarding times of high air pollution events

### Cassandra cluster setup
- We will setup Cassandra 3.11.9 on our machine, configure the cluster and open communication with other machine on the same network
1. If reinstalling first do:
```bash
sudo apt-get remove cassandra
sudo apt-get autoremove cassandra
sudo rm -rf /var/lib/cassandra
sudo rm -rf /var/log/cassandra
sudo rm -rf /etc/cassandra
```
- Delete everything it finds:  `sudo find / -name 'cassandra'`

2. Fresh installation of Cassandra
```bash
sudo rm -rf /etc/apt/sources.list.d/cassandra.sources.list
echo "deb https://downloads.apache.org/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra
```

3. Verify you see cassandra.yaml `ls /etc/cassandra/ `

4. Verify node is running : `nodetool status`

5. Run `cqlsh ` than exit

6. Stop cassandra: `nodetool drain` and let it finish, than run `systemctl stop cassandra`

7. Configure cassandra.yaml (configuration file): `sudo nano /etc/cassandra/cassandra.yaml`
- Change in the nano editor
  * rpc_address -> 0.0.0.0
  * broadcast_rpc_address: 1.2.3.4 -> uncomment this line
  * data_file_directories -> /StudentData/cassandra/data
  * commitlog_directory -> /StudentData/cassandra/commitlog

	* later on if you encouter querying request timeout  you should add few  zeroes (2-6) to every number like in:
   - https://stackoverflow.com/a/54800860/13727260
   - https://stackoverflow.com/questions/37114455/reading-error-in-cassandra

8. Create new data dir in the right disk partition, move existing cassandra folders to the partition
```bash
mkdir /StudentData/cassandra
sudo mv /var/lib/cassandra/data /StudentData/cassandra
sudo mv /var/lib/cassandra/commitlog /StudentData/cassandra
```


9. Finally, Start cassandra: `systemctl start cassandra` than `journalctl -f -u cassandra` and exit.

Thats it!

### References
<a id="1">[1]</a>
 [A Review of Missing Data Treatment Methods](https://spu.fem.uniag.sk/cvicenia/ksov/prokeinova/MBA-Business%20Modelling/Lecture%201/Missing%20values/missing%20values.pdf "A Review of Missing Data Treatment Methods"). Liu Peng, Lei Lei.

_____

[Yotam Martin](https://www.linkedin.com/in/yotam-martin-b41493170/ "Yotam Martin"), 
[Gal Goldstein](https://www.linkedin.com/in/gal-goldstein-8776b0168/ "Gal Goldstein")
