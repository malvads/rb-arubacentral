# rb-arubacentral
![tests](https://github.com/redBorder/rb-arubacentral/actions/workflows/tests.yml/badge.svg?event=push)
![rubocop](https://github.com/redBorder/rb-arubacentral/actions/workflows/rubocop.yml/badge.svg?event=push)
[![License: AGPL v3](https://img.shields.io/badge/License-AGPL_v3-blue.svg)](https://www.gnu.org/licenses/agpl-3.0)
[![codecov](https://codecov.io/gh/redBorder/rb-arubacentral/graph/badge.svg?token=PM3AX50GFX)](https://codecov.io/gh/redBorder/rb-arubacentral)
[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2FredBorder%2Frb-arubacentral.svg?type=shield)](https://app.fossa.com/projects/git%2Bgithub.com%2FredBorder%2Frb-arubacentral?ref=badge_shield)

Aruba Central is a cloud-based network management platform, similar to ALE (`Aruba Location Engine`) and Cisco MSE (`Mobility Services
Engine`). It is developed by Aruba Networks, a Hewlett Packard Enterprise company. Aruba Central provides centralized management and monitoring of Aruba network devices, such as switches, routers, and wireless access points.

The goal of the Aruba Central service is to fetch data from Aruba Central APs and transform it into real-time actionable information to track the mobility of people connected to Aruba Central APs.

## 1. Workflow
<p align="center">
  <img src="./assets/workflow.png">
</p>

The rb-arubacentral service processes data from the Aruba Central REST API. After retrieving the data, it generates two types of objects: the AP status and the mobility data. It then generates Kafka events and sends them to the Kafka broker in batches with the location data and the APs status data.

## 2. Caching
<p align="center">
  <img src="./assets/cache.png">
</p>

The rb-arubacentral can cache data for common requests. This is done to reduce API calls to the Aruba Central REST. The service first checks if the request is in the cache list. If it's not, then it will call the Aruba Central service as usual. If it's in the list, it will retrieve the data from the cache. Subsequently, it will read from the cache until it expires and then refresh its cache again by calling the REST.


## 3. Config
### 3.1 Sensors
- **sensor_name** (string): Name of the aruba central sensor
- **gateway** (string): Where the gather the information
- **email** (string): Aruba Central Email
- **password** (string): Aruba Central Password
- **client_id** (string): Aruba Central Client ID
- **client_secret** (string): Aruba Central Client Secret
- **customer_id** (string): Aruba Central Customer ID

### 3.2 Kafka
- **broker** (string): Kafka Broker
- **producer_name** (string): Kafka Producer Name
- **location_topic** (string): Kafka Location Topic
- **status_topic** (string): Kafka Status Topic

### 3.3 Service
- **sleep_time** (integer): Sleep time of service in seconds (for the main loop)
- **log_level** (integer): Log Level (2=info) (3=debug)

### 3.4 Flow Sensors
- **sensor_name** (string): Name of the sensor
- **sensor_uuid** (string): UUID v4 of the sensor
- **access_points** (array): Access points of the flow sensor

### 3.5 Cache
- **ttl** (hash): Cache ttl based on function method when requesting to aruba central 
- **keys** (array): List of functions to catch result for


### 3.6 Example

```yaml
sensors:
  -
       sensor_name: Aruba Central
       gateway: 'https://apigw-eucentral3.central.arubanetworks.com'
       email: 'admin@admin.com'
       password: 'admin'
       client_id: 'admin'
       client_secret: 'admin'
       customer_id: 'admin'
kafka:
  broker: 'kafka.redborder.cluster:9092'
  producer_name: my_aruba_producer
  location_topic: rb_loc
  status_topic: rb_status
service:
  sleep_time: 300
  log_level: 2
flow_sensors:
  -
       sensor_name: MySensorName
       sensor_uuid: 2e7241e4-12cf-4c6b-926c-5524ad537179
       access_points: ["00:00:00:00:00:01", "00:00:00:00:00:02", "00:00:00:00:00:03"]
cache:
  ttl:
    fetch_all_campuses: 3600
    fetch_campus: 1800
    fetch_floor_location: 7200
    fetch_building: 14400
  keys: [fetch_all_campuses fetch_campus fetch_floor_location fetch_building]
  ```

## 4. How to install?

The rb-arubacentral has been tested on ruby `2.1.2` and ruby `2.1.9`, you can install and run the program executing the following instructions

```bash
cd rb-arubacentral
rvm install 2.1.2
bundle install
```
### 4.1 Running

For running the service you can pass custom config file using `-c`, for example `ruby ./lib/rb_arubacentral.rb -c /path/to/my/config.yml` or you can just run `ruby ./lib/rb_arubacentral.rb` and it will take the default config file.

## 5. Contribute

* Create a fork of the project
* Create a branch `bugfix_myfix`, `feature_myfeature`, `improvement_myimprovement`
* Create the test
* Run the tests ensuring all pass using `rake test`
* Open a PR to the master branch

## 5. License
[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2FredBorder%2Frb-arubacentral.svg?type=large)](https://app.fossa.com/projects/git%2Bgithub.com%2FredBorder%2Frb-arubacentral?ref=badge_large)

## 6. Authors

* Miguel Álvarez Adsuara <malvarez@redborder.com>
* Nils Verschaeve <nverschaeve@redborder.com>
* David Vanhoucke <dvanhoucke@redborder.com>
