# LogScale Installation Guide

> A step-by-step guide for installing **CrowdStrike LogScale** (formerly Humio) on bare-metal Linux servers (RHEL / Rocky Linux).  
> This guide covers all required components: system preparation, Java, ZooKeeper, Kafka (KRaft mode), and LogScale itself.

## 1. System Preparation

> **Purpose:** Install essential utilities and disable the firewall so that component ports (ZooKeeper, Kafka, LogScale) are reachable during setup.

### 1.1 Install Required Packages

```bash
dnf install nano tar -y
```

### 1.2 Disable Firewall

```bash
systemctl stop firewalld
systemctl disable firewalld
```

## 2. Java Installation

> **Reference:** https://computingforgeeks.com/install-java-rhel-rocky-almalinux/  
> **Purpose:** LogScale and Kafka both require a JVM. This guide uses **OpenJDK 21 LTS**, the current Long-Term Support release.
### Prerequisites
- Root or `sudo` access to the server
- Active internet connection for package downloads
- System is up to date:

```bash
sudo dnf update -y
```

### Step 1 — Install OpenJDK 21

Install the full **JDK** (recommended — includes compiler tools):

```bash
sudo dnf install -y java-21-openjdk-devel
```

### Step 2 — Verify the Installation

Check the Java runtime version:

```bash
java -version
```

Also verify the compiler:

```bash
javac -version
```



### Step 3 — Set JAVA_HOME Environment Variable

> **Why:** Many Java-based tools (including Kafka) rely on the `JAVA_HOME` environment variable to locate the JDK. Setting it in a profile script ensures it persists across reboots and for all users.

**3a.** Find the Java installation path:

```bash
dirname $(dirname $(readlink -f $(which java)))
```

**3b.** Create the profile script at `/etc/profile.d/java.sh`:

```bash
cat << 'EOF' | sudo tee /etc/profile.d/java.sh
export JAVA_HOME=$(dirname $(dirname $(readlink -f $(which java))))
export PATH=$JAVA_HOME/bin:$PATH
EOF
```

**3c.** Make it executable:

```bash
sudo chmod +x /etc/profile.d/java.sh
```

**3d.** Load the script and verify:

```bash
source /etc/profile.d/java.sh
echo $JAVA_HOME
```

**3e.** Reboot to apply changes system-wide:

```bash
reboot
```



## 3. ZooKeeper Installation

> **References:**  
> - Release index: https://downloads01-he-fi.apache.org/zookeeper/  
> - Official releases: https://zookeeper.apache.org/releases.html#download  
>
> **Purpose:** ZooKeeper is a distributed coordination service used by LogScale for cluster coordination. Replace `x.x.x` with the desired version (e.g., `3.9.5`).



### Step 1 — Download and Extract ZooKeeper

Navigate to `/opt` and download the ZooKeeper binary archive:

```bash
cd /opt
wget https://archive.apache.org/dist/zookeeper/zookeeper-x.x.x/apache-zookeeper-x.x.x-bin.tar.gz
```

Extract the archive and create a stable symlink (makes upgrades easier):

```bash
tar -zxf apache-zookeeper-x.x.x-bin.tar.gz
ln -s /opt/apache-zookeeper-x.x.x-bin /opt/zookeeper
```



### Step 2 — Create the Data Directory

ZooKeeper stores its snapshots and transaction logs here:

```bash
cd /opt/zookeeper
mkdir -p /var/zookeeper/data
```



### Step 3 — Create a Dedicated System User

> Running ZooKeeper as a dedicated non-login system user limits the blast radius of any security issue.

```bash
sudo useradd -r -s /sbin/nologin -M -U zookeeper
```

| Flag | Meaning |
|---|---|
| `-r` | System account (no home directory by default) |
| `-s /sbin/nologin` | Prevents interactive login |
| `-M` | Do not create home directory |
| `-U` | Create a matching group |



### Step 4 — Configure ZooKeeper

Create the configuration file at `/opt/zookeeper/conf/zoo.cfg`:

```ini
tickTime = 2000
dataDir = /var/zookeeper/data
clientPort = 2181
initLimit = 5
syncLimit = 2
maxClientCnxns=60
autopurge.purgeInterval=1
admin.enableServer=false
4lw.commands.whitelist=*
server.1=127.0.0.1:2888:3888
```

### Step 5 — Set Ownership

```bash
chown -R zookeeper:zookeeper /opt/apache-zookeeper-x.x.x-bin
chown -R zookeeper:zookeeper /var/zookeeper/data
```

### Step 6 — Create the Systemd Service

Create `/etc/systemd/system/zookeeper.service`:

```ini
[Unit]
Description=ZooKeeper Daemon
Documentation=http://zookeeper.apache.org
Requires=network.target
After=network.target

[Service]
Type=forking
WorkingDirectory=/opt/zookeeper
User=zookeeper
Group=zookeeper
ExecStart=/opt/zookeeper/bin/zkServer.sh start /opt/zookeeper/conf/zoo.cfg
ExecStop=/opt/zookeeper/bin/zkServer.sh stop /opt/zookeeper/conf/zoo.cfg
ExecReload=/opt/zookeeper/bin/zkServer.sh restart /opt/zookeeper/conf/zoo.cfg
TimeoutSec=30
Restart=on-failure

[Install]
WantedBy=default.target
```

### Step 7 — Enable and Start ZooKeeper

```bash
systemctl start zookeeper
systemctl status zookeeper
systemctl enable zookeeper
```

## 4. Kafka Installation

> **References:**  
> - LogScale Kafka guide: https://library.humio.com/deployment/installation-baremetal-kafka.html  
> - Kafka downloads: https://downloads.apache.org/kafka/  
>
> **Purpose:** Kafka serves as the ingest queue for LogScale. This guide uses **KRaft mode** (Kafka without ZooKeeper metadata dependency) for Kafka's own coordination, while ZooKeeper above is used by LogScale's cluster logic.  
> Replace `x.xx` (Scala version) and `x.x.x` (Kafka version) with the actual release, e.g., `2.13-3.7.0`.

### Step 1 — Create a Dedicated Kafka User

```bash
sudo useradd -r -s /sbin/nologin -M -U kafka
```

### Step 2 — Download and Extract Kafka

```bash
cd /opt
wget https://downloads.apache.org/kafka/x.x.x/kafka_x.xx-x.x.x.tgz
tar zxf kafka_x.xx-x.x.x.tgz
```

Create a symlink for a stable path:

```bash
ln -s /opt/kafka_x.xx-x.x.x /opt/kafka
```

### Step 3 — Create Required Directories

```bash
mkdir -p /var/log/kafka /opt/kafka/logs
chown -R kafka:kafka /var/log/kafka /opt/kafka
```

### Step 4 — Configure Kafka (`server.properties`)

Open the configuration file for editing:

```bash
nano /opt/kafka_x.xx-x.x.x/config/server.properties
```

Choose the configuration that matches your deployment type:

#### Option A — Single Node Deployment

> Use this for testing or single-server setups.

```properties
node.id=1
controller.quorum.voters=1@127.0.0.1:9093
listeners=PLAINTEXT://127.0.0.1:9092,CONTROLLER://127.0.0.1:9093
advertised.listeners=PLAINTEXT://127.0.0.1:9092
num.partitions=6
log.dirs=/opt/kafka/kraft-combined-logs
```

#### Option B — Multi-Node Cluster (3 Nodes)

> Update `/etc/hosts` on each node first (see Step 5).

**Node 1** (`kafka1`):
```properties
node.id=1
controller.quorum.voters=1@kafka1:9093,2@kafka2:9093,3@kafka3:9093
listeners=PLAINTEXT://kafka1:9092,CONTROLLER://kafka1:9093
advertised.listeners=PLAINTEXT://kafka1:9092
num.partitions=6
log.dirs=/opt/kafka/kraft-combined-logs
```

**Node 2** (`kafka2`):
```properties
node.id=2
controller.quorum.voters=1@kafka1:9093,2@kafka2:9093,3@kafka3:9093
listeners=PLAINTEXT://kafka2:9092,CONTROLLER://kafka2:9093
advertised.listeners=PLAINTEXT://kafka2:9092
num.partitions=6
log.dirs=/opt/kafka/kraft-combined-logs
```

**Node 3** (`kafka3`):
```properties
node.id=3
controller.quorum.voters=1@kafka1:9093,2@kafka2:9093,3@kafka3:9093
listeners=PLAINTEXT://kafka3:9092,CONTROLLER://kafka3:9093
advertised.listeners=PLAINTEXT://kafka3:9092
num.partitions=6
log.dirs=/opt/kafka/kraft-combined-logs
```

### Step 5 — Configure `/etc/hosts` (Multi-Node Only)

> Add hostname-to-IP mappings so nodes can resolve each other by name.

```
192.168.1.15 kafka1
192.168.1.16 kafka2
192.168.1.17 kafka3
```

> ```
> 127.0.1.1 kafka1 localhost
> 127.0.1.1 kafka2 localhost
> 127.0.1.1 kafka3 localhost
> ```



### Step 6 — Initialize KRaft Storage

Create the KRaft log directory and set ownership:

```bash
mkdir -p /opt/kafka/kraft-combined-logs
chown -R kafka:kafka /opt/kafka_x.xx-x.x.x
```

Generate a unique cluster ID and format the storage:

```bash
KAFKA_CLUSTER_ID=$(/opt/kafka/bin/kafka-storage.sh random-uuid)

sudo -u kafka /opt/kafka/bin/kafka-storage.sh format \
  -t "$KAFKA_CLUSTER_ID" \
  -c /opt/kafka/config/server.properties
```

> ⚠️ **Important:** In a multi-node cluster, **use the same `KAFKA_CLUSTER_ID`** on all nodes. Generate it once on Node 1 and copy the value to the other nodes before running `format`.



### Step 7 — Create the Systemd Service

Create `/etc/systemd/system/kafka.service`:

```ini
[Unit]
Description=Apache Kafka (KRaft Mode)
After=network.target

[Service]
Type=simple
User=kafka
LimitNOFILE=800000
Environment="LOG_DIR=/var/log/kafka"
Environment="GC_LOG_ENABLED=true"
Environment="KAFKA_HEAP_OPTS=-Xms512M -Xmx4G"
ExecStart=/opt/kafka/bin/kafka-server-start.sh /opt/kafka/config/server.properties
Restart=on-failure
TimeoutSec=900

[Install]
WantedBy=multi-user.target
```

### Step 8 — Enable and Start Kafka

```bash
systemctl start kafka
systemctl status kafka
systemctl enable kafka
```

## 5. LogScale Installation

> **Reference:** https://library.humio.com/deployment/installation-baremetal-logscale-launcher.html  
> **Check latest version:** https://repo.humio.com/service/rest/repository/browse/maven-releases/com/humio/server-linux_x64/  
>
> **Purpose:** Install and configure the LogScale (Humio) server, which provides log ingestion, search, and dashboards.  
> Replace `x.xxx.x` with the actual version number (e.g., `1.131.0`).

### Step 1 — Create a Dedicated LogScale User

```bash
sudo useradd -r -s /sbin/nologin -M -U humio
```
### Step 2 — Restrict SSH Access for the humio User

> Prevents the `humio` service account from being used to log in via SSH or SFTP — an important security hardening step.

Append the deny rule to the SSH config:

```bash
echo "DenyUsers humio" | sudo tee -a /etc/ssh/sshd_config
```

Validate the SSH configuration before restarting (avoids locking yourself out):

```bash
sudo sshd -t
```

If no errors appear, restart SSH:

```bash
sudo systemctl restart sshd
```

### Step 3 — Create System Directories

```bash
mkdir -p /var/log/humio /var/humio/data /opt/humio /etc/humio
chown -R humio:humio /var/log/humio /var/humio/data /opt/humio
chown -R root:humio /etc/humio
chmod -R 750 /etc/humio
```

### Step 4 — Download and Extract LogScale

```bash
cd /opt/humio/
wget https://repo.humio.com/repository/maven-releases/com/humio/server-linux_x64/x.xxx.x/server-linux_x64-x.xxx.x.tar.gz
tar xzf /opt/humio/server-linux_x64-x.xxx.x.tar.gz
```

### Step 5 — Configure LogScale

Create the main configuration file at `/etc/humio/server.conf`:

```bash
sudo nano /etc/humio/server.conf
```

```properties
AUTHENTICATION_METHOD=single-user
DIRECTORY=/var/humio/data
HUMIO_AUDITLOG_DIR=/var/log/humio
HUMIO_DEBUGLOG_DIR=/var/log/humio
JVM_LOG_DIR=/var/log/humio
HUMIO_PORT=8080
ELASTIC_PORT=9200
KAFKA_SERVERS=kafka1:9092,kafka2:9092,kafka3:9092
KAFKA_BOOTSTRAP_SERVERS=kafka1:9092,kafka2:9092,kafka3:9092
EXTERNAL_URL=http://127.0.0.1:8080
PUBLIC_URL=http://127.0.0.1
CHATTER_INITIAL_REPLICATION_FACTOR=1
GLOBAL_INITIAL_REPLICATION_FACTOR=1
INGEST_QUEUE_INITIAL_REPLICATION_FACTOR=1
```

> 💡 For a single-node deployment, set `KAFKA_SERVERS=127.0.0.1:9092` and `KAFKA_BOOTSTRAP_SERVERS=127.0.0.1:9092`.



### Step 6 — Create the Systemd Service

Create `/etc/systemd/system/humio.service`:

```ini
[Unit]
Description=LogScale service
After=network.service

[Service]
Type=notify
Restart=on-abnormal
User=humio
Group=humio
LimitNOFILE=250000:250000
EnvironmentFile=/etc/humio/server.conf
WorkingDirectory=/var/humio
ExecStart=/opt/humio/humio/bin/humio-server-start.sh
TimeoutSec=900

[Install]
WantedBy=default.target
```

### Step 7 — Enable and Start LogScale

Fix final ownership, then start the service:

```bash
chown -R humio:humio /var/log/humio /var/humio/data
systemctl start humio
systemctl enable humio
```

Monitor the startup logs in real time:

```bash
journalctl -fu humio
```

> ✅ **Success indicator:** Look for a log line similar to `LogScale is ready` or `Started HTTP server on port 8080`. Then open `http://<server-ip>:8080` in your browser.



## Summary
| Component | Port(s) | Service Name | User |
|---|---:|---|---|
| ZooKeeper | 2181, 2888, 3888 | `zookeeper` | `zookeeper` |
| Kafka | 9092, 9093 | `kafka` | `kafka` |
| LogScale | 8080, 9200 | `humio` | `humio` |


## Troubleshooting Tips

- **ZooKeeper not starting:** Check `/opt/zookeeper/logs/` and ensure `dataDir` exists with correct ownership.
- **Kafka not starting:** Confirm KRaft storage was formatted with `kafka-storage.sh format` before first start.
- **LogScale not connecting to Kafka:** Verify `KAFKA_SERVERS` in `server.conf` matches your actual Kafka broker addresses and that Kafka is running.
- **Port conflicts:** Use `ss -tlnp | grep <port>` to check if a port is already in use.
- **Logs:** Use `journalctl -fu <service-name>` for any service to tail live logs.
