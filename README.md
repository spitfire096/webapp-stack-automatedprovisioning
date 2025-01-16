# Automating Web Application Stack Deployment with Maven

This project demonstrates how to automate the build and deployment of a web application stack using Maven. The stack includes MySQL, Memcached, RabbitMQ, Tomcat, and Nginx, with Maven managing dependencies, builds, and deployment to the configured environment.

---

## Prerequisites

Ensure the following are installed:

1. [Apache Maven](https://maven.apache.org/)
2. [Java Development Kit (JDK)](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) (minimum version 11)
3. [Git](https://git-scm.com/)
4. [Vagrant](https://www.vagrantup.com/)
5. [VirtualBox](https://www.virtualbox.org/)
6. [Git Bash](https://git-scm.com/)

---

## Repository Structure

```plaintext
project-root/
|-- pom.xml                   # Maven project descriptor
|-- src/
|   |-- main/
|       |-- java/             # Application source code
|       |-- resources/        # Configuration files
|       |   |-- application.properties
|-- deployment/
|   |-- scripts/              # Deployment scripts
|   |   |-- mysql.sh
|   |   |-- memcache.sh
|   |   |-- rabbitmq.sh
|   |   |-- tomcat.sh
|   |   |-- nginx.sh
|-- Vagrantfile               # VM configurations
```

---

## Maven Goals

### 1. **Dependency Management**
   Maven ensures all required dependencies for the web application (e.g., JDBC drivers, RabbitMQ client libraries) are resolved.

### 2. **Build Automation**
   - Compile Java code.
   - Package the application into a WAR file for deployment.

### 3. **Deployment**
   - Transfer built artifacts to the target VM (Tomcat).
   - Execute service-specific configurations (e.g., deploying to Tomcat, updating RabbitMQ settings).

---

## Configuration Steps

### 1. Clone the Repository

```bash
# Clone this repository
$ git clone https://github.com/spitfire096/webapp-stack-automatedprovisioning.git
$ cd maven-stack-automation
```

### 2. Update Configuration Files

1. **Vagrantfile:**
   The `Vagrantfile` sets up five VMs: `db01`, `mc01`, `rmq01`, `app01`, and `web01`. Each VM is provisioned with a corresponding shell script (e.g., `mysql.sh`, `nginx.sh`). Ensure the IPs and memory allocations match your requirements.

2. **application.properties:**
   Update the following configurations in `src/main/resources/application.properties`:

   - **Database Connection:**
     ```properties
     jdbc.url=jdbc:mysql://db01:3306/accounts?useUnicode=true&characterEncoding=UTF-8&zeroDateTimeBehavior=convertToNull
     jdbc.username=<your_username>
     jdbc.password=<your_password>
     ```

   - **Memcached Hosts:**
     ```properties
     memcached.active.host=mc01
     memcached.active.port=11211
     memcached.standBy.host=127.0.0.2
     memcached.standBy.port=11211
     ```

   - **RabbitMQ Configuration:**
     ```properties
     rabbitmq.address=rmq01
     rabbitmq.port=5672
     rabbitmq.username=test
     rabbitmq.password=test
     ```

   - **Elasticsearch Configuration:**
     ```properties
     elasticsearch.host=192.168.1.85
     elasticsearch.port=9300
     elasticsearch.cluster=projects
     elasticsearch.node=projectsnode
     ```

### 3. Provision Virtual Machines

Use Vagrant to set up the VMs as defined in the `Vagrantfile`:

```bash
# Start the virtual machines
$ vagrant up
```

### 4. Build the Application

Use Maven to build the project:

```bash
# Clean and package the project
$ mvn clean package
```

The generated WAR file will be located in the `target` directory.

### 5. Deploy the Application

Run the Maven deployment goal:

```bash
# Deploy the application
$ mvn exec:java -Dexec.mainClass="com.example.Deploy"
```

This will:
- Transfer the WAR file to the Tomcat VM (`app01`).
- Execute deployment scripts for other services (MySQL, RabbitMQ, etc.).

---

## Testing the Deployment

1. **Access Nginx:**
   Navigate to `http://192.168.56.x` to verify Nginx is serving requests.

2. **Access Tomcat:**
   Verify the application is deployed and running at `http://192.168.56.x:8080`.

3. **Check Service Status:**
   SSH into individual VMs to verify service statuses:

   ```bash
   $ vagrant ssh <vm_name>
   $ sudo systemctl status <service_name>
   ```

---

## Maven Commands Reference

- **Clean Build:**
  ```bash
  mvn clean
  ```

- **Package Application:**
  ```bash
  mvn package
  ```

- **Run Tests:**
  ```bash
  mvn test
  ```

- **Execute Custom Goal:**
  ```bash
  mvn exec:java -Dexec.mainClass="<fully.qualified.ClassName>"
  ```

