# Stock Market Kafka Real Time Data Engineering Project

## Introduction 
In this project, you will execute an End-To-End Data Engineering Project on Real-Time Stock Market Data using Kafka.

We are going to use different technologies such as Python, Amazon Web Services (AWS), Apache Kafka, Glue, Athena, and SQL.

## Architecture 
<img src="Architecture.jpg">

## Technology Used
- Programming Language - Python
- Amazon Web Service (AWS)
1. S3 (Simple Storage Service)
2. Athena
3. Glue Crawler
4. Glue Catalog
5. EC2
- Apache Kafka




---

## Setting Up and Running Kafka on an EC2 Instance

### Prerequisites
Ensure that you have Java installed on your EC2 instance. You can install it using the following commands:

```bash
java -version
sudo yum install java
java -version
```

### Step 1: Download and Extract Kafka

```bash
wget https://downloads.apache.org/kafka/3.5.2/kafka_2.13-3.5.2.tgz
tar -xvf kafka_2.13-3.5.2.tgz
```

### Step 2: Start ZooKeeper

Navigate to the Kafka directory and start ZooKeeper:

```bash
cd kafka_2.13-3.5.2/
bin/zookeeper-server-start.sh config/zookeeper.properties
```

### Step 3: Start Kafka Server

1. Open a new terminal session (or duplicate the current one).
2. SSH into your EC2 instance.
3. Set Kafka heap options:

```bash
export KAFKA_HEAP_OPTS="-Xmx256M -Xms128M"
```

4. Start the Kafka server:

```bash
cd kafka_2.13-3.5.2/
bin/kafka-server-start.sh config/server.properties
```

### Step 4: Configure Kafka to Use Public IP

To ensure Kafka is accessible from outside, update the `server.properties` file:

1. Open the `server.properties` file:

```bash
sudo nano config/server.properties
```

2. Modify the `ADVERTISED_LISTENERS` property to use the public IP of your EC2 instance:

```properties
advertised.listeners=PLAINTEXT://<public-ip>:9092
```

### Step 5: Create a Kafka Topic

1. Open a new terminal session (or duplicate the current one).
2. SSH into your EC2 instance.
3. Create a topic named `demo`:

```bash
cd kafka_2.13-3.5.2/
bin/kafka-topics.sh --create --topic demo --bootstrap-server <kafka-ip>:9092 --replication-factor 1 --partitions 1
```

### Step 6: Start a Kafka Producer

Start a Kafka producer to send messages to the `demo` topic:

```bash
bin/kafka-console-producer.sh --topic demo --bootstrap-server <kafka-ip>:9092
```

### Step 7: Start a Kafka Consumer

Open a new terminal session, SSH into your EC2 instance, and start a Kafka consumer to read messages from the `demo` topic:

```bash
cd kafka_2.13-3.5.2
bin/kafka-console-consumer.sh --topic demo --bootstrap-server <kafka-ip>:9092
```

### Local access of aws
To create an IAM user and get access and secret keys in AWS, follow these steps:

### Step 1: Sign in to AWS Management Console

1. Go to the [AWS Management Console](https://aws.amazon.com/console/).
2. Sign in using your AWS account credentials.

### Step 2: Navigate to IAM (Identity and Access Management)

1. In the AWS Management Console, open the **IAM** service by either:
   - Typing “IAM” in the search bar and selecting it.
   - Finding IAM under “Security, Identity, & Compliance” in the Services menu.

### Step 3: Create a New IAM User

1. In the IAM dashboard, click on **Users** in the left-hand menu.
2. Click the **Add user** button at the top of the page.
3. Enter the **User name** for the new IAM user.
4. Select **Programmatic access** under Access type. This will enable the user to access AWS services via API, CLI, and SDK.
5. Optionally, select **AWS Management Console access** if you want to allow the user to log in to the AWS Management Console. Set a custom password if needed.
6. Click **Next: Permissions**.

### Step 4: Set Permissions

1. Choose how you want to assign permissions to the user:
   - **Attach existing policies directly**: Choose policies that grant permissions directly to the user.
   - **Add user to group**: Add the user to an IAM group with predefined policies.
   - **Copy permissions from existing user**: Copy permissions from another user.
   - **Attach customer managed policies**: Attach custom policies if you have them.
2. Select the policies or groups you want to assign and click **Next: Tags**.

### Step 5: Add Tags (Optional)

1. Add any tags you want to assign to the user (e.g., `Department: Finance`).
2. Click **Next: Review**.

### Step 6: Review and Create User

1. Review the user details and permissions.
2. Click the **Create user** button.

### Step 7: Retrieve Access Key and Secret Key

1. Once the user is created, you will see a success message with the user’s **Access key ID** and **Secret access key**.
2. Click **Download .csv** to save the credentials to a file or copy them manually. **Important: Save the secret access key securely**, as it is only displayed once.

### Step 8: Configure AWS CLI (Optional)

1. Open your terminal or command prompt.
2. Run the command `aws configure`.
3. Enter the **Access Key ID** and **Secret Access Key** when prompted, along with the default region name and output format.

These steps will create an IAM user with programmatic access and allow you to use the access key and secret key to interact with AWS services.

---

---


## Data Pipeline Setup and Analysis

### 1. Set Up S3 Bucket

1. **Create an S3 Bucket**
   - Log in to the AWS Management Console.
   - Navigate to the S3 service.
   - Click on "Create bucket."
   - Enter a unique name for the bucket.
   - Choose a region and configure any other settings as needed.
   - Click "Create bucket."

2. **Update Your Kafka Consumer Notebook**
   - Ensure your Kafka consumer notebook is configured to write data to the newly created S3 bucket.

### 2. Set Up AWS Glue Crawler

1. **Navigate to AWS Glue**
   - Log in to the AWS Management Console.
   - Go to the AWS Glue service.

2. **Create a Glue Crawler**
   - In the Glue Console, select "Crawlers" from the left-hand menu.
   - Click "Add crawler."
   - Provide a name for the crawler.
   - Choose "Data stores" as the source type.
   - Select "S3" and specify the path to your S3 bucket.
   - Set up a crawler schedule (e.g., run on demand or schedule at intervals).
   - Choose or create an IAM role that has permissions to access S3 and Glue.
   - Configure output settings, including the database where the table metadata will be stored.

3. **Run the Crawler**
   - After creating the crawler, select it from the list and click "Run crawler."
   - The crawler will scan your S3 bucket, infer the schema, and create tables in the specified Glue database.

### 3. Analyze Data with Athena

1. **Navigate to AWS Athena**
   - Log in to the AWS Management Console.
   - Go to the Athena service.

2. **Configure Athena**
   - Set up a query result location in S3 if not already configured.
   - Choose the database that was created by the Glue Crawler.

3. **Run Queries**
   - Use the Athena query editor to run SQL queries on the tables created by the Glue Crawler.
   - Analyze your data and generate reports as needed.

### 4. Additional Tips

- **Monitor Glue Crawler and Athena**
  - Regularly check the AWS Glue and Athena dashboards for job statuses and query results.
  
- **Permissions and Security**
  - Ensure that the IAM roles used by Glue and Athena have the necessary permissions to access S3 and other resources.

