Virtualization
==============

> NOTE! This section is under development

Phundament supports various tools like [Docker](https://www.docker.com), [Vagrant](https://www.vagrantup.com), [VirtualBox](https://www.virtualbox.org) or [PuPHPet](https://puphpet.com) on different platforms.

You can choose from additionally available components, including VMWare, Parallels, HyperV, RedHat, Amazon AWS, DigitalOcean, Rackspace and many more.

Docker Container
----------------

Create project:

    composer create-project --stability=dev --prefer-dist phundament/app p4-docker
    cd p4-docker

### Option: fig

Copy fig config to project root:

    ./init --env=Dotenv
    cp ./environments/_docker/Dockerfile .
    cp ./environments/_docker/fig.yml .

Edit `./.env` to map the fig ENV vars to the Phundament application

    # Database configuration
    DATABASE_DSN=mysql:host={$DB_PORT_3306_TCP_ADDR};dbname={$DB_ENV_MYSQL_DATABASE}
    DATABASE_USER={$DB_ENV_MYSQL_USER}
    DATABASE_PASSWORD={$DB_ENV_MYSQL_PASSWORD}

Start the application containers:

    fig up

To initialize your application run the following commands once:

    fig run backend ./yii migrate

You should now be able to access the container under `http://docker.local:10080` and `http://docker.local:10081`



### Option: vagrant

Initialize and Copy files from `environments/_docker` to project root:

    ./init --env=Dotenv
    cp ./environments/_docker/Dockerfile .
    cp ./environments/_docker-vagrant/Vagrantfile .

> Note: For Mac OS X or Windows users!

> You may use the provided config from `./environments/_docker/dockerhost-vm/Vagrantfile` to create a docker host VM.
> Set the ENV your `DOCKER_HOST_VAGRANTFILE` to the path of the  Vagrantfile aforementioned and uncomment the config 
> settings for `docker.vagrant_vagrantfile` in `./Vagrantfile` .

Review the config settings, such as port-forwardings, in the `./Vagrantfile` and run the container:

    vagrant up --provider=docker

You also have to initialize application once:
    
    vagrant docker-run web -- sh /app/environments/_docker/container-init.sh

You should now be able to access the container under `http://192.168.7.6:8280` and `http://192.168.7.6:8280/backend`



Local VM
--------

- Install [Vagrant](https://www.vagrantup.com) and [VirtualBox](https://www.virtualbox.org)
- Go to an exsting Phundament 4 project or `git clone -b4.0 https://github.com/phundament/app.git` a new one.
- Upload the default configuration from `environments/puphpet/config-dist.yaml` via drag&drop to [PuPHPet](https://puphpet.com/)
  - Adjust VM values if needed, eg. make sure to install `curl` and `gd`.
  - Click **Create** and download VM configuration package.
- Extract the contents (`Vagrantfile`,`puphpet/`) to the project root folder.
- Initialize application in puphpet environment:

    ```
    cp ./environments/_puphpet/puphpet/files/exec-once/init.sh ./puphpet/files/exec-once/init.sh
    ```

    *Note: This will copy the needed initialization script for the VM, which will switch your environment to _Development_ by default.*
- To access the virtual host in the VM later, update your `/etc/hosts` file:

    ```
    192.168.42.42    phundament.vagrant admin.phundament.vagrant
    ```
- Bring up the virtual machine:

    ```
    vagrant up
    ```
- Open [phundament.vagrant](http://phundament.vagrant) or [admin.phundament.vagrant](http://admin.phundament.vagrant) in your browser.
- Login with `admin` / `admin1234`


### Accessing application in virtual machine

> TODO: check stdin for migration

To open a shell in the VM run:

```
vagrant ssh
```

You can run commands directly, for example to update the application in the virtual machine:

```
vagrant ssh --command /var/www/yii app/update
```


Cloud Installation
------------------

### AWS EC2

#### Setup

Install the following Vagrant plugins

    vagrant plugin install vagrant-aws
    vagrant plugin install vagrant-awsinfo

Check connection settings in EC2 management console and update the puphpet config variables with your data.
You may use the base configuration from `environments/virtual` and switch to AWS in the **Deploy Target** section

  * [EC2 Access Key ID](https://console.aws.amazon.com/iam/home?#security_credential)
  * [EC2 Secrect Access Key](https://portal.aws.amazon.com/gp/aws/securityCredentials?)
  * [EC2 Key Pair Name](https://console.aws.amazon.com/ec2/v2/home?#KeyPairs:)
  * Local Private Key Path (on your machine)
  * adjust EC2 Security groups, if needed (eg. instance needs Security Group `launch-wizard-1`)

#### Provisioning

    vagrant up --provider=aws

> Note: You can only have one provider per machine.
> See also http://docs.vagrantup.com/v2/providers/basic_usage.html (Limitations)

#### Troubleshooting

  * Testing (free) instance only available from `region: us-west-2`(?) US West (Oregon)
  * "Intelligent folder sync" (excluding vendor, web/assets...) Note: needs vagrans-aws >0.4.1 - no tag at the moment

### Digitalocean

tbd