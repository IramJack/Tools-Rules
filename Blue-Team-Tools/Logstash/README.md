# Logstash: Data Processing Unit

Logstash is an open-source data processing engine that enables you to **collect, enrich, and transform data** from multiple sources. It is commonly paired with other components of the **Elastic Stack (ELK)** — such as **Elasticsearch** and **Kibana** — to build a complete data processing and visualization pipeline.

By TryHackMe: https://tryhackme.com/room/logstash

In this room, we will dive into Logstash in detail, exploring how data can be ingested, parsed, normalized, and delivered to a variety of output destinations.

![Image](https://github.com/user-attachments/assets/59684481-7a45-4ac5-b33d-b2ec750484a2)

### Learning Objectives

By the end of this room, you should be able to:

* Understand the **ELK Stack** and its core components.
* Install and configure the ELK Stack.
* Explore various **input, filter, and output plugins** available in Logstash.
* Use the **Grok plugin** to parse and normalize unstructured data.

---

## Elasticsearch

![Image](https://github.com/user-attachments/assets/3cdba0b8-a7cb-4862-a107-5c0bd044a9b8)

**Elasticsearch** is a distributed, open-source **search and analytics engine** designed to store, search, and analyze large volumes of data in near real-time. Built on **Apache Lucene**, it provides scalability for:

* Full-text search
* Structured queries
* Advanced analytics

In this room, Elasticsearch will serve as the **storage layer**, housing data after it has been normalized and filtered by Logstash. To achieve this, let’s first install and configure Elasticsearch.

Official Reference: [[Elasticsearch Documentation](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html)](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html)

---

### Installing Elasticsearch

The installation package is located at `/home/tools/elasticsearch`.

Run the following command as the root user to install:

```bash
dpkg -i elasticsearch.deb
```

> **Note:** Switch to the root account with `sudo su` before running the command.

During first-time installation, a **security configuration** screen will appear, displaying the default password for the `elastic` user. Make sure to save this for later use.

<img width="1242" height="596" alt="Image" src="https://github.com/user-attachments/assets/b1c29c54-3202-4b74-9fa9-07f60f1ca898" />

Once completed, Elasticsearch is installed. To make the service persistent:

```bash
systemctl enable elasticsearch.service
systemctl start elasticsearch.service
```

---

### Checking Elasticsearch Status

Verify that the service is running:

```bash
systemctl status elasticsearch.service
```

If successful, the output will confirm Elasticsearch is active.

---

### Configuring Elasticsearch

Configuration files for Elasticsearch are stored in `/etc/elasticsearch`.

<img width="1123" height="114" alt="Image" src="https://github.com/user-attachments/assets/8b868eb9-fac8-47c4-845a-b3576d521061" />

Key files include:

* **elasticsearch.yml** → Defines cluster, network, and storage behavior.
* **jvm.options** → Controls JVM settings like memory and garbage collection.
* **log4j2.properties** → Configures logging outputs and levels.
* **users** → Manages user authentication and authorization.
* **roles.yml** & **roles\_mapping.yml** → Define roles and their assigned privileges.

Edit the configuration:

```bash
nano elasticsearch.yml
```

<img width="1213" height="164" alt="Image" src="https://github.com/user-attachments/assets/9058e968-5df1-4e63-935a-b6b7f462a1a8" />

Update the **network section**:

```yaml
network.host: 127.0.0.1
http.port: 9200
```

Save the changes and restart Elasticsearch:

```bash
systemctl restart elasticsearch.service
```

---

## Logstash

![Image](https://github.com/user-attachments/assets/8753668b-2ee0-4b1e-a849-2606b843b38f)

Logstash will process data before sending it to Elasticsearch. The installation package is located at `/home/tools/logstash`.

Install with:

```bash
dpkg -i logstash.deb
```

---

### Persistence and Status

Enable and start Logstash:

```bash
systemctl daemon-reload
systemctl enable logstash.service
systemctl start logstash.service
```

Check status:

```bash
systemctl status logstash.service
```

<img width="976" height="505" alt="Image" src="https://github.com/user-attachments/assets/96d1220b-79e9-4ba8-9a1f-4bf82a55b058" />

---

### Configuring Logstash

Configuration files are located in `/etc/logstash`.

<img width="964" height="60" alt="Image" src="https://github.com/user-attachments/assets/e0e1e9f0-9031-43d6-8262-f16fda592836" />

Important files and directories include:

* **logstash.yml** → Global Logstash configuration.
* **jvm.options** → JVM settings for memory and performance.
* **log4j2.properties** → Log configuration.
* **pipelines.yml** → Define multiple pipelines.
* **conf.d/** → Store pipeline config files.
* **patterns/** → Custom grok patterns.
* **startup.options** → Additional startup arguments.

Open `logstash.yml`:

```bash
nano logstash.yml
```

<img width="606" height="508" alt="Image" src="https://github.com/user-attachments/assets/6332e53d-784e-4ee0-af78-a750620897fc" />

Modify the pipeline settings:

```yaml
config.reload.automatic: true
config.reload.interval: 3s
```

This ensures Logstash reloads its configurations every 3 seconds when changes occur.

Restart Logstash:

```bash
systemctl restart logstash.service
```

Official Reference: [[Logstash Documentation](https://www.elastic.co/guide/en/logstash/current/index.html)](https://www.elastic.co/guide/en/logstash/current/index.html)

---

## Kibana

![Image](https://github.com/user-attachments/assets/7d7153f5-9c7e-4f55-b1a8-b54ac0f58237)

**Kibana** is the visualization layer of the ELK Stack. Its installation package is at `/home/tools/kibana`.

Install with:

```bash
dpkg -i kibana.deb
```

---

### Persistence and Status

Enable and start Kibana:

```bash
systemctl daemon-reload
systemctl enable kibana.service
systemctl start kibana.service
```

Check status:

```bash
systemctl status kibana.service
```

<img width="611" height="258" alt="Image" src="https://github.com/user-attachments/assets/95123faf-39e8-4b7f-a0fe-90057f74e250" />

---

### Configuring Kibana

The configuration directory is `/etc/kibana`.

Key files:

* **kibana.yml** → Main config file (server host/port, Elasticsearch URL, logging, security).
* **kibana.keystore** → Securely stores sensitive values like API keys and passwords.

Edit `kibana.yml`:

```bash
nano kibana.yml
```

<img width="613" height="547" alt="Image" src="https://github.com/user-attachments/assets/bd21c8ec-ef94-4859-9eea-c4e3acc43124" />

Update server settings:

```yaml
server.port: 5601
server.host: "0.0.0.0"
```

Restart Kibana:

```bash
systemctl restart kibana.service
```

Access the Kibana interface at:

```
http://MACHINE_IP:5601
```

Generate an enrollment token for Kibana:

```bash
/usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana
```

<img width="674" height="571" alt="Image" src="https://github.com/user-attachments/assets/9f41adfb-b34b-46e8-ad28-ab275e421814" />

Obtain the verification code:

```bash
/usr/share/kibana/bin/kibana-verification-code
```

<img width="942" height="680" alt="Image" src="https://github.com/user-attachments/assets/fe23a427-c410-4299-aa45-ef0bf392b347" />

---

## ELK Installation Complete

Congratulations — the **ELK Stack** is now installed successfully!

<img width="961" height="895" alt="Image" src="https://github.com/user-attachments/assets/68725630-4940-424b-9ffc-e18d6c894561" />

Kibana can now be used to visualize logs parsed and filtered by Logstash.

**Note:** Since this room focuses on Logstash, stop Kibana to avoid unnecessary VM performance issues:

```bash
systemctl stop kibana.service
```
Official Reference: [[Kibana Documentation](https://www.elastic.co/guide/en/kibana/current/index.html)](https://www.elastic.co/guide/en/kibana/current/index.html)

With Elasticsearch, Logstash, and Kibana configured, you now have a fully functional ELK environment ready for data ingestion, processing, and visualization.


---

# Logstash: Overview

Let’s dive deeper into the details of **Logstash**.

Logstash is a powerful **data processing engine** that ingests data from multiple sources, applies transformations or filters, and sends the results to destinations such as **Kibana**, **Elasticsearch**, or even a listening port.

A Logstash configuration file is divided into **three key sections**:

![Image](https://github.com/user-attachments/assets/428496ed-fa29-4099-83da-d88e91d743d5)

Each section is explained in detail below.

---

## Input

Logstash provides a wide range of **input plugins** that allow you to capture data from diverse sources such as:

* Log files
* System metrics
* Databases
* Message queues
* APIs
* Cloud services

These input plugins support multiple formats and protocols, making Logstash highly versatile in data collection.

<img width="2085" height="1113" alt="Image" src="https://github.com/user-attachments/assets/7668b5d4-b4d8-4d32-9756-ea3538e349d9" />

Below is a list of the **top 10 input plugins** with brief explanations:

<img width="842" height="484" alt="Image" src="https://github.com/user-attachments/assets/3ccca4f9-0cf6-422d-b2f9-a3386015037c" />

Reference: [[Logstash Input Plugins Documentation](https://www.elastic.co/guide/en/logstash/current/input-plugins.html)](https://www.elastic.co/guide/en/logstash/current/input-plugins.html)

---

## Filter

After data is ingested, Logstash offers a rich set of **filter plugins** to transform and enrich the data. Filters can:

* Parse unstructured logs
* Extract specific fields
* Convert data formats
* Apply regular expressions
* Add timestamps
* Enrich data from external sources

Filters are **optional** and should be applied based on your use case.

<img width="2066" height="1031" alt="Image" src="https://github.com/user-attachments/assets/d30bbd61-31b4-4805-a998-6e1a6c8063ab" />

Below is a list of the **top 10 filter plugins** with brief explanations:

<img width="841" height="489" alt="Image" src="https://github.com/user-attachments/assets/6b57605e-7ec2-4378-876c-49693e42c784" />

Reference: [[Logstash Filter Plugins Documentation](https://www.elastic.co/guide/en/logstash/current/filter-plugins.html)](https://www.elastic.co/guide/en/logstash/current/filter-plugins.html)

---

## Output

Finally, Logstash provides **output plugins** to forward processed data to different destinations, including:

* **Elasticsearch** (for indexing and search)
* Databases
* Message queues
* Cloud storage
* Monitoring platforms

You can configure one or more output destinations as needed.

<img width="2078" height="1067" alt="Image" src="https://github.com/user-attachments/assets/5923305c-bc64-4ba4-a87f-187f04d8bbf6" />

Some of the most commonly used output plugins are explained below:

<img width="845" height="484" alt="Image" src="https://github.com/user-attachments/assets/1eda3d54-ddd2-4e31-aacb-98488f6b8a90" />

Reference: [[Logstash Output Plugins Documentation](https://www.elastic.co/guide/en/logstash/current/output-plugins.html)](https://www.elastic.co/guide/en/logstash/current/output-plugins.html)

---

## Writing Configurations

When setting up Logstash earlier, we modified `logstash.yml` to reload configuration files every **3 seconds**. By default, Logstash looks for configuration files inside:

```
/etc/logstash/conf.d/
```

Let’s walk through an example configuration.

### Example Scenario

Take input from **TCP port 5456**, receive **JSON logs**, apply a JSON filter, and then send the output to **Elasticsearch**.

---

### 1) Input: TCP Plugin

For this example, we’ll use the **TCP plugin**. In its configuration, the only required field is the **port**.

<img width="1485" height="991" alt="Image" src="https://github.com/user-attachments/assets/67db69f6-8c9b-4052-a04a-6b04c33e26db" />

Configuration:

<img width="593" height="226" alt="Image" src="https://github.com/user-attachments/assets/57525031-1354-4517-8467-f432800f3c5d" />

---

### 2) Filter: JSON Plugin

The input stream is in JSON format, so we’ll use the **JSON filter plugin**. It requires specifying a **source** field.

<img width="1455" height="861" alt="Image" src="https://github.com/user-attachments/assets/805e268d-1003-46b4-95da-1fa688427b8e" />

Configuration:

<img width="627" height="121" alt="Image" src="https://github.com/user-attachments/assets/9c0ee486-2d8a-4ac4-8b4c-53a28065310f" />

---

### 3) Output: Elasticsearch

Finally, we send the output to Elasticsearch. While no field is mandatory, it’s best practice to specify the **host** and **index**.

<img width="1688" height="1094" alt="Image" src="https://github.com/user-attachments/assets/a84e548d-6c56-43e9-be54-34b621c9e725" />

Configuration:

<img width="579" height="294" alt="Image" src="https://github.com/user-attachments/assets/0c970943-db32-4be7-8a22-25054c7d80e2" />

---

### Final Configuration

Bringing it all together, the complete pipeline looks like this:

<img width="538" height="449" alt="Image" src="https://github.com/user-attachments/assets/8bd79281-0fe2-46c1-89fc-676a6a3d0a49" />

Save this configuration file with a `.conf` extension inside:

```
/etc/logstash/conf.d/
```

Logstash will automatically load and apply it.

---

## Logstash: Input Configurations

Now that we’ve covered the basics, let’s look at a few **commonly used input plugins** and their configuration options.

---

### File Input Plugin

Reads data from local or network-based files.

<img width="606" height="239" alt="Image" src="https://github.com/user-attachments/assets/7048e6ff-b6f1-4445-99b0-50cb6ce9ef2f" />

* **path** → Path to the file(s) to read.
* **start\_position** → Where to start reading (e.g., `"beginning"`).
* **sincedb\_path** → Tracks the current read position.

---

### Beats Input Plugin

Receives data from **Beats**, lightweight shippers that forward logs to Logstash.

<img width="674" height="175" alt="Image" src="https://github.com/user-attachments/assets/f369bc67-2151-491c-8d49-271b3d354404" />

* **port** → Port number for Beats connections.

---

### TCP Input Plugin

Listens for data over TCP.

<img width="613" height="210" alt="Image" src="https://github.com/user-attachments/assets/deb075d7-a8b6-4fbe-b3c6-e0be3d8cf799" />

* **port** → TCP port number.
* **codec** → Codec for decoding (e.g., JSON).

---

### UDP Input Plugin

Listens for data over UDP.

<img width="785" height="213" alt="Image" src="https://github.com/user-attachments/assets/c3c4866e-81c9-48e4-b1c7-b0916f5cb73f" />

* **port** → UDP port number.
* **codec** → Codec (e.g., plain text).

---

### HTTP Input Plugin

Creates an HTTP endpoint to receive data via HTTP requests.

<img width="532" height="189" alt="Image" src="https://github.com/user-attachments/assets/a34331fc-3d33-438f-ae88-6498638bef9a" />

* **port** → HTTP port number.

---

## Summary

Each **input plugin** comes with configuration options tailored to its purpose. By specifying the right parameters (paths, ports, codecs, etc.), you can customize Logstash to fit your data ingestion needs.

Reference: [[Logstash Plugin Reference](https://www.elastic.co/guide/en/logstash/current/plugins.html)](https://www.elastic.co/guide/en/logstash/current/plugins.html)

In the upcoming tasks, we’ll explore additional plugins and build more complex Logstash pipelines for real-world use cases.

---

# Logstash: Filter Configurations

## Filters

Now that we’ve explored Logstash input plugins, let’s move on to filters. Filters enable powerful data transformations, allowing analysts to manipulate, enrich, or clean data before sending it to the final destination. Below are some commonly used filter plugins, along with explanations and examples:

### Adding a Field Using the Mutate Filter

<img width="649" height="183" alt="Image" src="https://github.com/user-attachments/assets/72c0a977-0925-44ad-9c88-ad8baf6b7e3d" />

**Explanation:** This configuration applies the `mutate` filter to add a new field called `new_field` with the value `"new_value"` to every event. The `mutate` filter supports multiple operations for modifying event fields.

### Converting a Field to Lowercase Using the Mutate Filter

<img width="663" height="184" alt="Image" src="https://github.com/user-attachments/assets/ab814539-e0b9-4a0f-bb22-f1fa82ef7ff7" />

**Explanation:** This example uses the `mutate` filter to convert the value of `field_name` to lowercase. The original value is replaced with its lowercase equivalent.

### Extracting Data Using the Grok Filter

<img width="800" height="190" alt="Image" src="https://github.com/user-attachments/assets/fce29e5b-be40-4327-8c8d-fb909d3577f8" />

**Explanation:** Here, the `grok` filter parses data from the `message` field using a predefined pattern (`%{PATTERN}`). The extracted values are stored in a new field (`field_name`). Grok is widely used for parsing unstructured logs.

### Removing Empty Fields Using the Prune Filter

<img width="666" height="182" alt="Image" src="https://github.com/user-attachments/assets/a5e5c665-0d18-4538-b7df-17515d53d77f" />

**Explanation:** The `prune` filter removes empty fields from an event while keeping those defined in the `whitelist_names` parameter. In this case, only `field1` and `field2` are retained.

### Replacing Field Values Using the Translate Filter

<img width="885" height="222" alt="Image" src="https://github.com/user-attachments/assets/7dceaab1-bf49-434d-81aa-9fe31563310f" />

**Explanation:** This configuration applies the `translate` filter to map field values. For instance, the value `"US"` in the `country` field is replaced with `"United States"`, and `"CA"` with `"Canada"`.

### Dropping Events Based on a Condition

<img width="767" height="210" alt="Image" src="https://github.com/user-attachments/assets/737e3489-3a62-48d6-98d8-984debd43b23" />

**Explanation:** By combining conditional logic (`if-else`) with the `drop` filter, Logstash can discard unwanted events. In this case, if `status` equals `"error"`, the event is dropped; otherwise, it proceeds through the pipeline.

### Parsing Key-Value Pairs Using the KV Filter

<img width="780" height="186" alt="Image" src="https://github.com/user-attachments/assets/a94b4e01-7bf1-4b3a-8ebc-5421dc86e474" />

**Explanation:** The `kv` filter parses key-value pairs from a field. With `field_split => "&"` and `value_split => "="`, it separates data into structured fields.

### Performing Conditional Field Renaming Using the Rename Filter

<img width="710" height="212" alt="Image" src="https://github.com/user-attachments/assets/0040b178-8cc8-4067-9d8c-f9a323af4c42" />

**Explanation:** This configuration uses the `rename` filter to change `old_field` to `new_field`. Additionally, it adds `another_field` with the value `"value"`. This filter is useful for normalizing field names across data sources.

---

# Logstash: Output Plugins

The **output** section of a Logstash configuration defines where transformed events should go. Logstash supports a wide variety of destinations, from search engines to databases and message queues. Below are common examples:

### Sending Data to Elasticsearch

<img width="597" height="204" alt="Image" src="https://github.com/user-attachments/assets/b946dc18-9e47-4de8-9cd3-848e6b44e6fc" />

**Explanation:** This configuration uses the `elasticsearch` output plugin to send data to an Elasticsearch cluster. The `hosts` parameter specifies the server address, while `index` defines where the data will be stored.

### Writing Data to a File

<img width="683" height="189" alt="Image" src="https://github.com/user-attachments/assets/02dc7ce5-451f-497c-8f33-f767214c0da1" />

**Explanation:** The `file` output plugin writes processed events to a file (e.g., `/path/to/output.txt`). Each event is appended as a new line.

### Sending Data to RabbitMQ

<img width="636" height="227" alt="Image" src="https://github.com/user-attachments/assets/51c16194-274e-45f5-931b-e089e67077d6" />

**Explanation:** This example configures the `rabbitmq` output plugin to deliver events to a RabbitMQ server. `exchange` and `routing_key` handle message routing.

### Forwarding Data to Another Logstash Instance

<img width="657" height="185" alt="Image" src="https://github.com/user-attachments/assets/75428ced-28cb-4f16-bc0f-984b1c950b5e" />

**Explanation:** With the `logstash` output plugin, events are forwarded to another Logstash pipeline. The `host` and `port` parameters define the destination.

### Sending Data to a Database (JDBC)

<img width="797" height="231" alt="Image" src="https://github.com/user-attachments/assets/53ef438a-5abd-4c5c-b39f-2742ee430e01" />

**Explanation:** The `jdbc` output plugin allows inserting events into relational databases like MySQL. The `connection_string` holds the DB details, while the `statement` specifies the SQL command.

### Forwarding Data to a TCP Server

<img width="643" height="206" alt="Image" src="https://github.com/user-attachments/assets/c8f4dd79-a2f7-4807-9ad5-de9a90ba3456" />

**Explanation:** This configuration uses the `tcp` output plugin to send events over TCP. The `host` and `port` values define the remote server.

### Publishing Data to Kafka

<img width="758" height="210" alt="Image" src="https://github.com/user-attachments/assets/1e17ced0-5c1a-4c06-818a-4deb2ae009bd" />

**Explanation:** The `kafka` output plugin sends events to an Apache Kafka topic. The `bootstrap_servers` parameter specifies Kafka brokers, while `topic_id` defines the destination topic.

### Sending Data to a WebSocket Endpoint

<img width="759" height="181" alt="Image" src="https://github.com/user-attachments/assets/6cd993e4-9649-406d-a3aa-894893caaa38" />

**Explanation:** With the `websocket` output plugin, Logstash can forward events to WebSocket endpoints. The `url` parameter defines the server and endpoint.

### Forwarding Data to a Syslog Server

<img width="774" height="233" alt="Image" src="https://github.com/user-attachments/assets/368f8b82-096a-4d52-9964-c831ece3b4b0" />

**Explanation:** The `syslog` output plugin allows sending logs to a Syslog server. Parameters include `host`, `port`, and transport protocol (e.g., UDP).

### Writing Data to the Console (Debugging)

<img width="590" height="139" alt="Image" src="https://github.com/user-attachments/assets/8d9c3ae5-3f01-4943-9a98-bf6450de408c" />

**Explanation:** The `stdout` output plugin prints events directly to the console. This is typically used for debugging and verifying pipeline behavior.

---

# Logstash: Running Configurations

Let’s now look at how Logstash configurations are executed.

<img width="889" height="185" alt="Image" src="https://github.com/user-attachments/assets/2d3fc2f7-1e81-4acc-b568-f7f019f4c21d" />

![Image](https://github.com/user-attachments/assets/8daa9353-fd39-4bad-9246-3ff911a0b971)

Logstash configuration files are stored in **`/etc/logstash/conf.d/`**, where the service automatically reads them. Below are two practical configuration examples.

### Configuration #1: Stdin/Stdout

This is the simplest example, using `stdin` for input and `stdout` for output.

<img width="685" height="191" alt="Image" src="https://github.com/user-attachments/assets/727b5cf9-2a87-4aeb-9779-26763faa6aac" />

To test it directly from the terminal:

```bash
/usr/share/logstash/bin/logstash -e 'input { stdin {} } output { stdout {} }'
```

<img width="820" height="498" alt="Image" src="https://github.com/user-attachments/assets/7fdfc1a6-718a-4aad-b516-0aa484dfd0e7" />

Here, we entered:

* `HELLO`
* `Welcome to the TRYHACKME Learning Lab`

Both were reflected back, since no filters were applied.

---

### Configuration #2: Reading Authentication Logs

For a more practical scenario, let’s process Linux authentication logs (`/var/log/auth.log`) and send the filtered results to a file.

<img width="658" height="414" alt="Image" src="https://github.com/user-attachments/assets/cc2ebee6-2d53-487a-a1ff-8e284e0e0aa2" />

Save this configuration as **`logstash.conf`** and run:

```bash
/usr/share/bin/logstash
logstash -f logstash.conf
```

Logstash will begin monitoring authentication logs, apply the defined filters, and write structured results to the specified output file on your desktop.

With these examples, you now have a clear understanding of how **filters**, **outputs**, and **execution commands** come together to form Logstash pipelines.

---

# Exercise: Parsing Web Attack Logs

To put everything together, let’s go through a simple exercise. We will use a CSV file called **`web_attacks.csv`**, apply filters to parse its columns, and then write the processed data to another file on the Desktop.

### Configuration Example

```yaml
input {
  file {
    path => "/home/Desktop/web_attacks.csv"
    start_position => "beginning"
    sincedb_path => "/dev/null"
  }
}

filter {
  csv {
    separator => ","
    columns => ["timestamp", "ip_address", "request", "referrer", "user_agent", "attack_type"]
  }

  # Example: Filter only for SQL Injection and Brute Force attempts (optional)
  if [attack_type] =~ /SQL Injection|Brute Force/ {
    mutate {
      add_field => { "alert" => "Suspicious Attack Detected" }
    }
  }
}

output {
  file {
    path => "/home/Desktop/updated-web-attacks.csv"
  }
}
```

---

### Explanation of the Configuration

* **Input**

  * Reads the CSV file `web_attacks.csv` from the Desktop.
  * `start_position => "beginning"` ensures it reads the file from the top.
  * `sincedb_path => "/dev/null"` avoids Logstash remembering file read positions, so it always starts fresh.

* **Filter**

  * The `csv` filter parses fields into structured columns: `timestamp`, `ip_address`, `request`, `referrer`, `user_agent`, and `attack_type`.
  * An optional conditional checks if the `attack_type` contains **SQL Injection** or **Brute Force** and, if so, adds a new field called `alert` with a warning message.

* **Output**

  * Writes the processed events to a new file called `updated-web-attacks.csv` on the Desktop.

---

# Conclusion

That wraps up this room! 🎯

Logstash has proven to be a powerful tool for log processing and enrichment. In this exercise, we saw how a simple CSV file of web attacks can be transformed into structured and filtered logs ready for deeper analysis.

### Key Takeaways:

* How to **install and configure ELK components**.
* Different **input, filter, and output plugins** available in Logstash.
* How to use the **Logstash documentation** to explore plugins.
* Writing **simple to complex configurations** for real-world use cases.


