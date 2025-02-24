# REST API DEMO

This section demonstrates using the IBM Db2 Event Store REST endpoint in order to perform queries and return results.

For more information, please find on [**IBM Db2 Event Store REST API (2.0.0)**](https://www.ibm.com/docs/en/db2-event-store/2.0.0?topic=store-rest-api)

Task performed through the REST API example:

- Obtain the count of records that matches a filter condition from a existing Event Store database table

- Display all records that match a filter condition from a existing Event Store database table

### Prerequisites
- Created the Event Store database and an `IOT_TEMP` table using the notebook or running the ODBC `ExampleODBCApp` test  [IoT applications](https://github.com/IBMProjectEventStore/db2eventstore-IoT-Analytics/tree/master/AdvancedApplications/ODBCApplication).
- Ingested sample data into the database table using [`load.sh`](../data/load.sh) or running the ODBC [IoT applications](https://github.com/IBMProjectEventStore/db2eventstore-IoT-Analytics/tree/master/AdvancedApplications) as that will ingest sample data as well .
- Run [`install.sh`](install.sh) to install the prerequisite packages. 

**If using the IoT demo docker container, all of these variables are automatically set up for you and you do not have define them as described below, skip to the Sample Execution section**
- Define `IP`, `EVENTSTORE_USER`, `EVENTSTORE_PASSWORD`, `DB2_PORT`, `IPREST`, and `DEPLOYMENT_ID` as shell environment variables.
- The `DB2_PORT` variable, this is the Db2 port accessible outside of OpenShift cluster, Db2 listens on a port inside the OpenShift cluster, this typically is mapped to another port and exposed outside the cluster and is referred  to as the Db2 external port, this external port is what is needed.  If using haproxy on the infrastructure node, this port may be obtained by this command from the infrastructure node
```
sed -n '/frontend db2/{n;p;}' /etc/haproxy/haproxy.cfg | cut -d : -f2
```
If the output of that command is `9177` or if the external Db2 port is `9177`, run the following command:
```
export DB2_PORT=9177
```
- Define `DEPLOYMENT_ID` variable,  This value is specific to the Event Store database and can be retrieved from the event store cloudpak for data User Interface (UI) at: `Data ... Databases ... Detail`s. It will be a value that appears similar to: db2eventstore-1630513601941818
```
export DEPLOYMENT_ID=db2eventstore-1630513601941818
```

- For the `IPREST` variable, which you can obtain from a terminal session connected to the OpenShift cluster running Event Store
```
oc get route  | grep -v 'HOST/PORT' | awk '{print $2}'
```
If the output of that command is `zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com` then run
```
export IPREST=zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com
```

### Sample Execution

To Run this example, simply execute [`./run.sh`](run.sh).  Run the IoT [ODBC test](https://github.com/IBMProjectEventStore/db2eventstore-IoT-Analytics/tree/master/AdvancedApplications/ODBCApplication) first as this will create the required `IOT_TEMP` table requires by this rest test.

So just execute this
```
cd $HOME/db2eventstore-IoT-Analytics/rest
./install.sh
./run.sh
```
or you can specify the variables manually such as 
```
cd $HOME/db2eventstore-IoT-Analytics/rest
./install.sh
./run.sh --endpoint 9.46.196.49 --deployment-id db2eventstore-1630513601941818
```
or with all variables manually with
```
cd $HOME/db2eventstore-IoT-Analytics/rest
./install.sh
./run.sh --user admin --password password --endpoint 9.46.196.49 --endpointRest zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com --db2_port 9177 --deployment-id db2eventstore-1630513601941818
```

### Successful Execution Output
Simply run this command
```
./run.sh
```
output was 

```
[root@a745f85ea5ea rest]# ./run.sh
+ NAMESPACE=zen
+ '[' -n '' ']'
+ '[' -z admin ']'
+ '[' -z password ']'
+ '[' -z 9.46.196.49 ']'
+ '[' -z 9177 ']'
+ '[' -z zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com ']'
+ '[' -z db2eventstore-1630513601941818 ']'
+ node test.js --engine=9.46.196.49: --server=https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443 --user=admin --password=password --namespace=zen --deployment-id=db2eventstore-1630513601941818
Using Engine: 9.46.196.49:
Using Server: https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443
Using Username: admin
Using Password: password
Namespace: zen
Deployment id: db2eventstore-1630513601941818

Authorization in header
Basic YWRtaW46cGFzc3dvcmQ=
Getting the IDP Bearer Token
==========================
https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443/v1/preauth/validateAuth
(node:67) [DEP0005] DeprecationWarning: Buffer() is deprecated due to security and usability issues. Please use the Buffer.alloc(), Buffer.allocUnsafe(), or Buffer.from() methods instead.
(Use `node --trace-deprecation ...` to show where the warning was created)
Token from IDP Cluster successfully retrieved:
==========================
eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIiwicm9sZSI6IkFkbWluIiwicGVybWlzc2lvbnMiOlsiYWRtaW5pc3RyYXRvciIsImNhbl9wcm92aXNpb24iXSwiZ3JvdXBzIjpbMTAwMDBdLCJzdWIiOiJhZG1pbiIsImlzcyI6IktOT1hTU08iLCJhdWQiOiJEU1giLCJ1aWQiOiIxMDAwMzMwOTk5IiwiYXV0aGVudGljYXRvciI6ImRlZmF1bHQiLCJpYXQiOjE2MzEwNjI5MzksImV4cCI6MTYzMTEwNjEwM30.IQ2I7BGQt_IyIJOE_qyrtXN5JLrt-ll2_y0xjRj2DVMRldS014MAiKozSrNPLflvXy2VxH7Mrork1xl2MrO9hu3k14UgHKq3qS-Mielg67MUaKDSWHchcHlB1gVtGF5p0KYEuQapYrj1c8MrVcElEGWQfL3GdtW_wapp70tg4SBtcYB0HAZfNiguFWdaxm1yPtYJVms9d6F53P-pmU3R2FEcXiPQiJduP-4baWs2OVbRf6TB-RN8_dFuvJ3hXjRxDgQl1p8X6y0R0iCJBcylsGfyIXy7NTFks5e2mvEDnJVdUuMV__a0G0kTZFXaBS9AiIgi8YQXzSbIsut0XS27Rw
==========================


==========================
IDP Cluster located at: https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443

Running test: Connect to Engine
https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443/icp4data-databases/db2eventstore-1630513601941818/zen/com/ibm/event/api/v1/init/engine?engine=9.46.196.49:
==========================
==========================
URL Called -> https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443/icp4data-databases/db2eventstore-1630513601941818/zen/com/ibm/event/api/v1/init/engine?engine=9.46.196.49:
** Received response **
Response Returned -> [object Object]
Body Returned -> {"code":"ES100","message":"Engine initialization succeeded"}
RESPONSE -> ES100
MESSAGE -> Engine initialization succeeded

Running test: Get Database
https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443/icp4data-databases/db2eventstore-1630513601941818/zen/com/ibm/event/api/v1/oltp/databases
==========================
URL Called -> https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443/icp4data-databases/db2eventstore-1630513601941818/zen/com/ibm/event/api/v1/oltp/databases
** Received response **
Response Returned -> [object Object]
Body Returned -> {"code":"ES350","message":"List of all databases successfully retrieved","data":[{"name":"EVENTDB"}]}
RESPONSE -> ES350
MESSAGE -> List of all databases successfully retrieved

Running test: Get the table Info
https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443/icp4data-databases/db2eventstore-1630513601941818/zen/com/ibm/event/api/v1/oltp/table?tableName=IOT_TEMP&databaseName=EVENTDB
==========================
URL Called -> https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443/icp4data-databases/db2eventstore-1630513601941818/zen/com/ibm/event/api/v1/oltp/table?tableName=IOT_TEMP&databaseName=EVENTDB
** Received response **
Response Returned -> [object Object]
Body Returned -> {"code":"ES370","message":"Table information successfully retrieved","data":{"groupID":24,"tableGroupName":"sys_ADMINIOT_TEMP","id":48,"numberShards":36}}
RESPONSE -> ES370
MESSAGE -> Table information successfully retrieved

Submitting a SparkSql query
==========================
Submitting -> "select count(*) as count from IOT_TEMP where deviceID=1 and sensorID=31"
Result of SparkSql query:
https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443/icp4data-databases/db2eventstore-1630513601941818/zen/com/ibm/event/api/v1/spark/sql?tableName=IOT_TEMP&databaseName=EVENTDB
==========================
URL Called -> https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443/icp4data-databases/db2eventstore-1630513601941818/zen/com/ibm/event/api/v1/spark/sql?tableName=IOT_TEMP&databaseName=EVENTDB
** Received response **
Response Returned -> [object Object]
Body Returned -> {"code":"ES400","message":"SparkSQL query was correctly executed","data":"[{\"count\":1155}]"}

**Query Result:**
[{"count":1155}]

Submitting a SparkSql query
==========================
Submitting -> "select * from IOT_TEMP where deviceID=1 and sensorID=31 limit 5"
Result of SparkSql query:
https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443/icp4data-databases/db2eventstore-1630513601941818/zen/com/ibm/event/api/v1/spark/sql?tableName=IOT_TEMP&databaseName=EVENTDB
==========================
URL Called -> https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443/icp4data-databases/db2eventstore-1630513601941818/zen/com/ibm/event/api/v1/spark/sql?tableName=IOT_TEMP&databaseName=EVENTDB
** Received response **
Response Returned -> [object Object]
Body Returned -> {"code":"ES400","message":"SparkSQL query was correctly executed","data":"[{\"DEVICEID\":1,\"SENSORID\":31,\"TS\":1541627199220,\"AMBIENT_TEMP\":27.593995991939863,\"POWER\":6.921405275716975,\"TEMPERATURE\":44.1656966644114},{\"DEVICEID\":1,\"SENSORID\":31,\"TS\":1541310567376,\"AMBIENT_TEMP\":27.08558496190764,\"POWER\":13.62873063735008,\"TEMPERATURE\":50.3360236897283},{\"DEVICEID\":1,\"SENSORID\":31,\"TS\":1541543326122,\"AMBIENT_TEMP\":26.047510749164648,\"POWER\":12.6674332022278,\"TEMPERATURE\":47.016434953018525},{\"DEVICEID\":1,\"SENSORID\":31,\"TS\":1541054234074,\"AMBIENT_TEMP\":25.60663139977397,\"POWER\":14.718642454235619,\"TEMPERATURE\":47.998245262863314},{\"DEVICEID\":1,\"SENSORID\":31,\"TS\":1541699793364,\"AMBIENT_TEMP\":24.66876440073116,\"POWER\":12.570519460858517,\"TEMPERATURE\":43.262131448398314}]"}

**Query Result:**
[{"DEVICEID":1,"SENSORID":31,"TS":1541627199220,"AMBIENT_TEMP":27.593995991939863,"POWER":6.921405275716975,"TEMPERATURE":44.1656966644114},{"DEVICEID":1,"SENSORID":31,"TS":1541310567376,"AMBIENT_TEMP":27.08558496190764,"POWER":13.62873063735008,"TEMPERATURE":50.3360236897283},{"DEVICEID":1,"SENSORID":31,"TS":1541543326122,"AMBIENT_TEMP":26.047510749164648,"POWER":12.6674332022278,"TEMPERATURE":47.016434953018525},{"DEVICEID":1,"SENSORID":31,"TS":1541054234074,"AMBIENT_TEMP":25.60663139977397,"POWER":14.718642454235619,"TEMPERATURE":47.998245262863314},{"DEVICEID":1,"SENSORID":31,"TS":1541699793364,"AMBIENT_TEMP":24.66876440073116,"POWER":12.570519460858517,"TEMPERATURE":43.262131448398314}]

```
Successful output with running `./install.sh` before `./run.sh` is below
```
[root@d049cf169f64 ~]# cd $HOME/db2eventstore-IoT-Analytics/rest
[root@d049cf169f64 rest]# ./install.sh
Installing packages for REST

## Installing the NodeSource Node.js 16.x repo...


## Inspecting system...

+ rpm -q --whatprovides redhat-release || rpm -q --whatprovides centos-release || rpm -q --whatprovides cloudlinux-release || rpm -q --whatprovides sl-release || rpm -q --whatprovides fedora-release
+ uname -m

## Confirming "el8-x86_64" is supported...

+ curl -sLf -o /dev/null 'https://rpm.nodesource.com/pub_16.x/el/8/x86_64/nodesource-release-el8-1.noarch.rpm'

## As yum will try to install Node.js from the AppStream repository
instead of the NodeSource repository, the AppStream's version of Node.js has to be disabled.
## Run `sudo yum module enable -y nodejs` to reactivate the AppStream's Node.js repository.

+ yum module disable -y nodejs
Updating Subscription Management repositories.
Unable to read consumer identity

This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.

Node.js Packages for Enterprise Linux 8 - x86_64                                                                                                 402 kB/s | 259 kB     00:00    
Red Hat Universal Base Image 8 (RPMs) - BaseOS                                                                                                   893 kB/s | 786 kB     00:00    
Red Hat Universal Base Image 8 (RPMs) - AppStream                                                                                                2.8 MB/s | 2.4 MB     00:00    
Red Hat Universal Base Image 8 (RPMs) - CodeReady Builder                                                                                         30 kB/s |  15 kB     00:00    
Dependencies resolved.
Nothing to do.
Complete!

## Downloading release setup RPM...

+ mktemp
+ curl -sL -o '/tmp/tmp.FohnbNVU6x' 'https://rpm.nodesource.com/pub_16.x/el/8/x86_64/nodesource-release-el8-1.noarch.rpm'

## Installing release setup RPM...

+ rpm -i --nosignature --force '/tmp/tmp.FohnbNVU6x'

## Cleaning up...

+ rm -f '/tmp/tmp.FohnbNVU6x'

## Checking for existing installations...

+ rpm -qa 'node|npm' | grep -v nodesource

## Run `sudo yum install -y nodejs` to install Node.js 16.x and npm.
## You may run dnf if yum is not available:
     sudo dnf install -y nodejs
## You may also need development tools to build native addons:
     sudo yum install gcc-c++ make
## To install the Yarn package manager, run:
     curl -sL https://dl.yarnpkg.com/rpm/yarn.repo | sudo tee /etc/yum.repos.d/yarn.repo
     sudo yum install yarn

Updating Subscription Management repositories.
Unable to read consumer identity

This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.

Last metadata expiration check: 0:00:02 ago on Wed Sep  8 05:45:02 2021.
Package nodejs-2:16.8.0-1nodesource.x86_64 is already installed.
Dependencies resolved.
Nothing to do.
Complete!
npm WARN deprecated har-validator@5.1.5: this library is no longer supported
npm WARN deprecated uuid@3.4.0: Please upgrade  to version 7 or higher.  Older versions may use Math.random() in certain circumstances, which is known to be problematic.  See https://v8.dev/blog/math-random for details.
npm WARN deprecated request@2.88.2: request has been deprecated, see https://github.com/request/request/issues/3142

added 48 packages, and audited 49 packages in 4s

2 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
npm notice 
npm notice New minor version of npm available! 7.21.0 -> 7.22.0
npm notice Changelog: https://github.com/npm/cli/releases/tag/v7.22.0
npm notice Run npm install -g npm@7.22.0 to update!
npm notice 
[root@d049cf169f64 rest]# ./run.sh
+ NAMESPACE=zen
+ '[' -n '' ']'
+ '[' -z admin ']'
+ '[' -z password ']'
+ '[' -z 9.46.196.49 ']'
+ '[' -z 9177 ']'
+ '[' -z zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com ']'
+ '[' -z db2eventstore-1630513601941818 ']'
+ node test.js --engine=9.46.196.49:9177 --server=https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443 --user=admin --password=password --namespace=zen --deployment-id=db2eventstore-1630513601941818
Using Engine: 9.46.196.49:9177
Using Server: https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443
Using Username: admin
Using Password: password
Namespace: zen
Deployment id: db2eventstore-1630513601941818

Authorization in header
Basic YWRtaW46cGFzc3dvcmQ=
Getting the IDP Bearer Token
==========================
https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443/v1/preauth/validateAuth
(node:136) [DEP0005] DeprecationWarning: Buffer() is deprecated due to security and usability issues. Please use the Buffer.alloc(), Buffer.allocUnsafe(), or Buffer.from() methods instead.
(Use `node --trace-deprecation ...` to show where the warning was created)
Token from IDP Cluster successfully retrieved:
==========================
eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIiwicm9sZSI6IkFkbWluIiwicGVybWlzc2lvbnMiOlsiYWRtaW5pc3RyYXRvciIsImNhbl9wcm92aXNpb24iXSwiZ3JvdXBzIjpbMTAwMDBdLCJzdWIiOiJhZG1pbiIsImlzcyI6IktOT1hTU08iLCJhdWQiOiJEU1giLCJ1aWQiOiIxMDAwMzMwOTk5IiwiYXV0aGVudGljYXRvciI6ImRlZmF1bHQiLCJpYXQiOjE2MzEwNzk5MDksImV4cCI6MTYzMTEyMzA3M30.CtkoniDv_SzxUBcH1_mgBYNaaRsv9GyozmfC-wCqP2KhDbqrigknN8zt85_n7ue7hI43KchcY9yBDvf8erQgkP6R3Ztbj4ncnWNKukdFhBiKUNPUCCFifcd5F86iLdviCFqAjaq1mMlb_xDXmYIVqyfN8MlMwDSOnQCJX4An_Bcr_HL5V0Je7o4ivp67Bs-ToLOFojfY4EFEUmlg4VkGoiT2nFOBPoBTNZx6xrh4kVCRXpsK7xW_JEBHFk8gO1Q0CI9zrfU1kcXwmJodhSlMOUe6-Cp_2_1Cc7fJ6TbP6Y5aSQjjoZROaX-7uxYsZcXN1LV0ttZyVq7izYZiS2TGlg
==========================


==========================
IDP Cluster located at: https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443

Running test: Connect to Engine
https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443/icp4data-databases/db2eventstore-1630513601941818/zen/com/ibm/event/api/v1/init/engine?engine=9.46.196.49:9177
==========================
==========================
URL Called -> https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443/icp4data-databases/db2eventstore-1630513601941818/zen/com/ibm/event/api/v1/init/engine?engine=9.46.196.49:9177
** Received response **
Response Returned -> [object Object]
Body Returned -> {"code":"ES100","message":"Engine initialization succeeded"}
RESPONSE -> ES100
MESSAGE -> Engine initialization succeeded

Running test: Get Database
https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443/icp4data-databases/db2eventstore-1630513601941818/zen/com/ibm/event/api/v1/oltp/databases
==========================
URL Called -> https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443/icp4data-databases/db2eventstore-1630513601941818/zen/com/ibm/event/api/v1/oltp/databases
** Received response **
Response Returned -> [object Object]
Body Returned -> {"code":"ES350","message":"List of all databases successfully retrieved","data":[{"name":"EVENTDB"}]}
RESPONSE -> ES350
MESSAGE -> List of all databases successfully retrieved

Running test: Get the table Info
https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443/icp4data-databases/db2eventstore-1630513601941818/zen/com/ibm/event/api/v1/oltp/table?tableName=IOT_TEMP&databaseName=EVENTDB
==========================
URL Called -> https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443/icp4data-databases/db2eventstore-1630513601941818/zen/com/ibm/event/api/v1/oltp/table?tableName=IOT_TEMP&databaseName=EVENTDB
** Received response **
Response Returned -> [object Object]
Body Returned -> {"code":"ES370","message":"Table information successfully retrieved","data":{"groupID":24,"tableGroupName":"sys_ADMINIOT_TEMP","id":48,"numberShards":36}}
RESPONSE -> ES370
MESSAGE -> Table information successfully retrieved

Submitting a SparkSql query
==========================
Submitting -> "select count(*) as count from IOT_TEMP where deviceID=1 and sensorID=31"
Result of SparkSql query:
https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443/icp4data-databases/db2eventstore-1630513601941818/zen/com/ibm/event/api/v1/spark/sql?tableName=IOT_TEMP&databaseName=EVENTDB
==========================
URL Called -> https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443/icp4data-databases/db2eventstore-1630513601941818/zen/com/ibm/event/api/v1/spark/sql?tableName=IOT_TEMP&databaseName=EVENTDB
** Received response **
Response Returned -> [object Object]
Body Returned -> {"code":"ES400","message":"SparkSQL query was correctly executed","data":"[{\"count\":1155}]"}

**Query Result:**
[{"count":1155}]

Submitting a SparkSql query
==========================
Submitting -> "select * from IOT_TEMP where deviceID=1 and sensorID=31 limit 5"
Result of SparkSql query:
https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443/icp4data-databases/db2eventstore-1630513601941818/zen/com/ibm/event/api/v1/spark/sql?tableName=IOT_TEMP&databaseName=EVENTDB
==========================
URL Called -> https://zen-cpd-zen.apps.stroud-es-2010-os-4631.cp.fyre.ibm.com:443/icp4data-databases/db2eventstore-1630513601941818/zen/com/ibm/event/api/v1/spark/sql?tableName=IOT_TEMP&databaseName=EVENTDB
** Received response **
Response Returned -> [object Object]
Body Returned -> {"code":"ES400","message":"SparkSQL query was correctly executed","data":"[{\"DEVICEID\":1,\"SENSORID\":31,\"TS\":1541627199220,\"AMBIENT_TEMP\":27.593995991939863,\"POWER\":6.921405275716975,\"TEMPERATURE\":44.1656966644114},{\"DEVICEID\":1,\"SENSORID\":31,\"TS\":1541310567376,\"AMBIENT_TEMP\":27.08558496190764,\"POWER\":13.62873063735008,\"TEMPERATURE\":50.3360236897283},{\"DEVICEID\":1,\"SENSORID\":31,\"TS\":1541543326122,\"AMBIENT_TEMP\":26.047510749164648,\"POWER\":12.6674332022278,\"TEMPERATURE\":47.016434953018525},{\"DEVICEID\":1,\"SENSORID\":31,\"TS\":1541054234074,\"AMBIENT_TEMP\":25.60663139977397,\"POWER\":14.718642454235619,\"TEMPERATURE\":47.998245262863314},{\"DEVICEID\":1,\"SENSORID\":31,\"TS\":1541699793364,\"AMBIENT_TEMP\":24.66876440073116,\"POWER\":12.570519460858517,\"TEMPERATURE\":43.262131448398314}]"}

**Query Result:**
[{"DEVICEID":1,"SENSORID":31,"TS":1541627199220,"AMBIENT_TEMP":27.593995991939863,"POWER":6.921405275716975,"TEMPERATURE":44.1656966644114},{"DEVICEID":1,"SENSORID":31,"TS":1541310567376,"AMBIENT_TEMP":27.08558496190764,"POWER":13.62873063735008,"TEMPERATURE":50.3360236897283},{"DEVICEID":1,"SENSORID":31,"TS":1541543326122,"AMBIENT_TEMP":26.047510749164648,"POWER":12.6674332022278,"TEMPERATURE":47.016434953018525},{"DEVICEID":1,"SENSORID":31,"TS":1541054234074,"AMBIENT_TEMP":25.60663139977397,"POWER":14.718642454235619,"TEMPERATURE":47.998245262863314},{"DEVICEID":1,"SENSORID":31,"TS":1541699793364,"AMBIENT_TEMP":24.66876440073116,"POWER":12.570519460858517,"TEMPERATURE":43.262131448398314}]
[root@d049cf169f64 rest]# 
```
