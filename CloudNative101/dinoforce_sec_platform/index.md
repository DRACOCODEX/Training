# Dinohead’s DinoForce Cybersecurity Platform (Draco)

The [Dinoforce](https://dinoforce.com) "DevSecOps" Platform contains all of the tools to standup a Software Factory and begin modernizing Applications on Day One. This consists of Three (3) Integrated Independent Platforms for Development, Cybersecurity, and Operations (DevSecOps). The focus for today's training is to standup a small sub-set of the DinoForce "Sec" Platform.

## Applications Used for this Training
  - [Keycloak](https://www.keycloak.org): is an open source Identity and Access Management solution aimed at modern applications and services. It makes it easy to secure applications and services with little to no code.
  - [Postgres](https://www.postgresql.org): is a powerful, open source object-relational database system with over 30 years of active development that has earned it a strong reputation for reliability, feature robustness, and performance.
  - [OpenRMF](https://www.openrmf.io): is the only web-based open source tool allowing you to collaborate on your DoD STIG checklists, DISA, OpenSCAP and Nessus SCAP scans, Nessus ACAS patch data, and generate NIST compliance in minutes (or less). All with one tool!
      - We are only installing Keycloak & Postgres for this example.  The full install is 39 Containers and includes multiple tools.

## Keycloak Install

  1. Download Packaged Zip File

   ```shell
   sudo curl -L https://github.com/DINOFORCE-REPO/Training/releases/download/Training/Keycloak-v12.0.3.zip -O
   ```

  2. Unzip File

  ```shell
  unzip Keycloak-v12.0.3.zip
  ```

  3. Run Start.sh inside the Keycloak-v12.0.3 Directory.  This will run more docker-compose to download the Keycloak and Postgres Containers.  It will also run them as soon as they are downloaded.

  ```shell
  cd Keycloak-v12.0.3
  sudo ./start.sh
  ```

  4. While waiting for the containers to finish up, run the following command to view the containers running:

  ```shell
  sudo docker ps
  ```

## Keycloak Configuration
We will setup Users, Theme, Password Policy, Roles & Disable SSL

  1. Find your IP address for the next step. Ifconfig was removed in RHEL 8
  ```shell
  ip addr show eth0
  ```
  2. From the Keycloak-v12.0.3 Directory, run:
  ```shell
  sudo ./setup-realm-linux.sh
  ```
    - Enter IP address from step above

    - Enter Name for first OpenRMF Admin: “rmf-admin”

## Test the Application

  1. Open Browser and visit: http://“IP ADDR Entered”:9001
  2. Sign In
      - Select Admin Console
      - Enter admin / admin
  3. Look Cnfigs Loaded From Scripts and docker-compose File
      - Select Realm Settings
      - Select Roles
      - Select Clients
  4. Select Users
      - Select View All Users
      - Select the “rmf-admin” User you Created
      - Select Credentials and Set the Password

## Explore the Containers
We will look at the Keycloak and Postgres Container to see they actually are running different OS's.

  1. View Containers running  
      ```shell
      sudo docker ps
      ```
  2.  Use Bash in the Keycloak Container
        ```shell
        sudo docker exec -it “Container ID” /bin/bash
        cat /etc/redhat-release
        rpm -qa
        rpm -qa | wc -l
        ```
  3.  Use Bash in the Postgress Container
        ```shell
          sudo docker exec -it “Container ID” /bin/bash
          cat /etc/os-release
          apt list --installed
          apt list --installed | wc -l
        ```
