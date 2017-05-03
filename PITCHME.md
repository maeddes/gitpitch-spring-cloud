
![Cloud Foundry](https://www.cloudfoundry.org/wp-content/uploads/2015/11/CloudFoundaryCorp_rgb.png) 

PaaS for the good ones :-)


---

## Agenda

* Intro to Cloud Foundry
* Administration
* cf cli
* Apps - Deployment
* Apps - Runtime
* Services 
 
---

## Sources

 * docs.cloudfoundry.org
 * twitter
 * slideshare
 * github
 
--- 

# INTRO

+++

## Some facts

* First released in 2011
* Open Cloud Native Platform / Platform as a Service
* Fast and easy to build, test, deploy & scale apps
* Works with many languages or frameworks
* Available as open source, commercial distributions or hosted offerings

+++

## Here is my source code 
## Run it on the cloud for me 
## I do not care how

A haiku dedicated to ‘cf push’ by Onsi Fakhouri, Pivotal

+++

![CF Voodoo](https://pbs.twimg.com/media/C99itvAWAAY1SXS.jpg)
https://twitter.com/swardley/status/855511570360795136

+++

<iframe data-src="https://www.cloudfoundry.org/membership/members/" height="480px" width="100%"></iframe>

 https://www.cloudfoundry.org/membership/members/

+++

IaaS / PaaS Taxonomy

![Cloud Stack](https://docs.cloudfoundry.org/concepts/images/power-of-platform.png)

(Source: docs.cloudfoundry.org)

+++

## PaaS Concepts

* Anyone can deploy applications quickly and make them accessible for everybody
* In case of high requests, the platform can scale the app easily
* Focus is on application an it's logic - not the underyling infrastructure

+++

## Cloud Load Balancing

* BOSH creates and deploys virtual machines (VMs) on top of a physical computing infrastructure, and deploys and runs Cloud Foundry on top of this cloud. 
* The CF Cloud Controller runs the apps and other processes on the cloud’s VMs, balancing demand and managing app lifecycles.
* The router routes incoming traffic from the world to the VMs that are running the apps that the traffic demands

+++

![CF Diagram](https://pbs.twimg.com/media/C9zpBi9VoAAt0LA.jpg)
https://twitter.com/wattersjames/status/854814439572250624

+++

## Try it out

<iframe data-src="https://www.cloudfoundry.org/how-can-i-try-out-cloud-foundry-2016" height="480px" width="100%"></iframe>
https://www.cloudfoundry.org/how-can-i-try-out-cloud-foundry-2016/

+++

## Try it out pt 2

<iframe data-src="https://www.cloudfoundry.org/certified-platforms/" height="480px" width="100%"></iframe>
https://www.cloudfoundry.org/certified-platforms/

---

# Administration

+++

## Orgs

- The outer grouping boundary
- An org is a development account that an individual or multiple collaborators can own and use. 
- All collaborators access an org with user accounts. 
- Collaborators in an org share a resource quota plan, applications, services availability, and custom domains.

+++

## Spaces

- The inner grouping boundary
- Every application and service is scoped to a space. 
- Each org contains at least one space. 
- A space provides users with access to a shared location for application development, deployment, and maintenance.
- Each space role applies only to a particular space.

+++

## User Accounts

- A user account represents an individual person within the context of a CF installation. 
- A user can have different roles in different spaces within an org, governing what level and type of access they have within that space.

+++

## Roles and Permissions

- A user can have one or more roles. 
- The combination of these roles defines the user’s overall permissions in the org and within specific spaces in that org.

+++

## Domains

* The term domain in this topic differs from its common use and is specific to Cloud Foundry. 
* The use of domain name, root domain, and subdomain refers to DNS records.
* Domains indicate to a developer that requests for any route created from the domain will be routed to Cloud Foundry.

+++

## Quotas

Default Quota Plan for an Org

* Memory Limit: 10240 MB
* Total Routes: 1000
* Total Services: 100
* Non-basic Services Allowed: True
* Trial DB Allowed: True

---

# CF CLI

+++?gist=afaae0cfafd7e2dcb4193b4d29b613e6

+++

## Targets

```bash
$ cf login -a https://api.example.com -u username@example.com
API endpoint: https://api.example.com

Password>
Authenticating...
OK

Select an org (or press enter to skip):
1. example-org
2. example-other-org

Org> 1
Targeted org example-org

Select a space (or press enter to skip):
1. development
2. staging
3. production

Space> 1
Targeted space development
```

+++

##Login

- Upon successful login, the cf CLI saves a config.json file containing your API endpoint, org, space values, and access token. If you change these settings, the config.json file is updated accordingly.

- By default, config.json is located in your ~/.cf directory. The CF_HOME environment variable allows you to locate the config.json file wherever you like.

---

#Applications
##Buildpacks & Droplets

+++

![cf push](https://1.bp.blogspot.com/-4aQw8F8suu4/VrkvObvIjkI/AAAAAAAAG08/X3At9XP5A9k/s1600/Selection_019.png)

(source: http://nanduni.blogspot.de)

+++

![sketch1](img/20170421_202906.jpg)

+++

# cf push

+++

```bash
mhs@R2-D2:~$ cf push --help
NAME:
   push - Push a new app or sync changes to an existing app

OPTIONS:
   -b                           Custom buildpack by name (e.g. my-buildpack) or Git URL (e.g. 'https://github.com/cloudfoundry/java-buildpack.git') or Git URL with a branch or tag (e.g. 'https://github.com/cloudfoundry/java-buildpack.git#v3.3.0' for 'v3.3.0' tag). To use built-in buildpacks only, specify 'default' or 'null'
   -f                           Path to manifest
   --health-check-type, -u      Application health check type (Default: 'port', 'none' accepted for 'process', 'http' implies endpoint '/')
   -i                           Number of instances
   -k                           Disk limit (e.g. 256M, 1024M, 1G)
   -m                           Memory limit (e.g. 256M, 1024M, 1G)
   --no-manifest                Ignore manifest file
   --no-route                   Do not map a route to this app and remove routes from previous pushes of this app
   --no-start                   Do not start an app after pushing
   -p                           Path to app directory or to a zip file of the contents of the app directory
   -t                           Time (in seconds) allowed to elapse between starting up an app and the first healthy response from the app

ENVIRONMENT:
   CF_STAGING_TIMEOUT=15        Max wait time for buildpack staging, in minutes
   CF_STARTUP_TIMEOUT=5         Max wait time for app instance startup, in minutes
```
+++

### App creation and binding
```bash
mhs@R2-D2:~/git/cf-simple-hello/cf-simple-hello$ cf push
Using manifest file /home/mhs/git/cf-simple-hello/cf-simple-hello/manifest.yml

Creating app cf-simple-hello in org pcfdev-org / space pcfdev-space as admin...
OK

Using route cf-simple-hello.local.pcfdev.io
Binding cf-simple-hello.local.pcfdev.io to cf-simple-hello...
OK

Uploading cf-simple-hello...
Uploading app files from: /tmp/unzipped-app693955360
Uploading 314.6K, 86 files
Done uploading               
OK
```

+++

### Staging
```bash
Starting app cf-simple-hello in org pcfdev-org / space pcfdev-space as admin...
Downloading java_buildpack...
Downloaded java_buildpack
Creating container
Successfully created container
Downloading app package...
Downloaded app package (12.2M)
Staging...
-----> Java Buildpack Version: v3.10 (offline) | https://github.com/cloudfoundry/java-buildpack.git#193d6b7
-----> Downloading Open Jdk JRE 1.8.0_111 from https://java-buildpack.cloudfoundry.org/openjdk/trusty/x86_64/openjdk-1.8.0_111.tar.gz (found in cache)
       Expanding Open Jdk JRE to .java-buildpack/open_jdk_jre (4.0s)
-----> Downloading Open JDK Like Memory Calculator 2.0.2_RELEASE from https://java-buildpack.cloudfoundry.org/memory-calculator/trusty/x86_64/memory-calculator-2.0.2_RELEASE.tar.gz (found in cache)
       Memory Settings: -Xmx681574K -XX:MaxMetaspaceSize=104857K -Xms681574K -XX:MetaspaceSize=104857K -Xss349K
-----> Downloading Spring Auto Reconfiguration 1.10.0_RELEASE from https://java-buildpack.cloudfoundry.org/auto-reconfiguration/auto-reconfiguration-1.10.0_RELEASE.jar (found in cache)
Exit status 0
Staging complete
Uploading droplet, build artifacts cache...
Uploading build artifacts cache...
Uploading droplet...
Uploaded build artifacts cache (108B)
Uploaded droplet (57.3M)
Uploading complete
Destroying container
Successfully destroyed container
```

+++

###Starting
```bash
0 of 1 instances running, 1 starting
0 of 1 instances running, 1 starting
0 of 1 instances running, 1 starting
0 of 1 instances running, 1 starting
1 of 1 instances running

App started


OK

App cf-simple-hello was started using this command `CALCULATED_MEMORY=$($PWD/.java-buildpack/open_jdk_jre/bin/java-buildpack-memory-calculator-2.0.2_RELEASE -memorySizes=metaspace:64m..,stack:228k.. -memoryWeights=heap:65,metaspace:10,native:15,stack:10 -memoryInitials=heap:100%,metaspace:100% -stackThreads=300 -totMemory=$MEMORY_LIMIT) && JAVA_OPTS="-Djava.io.tmpdir=$TMPDIR -XX:OnOutOfMemoryError=$PWD/.java-buildpack/open_jdk_jre/bin/killjava.sh $CALCULATED_MEMORY" && SERVER_PORT=$PORT eval exec $PWD/.java-buildpack/open_jdk_jre/bin/java $JAVA_OPTS -cp $PWD/. org.springframework.boot.loader.JarLauncher`

Showing health and status for app cf-simple-hello in org pcfdev-org / space pcfdev-space as admin...
OK

requested state: started
instances: 1/1
usage: 256M x 1 instances
urls: cf-simple-hello.local.pcfdev.io
last uploaded: Fri Apr 21 15:38:16 UTC 2017
stack: cflinuxfs2
buildpack: java_buildpack

     state     since                    cpu      memory           disk             details
#0   running   2017-04-21 05:39:36 PM   220.1%   149.6M of 256M   135.7M of 512M
```

+++

# manifest.yml

+++

## sample

```shell

mhs@R2-D2:~/git/cf-simple-hello/cf-simple-hello$ cat manifest.yml 

applications:
- name: cf-simple-hello
  memory: 512M
  instances: 1
  path: /home/mhs/git/cf-simple-hello/cf-simple-hello/target/demo-0.0.1-SNAPSHOT.jar
  buildpack: java_buildpack_offline

```

+++

<iframe data-src="https://docs.cloudfoundry.org/devguide/deploy-apps/manifest.html" height="480px" width="100%"></iframe>

https://docs.cloudfoundry.org/devguide/deploy-apps/manifest.html

+++

#Buildpacks

+++

- Buildpacks provide framework and runtime support for your applications
- Buildpacks typically examine user-provided artifacts to determine what dependencies to download and how to configure applications to communicate with bound services.
- When you push an application, Cloud Foundry automatically detects which buildpack is required and installs it in the container where the application needs to run.

+++

<iframe data-src="https://docs.cloudfoundry.org/buildpacks/#system-buildpacks" height="480px" width="100%"></iframe>
[Buildpacks](https://docs.cloudfoundry.org/buildpacks/#system-buildpacks)

+++

<iframe data-src="https://github.com/cloudfoundry-community/cf-docs-contrib/wiki/Buildpacks#community-created" height="480px" width="100%"></iframe>
[Community-BPs](https://github.com/cloudfoundry-community/cf-docs-contrib/wiki/Buildpacks#community-created)

+++

![Liberty](img/cf_liberty.png)

+++

- It is possible to build own buildpacks
- It is possible to create "offline" buildpacks
- Very often a local CF deployment does not have full Internet access
- Offline buildpacks can solve this problem, but need manual steps

---

#Applications
##Environment, Scaling & ssh

+++

### Application status

```bash
mhs@R2-D2:~/git/cf-simple-hello/cf-simple-hello$ cf app cf-simple-hello
Showing health and status for app cf-simple-hello in org pcfdev-org / space pcfdev-space as admin...
OK

requested state: started
instances: 1/1
usage: 256M x 1 instances
urls: cf-simple-hello.local.pcfdev.io
last uploaded: Fri Apr 21 15:38:16 UTC 2017
stack: cflinuxfs2
buildpack: java_buildpack

     state     since                    cpu    memory           disk             details
#0   running   2017-04-21 05:39:36 PM   1.8%   185.3M of 256M   135.7M of 512M
```

+++

### Application environment

```bash
mhs@R2-D2:~/git/cf-simple-hello/cf-simple-hello$ cf env cf-simple-hello
Getting env variables for app cf-simple-hello in org pcfdev-org / space pcfdev-space as admin...
OK

System-Provided:


{
 "VCAP_APPLICATION": {
  "application_id": "8e8eaac1-3722-40c2-88ee-22e6b0c1bf4b",
  "application_name": "cf-simple-hello",
  "application_uris": [
   "cf-simple-hello.local.pcfdev.io"
  ],
  "application_version": "3bc37ef1-b175-4362-b8d6-8deae737aced",
  "cf_api": "http://api.local.pcfdev.io",
  "limits": {
   "disk": 512,
   "fds": 16384,
   "mem": 256
  },
  "name": "cf-simple-hello",
  "space_id": "d928cac8-5f3d-4b5a-bc0f-7ac221e243ea",
  "space_name": "pcfdev-space",
  "uris": [
   "cf-simple-hello.local.pcfdev.io"
  ],
  "users": null,
  "version": "3bc37ef1-b175-4362-b8d6-8deae737aced"
 }
}

No user-defined env variables have been set

No running env variables have been set

No staging env variables have been set
```

+++

### Logging

```bash
mhs@R2-D2:~/git/cf-simple-hello/cf-simple-hello$ cf logs cf-simple-hello
Connected, tailing logs for app cf-simple-hello in org pcfdev-org / space pcfdev-space as admin...

2017-04-21T18:03:55.46+0200 [RTR/0]      OUT cf-simple-hello.local.pcfdev.io - [21/04/2017:16:03:55.467 +0000] "GET / HTTP/1.1" 200 0 34 "-" "curl/7.47.0" 192.168.11.1:33344 10.0.2.15:60138 x_forwarded_for:"192.168.11.1" x_forwarded_proto:"http" vcap_request_id:bafbf532-409f-4268-794b-635aa2351e3a response_time:0.002067835 app_id:8e8eaac1-3722-40c2-88ee-22e6b0c1bf4b app_index:0
2017-04-21T18:03:56.50+0200 [RTR/0]      OUT cf-simple-hello.local.pcfdev.io - [21/04/2017:16:03:56.504 +0000] "GET / HTTP/1.1" 200 0 34 "-" "curl/7.47.0" 192.168.11.1:33346 10.0.2.15:60138 x_forwarded_for:"192.168.11.1" x_forwarded_proto:"http" vcap_request_id:d8929a1a-6dec-4844-5359-e25b5c4d7040 response_time:0.001836894 app_id:8e8eaac1-3722-40c2-88ee-22e6b0c1bf4b app_index:0
2017-04-21T18:03:57.54+0200 [RTR/0]      OUT cf-simple-hello.local.pcfdev.io - [21/04/2017:16:03:57.541 +0000] "GET / HTTP/1.1" 200 0 34 "-" "curl/7.47.0" 192.168.11.1:33348 10.0.2.15:60138 x_forwarded_for:"192.168.11.1" x_forwarded_proto:"http" vcap_request_id:c28451eb-b474-4d3c-5923-0e52f79cc479 response_time:0.001895981 app_id:8e8eaac1-3722-40c2-88ee-22e6b0c1bf4b app_index:0
2017-04-21T18:03:58.58+0200 [RTR/0]      OUT cf-simple-hello.local.pcfdev.io - [21/04/2017:16:03:58.578 +0000] "GET / HTTP/1.1" 200 0 34 "-" "curl/7.47.0" 192.168.11.1:33350 10.0.2.15:60138 x_forwarded_for:"192.168.11.1" x_forwarded_proto:"http" vcap_request_id:93a27303-4980-473b-68e6-0db29b3a2266 response_time:0.00169934 app_id:8e8eaac1-3722-40c2-88ee-22e6b0c1bf4b app_index:0
2017-04-21T18:03:59.61+0200 [RTR/0]      OUT cf-simple-hello.local.pcfdev.io - [21/04/2017:16:03:59.615 +0000] "GET / HTTP/1.1" 200 0 34 "-" "curl/7.47.0" 192.168.11.1:33352 10.0.2.15:60138 x_forwarded_for:"192.168.11.1" x_forwarded_proto:"http" vcap_request_id:efa76e2e-d320-4bb5-6710-0823b0dcb095 response_time:0.00185939 app_id:8e8eaac1-3722-40c2-88ee-22e6b0c1bf4b app_index:0
^C
```

+++

### Scaling

```bash
mhs@R2-D2:~/git/cf-simple-hello/cf-simple-hello$ cf scale cf-simple-hello
Showing current scale of app cf-simple-hello in org pcfdev-org / space pcfdev-space as admin...
OK

memory: 256M
disk: 512M
instances: 1
mhs@R2-D2:~/git/cf-simple-hello/cf-simple-hello$ cf scale cf-simple-hello -i 3
Scaling app cf-simple-hello in org pcfdev-org / space pcfdev-space as admin...
OK
mhs@R2-D2:~/git/cf-simple-hello/cf-simple-hello$ cf scale cf-simple-hello
Showing current scale of app cf-simple-hello in org pcfdev-org / space pcfdev-space as admin...
OK

memory: 256M
disk: 512M
instances: 3
mhs@R2-D2:~/git/cf-simple-hello/cf-simple-hello$ 
```

+++

### Observing the instances live

```bash
mhs@R2-D2:~$ watch -n 1 cf app cpd-mysql

Showing health and status for app cf-simple-hello in org pcfdev-org / space pcfd
ev-space as admin...
OK

requested state: started
instances: 3/3
usage: 256M x 3 instances
urls: cf-simple-hello.local.pcfdev.io
last uploaded: Fri Apr 21 15:38:16 UTC 2017
stack: cflinuxfs2
buildpack: java_buildpack

     state     since                    cpu    memory           disk
 details
#0   starting  2017-04-21 09:40:54 PM   0.4%   178.2M of 256M   135.7M of 512M
#1   running   2017-04-21 07:41:21 PM   0.3%   230.9M of 256M   135.7M of 512M
#2   crashed   2017-04-21 07:41:16 PM   0.3%   223.2M of 256M   135.7M of 512M


```

+++

### ssh access

```bash
mhs@R2-D2:~/git/cf-simple-hello/cf-simple-hello$ cf ssh-enabled cf-simple-hello
ssh support is enabled for 'cf-simple-hello'

mhs@R2-D2:~/git/cf-simple-hello/cf-simple-hello$ cf ssh cf-simple-hello
vcap@898a7598-88da-4dcc-40b2-dff660fb5b37:~$ ls
app  logs  staging_info.yml  tmp
vcap@898a7598-88da-4dcc-40b2-dff660fb5b37:~$ pwd
/home/vcap
vcap@898a7598-88da-4dcc-40b2-dff660fb5b37:~$ ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 17:41 ?        00:00:00 /proc/self/exe init
vcap         6     0  0 17:41 ?        00:00:40 /home/vcap/app/.java-buildpack/open_jdk_jre/bin/j
vcap        11     0  0 17:41 ?        00:00:00 /tmp/lifecycle/diego-sshd --allowedKeyExchanges= 
vcap      2382    11  0 19:40 pts/0    00:00:00 /bin/bash
vcap      2393  2382  0 19:40 pts/0    00:00:00 ps -ef

```

---

# SERVICES

+++

```bash
$ cf
[..]
SERVICES:
   marketplace                            List available offerings in the marketplace
   services                               List all service instances in the target space
   service                                Show service instance info

   create-service                         Create a service instance
   update-service                         Update a service instance
   delete-service                         Delete a service instance
   rename-service                         Rename a service instance

   create-service-key                     Create key for a service instance
   service-keys                           List keys for a service instance
   service-key                            Show service key info
   delete-service-key                     Delete a service key

   bind-service                           Bind a service instance to an app
   unbind-service                         Unbind a service instance from an app

   bind-route-service                     Bind a service instance to an HTTP route
   unbind-route-service                   Unbind a service instance from an HTTP route

   create-user-provided-service           Make a user-provided service instance available to CF apps
   update-user-provided-service           Update user-provided service instance

```

+++

### Marketplace
```bash
mhs@R2-D2:~/git/cf-simple-hello/cf-simple-hello$ cf marketplace
Getting services from marketplace in org pcfdev-org / space pcfdev-space as admin...
OK

service        plans             description
local-volume   free-local-disk   Local service docs: https://github.com/cloudfoundry-incubator/local-volume-release/
p-mysql        512mb, 1gb        MySQL databases on demand
p-rabbitmq     standard          RabbitMQ is a robust and scalable high-performance multi-protocol messaging broker.
p-redis        shared-vm         Redis service to provide a key-value store

TIP:  Use 'cf marketplace -s SERVICE' to view descriptions of individual plans of a given service

mhs@R2-D2:~/git/cf-simple-hello/cf-simple-hello$ cf marketplace -s p-mysql
Getting service plan information for service p-mysql as admin...
OK

service plan   description            free or paid
512mb          PCF Dev MySQL Server   free
1gb            PCF Dev MySQL Server   free
```

+++

### Service creation

```bash
mhs@R2-D2:~/git/cf-simple-hello/cf-simple-hello$ cf create-service p-mysql 512mb cf-simple-mysql
Creating service instance cf-simple-mysql in org pcfdev-org / space pcfdev-space as admin...
OK

mhs@R2-D2:~/git/cf-simple-hello/cf-simple-hello$ cf service cf-simple-mysql

Service instance: cf-simple-mysql
Service: p-mysql
Bound apps: 
Tags: 
Plan: 512mb
Description: MySQL databases on demand
Documentation url: 
Dashboard: http://mysql-broker.local.pcfdev.io/manage/instances/24ab1a85-b547-4721-8870-22726819828c

Last Operation
Status: create succeeded
Message: 
Started: 2017-04-21T16:41:45Z
Updated: 2017-04-21T16:41:45Z
```
+++

# Service binding

```bash
mhs@R2-D2:~/git/cf-simple-hello/cf-simple-hello$ cf bind-service cf-simple-hello cf-simple-mysql
Binding service cf-simple-mysql to app cf-simple-hello in org pcfdev-org / space pcfdev-space as admin...
OK
TIP: Use 'cf restage cf-simple-hello' to ensure your env variable changes take effect
```

+++

```bash
mhs@R2-D2:~/git/cf-simple-hello/cf-simple-hello$ cf env cf-simple-hello
Getting env variables for app cf-simple-hello in org pcfdev-org / space pcfdev-space as admin...
OK

System-Provided:
{
 "VCAP_SERVICES": {
  "p-mysql": [
   {
    "credentials": {
     "hostname": "mysql-broker.local.pcfdev.io",
     "jdbcUrl": "jdbc:mysql://mysql-broker.local.pcfdev.io:3306/cf_24ab1a85_b547_4721_8870_22726819828c?user=YlukSlUdSdsjA6jz\u0026password=gXqlUeLOIN9j4QWJ",
     "name": "cf_24ab1a85_b547_4721_8870_22726819828c",
     "password": "gXqlUeLOIN9j4QWJ",
     "port": 3306,
     "uri": "mysql://YlukSlUdSdsjA6jz:gXqlUeLOIN9j4QWJ@mysql-broker.local.pcfdev.io:3306/cf_24ab1a85_b547_4721_8870_22726819828c?reconnect=true",
     "username": "YlukSlUdSdsjA6jz"
    },
    "label": "p-mysql",
    "name": "cf-simple-mysql",
    "plan": "512mb",
    "provider": null,
    "syslog_drain_url": null,
    "tags": [
     "mysql"
    ],
    "volume_mounts": []
   }
  ]
 }
}

{
 "VCAP_APPLICATION": {
  "application_id": "8e8eaac1-3722-40c2-88ee-22e6b0c1bf4b",
  "application_name": "cf-simple-hello",
  "application_uris": [
   "cf-simple-hello.local.pcfdev.io"
  ],
  "application_version": "3bc37ef1-b175-4362-b8d6-8deae737aced",
  "cf_api": "http://api.local.pcfdev.io",
  "limits": {
   "disk": 512,
   "fds": 16384,
   "mem": 256
  },
  "name": "cf-simple-hello",
  "space_id": "d928cac8-5f3d-4b5a-bc0f-7ac221e243ea",
  "space_name": "pcfdev-space",
  "uris": [
   "cf-simple-hello.local.pcfdev.io"
  ],
  "users": null,
  "version": "3bc37ef1-b175-4362-b8d6-8deae737aced"
 }
}

```

---

# Demo

---

# Note

### pcf dev users use cf suspend/resume for your VM


---

### Lessons learned

- Concept of "Bring your own code"
- Orgs, Spaces, Users, Quotas
- App + Buildpack = Droplet
- App logging, env, scaling & access
- Services - Managed & User-provided

---

## Clear by now?

![sketch1](img/20170421_202906.jpg)

---

### Advantages

- Easy deployment - only code necessary
- Automatic setup of immutable artifact
- Automatic restart - resilience
- Aggregated logging
- Scaling & Load-Balancing
- Fault tolerance

---

### Exercises

- Build a sample app (or re-use existing one)
- implement a manifest.yml (or modify existing one)
- Deploy the application
- Use the CLI to
  - analyze the logs
  - ssh into your application's container
  - kill it :)
  - scale the instances up and down
  
---
  
### Exercises II

- Run a while loop against an HTTP endpoint of your app
- Observe the round robin access of your instances
- Observe the availability with single & multiple instances
- List the services from the marketplace
- Create a service
- Bind a service
- Use the service (not described!)

---
  
### Exercises - for the fast ones :)

- Read about Blue - Green Deployment
- https://docs.cloudfoundry.org/devguide/deploy-apps/blue-green.html
- Do it!

---

![Meetup](https://secure.meetupstatic.com/photos/event/e/8/a/6/highres_458579558.jpeg)
https://www.meetup.com/Stuttgart-Cloud-Foundry-Meetup/events/237897736/

---

https://gitpitch.com/maeddes/cloudfoundry-gitpitch

