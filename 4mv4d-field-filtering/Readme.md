# Filtering properties from responses , depending on the Developer App or Product

This is an example proxy that illustrates how to use Apigee Edge to filter fields of data
from responses, dynamically based on the API Product used or the Developer App used.

In either case the Proxy retrieves a set of fields to include or exclude from the response, from the metadata attached to the entity (API Product or App).

This is a handy pattern for building adaptable facades for APIs.
It also supports the idea of allowing developers of apps to specify the fields they'd like to include or exclude.

## The Easy Way

The easy way to run this demonstration is to use the [provision-4mv4d-field-filtering-demo.sh](provision-4mv4d-field-filtering-demo.sh) script to provision the api proxy, api products, and developer apps necessary, and then to import the generated postman collection and invoke APIs from Postman.

Each of the API Requests in the postman collection will demonstrate one aspect of the demo.

They are:
  - verifying a valid api key, no filtering
  - verifying a valid api key, no filtering, with response cache
  - Rejecting an expired api key
  - Rejecting an invalid api key 
  - verifying a valid api key, filtering based on API Product metadata, 
  - verifying a valid api key, iltering based on API Product metadata, with response cache
  - verifying a valid api key, filtering based on App (Client) metadata, 
  - verifying a valid api key, iltering based on App (Client) metadata, with response cache


There are three flavors of "flow" in the API Proxy: t1, t2, t3.

* The t1 flow does no filtering.
* The t2 flow uses API Product metadata to direct filtering
* the t3 flow uses metadata on the Developer App to guide the filtering

There are two API Products, each of which has different filtering metadata, and two Developer Apps, each of which has different filtering metadata.  So you've got a number of different combinations to exercise.

You wouldn't use ALL of these in a real system. The idea is just to show some of what is possible. 


## Manually Preparing and Provisioning

Ok, you don't like the easy way. If you want to manually provision this demo, here's how:

1. Import the proxy bundle into any Apigee Edge organization

2. create two API Products, both of which have the api proxy imported above.
   Both should have "custom attributes", like this:  
   | API Product     | attr name     |  value                |
   |-----------------|---------------|-----------------------|
   | Filter-Product1 | filter_fields | city.name, city.state, airports.code, airports.name |
   |                 | filter_action | include               |
   | Filter-Product2 | filter_fields | city, airports.country, airports.city_code |
   |                 | filter_action | exclude               |
   |
   
3. create a Developer within Apigee Edge

4. create two Developer Apps within Apigee Edge, associated to the new developer, each with authorization
   to one of the above API Products.
   
   Also, each should have "custom attributes". Configure them like so:
   || Developer App || API Product    || attr name    ||  value                ||
   |----------------|-----------------|---------------|-------------------------|
   | Filter-App1    | Filter-Product1 | filter_fields | city.name, city.state, airports.location, airports.code, airports.aircraft_movements |
   |                |                 | filter_action | include                 |
   | Filter-App2    | Filter-Product2 | filter_fields | city, airports.location |
   |                |                 | filter_action | exclude                 |
   |


5. View and copy the client_id (aka API Key) for each of the above developer apps.  You may want to create other apps that have no access to the api proxy in question, to demonstrate key rejection. 


## Running the Demonstrations "manually"

The APIKey is passed in the header "APIKEY".

The requests are of the form
`GET /4mv4d-filtering-demo/PATH/IATA_CODE`

...where PATH is replaced by one of
`iata-t1` , `iata-t2` , `iata-t3` , `iata-t1c` , `iata-t2c` , `iata-t3c` .

* t1 implies no filtering
* t2 filters based on the custom attribute on the API Product
* t3 filters based on the custom attribute on the Client (Developer App)
* the c suffix indicates that the flow uses the response cache

