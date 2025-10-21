# Creating a Connection from HVR to Oracle Autonomous Database

This guide shows you how to configure HVR Software connectivity to Oracle Autonomous Database (ADB). It describes how to connect Oracle Autonomous Database using the wallet or mTLS. If you want to connect without the wallet click [here](https://oracle-samples.github.io/adb-connectors/common/tls-no-wallet/workshops/freetier/).

HVR supports log-based Change Data Capture (CDC) for heterogeneous data replication from several source database technologies, into a multitude of destination technologies and data formats. HVR 5.6 is the first version certified to deliver data into Oracle Autonomous Database.

| **Certification Matrix** | **Version**                               |
| -------------------- | ----------------------------------------- |
| HVR  | 5.6                  |
| OCI Driver  | Oracle Client Version 18.0.0.0 & higher |

For optimum performance integrating changes into Oracle Autonomous Database, HVR recommends an architecture that features the use of agents. Agents are installations of the HVR software that are located as close as possible to the source and destination technologies. The use of agents will optimize network communication, and distribute load. HVR features such as initial load, ongoing replication and compare/repair all take advantage of the agents. With Oracle Autonomous Database as the target, the recommended setup is to include an agent in Oracle Cloud Infrastructure (OCI), in the same availability domain as the Autonomous Database, on a compute instance, or bare metal instance. TCP/IP communication into Oracle Cloud Infrastructure can be encrypted.

![Picture2](./images/hvr_pic2.png)

---

## Step 1: Provision Autonomous Database plus Install and Configure the Oracle Client

1. Provision Oracle Autonomous Database and [download](../common/wallet/wallet.md) the wallet to the system that will have the HVR target agent installation. For the Oracle documentation to provision Autonomous Database, refer to the [Oracle Autonomous Database documentation](https://docs.oracle.com/en/cloud/paas/autonomous-database/index.html). Also check [Downloading Client Credentials (Wallets)](../common/wallet/wallet.md).

2. All connections to Oracle Autonomous Database use certificate-based authentication and Secure Sockets Layer (SSL). Extract the **wallet** file into a secure folder.

3. Download the Oracle Database Client to the system that runs the HVR integration agent.

    1. [Download Linux Client](../common/instant-client/instant-client-linux-64.md)

    2. [Download Windows Client](../common/instant-client/instant-client-windows-64.md)

4. Edit the sqlnet.ora file, replacing "?/network/admin" with the name of the folder containing the client credentials.

    ![Picture3](./images/hvr_pic3.png)

5. The TNS_ADMIN environment variable can be set to point to an alternative location for the sqlnet.ora and tnsnames.ora files. Define the variable if you plan to not use $ORACLE_HOME/network/admin. The tnsnames.ora file in the wallet contains multiple database service names identifiable as **high**, **medium**, **low**, **tp**, and **tpurgent**. The predefined service names provide different levels of performance and concurrency for Oracle Autonomous Database. Use one of these service names in your connect string.

6. Test the Oracle Client with Oracle SQL*Plus:

     ```
     sqlplus username@<connect string>
     ```

   <enter password at prompt>
    or
    ```
    sqlplus /nolog
    SQL> connect username@<connect string>
    ```
   <enter password at prompt>

    If the connection is successful you are ready to move to the next step.

---

## Step 2: Install HVR

Follow the [installation instructions](https://www.hvr-software.com/docs/installing-and-upgrading-hvr) to install HVR.

Note that the HVR hub must be able to reach out to an agent installation through TCP/IP communication. As needed, open the firewall for the HVR listener port to the machine running the HVR agent. Also make sure the operating system allows the connection to go through.

---

## Step 3: Configuring HVR to Connect with Autonomous Database

Connect the HVR GUI to the hub, connect to the metadata repository, and create a new Location. If the HVR hub resides in Oracle Cloud Infrastructure then you may choose to connect directly to Autonomous Database. Use the connect string you tested during the first installation step.

  ![Picture4](./images/hvr_pic4.png)

More commonly users will connect to Autonomous Database using an agent running on a compute instance in Oracle Cloud Infrastructure.

  ![Picture5](./images/hvr_pic5.png)

You can now set up channels using the location as a target and enjoy the benefits of Oracle Autonomous Database.

---

**Note:** Depending on your HVR version you may, during initialize, have to use the low resource TNS connection, or as an alternative set an Environment action name HVR_SQL_INIT with value "alter session disable parallel dml".