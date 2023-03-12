# Terraform and GCP

# GCP

### Initial Setup

For this course, we'll use a free version (upto EUR 300 credits). 

1. Create an account with your Google email ID 
2. Setup your first [project](https://console.cloud.google.com/) if you haven't already
    * eg. "DTC DE Course", and note down the "Project ID" (we'll use this later when deploying infra with TF)
3. Setup [service account & authentication](https://cloud.google.com/docs/authentication/getting-started) for this project
    * Grant `Viewer` role to begin with.
    * Download service-account-keys (.json) for auth.
4. Download [SDK](https://cloud.google.com/sdk/docs/quickstart) for local setup
5. Set environment variable to point to your downloaded GCP keys:
   ```shell
   export GOOGLE_APPLICATION_CREDENTIALS="<path/to/your/service-account-authkeys>.json"
   
   # Refresh token/session, and verify authentication
   gcloud auth application-default login
   ```
   
### Setup for Access
 
1. [IAM Roles](https://cloud.google.com/storage/docs/access-control/iam-roles) for Service account:
   * Go to the *IAM* section of *IAM & Admin* https://console.cloud.google.com/iam-admin/iam
   * Click the *Edit principal* icon for your service account.
   * Add these roles in addition to *Viewer* : **Storage Admin** + **Storage Object Admin** + **BigQuery Admin**
   
2. Enable these APIs for your project:
   * https://console.cloud.google.com/apis/library/iam.googleapis.com
   * https://console.cloud.google.com/apis/library/iamcredentials.googleapis.com
   
3. Please ensure `GOOGLE_APPLICATION_CREDENTIALS` env-var is set.
   ```shell
   export GOOGLE_APPLICATION_CREDENTIALS="<path/to/your/service-account-authkeys>.json"
   ```
 
# Terraform

Terraform is an open-source infrastructure as code (IaC) tool that allows you to create, modify, and manage infrastructure resources in a declarative and reproducible way. With Terraform, you can define your infrastructure as code using a simple and readable configuration language, and then apply that configuration to your cloud provider to create, update, or delete resources like virtual machines, networks, load balancers, and more.

Terraform is designed to work with a wide range of cloud providers, including Amazon Web Services (AWS), Microsoft Azure, Google Cloud Platform (GCP), and many others. It also supports various types of resources, including compute, storage, networking, security, and more.

Package manager for Linux
```bash
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform
```

#### Files

* `main.tf`
* `variables.tf`
* Optional: `resources.tf`, `output.tf`
* `.tfstate`

#### Declarations
* `terraform`: configure basic Terraform settings to provision your infrastructure
   * `required_version`: minimum Terraform version to apply to your configuration
   * `backend`: stores Terraform's "state" snapshots, to map real-world resources to your configuration.
      * `local`: stores state file locally as `terraform.tfstate`
   * `required_providers`: specifies the providers required by the current module
* `provider`:
   * adds a set of resource types and/or data sources that Terraform can manage
   * The Terraform Registry is the main directory of publicly available providers from most major infrastructure platforms.
* `resource`
  * blocks to define components of your infrastructure
  * Project modules/resources: google_storage_bucket, google_bigquery_dataset, google_bigquery_table
* `variable` & `locals`
  * runtime arguments and constants


#### Execution steps
1. `terraform init`: 
    * Initializes & configures the backend, installs plugins/providers, & checks out an existing configuration from a version control 
2. `terraform plan`:
    * Matches/previews local changes against a remote state, and proposes an Execution Plan.
3. `terraform apply`: 
    * Asks for approval to the proposed plan, and applies changes to cloud
4. `terraform destroy`
    * Removes your stack from the Cloud

### Execution

```shell
# Refresh service-account's auth-token for this session
gcloud auth application-default login

# Initialize state file (.tfstate)
terraform init

# Check changes to new infra plan
terraform plan -var="project=<your-gcp-project-id>"
```

```shell
# Create new infra
terraform apply -var="project=<your-gcp-project-id>"
```

```shell
# Delete infra after your work, to avoid costs on any running services
terraform destroy
```


### Setup an Environment in GCP

For the course you'll need:

* Python 3 (e.g. installed with Anaconda)
* Google Cloud SDK
* Docker with docker-compose
* Terraform

Setting up the environment on cloud VM
  * Generating SSH key for login to 

If you connect to VMs using the Google Cloud console or the Google Cloud CLI, Compute Engine creates SSH keys on your behalf. You can follow the instructions in this [link](https://cloud.google.com/compute/docs/connect/create-ssh-keys)

You can print the public key in .ssh directory with this command

```bash
cat gcp.pub
```
After printing the public key we need to go to google cloud platform > compute engine > metadata section. On this page we can add our ssh key.

  * Creating a virtual machine on GCP
  * Connecting to the VM with SSH

We can connect to the VM using the code below in the home directory.

```
ssh -i ~/.ssh/gcp <user_name>@<external_ip>
```
And we can check what kind of VM we get with `htop` command.
  * Installing Anaconda

```
wget https://repo.anaconda.com/archive/Anaconda3-2022.10-Linux-x86_64.sh
```

```
bash Anaconda3-2022.10-Linux-x86_64.sh
```
  * Installing Docker
```
sudo apt-get install docker.io
```

Run Docker commands without sudo
https://github.com/sindresorhus/guides/blob/main/docker-without-sudo.md

  * Creating SSH `config` file
The SSH config file is a configuration file used by the OpenSSH client program (ssh) to specify options for connecting to remote servers. The file is usually located in the user's home directory at ~/.ssh/config (on Unix-like systems).

The config file allows users to define various SSH options, such as the host to connect to, the user to log in as, the port to use, and any custom options that may be required. These options can be set globally or for specific hosts, allowing for greater flexibility and convenience in managing SSH connections.

Create a file `touch config` then open it with VScode `code config`

```config
Host de-zoomcamp
    HostName <external_ip>
    User <user_name>
    IdentityFile c:/Users/<user_name>/.ssh/gcp
```
  * Accessing the remote machine with VS Code and SSH remote

We need to install Remote - SSH extension and then we will click on the 'open a remote window' button at the bottom left of the VScode. After that you can choose 'Connect to Host' and you will see the de-zoomcamp section because of our config file.

Name: Remote - SSH
VS Marketplace Link: https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh

  * Installing docker-compose

```
mkdir bin
```

https://github.com/docker/compose/releases/tag/v2.16.0

```
wget https://github.com/docker/compose/releases/download/v2.16.0/docker-compose-linux-x86_64 -O docker-compose
```
for make it executable
```
chmod +x docker-compose
```
To make it visible from any directory we need to edit path variable. this can be accomplished by editing .bashrc  We use nano command to open .bashrc file
```
nano .bashrc
```
The command export `PATH="${HOME}/bin:${PATH}"` sets the value of the PATH environment variable in a Unix-like operating system.
the command export `PATH="${HOME}/bin:${PATH}"` adds the ~/bin directory to the beginning of your PATH variable, ensuring that any executables you store in that directory will be found before any other directories in your PATH.

The `source .bashrc` command is used to reload the configuration file. Sometimes changes made to the `.bashrc` file may not take effect immediately. In this case, the `source .bashr` command is used. This command immediately loads and activates any changes made to the `.bashrc` file. 

  * Installing pgcli
We have previously downloaded the de-zoomcamp repository to the VM. We will run docker compose using the docker-compose.yaml file in the ~/data-engineering-zoomcamp/week_1_basics_n_setup/2_docker_sql directory. I'm using a different yaml file in my own repository but for now I'll keep it that way. For docker compose to run in the background we use -d (detached mode)

```
(base) senat@de-zoomcamp:~/data-engineering-zoomcamp/week_1_basics_n_setup/2_docker_sql$ docker-compose up -d
```
After running docker compose, we can see the status of the images with the `docker ps` command. Then go to the home directory and install pgcli `pip install pgcli` 

Now we can connect to the database with pgcli. host:localhost, user:root, database:ny_taxi
```
pgcli -h localhost -U root -d ny_taxi
```
Everything seems fine for me, but we will uninstall pgcli and install it from conda. First  `pip uninstall pgcli` and then `conda install -c conda-forge pgcli`

  * Port-forwarding with VS code: connecting to pgAdmin and Jupyter from the local computer

In VScode (remotly conneted to GCP VM) got to ports tab and add 5432 and 8080. Then we can access postgres via localhost:8080

In bash go to `cd data-engineering-zoomcamp/week_1_basics_n_setup/2_docker_sql` directory and download data from ` wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/yellow/yellow_tripdata_2021-01.csv.gz` an unzip cvs.gz file with `find . -name 'yellow_tripdata_2021-01.csv.gz' -print0 | xargs -0 -n1 gzip -d` 

run jupyter `jupyter notebook` in `~/data-engineering-zoomcamp/week_1_basics_n_setup/2_docker_sql$`

In jupyter open `upload-data.ipynb` file and create a schema first, then insert 100 row.

```jupyter
import pandas as pd

df = pd.read_csv('yellow_tripdata_2021-01.csv', nrows=100)

df.tpep_pickup_datetime = pd.to_datetime(df.tpep_pickup_datetime)

df.tpep_dropoff_datetime = pd.to_datetime(df.tpep_dropoff_datetime)

from sqlalchemy import create_engine

engine = create_engine('postgresql://root:root@localhost:5432/ny_taxi')

df.head(n=0).to_sql(name='yellow_taxi_data', con=engine, if_exists='replace')

df.to_sql(name='yellow_taxi_data', con=engine, if_exists='append')

```

For check 
` pgcli -h localhost -U root -d ny_taxi`
` \dt`
`SELECT COUNT(1) from yellow_taxi_data;`

  * Installing Terraform
`cd bin`
`wget https://releases.hashicorp.com/terraform/1.4.0/terraform_1.4.0_linux_amd64.zip`
`sudo apt-get install unzip`
`unzip  terraform_1.4.0_linux_amd64.zip`
`rm terraform_1.4.0_linux_amd64.zip`

  * Using `sftp` for putting the credentials to the remote machine
  * Shutting down and removing the instance