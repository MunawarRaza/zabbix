# Table of contents #
1. [What is Zabbix](#What-is-Zabbix)
2. [Zabbix features](#zabbix-features)
3. [Architecture of Zabbix](#architecture-of-zabbix)
   - [Server](#server)
   - [Database Storage](#database-storage)
   - [Web interface ](#web-interface)
   - [Agent](#agent)
4. [Terms used in Zabbix](#terms-used-in-zabbix)
   - 
 

## What is Zabbix ##
Zabbix is a software that monitors numerous parameters of a network and the health and integrity of servers, virtual machines, applications, services, databases, websites, the cloud and more.

Zabbix uses a flexible notification mechanism that allows users to configure email-based alerts for virtually any event

Zabbix supports both polling and trapping.
        Polling:
                When monitoring software ask to the devices(router,printer..) for his health
        Trapping:
                When devices(routers, printers...) sends to monitoring software when something happend

## Zabbix features ##
- Data gathering
- Flexible threshold definitions
- Highly configurable alerting
- Real-time graphing
- Web monitoring capabilities
- Historical data storage

## Architecture of Zabbix ##

Zabbix consists of several major software components.

### Server ###
Zabbix server is the central component to which agents report their availability and integrity information and statistics.

### Database storage ###
All configuration information as well as the data gathered by Zabbix is stored in a database

### Web interface ###
For an easy access to Zabbix from anywhere and from any platform, the web-based interface is provided

### Agent ###

Zabbix agents are deployed on monitoring targets to actively monitor local resources and applications and report the gathered data to Zabbix server.

There are tow types of zabbix agents
-  Zabbix agent (lightweight, supported on many platforms, written in C)
-  Zabbix agent 2 (extra-flexible, easily extendable with plugins, written in Go)

## Terms used in Zabbix ##
Terms commonly used in Zabbix
### host ###
Any physical or virtual device, application, service, or any other logically-related collection of monitored parameters

### host group ###
A logical grouping of hosts. Host groups are used when assigning access rights to hosts for different user groups.

### item ###
A particular piece of data that you want to receive from a host, a metric of data like number of running process, number of vCPUs etc.
Items are used for collecting data. Once you have configured a host, you must add items to get actual data

#### Unit conversion ####
By default, specifying a unit for an item results in a multiplier prefix being added - for example, an incoming value '2048' with unit 'B' would be displayed as '2KB'.
To prevent a unit from conversion, use the ! prefix, for example, !B. To better understand how the conversion works with and without the exclamation mark, see the following examples of values and units:

```
1024 !B → 1024 B
1024 B → 1 KB
61 !s → 61 s
61 s → 1m 1s
0 !uptime → 0 uptime
0 uptime → 00:00:00
0 !! → 0 !
0 ! → 0
```

#### Item key format ####

Following is the format to specify the key

key_name[parameter1, parameter2, parameter3...]

key_name can be --> 0-9a-zA-Z-_.

paramters:
        
- An item key can have multiple parameters that are comma separated
- Each key parameter can be either

        - a quoted string 
        - an unquoted string
        - an array
- The parameter can also be left empty, thus using the default value. In that case, the appropriate number of commas must be added if any further parameters are specified. For example, item key icmpping[,,200,,500] would specify that the interval between individual pings is 200 milliseconds, timeout - 500 milliseconds, and all other parameters are left at their defaults
- It is possible to include macros in the parameters. Those can be user macros or some of the built-in macros

        - To see what particular built-in macros are supported in item key parameters, search the page Supported macros for[a link](https://www.zabbix.com/documentation/current/en/manual/appendix/macros/supported_by_location)


#### Item types ####
Item types cover various methods of acquiring data from your system. Each item type comes with its own set of supported item keys and required parameters.
Its means, it depends on you, how to get or using which type of item type you want to get the value from the target machine
Following are some of supported Item types:
- Zabbix agent checks
- SNMP agent checks
- Log file monitoring
- HTTP checks
- Prometheus checks
- Script items ( get data using user define javascript code )
- SSH Checks ( agent less monitoring. no need of zabbix-agent)
- Telnet checks ( agent less monitoring. no need of zabbix-agent)

Each item type has some supported item keys.

#### Mass Update ####
Sometimes you may want to change some attribute for a number of items at once. Instead of opening each individual item for editing, you may use the mass update function for that



### value preprocessing ###
A transformation of received metric value before saving it to the database

### trigger ###

logical expression that defines a problem threshold and is used to "evaluate" data received in items. For example, define a threshold for any incoming data. On the basis of this data, envent happend 

When received data are above the threshold, triggers go from 'Ok' into a 'Problem' state. When received data are below the threshold, triggers stay in/return to an 'Ok' state.

- Ok
- Problem
- Unknown

#### Trigger expression ####
Logical expression are used in triggers to calculate and to evaluate the data recevied from the target machines.

Flow:

A simple expression uses a function that is applied to the item with some parameters. The function returns a result that is compared to the threshold, using an operator and a constant.

Syntax: 

function(/host/key,parameter)<operator><constant>

e.g:

min(/Zabbix server/net.if.in[eth0,bytes],5m)>100K

above will trigger if the number of received bytes during the last five minutes was always over 100 kilobytes

#### Functions ####
Functions allow to calculate the collected values (average, minimum, maximum, sum), find strings, reference current time and other factors.

#### Function parameters ####

#### Function-specific parameters #####
Function-specific parameters are placed after the item key and are separated from the item key by a comma.

- Most of numeric functions accept time as a parameter. You may use seconds or time suffixes to indicate time
- Preceded by a hash mark, the parameter has a different meaning. They denote the Nth previous value
```
Expression	        Description
sum(/host/key,10m)	Sum of values in the last 10 minutes.
sum(/host/key,#10)	Sum of the last ten values.
```

### Event ###
Envent is the state of any triggers. For example we have defined a threshold 80% for RAM. If RAM is go above 80% there should be an itimation. So if RAM usage is below 80% its OK, but when RAM is above to 80% its not OK, it's dangerous. These changes are called events. 
Trigger Events (OK --> Problem --> OK)

### template ###

A set of entities (items, triggers, graphs, low-level discovery rules, web scenarios) ready to be applied to one or several hosts.

The job of templates is to speed up the deployment of monitoring tasks on a host; also to make it easier to apply mass changes to monitoring tasks. Templates are linked directly to individual hosts.

### Dataflow ###
host --> item --> trigger --> action

### Role ###
The role determines which frontend elements the user can view and which actions he is allowed to perform

### Macros ###
Zabbix supports a number of built-in macros which may be used in various situations. These macros are variables, identified by a specific syntax
 ```
 {MACRO}
 ```
Macros resolve to a specific value depending on the context.

Zabbix supports the following macros:

- {MACRO} - built-in macro [see full list](https://www.zabbix.com/documentation/current/en/manual/appendix/macros/supported_by_location)

- `{<macro>.<func>(<params>)}` - [macro functions](https://www.zabbix.com/documentation/current/en/manual/config/macros/macro_functions)

- {$MACRO} - user-defined [macro](https://www.zabbix.com/documentation/current/en/manual/config/macros/user_macros), optionally with [context](https://www.zabbix.com/documentation/current/en/manual/config/macros/user_macros_context)
- {#MACRO} - macro for low-level [discovery](https://www.zabbix.com/documentation/current/en/manual/config/macros/lld_macros)
- {?EXPRESSION} - [expression macro](https://www.zabbix.com/documentation/current/en/manual/config/macros/expression_macros)

### More ###

https://www.zabbix.com/documentation/current/en/manual/definitions

Note:

Best practice in Zabbix.

Create a template group.
Create a template and assign above created template group.
Create number of items, graphs, triggers, web scenarios in above created template.
Assign above template to any hosts. With this, all created items, graphs, triggers and web scenerios will be assigned to this host.

## Installation of Zabbix ##

There are four ways of getting Zabbix:

- Install it from the distribution packages
- Download the latest source archive and compile it yourself
- Install it from the containers
- Download the virtual appliance

```

######## Install postgres database ########

sudo apt install curl ca-certificates
sudo install -d /usr/share/postgresql-common/pgdg
sudo curl -o /usr/share/postgresql-common/pgdg/apt.postgresql.org.asc --fail https://www.postgresql.org/media/keys/ACCC4CF8.asc
sudo sh -c 'echo "deb [signed-by=/usr/share/postgresql-common/pgdg/apt.postgresql.org.asc] https://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
sudo apt update
sudo apt -y install postgresql

Reference:
https://www.postgresql.org/download/linux/ubuntu/

# Create user #
sudo -u postgres createuser --pwprompt zabbix

# Create new database # 
sudo -u postgres createdb -O zabbix zabbix


######## Install Zabbix ########
1- Install zabbix repository
go to the website and select your os and other things.
https://www.zabbix.com/download?zabbix=7.0&os_distribution=ubuntu&os_version=22.04&components=server_frontend_agent&db=pgsql&ws=nginx

wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_7.0-2+ubuntu22.04_all.deb
dpkg -i zabbix-release_7.0-2+ubuntu22.04_all.deb
apt update

2- Install Zabbix server, frontend
apt install zabbix-server-pgsql zabbix-frontend-php php8.1-pgsql zabbix-nginx-conf zabbix-sql-scripts

2.1 install zabbix agent
apt install zabbix-agent


3- On Zabbix server host import initial schema and data. You will be prompted to enter your newly created password.
zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix

4- Configure the database for Zabbix server
Edit file /etc/zabbix/zabbix_server.conf
    DBPassword=zabbix

######## Install nginx ########
sudo apt install nginx

5- Configure PHP for Zabbix frontend
Edit file /etc/zabbix/nginx.conf uncomment and set 'listen' and 'server_name' directives.
# listen 8080;
# server_name example.com;

6- mv the nginx file in nginx configuration
mv /etc/zabbix/nginx.conf /etc/nginx/conf.d/

# configure php.ini #
if found errors install the following dependencies

apt install php8.1-gd php8.1-bcmath php8.1-ctype php8.1-libXML php8.1-xmlreader php8.1-xmlwriter php8.1-session php8.1-sockets php8.1-mbstring php8.1-gettext ldap php8.1-openssl php8.1-pgsql php8.1-curl

edit the file /etc/php/8.1/fpm/php.ini
past the following lines at the end of file

extension=pdo
extension=iconv
extension=bcmath
;extension=mbstring
extension=sockets
extension=gd
extension=xml
extension=mbstring
extension=ctype
extension=ldap
extension=gettext
extension=curl
extension=dom
extension=xmlreader.so
extension=xmlwriter

7- Restart the services
systemctl restart zabbix-server nginx php8.1-fpm

Reference:
https://www.zabbix.com/download?zabbix=7.0&os_distribution=ubuntu&os_version=22.04&components=server_frontend_agent&db=pgsql&ws=nginx

==================< nginx conf for zabbix >==================

server {
        listen 443;
        server_name zabbix.oneloadpk.com;
        ssl_certificate /etc/ssl/oneloadpk/oneload_chain.crt;
        ssl_certificate_key /etc/ssl/oneloadpk/oneloadpk.key;
        root /usr/share/zabbix;
        location / {
                index   index.php;
                error_page      403     404     502     503     504     /zabbix/index.php;

                location ~\.php$ {
                        if ( !-f $request_filename ) { return 404; }
                        expires epoch;
                        include /etc/nginx/fastcgi_params;
                        fastcgi_index index.php;
                        fastcgi_pass unix:/var/run/php/zabbix.sock;
                        fastcgi_param SCRIPT_FILENAME   /usr/share/zabbix/$fastcgi_script_name;
                }
                location ~ \.(jpg|jpeg|gif|png|ico)$ {
                        access_log off;
                        expires 33d;
                }
        }
}
```

## How to use custome template ##
To use the template following steps are required.

1. Create a template group
2. Create a template and assign it to the above created template group
3. Now create items, triggers, graphs, dashboards, and web. So that when this template is assigned to any host, all these configured entities should be accessible to that host, and we can use any thing from these entities.
4. Assign this new template to hosts.
5. Use required things from this template. For example, i have created a template, in which i created 2 graphs, and 4 items. I have attached this template to any host. I want to use one graph and 2 items from this template. So i can do that.


## Zabbix Agent ##

Zabbix agent is deployed on a monitoring target to actively monitor local resources and applications (hard drives, memory, processor statistics, etc.).
The agent gathers operational information locally and reports data to Zabbix server for further processing

### Passive and active checks ###
### Passive Check ####
Zabbix Server ask to zabbix agent for the data (like zabbix server request to zabbix agent that sends me the information of CPU, RAM etc)
#### Active Check ####
Active checks require more complex processing. The agent must first retrieve a list of items from Zabbix server for independent processing. Then it will periodically send new values to the server.

