## Performance Test Harness

1. Follow the instructons on ['Creating and testing message loads'](https://ibm.github.io/event-automation/es/getting-started/testing-loads/)
2. Install via your preffered method outside of the cluster, proceed to configure the `producer.config` file
Configuration to connect to the kafka cluster as below:

### Bootstrap servers 
- This is the external url of the kafka listener, this can be found by Navigating to and selecting `Connect to this cluster`
 > Set this to the listener with the credential type you wish to use for authentication i.e TLS or SCRAM
    ```
    bootstrap.servers=<external-listner-address>:<PORT>
    ```

### Cluster Certificates
1. In the UI, select `topics` from the side bar and select the topic you wish to populate, or create one if you have yet to.
2. In the top right select `Connect to this Topic`
3. Navigate to the subsection `Certificates` and download the PKCS12 certificate, and take note of the password
4. Fill details for cluster certificate as below:
    ```
    ssl.truststore.location=PATH_TO_DOWNLOADED_CERT
    ssl.truststore.password=GENERATED_PASSWORD
    ssl.truststore.type=PKCS12
    ```
### Auth
1. Assuming TLS credentials are being used, in the same Cluster connection page within the UI select `Generate TLS Credentials`
2. Enter a credential name
3. Select `Produce messages, consume messages and create topics and schemas` 
4. Choose whether you want to provide access to more than this topic
5. Do the same for consumer groups and transactions
6. Click generate credentials
7. Download certificate and password
8. Fill config as follows:
    ```
    security.protocol=SSL
    ssl.keystore.type=PKCS12
    ssl.keystore.location=PATH_TO_DOWNLOADED_CERT
    ssl.keystore.password=GENERATED_PASSWORD
    ```
- If using SCRAM follow the same steps for `Generate SCRAM Credentials` and fill details as below
    ```
    #security.protocol=SASL_SSL
    #ssl.protocol=TLSv1.2
    #sasl.mechanism=SCRAM-SHA-512
    #sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="<USER> password="<PASSWORD>";
    ```
> NOTE: ensure #ssl.protocol is commented out for the inactive auth.

## Running test 
Full instructions for running can be found in the [documentation](https://ibm.github.io/event-automation/es/getting-started/testing-loads/#specify)

To use a predefined load size from the producer application, use the `es-producer.jar`with the `-s` option:
```sh
java -jar target/es-producer.jar -t <topic-name> -s <small/medium/large>
```
