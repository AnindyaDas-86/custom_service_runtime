# Unit Testing Report for CSBS Service

## Environment Details
- **Tenant URL**: https://service7-staging.cxm-salescloud.com
- **User**: `admin`
- **Password**: `North$tar1`

---

## Communication System Details
- **App URL**:  
  [https://salesservicecloudextensibility-external-func-services-d77d2ede2.cfapps.eu10.hana.ondemand.com/project-order-service/ProjectOrder](https://salesservicecloudextensibility-external-func-services-d77d2ede2.cfapps.eu10.hana.ondemand.com/project-order-service/ProjectOrder)
- **Token URL**:  
  [https://external-func-services.authentication.eu10.hana.ondemand.com/oauth/token](https://external-func-services.authentication.eu10.hana.ondemand.com/oauth/token)
- **Client ID**: `sb-customservice-SalesServiceCloudExtensibility_external-func-services-dev!t173918`
- **Secret**: `bk3Kl42CbY55Gh9usj8xSUbrJ3k=`

---

## Test Scenarios and Results

### 1. Design-Time Configurations

#### Communication System
**created from DC admin UI**:
```json
{
    "count": 1,
    "value": [
        {
            "id": "ad67cfb4-a49e-4485-8282-79f30e9c0b30",
            "displayId": "projectOrderCAPsvc",
            "description": "testing",
            "status": "ACTIVE",
            "inUse": true,
            "outboundCommunicationSystem": {
                "hostname": "salesservicecloudextensibility-external-func-services-d77d2ede2.cfapps.eu10.hana.ondemand.com",
                "protocol": "https",
                "authenticationPreference": [
                    {
                        "type": "OAUTH_2_CLIENT_CREDENTIALS",
                        "credentialId": "30f85534-40bc-4f23-9416-8ffe3d4d7e20"
                    }
                ]
            },
            "adminData": {
                "updatedBy": "a601eb3f-e53d-11e9-b3ea-43da9e805f15",
                "updatedOn": "2025-01-24T05:33:01.631Z",
                "createdBy": "a601eb3f-e53d-11e9-b3ea-43da9e805f15",
                "createdOn": "2025-01-24T05:33:01.631Z"
            }
        }
    ]
}
```
Communication Configurations

Request:
POST: https://service7-staging.cxm-salescloud.com/sap/c4c/api/v1/data-connector-service/customHTTPDataOutbound
```json
{
    "description": "Project Order CAP Communication Configuration",
    "communicationSystem": "projectOrderCAPsvc",
    "outboundConfigurations": [
        {
            "description": "project order",
            "serviceFullName": "customer.ssc.service.ProjectOrderService",
            "apiPath": "/project-order-service/ProjectOrder",
            "httpMethods": ["POST", "GET"],
            "accessScope": {
                "type": "PUBLIC",
                "value": "CUSTOM"
            }
        },
        {
            "description": "GetProjectID",
            "serviceFullName": "customer.ssc.service.ProjectOrderService",
            "apiPath": "/project-order-service/ProjectOrder/{{projectId}}",
            "httpMethods": ["POST", "GET", "PATCH", "DELETE"],
            "apiPathPlaceHolders": [
                {
                    "name": "{{projectId}}",
                    "isReadOnly": false
                }
            ],
            "accessScope": {
                "type": "PUBLIC",
                "value": "CUSTOM"
            }
        },
        {
            "description": "GetProducts",
            "serviceFullName": "customer.ssc.service.ProjectOrderService",
            "apiPath": "/project-order-service/ProjectOrder/{{projectId}}/products",
            "httpMethods": ["POST", "GET", "PATCH", "DELETE"],
            "apiPathPlaceHolders": [
                {
                    "name": "{{projectId}}",
                    "isReadOnly": false
                }
            ],
            "accessScope": {
                "type": "PUBLIC",
                "value": "CUSTOM"
            }
        }
    ],
    "accessScope": {
        "type": "PUBLIC",
        "value": "CUSTOM"
    },
    "status": "ACTIVE"
}
```
### 2. Runtime Scenarios

###GET All Records
- ***Endpoint***:
`/sap/c4c/api/v1/custom-service-bridge-service/connector/customer.ssc.service.ProjectOrderService/project-order-service/ProjectOrder`
- Status Code: 200
- Response Time: 901ms
```json
{
    "value": {
        "status": 200,
        "apiResponse": {
            "@odata.context": "$metadata#ProjectOrder",
            "value": [
                {
                    "@odata.etag": "W/\"2025-01-24T06:00:27.662Z\"",
                    "modifiedAt": "2025-01-24T06:00:27.662Z",
                    "id": "198f1d9e-cee7-48da-b62c-2a2e258fd5c5",
                    "displayId": "PO_11111111",
                    "status": "ACTIVE",
                    "startDate": "2025-01-01T05:43:00.000Z",
                    "endDate": "2025-01-31T05:43:00.000Z",
                    "estimatedRevenue": {
                        "currencyCode": "AED",
                        "content": 45
                    },
                    "account": {
                        "id": "00163e03-63a4-1ed2-8783-4e1664590600",
                        "displayId": "MDEC90099",
                        "formattedName": "AHT Japan"
                    }
                }
            ]
        }
    }
}
```
## GET with Query Parameters
| **Parameter**       | **Example Query**                     | **Status Code** | **Response Time** | **Notes**                                              |
|---------------------|----------------------------------------|-----------------|-------------------|--------------------------------------------------------|
| `$count`            | `?$count=true`                        | `200`           | `334ms`           | Success                                                |
| `$top`              | `?$count=true&$top=2`                 | `200`           | `493ms`           | Success                                                |
| `$skip`             | `?$count=true&$top=2&$skip=1`         | `200`           | `499ms`           | Success                                                |
| `$filter (status)`  | `?$filter=status eq 'ACTIVE'`         | `400`           | `313ms`           | ⚠️ **Error**: "The type 'Edm.String' is not compatible to 'Edm.Boolean'" |
| `$filter (date)`    | `?$filter=endDate eq '2024-12-24'`    | `400`           | `313ms`           | ⚠️ **Error**: "The type 'Edm.String' is not compatible to 'Edm.Boolean'"|

---

### Create Operation
- **Endpoint**:  
  `/sap/c4c/api/v1/custom-service-bridge-service/connector/customer.ssc.service.ProjectOrderService/project-order-service/ProjectOrder`
- **Status Code**: `400`
- **Error Message**:  ⚠️ **Error**: "Invalid value \"undefined\" in content-type header."

**Payload Example**:
```json
{
    "Name": "testAnindyaDC",
    "Customer": "11efb128-7b71-d87e-afdb-81cf49a8c000",
    "startDate": "2025-01-01T06:52:00.000Z",
    "endDate": "2025-01-31T06:52:00.000Z",
    "estimatedRevenue": {
        "content": 100,
        "currencyCode": "DKK"
    },
    "status": "ACTIVE"
}
```
## GET by ID
- **Endpoint**:
`/sap/c4c/api/v1/custom-service-bridge-service/connector/customer.ssc.service.ProjectOrderService/project-order-service/ProjectOrder/{id}`
	•	Status Code: 200
	•	Response Time: 326ms

Response Payload:
```json
{
    "value": {
        "status": 200,
        "apiResponse": {
            "@odata.context": "$metadata#ProjectOrder/$entity",
            "id": "198f1d9e-cee7-48da-b62c-2a2e258fd5c5",
            "displayId": "PO_11111111",
            "status": "ACTIVE",
            "startDate": "2025-01-01T05:43:00.000Z",
            "endDate": "2025-01-31T05:43:00.000Z",
            "estimatedRevenue": {
                "currencyCode": "AED",
                "content": 45
            },
            "account": {
                "id": "00163e03-63a4-1ed2-8783-4e1664590600",
                "displayId": "MDEC90099",
                "formattedName": "AHT Japan"
            }
        }
    }
}
```

## DELETE Operation
- **Endpoint**:
`/sap/c4c/api/v1/custom-service-bridge-service/connector/customer.ssc.service.ProjectOrderService/project-order-service/ProjectOrder/{id}`
	•	Status Code: 428
	•	Error Message: "Precondition required"


## Summary of Erroneous URLs

| **URL**                                                                                       | **Issue**                                                                                     |
|-----------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------|
| `/sap/c4c/api/v1/.../ProjectOrder?$filter=status eq 'ACTIVE'`                                 | "The type 'Edm.String' is not compatible to 'Edm.Boolean'"                                    |
| `/sap/c4c/api/v1/.../ProjectOrder?$filter=endDate eq '2024-12-24'`                            | "The type 'Edm.String' is not compatible to 'Edm.Boolean'"                                    |
| `/sap/c4c/api/v1/.../ProjectOrder` (POST)                                                    | "Invalid value \"undefined\" in content-type header."                                         |
| `/sap/c4c/api/v1/.../ProjectOrder/{id}` (DELETE)                                             | "Precondition required" (ETag/If-Match missing).                                              |

---

## Conclusion

- **Successful Tests**: Basic GET operations and query parameter usage (`$count`, `$top`, `$skip`) worked as expected.
- **Issues Identified**:  
  1. `$filter` queries (`status`, `endDate`) failed due to type compatibility issues.  
  2. POST operations failed 
  3. DELETE operations require ETag/If-Match headers. Support of etag/if-match to be provided by data connector service.
