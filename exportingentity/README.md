# Exporting Entity Organization's Application
This application is hosted and maintained by a Exporting Entity Organization for its users to access and guide trade workflows. One role is currently supported:
- `client`: for individual exporters to accept trades, procure export licenses, track shipments, and claim payments.


# Pre-requisites to Run the Application
- VSCode with Java Language and SpringBook extensions.
- Any web client, e.g, `curl`, to send requests to the application web server.

# Configuration Settings
Edit [application.properties](src/main/resources/application.properties):
- `connectionProfilesBaseDir`: should point to the folder where the Exporting Entity Organization's connection profile is stored.
- `connectionProfileJson`: filename with connection profile in JSON format.
- `walletsBaseDir`: folder containing organization-specific wallets. A folder named `ExportingEntityOrgMSP` (the Exporting Entity Organization's MSP ID) may already be present with credentials, or one will be created to store credentials once the application is exercised.

# Start the Application
Open [ExportingEntityApplication.java](src/main/java/org/trade/exportingentity/ExportingEntityApplication.java) in the text editor in VSCode and click on either **Run** or **Debug** just above the `main` function definition. This will start a web server that will listen for requests on port 8000.

# Instructions to Exercise the REST API
You can perform the following operations (indicated below using `curl`, but you can use any web client, like Postman):   

For every operation, an error will result in an output JSON as follows:
```
{ "result": false, "error": <error-message> }
```
In the list below, we only mention the output of a successful operation.

- **Register a User**:
  ```
  curl -X POST http://localhost:8000/register -H "content-type: application/x-www-form-urlencoded" -d 'registrarUser=admin&registrarPassword=adminpw&username=<user>&password=<password>&role=<role>'
  ```
  This is meant to be run by a site administrator who knows the `registrarUser` and `registrarPassword` (in our case, we use the same credentials a Fabric CA registrar does.)   
  `<user>` and `<password>` can be any strings.   
  `<role>` MUST be either `client` or `admin`.   
  *Output JSON*:
  ```
  { "roles":[<role>], "username":<user> }
  ```
  If the request is successful, `<role>` and `<user>` in the output will match that in the input.

- **Login a User**:
  ```
  curl -X POST http://localhost:8000/login -H "content-type: application/x-www-form-urlencoded" -d 'username=<user>&password=<password>'
  ```
  This is meant to be run by any registered user. Unregistered users will see an authorization error if they exercise this function.   
  `<user>` and `<password>` must match what was used in the earlier registration step.   
  *Output JSON*:
  ```
  { "token":<token-value> }
  ```
  `<token-value>` will be a long string like the following (example): `eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJ0b20iLCJleHAiOjE1ODcwMDU4ODEsImlhdCI6MTU4Njk1MTg4MX0.DE1odMcZpiIsy57n9ot8Ki3QGOrpMIrp97UB14ZCyu04msdLLNO7cAZpHf3ZhHkCl2QbKrWBKiHwbpkNkN5lpA`.

- **Accept a Trade**:
  ```
  curl -X POST http://localhost:8000/acceptTrade -H "content-type: application/x-www-form-urlencoded" -H "authorization: Bearer <token-value>" -d 'tradeId=<trade-id>'
  ```
  Replace `<token-value>` with the output of the `login` request.   
  Only a user with the `client` role may perform this function.   
  `<trade-id>` can be any string.   
  *Output JSON*:
  ```
  { "result": true, "payload": "" }
  ```

- **Request an Export License**:
  ```
  curl -X POST http://localhost:8000/requestEL -H "content-type: application/x-www-form-urlencoded" -H "authorization: Bearer <token-value>" -d 'tradeId=<trade-id>'
  ```
  Replace `<token-value>` with the output of the `login` request.   
  Only a user with the `client` role may perform this function.   
  `<trade-id>` can be any string.   
  *Output JSON*:
  ```
  { "result": true, "payload": "" }
  ```

- **Prepare a Shipment**:
  ```
  curl -X POST http://localhost:8000/prepareShipment -H "content-type: application/x-www-form-urlencoded" -H "authorization: Bearer <token-value>" -d 'tradeId=<trade-id>'
  ```
  Replace `<token-value>` with the output of the `login` request.   
  Only a user with the `client` role may perform this function.   
  `<trade-id>` can be any string.   
  *Output JSON*:
  ```
  { "result": true, "payload": "" }
  ```

- **Get Trade Info**:
  ```
  curl -X GET http://localhost:8000/getTrade?tradeId=<trade-id> -H "content-type: application/x-www-form-urlencoded" -H "authorization: Bearer <token-value>"
  ```
  Replace `<token-value>` with the output of the `login` request.   
  Only a user with the `client` role may perform this function.   
  `<trade-id>` can be any string.   
  *Output JSON*:
  ```
  { "result": true, "payload": <trade-info> }
  ```
  `<trade-info>` is a JSON string containg details of the trade.

- **Get Trade Status**:
  ```
  curl -X GET http://localhost:8000/getTradeStatus?tradeId=<trade-id> -H "content-type: application/x-www-form-urlencoded" -H "authorization: Bearer <token-value>"
  ```
  Replace `<token-value>` with the output of the `login` request.   
  Only a user with the `client` role may perform this function.   
  `<trade-id>` can be any string.   
  *Output JSON*:
  ```
  { "result": true, "payload": <trade-status> }
  ```
  `<trade-status>` is a JSON string with the following format: `{ "Status": <status> }`. `<status>` can be `REQUESTED` or `ACCEPTED`.

- **Get Letter Of Credit Info**:
  ```
  curl -X GET http://localhost:8000/getLC?tradeId=<trade-id> -H "content-type: application/x-www-form-urlencoded" -H "authorization: Bearer <token-value>"
  ```
  Replace `<token-value>` with the output of the `login` request.   
  Only a user with the `client` role may perform this function.   
  `<trade-id>` can be any string.   
  *Output JSON*:
  ```
  { "result": true, "payload": <letter-of-credit-info> }
  ```
  `<letter-of-credit-info>` is a JSON string containg details of the letter of credit.

- **Get Letter of Credit Status**:
  ```
  curl -X GET http://localhost:8000/getLCStatus?tradeId=<trade-id> -H "content-type: application/x-www-form-urlencoded" -H "authorization: Bearer <token-value>"
  ```
  Replace `<token-value>` with the output of the `login` request.   
  Only a user with the `client` role may perform this function.   
  `<trade-id>` can be any string.   
  *Output JSON*:
  ```
  { "result": true, "payload": <letter-of-credit-status> }
  ```
  `<letter-of-credit-status>` is a JSON string with the following format: `{ "Status": <status> }`. `<status>` can be `REQUESTED`, `ISSUED`, or `ACCEPTED`.

- **Get Export License Info**:
  ```
  curl -X GET http://localhost:8000/getEL?tradeId=<trade-id> -H "content-type: application/x-www-form-urlencoded" -H "authorization: Bearer <token-value>"
  ```
  Replace `<token-value>` with the output of the `login` request.   
  Only a user with the `client` role may perform this function.   
  `<trade-id>` can be any string.   
  *Output JSON*:
  ```
  { "result": true, "payload": <export-license-info> }
  ```
  `<export-license-info>` is a JSON string containg details of the export license.

- **Get Export License Status**:
  ```
  curl -X GET http://localhost:8000/getELStatus?tradeId=<trade-id> -H "content-type: application/x-www-form-urlencoded" -H "authorization: Bearer <token-value>"
  ```
  Replace `<token-value>` with the output of the `login` request.   
  Only a user with the `client` role may perform this function.   
  `<trade-id>` can be any string.   
  *Output JSON*:
  ```
  { "result": true, "payload": <export-license-status> }
  ```
  `<export-license-status>` is a JSON string with the following format: `{ "Status": <status> }`. `<status>` can be `REQUESTED` or `ISSUED`.

- **Get Shipment Location Info**:
  ```
  curl -X GET http://localhost:8000/getShipmentLocation?tradeId=<trade-id> -H "content-type: application/x-www-form-urlencoded" -H "authorization: Bearer <token-value>"
  ```
  Replace `<token-value>` with the output of the `login` request.   
  Only a user with the `client` role may perform this function.   
  `<trade-id>` can be any string.   
  *Output JSON*:
  ```
  { "result": true, "payload": <shipment-location-info> }
  ```
  `<shipment-location-info>` is a JSON string with the following format: `{ "Location": <location> }`. `<location>` is a string.

- **Get Bill of Lading Info**:
  ```
  curl -X GET http://localhost:8000/getBillOfLading?tradeId=<trade-id> -H "content-type: application/x-www-form-urlencoded" -H "authorization: Bearer <token-value>"
  ```
  Replace `<token-value>` with the output of the `login` request.   
  Only a user with the `client` role may perform this function.   
  `<trade-id>` can be any string.   
  *Output JSON*:
  ```
  { "result": true, "payload": <bill-of-lading-info> }
  ```
  `<bill-of-lading-info>` is a JSON string containg details of the B/L.

- **Get Account Balance**:
  ```
  curl -X GET http://localhost:8000/getAccountBalance -H "content-type: application/x-www-form-urlencoded" -H "authorization: Bearer <token-value>"
  ```
  Replace `<token-value>` with the output of the `login` request.   
  Only a user with the `client` role may perform this function.   
  *Output JSON*:
  ```
  { "result": true, "payload": <account-balance-info> }
  ```
  `<account-balance-info>` is a JSON string with the following format: `{ "Balance": <balance> }`. `<balance>` is a string representation of a number.

- **List Trades**:
  ```
  curl -X GET http://localhost:8000/listTrades -H "content-type: application/x-www-form-urlencoded" -H "authorization: Bearer <token-value>"
  ```
  Replace `<token-value>` with the output of the `login` request.   
  Only a user with the `client` role may perform this function.   
  *Output JSON*:
  ```
  { "result": true, "payload": [<trade-info>] }
  ```
  `[<trade-info>]` is an array of JSON strings, each string (`<trade-info>`) containing details of a trade the user has been party to.
