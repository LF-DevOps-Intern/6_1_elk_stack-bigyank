## Q.1.

1.1 Install and enable elasticsearch on server 1

```bash
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
sudo apt update
sudo apt install elasticsearch
```

1.2 Verify elastic search is working

![enter image description here](https://i.imgur.com/kZpb68T.png)

Issue the following command using curl to verify that elastic search is working

```bash
curl -X GET "localhost:9200/?pretty"

```

![enter image description here](https://i.imgur.com/uaS0gaA.png)

1.3 Install kibana

```bash
sudo apt install kibana
```

To configure Kibana to start automatically when the system boots up, run the following commands:

```bash
sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable kibana.service
```

![enter image description here](https://i.imgur.com/pW8GVYU.png)


1.4.1 Enable xpack and expose node

Insert/Modify the following lines in `/etc/elasticsearch/elasticsearch.yml`

```bash
network.host: 192.168.0.7    
xpack.security.enabled: true
discovery.type: single-node
xpack.security.authc.api_key.enabled: true
```

![enter image description here](https://i.imgur.com/Fs5yWUs.png)

1.4.2 Create password for builtin users

```bash
sudo systemctl start elasticsearch
./bin/elasticsearch-setup-passwords interactive

```

![enter image description here](https://i.imgur.com/LeHcqye.png)

Check that Basic authentication is working

![enter image description here](https://i.imgur.com/NTL9bqc.png)

![enter image description here](https://i.imgur.com/nms5rkc.png)

1.4.3 Configure Kibana with username and password

Update the following settings in the `kibana.yml` configuration file:

```bash
elasticsearch.password: "elastic"
xpack.security.encryptionKey: "TP8NiVwuuSG72AqziMdul8li3nPcFXGF"

```

the `xpack.security.encryptionKey` property in the `kibana.yml` configuration file. You can use any text string that is 32 characters or longer as the encryption key.

![enter image description here](https://i.imgur.com/Kh3I5Q0.png)

After this restart the kibana service and visit brower to see login prompt

![enter image description here](https://i.imgur.com/qwnmiG0.png)

![enter image description here](https://i.imgur.com/eOg2neS.png)

1.5 Collect Memory usage, Disk usage, Load average in server2 and send them to elasticsearch. Store them in an index named "server2-metrics".

1.5.1 Install `metricbeat` 

We can install metricbeat with following command

```bash
curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.15.2-amd64.deb
sudo dpkg -i metricbeat-7.15.2-amd64.deb
```

![enter image description here](https://i.imgur.com/9hDaPwx.png)

1.5.2 Configure `metricbeat` 
Set the host and port where Metricbeat can find the Elasticsearch installation, and set the username and password of a user who is authorized to set up Metricbeat. For example:

```
output.elasticsearch:
  hosts: ["localhost:9200"]
  username: "elastic"
  password: "elastic"
```

![enter image description here](https://i.imgur.com/quZDEmA.png)

We can verify that the metricbeat service is runn with systmctl command

![enter image description here](https://i.imgur.com/SQOAETr.png)

We can also see the index created by the metricbeat on Kibana

![enter image description here](https://i.imgur.com/l2qtVex.png)

1.5.3 Create index named `server2-metric`

1.5.4 Use `system` module in `metricbeat` for collecting memory usage, disk usage and load average

Add the following lines to the metricbeat configuration

![enter image description here](https://i.imgur.com/f4O0l3K.png)

We can verify the new index on kibana

![enter image description here](https://i.imgur.com/DtGt0F0.png)

## Q.2.

2.1 Create a dashboard in kibana, generate visual report(line graph) for Memory usage and load average of server2 with relation to time

First Create Index Pattern

![enter image description here](https://i.imgur.com/gbMEkIQ.png)

Now verify that the data are available

![enter image description here](https://i.imgur.com/16iOWkM.png)

Next create a dashboard

![enter image description here](https://i.imgur.com/73RtZSX.png)

In the end we can visualize the dashboard

![enter image description here](https://i.imgur.com/64JrYo3.png)

