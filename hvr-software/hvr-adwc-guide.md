# Creating a Connection from Fivetran HVR to Oracle Autonomous Database

## Author

Mark Van de Wiel

---

## Overview

This document provides an overview of the steps required to configure Fivetran HVR to connect to Oracle Autonomous Database.

Fivetran HVR supports log-based Change Data Capture (CDC) for heterogeneous data replication from several source database technologies into a multitude of destination technologies and data formats. HVR 6.1 and 6.2 are the latest versions certified to deliver data into Oracle Autonomous Database (versions 19c and 23ai).

For optimum performance integrating changes into Oracle Autonomous Database, Fivetran recommends an architecture that features the use of agents. Agents are installations of the HVR software that are located as close as possible to the source and destination technologies. The use of agents optimizes network communication and distributes load.

HVR features such as initial load, ongoing replication, and compare/repair all take advantage of the agents. With Oracle Autonomous Database as the target, the recommended setup is to include an agent in Oracle Cloud Infrastructure (OCI), in the same availability domain as the Autonomous Database—containerized, on a compute instance, or installed on a bare metal instance. TCP/IP communication between Fivetran HVR installations is always encrypted over TLS 1.3.

---

## Connecting Fivetran HVR to Oracle Autonomous Database

![Autonomous Database Connection Architecture](hvr-software/images/wta1.png)

### Step 1: Provision Autonomous Database and Install & Configure the Oracle Client

1. **Provision Oracle Autonomous Database** and download the corresponding database wallet (credentials.zip file) to the system that will have the HVR target agent installation.  
    - For Oracle documentation to provision Autonomous Database, refer to the [Oracle Autonomous Database documentation](https://docs.oracle.com/en/cloud/paas/autonomous-database/index.html).  
    - Also check [Downloading Client Credentials (Wallets)](https://docs.oracle.com/en/cloud/paas/autonomous-database/adbsa/connect-download-wallet.html).

2. **Extract the wallet file** into a secure folder.

3. **Download the Oracle Database Client** (19c or later) to the system that runs the HVR integration agent.

4. **Edit the `sqlnet.ora` file**, replacing `?/network/admin` with the name of the folder containing the client credentials.

    Example:
    ```ini
    WALLET_LOCATION = (SOURCE = (METHOD = file) (METHOD_DATA =
    (DIRECTORY="/home/adb_wallet")))
    SSL_SERVER_DN_MATCH = yes
    ```

5. The `TNS_ADMIN` environment variable can be set to point to an alternative location for the `sqlnet.ora` and `tnsnames.ora` files. Define the variable if you plan to not use `$ORACLE_HOME/network/admin`.

6. The `tnsnames.ora` file provided with the wallet contains multiple database service names including **high**, **medium**, **low**, **tp**, and **tpurgent**. These provide different levels of performance and concurrency for Oracle Autonomous Database. Select the appropriate service name based on your workload requirements for use in your TNS connect string.

7. **Test the Oracle Client with Oracle SQL*Plus:**
    ```sh
    sqlplus username@<connect string>
    # Enter password at prompt
    ```
    or
    ```sh
    sqlplus /nolog
    SQL> connect username@<connect string>
    # Enter password at prompt
    ```

    If the connection is successful, you are ready to move to the next step.

---

### Step 2: Install HVR

- Follow the installation instructions to install HVR.
- Ensure the HVR hub can reach an agent installation through TCP/IP communication.
- Open the firewall for the HVR listener port to the machine running the HVR agent, as needed.
- Make sure the operating system allows the connection.

---

### Step 3: Create an HVR Location to Connect with Autonomous Database

- In the HVR console, create a new **Location**.
- Choose location type **Oracle**, and enter your configuration information.
- If the HVR hub resides in Oracle Cloud Infrastructure, you may choose to connect directly to Autonomous Database.
- Use the connect string you tested during the first installation step.

![Creating a New Location in HVR](hvr-software/images/wta2.png)

- Typically, users will connect to Autonomous Database using an agent running on a compute instance in Oracle Cloud Infrastructure.

---

### Step 4: Create a Channel to Deliver Data into Autonomous Database

- In the HVR console, create a new **Channel**.
- Choose your source location, target Oracle Autonomous Database, and complete the channel setup.
- Refresh the tables and start replicating data!

![Creating a Channel in HVR](hvr-software/images/wta3.png)

- The channel configuration screen allows you to select tables, define replication options, and set up scheduling.
- After saving the channel, you can monitor replication status and review logs for troubleshooting.
- For more details on channel configuration, refer to the [Fivetran HVR documentation](https://fivetran.com/docs/hvr).

---

**Last Edited By: Blake Hendricks**