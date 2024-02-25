# Install Airflow on Ubuntu 20.04 EC2 instance

Firstly create a new EC2 instance a free-tier instance with `t2.small` configuration will suffice. Download a key pair or create a new (RSA) key pair - the `.pem` file will download locally. On the Firewall security settings `Allow HTTP and HTTPS traffic from the internet`. Then edit the security groups and add `Edit inbound rules` and add a rule:

``` text
Custom TCP | TCP | 8080 | Anywhere IPv4
```

Connect to the EC2 instance using `EC2 Instance Connect` or `SSH client`

Then follow the install instructions from docs <https://airflow.apache.org/docs/apache-airflow/stable/start.html>

## Step 1: Update the System

```bash
sudo apt update
sudo apt upgrade -y
```

##  Step 2: Install Python

```bash
python3 --version

sudo apt install python3-pip python3-venv -y
```

## Step 3: Create a Virtual Environment

```bash
export AIRFLOW_HOME=~/airflow

mkdir airflow && cd airflow

python3 -m venv airflow_venv

source airflow_venv/bin/activate

sudo pip install pandas

sudo pip install s3fs

pip install connexion[swagger-ui]

AIRFLOW_VERSION=2.8.1
```

### Extract the version of Python you have installed. If you're currently using a Python version that is not supported by Airflow, you may want to set this manually.

```bash
PYTHON_VERSION="$(python --version | cut -d " " -f 2 | cut -d "." -f 1-2)"

CONSTRAINT_URL="https://raw.githubusercontent.com/apache/airflow/constraints-${AIRFLOW_VERSION}/constraints-${PYTHON_VERSION}.txt"
```

### For example this would install 2.8.1 with python 3.8: <https://raw.githubusercontent.com/apache/airflow/constraints-2.8.1/constraints-3.8.txt>

###  Step 4: Install Apache Airflow

```bash
pip install "apache-airflow==${AIRFLOW_VERSION}" --constraint "${CONSTRAINT_URL}"
```

###  Step 5: Initialize the Airflow Database

```bash
airflow db init

airflow users create \
    --username admin \
    --firstname etty \
    --lastname sekhon \
    --role Admin \
    --email <etty.sekhon@gmail.com>
```

###   Step 8: Start the Airflow Scheduler

either run

```bash
airflow standalone
```

or

```bash
airflow webserver --port 8080
```

In a new terminal session (SSH into your EC2 instance again), activate your virtual environment as before and start the Airflow scheduler:

```bash
cd airflow
source airflow_venv/bin/activate
airflow scheduler
```

### Step 9: Access the Airflow Web Interface

You should now be able to access the Airflow web interface by navigating to http://YOUR_EC2_IP:8080 in a web browser. Replace YOUR_EC2_IP with the public IP address of your EC2 instance.

You may need to check if Airflow webserver is running or check if process is running on 8080:

```bash
sudo lsof -i tcp:8080
```

AWS commands:

```bash
sudo apt  install awscli
aws configure
aws sts get-session-token
```
