# Beanstalk Configuration

There are two types of configuration you will need to do with Beanstalk. 

* **Instance Environment** Configure the server instance dependencies (GraphicMagick, etc) using the `.ebextensions`.
* **Beanstalk Environment** Configure the Beanstalk Environment using UI Console and then save config to `/env.yaml`.

## NPM Configuration

* https://github.com/jakemmarsh/ebs-setup
* https://github.com/kopurando/better-faster-elastic-beanstalk

## EB CLI Config
* http://stackoverflow.com/a/29222254

## Beanstalk Environment

You can create a throw-away environment and configure using the UI console. Then save this configuration as `/env.yaml`. The `env.yaml` is:

* Only used during the creation of environments. 
* Does nothing during a deployment so you can't change the `env.yaml` file and re-deploy.

In general, I have found the environment configuration to be flakey. 

http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/customize-containers.html

1. Create a throw-away environment and use the UI Console to configure the environment as you would like. 
2. Save the configuration using the UI Console as 'temp'.
3. Use the `eb config get temp` and save the configuration as `/env.yaml`.

    ```bash
    Configuration saved at: /Users/me/github/repo/.elasticbeanstalk/saved_configs/temp.cfg.yml
    ```

4. Rename the config file to `/env.yaml`.

## Instance Environment

### OS Packages
Amazon Linux uses a version of CentOS. The package manager is [yum](https://www.centos.org/docs/5/html/5.1/Deployment_Guide/s1-yum-useful-commands.html).

#### Finding Packages

* Packages available from standard repository:  
http://mirror.centos.org/centos/6/os/x86_64/Packages/ or http://centos-packages.com/6/

* Packages available from the extended epel repository:  
https://dl.fedoraproject.org/pub/epel/6/x86_64/

* AWS documentation for package installation:  
http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/customize-containers-ec2.html#linux-packages

#### Example ebextensions

* graphicsmagick.config

    ```yaml
    commands:
        01-command:
            command: yum install -y --enablerepo=epel GraphicsMagick
    ```

* imagemagick.config

    ```yaml
    packages:
        yum:
            ImageMagick-devel: []
            ImageMagick: []
    ```

* fonts.config

    ```yaml
    packages:
        yum:
            freetype: []
            ghostscript: []
    ```

* dmtx.config

    ```yaml
    commands:
        libdmtx:
            command: yum install -y --enablerepo=epel libdmtx
        libdmtx-utils:
            command: yum install -y --enablerepo=epel libdmtx-utils
    ```

### CRON Jobs

Create a `cron.yaml` file in the project root directory.

http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features-managing-env-tiers.html#worker-periodictasks

> Not sure if this works on worker only or if this works on a web application.

* cron.yaml

    ```yaml
    version: 1
    cron: 
      - name: "task1"
        url: "/scheduled.php"
        schedule: "*/1 * * * *"
    ```
