# Schema Change History

## Landing Zone Schemas

### April 21, 2022

* Schema definition update for Machine Learning & Healthcare archetypes.  Expanded the spoke network subnet configuration to contain 0 or more optional subnets.  This change enables network configuration to be more flexible.

  * Machine Learning archetype network configuration with optional subnets

    <details>
      <summary>Expand/collapse</summary>

      ```json
          "network": {
            "value": {
              "peerToHubVirtualNetwork": true,
              "useRemoteGateway": false,
              "name": "azmlsqlauth2022Q1vnet",
              "dnsServers": [
                "10.18.1.4"
              ],
              "addressPrefixes": [
                "10.6.0.0/16"
              ],
              "subnets": {
                "sqlmi": {
                  "comments": "SQL Managed Instances Delegated Subnet",
                  "name": "sqlmi",
                  "addressPrefix": "10.6.5.0/25"
                },
                "databricksPublic": {
                  "comments": "Databricks Public Delegated Subnet",
                  "name": "databrickspublic",
                  "addressPrefix": "10.6.6.0/25"
                },
                "databricksPrivate": {
                  "comments": "Databricks Private Delegated Subnet",
                  "name": "databricksprivate",
                  "addressPrefix": "10.6.7.0/25"
                },
                "privateEndpoints": {
                  "comments": "Private Endpoints Subnet",
                  "name": "privateendpoints",
                  "addressPrefix": "10.6.8.0/25"
                },
                "aks": {
                  "comments": "AKS Subnet",
                  "name": "aks",
                  "addressPrefix": "10.6.9.0/25"
                },
                "appService": {
                  "comments": "App Service Subnet",
                  "name": "appService",
                  "addressPrefix": "10.6.10.0/25"
                },
                "optional": [
                  {
                    "comments": "Optional Subnet 1",
                    "name": "virtualMachines",
                    "addressPrefix": "10.6.11.0/25",
                    "nsg": {
                      "enabled": true
                    },
                    "udr": {
                      "enabled": true
                    }
                  },
                  {
                    "comments": "Optional Subnet 2 with delegation for NetApp Volumes",
                    "name": "NetappVolumes",
                    "addressPrefix": "10.6.12.0/25",
                    "nsg": {
                      "enabled": false
                    },
                    "udr": {
                      "enabled": false
                    },
                    "delegations": {
                        "serviceName": "Microsoft.NetApp/volumes"
                    }
                  }
                ]
              }
            }
          }
      ```

    </details>

  * Healthcare archetype network configuration with optional subnets

    <details>
      <summary>Expand/collapse</summary>

      ```json
          "network": {
              "value": {
                  "peerToHubVirtualNetwork": true,
                  "useRemoteGateway": false,
                  "name": "health2022Q1vnet",
                  "dnsServers": [
                      "10.18.1.4"
                  ],
                  "addressPrefixes": [
                      "10.5.0.0/16"
                  ],
                  "subnets": {
                      "databricksPublic": {
                          "comments": "Databricks Public Delegated Subnet",
                          "name": "databrickspublic",
                          "addressPrefix": "10.5.5.0/25"
                      },
                      "databricksPrivate": {
                          "comments": "Databricks Private Delegated Subnet",
                          "name": "databricksprivate",
                          "addressPrefix": "10.5.6.0/25"
                      },
                      "privateEndpoints": {
                          "comments": "Private Endpoints Subnet",
                          "name": "privateendpoints",
                          "addressPrefix": "10.5.7.0/25"
                      },
                      "web": {
                          "comments": "Azure Web App Delegated Subnet",
                          "name": "webapp",
                          "addressPrefix": "10.5.8.0/25"
                      },
                      "optional": [
                          {
                              "comments": "Optional Subnet 1",
                              "name": "virtualMachines",
                              "addressPrefix": "10.5.9.0/25",
                              "nsg": {
                              "enabled": true
                              },
                              "udr": {
                              "enabled": true
                              }
                          },
                          {
                              "comments": "Optional Subnet 2 with delegation for NetApp Volumes",
                              "name": "NetappVolumes",
                              "addressPrefix": "10.5.10.0/25",
                              "nsg": {
                              "enabled": false
                              },
                              "udr": {
                              "enabled": false
                              },
                              "delegations": {
                                  "serviceName": "Microsoft.NetApp/volumes"
                              }
                          }
                      ]
                  }
              }
          }
      ```

    </details>

### April 20, 2022

* Schema definition update for Generic Subscription.  Spoke network's subnet configuration is now defined as an array.  The array can have 0 to many subnet definitions.

* Removed 4 subnets from Machine Learning archetype's virtual network: `oz`, `paz`, `rz` and `hrz`.

* Removed 4 subnets from Healthcare archetype's virtual network: `oz`, `paz`, `rz` and `hrz`.

* Schema definition for Hub Networking archetypes (Azure Firewall & NVA).  See documentation:

  * [Hub Networking with Azure Firewall](../../docs/archetypes/hubnetwork-azfw.md)
  * [Hub Networking with Network Virtual Appliance (e.g. Fortigate Firewalls)](../../docs/archetypes/hubnetwork-nva-fortigate.md)

### April 18, 2022

Change in `synapse` schema object to support Azure AD authentication.

| Setting | Type | Description |
| ------- | ---- | ----------- |
| aadAuthenticationOnly | Boolean | Indicate that either AAD auth only or both AAD & SQL auth (required) |
| sqlAuthenticationUsername | String | The SQL authentication user name optional, required when `aadAuthenticationOnly` is false |
| aadLoginName | String | The name of the login or group in the format of first-name last-name |
| aadLoginObjectID | String | The object id of the Azure AD object whether it's a login or a group |
| aadLoginType | String | Represent the type of the object, it can be **User**, **Group** or **Application** (in case of service principal) |

**Examples**

SQL authentication only | Json (used in parameter files)

```json
"synapse": {
      "value": {
        "aadAuthenticationOnly": false,
        "sqlAuthenticationUsername": "azadmin"
      }
```
  
SQL authentication only | bicep (used when calling bicep module from another)
  
```bicep
{
  aadAuthenticationOnly: false 
  sqlAuthenticationUsername: 'azadmin'
}
```
  
Azure AD authentication only | Json (used in parameters files)
  
```json
   "synapse": {
      "value": {
        "aadAuthenticationOnly": true,
        "aadLoginName": "az.admins",
        "aadLoginObjectID": "e0357d81-55d8-44e9-9d9c-ab09dc710785",
        "aadLoginType":"Group"
      }
```

Azure AD authentication only | bicep (used when calling bicep module from another)
  
```bicep
{
  aadAuthenticationOnly: true 
  aadLoginName:'John Smith',
  aadLoginObjectID:'88888-888888-888888-888888',
  aadLoginType:'User'
}
```
  
Mixed authentication |  Json (used in parameters files)

```json
     "synapse": {
      "value": {
        "aadAuthenticationOnly": false,
        "sqlAuthenticationUsername": "azadmin",
        "aadLoginName": "az.admins",
        "aadLoginObjectID": "e0357d81-55d8-44e9-9d9c-ab09dc710785",
        "aadLoginType":"Group"
      }
 ```
  
  Mixed authentication | bicep (used when calling bicep module from another)
  
```bicep
  {
    aadAuthenticationOnly: false
    sqlAuthenticationUsername: 'azadmin' 
    aadLoginName:'John Smith',
    aadLoginObjectID:'88888-888888-888888-888888',
    aadLoginType:'User'
  }
```

### April 7, 2022

Schema definition for Logging archetype.  See [documentation](../../docs/archetypes/logging.md).

### April 6, 2022

Added `logAnalyticsWorkspaceResourceId` to archetypes.  This is an optional parameter in the JSON file as it can be set at runtime.

**Example**

```json
    "logAnalyticsWorkspaceResourceId": {
        "value": "LOG_ANALYTICS_WORKSPACE_RESOURCE_ID"
    }
```

### February 14, 2022

Added location schema object.  This is an optional setting for archetypes.  This setting will default to `deployment().location`.

**Example**

```json
    "location": {
        "value": "canadacentral"
    }
```

### January 16, 2021
Changed `appServiceLinuxContainer` schema object to support optional inbound private endpoint.

**Example**
```json
"appServiceLinuxContainer": {
  "value": {
    "enablePrivateEndpoint": true
  }
}
```

### December 30, 2021

Changed `aks` schema object to support optional deployment of AKS using the `enabled` key as a required field.

**Example**
```json
"aks": {
  "value": {
    "enabled": true
  }
}
```

Added `appServiceLinuxContainer` schema object to support optional deployment of App Service (for model deployments) using the `enabled` key as a required field. Sku name and tier are also required fields.

**Example**
```json
"appServiceLinuxContainer": {
  "value": {
    "enabled": true,
    "skuName": "P1V2",
    "skuTier": "Premium"
  }
}
```

Added required `appService` subnet as well as the `appServiceLinuxContainer` object in machine learning schema json file.


### November 27, 2021

Change in `aks` schema object to support Options for the creation of AKS Cluster with one of the following three scenarios:

* Network Plugin: Kubenet + Network Policy: Calico (Network Policy)
* Network Plugin: Azure CNI + Network Policy: Calico (Network Policy)
* Network Plugin: Azure CNI + Network Policy: Azure (Network Policy).

| Setting | Type | Description |
| ------- | ---- | ----------- |
| version | String | Kubernetes version to use for the AKS Cluster (required) |
| networkPlugin | String | Network Plugin to use: `kubenet` (for Kubenet) **or** `azure` (for Azure CNI) (required) |
| networkPolicy | String | Network Policy to use: `calico` (for Calico); which can be used with either **kubenet** or **Azure** Network Plugins **or** `azure` (for Azure NP); which can only be used with **Azure CNI**  |

**Note**

`podCidr` value shoud be set to ( **''** ) when Azure CNI is used

**Examples**

* Network Plugin: Kubenet + Network Policy: Calico (Network Policy)

```json
"aks": {
  "value": {
    "version": "1.21.2",
    "networkPlugin": "kubenet" ,
    "networkPolicy": "calico",
    "podCidr": "11.0.0.0/16",
    "serviceCidr": "20.0.0.0/16" ,
    "dnsServiceIP": "20.0.0.10",
    "dockerBridgeCidr": "30.0.0.1/16"
  }
}
```

* Network Plugin: Azure CNI + Network Policy: Calico (Network Policy)

```json
"aks": {
  "value": {
    "version": "1.21.2",
    "networkPlugin": "azure" ,
    "networkPolicy": "calico",
    "podCidr": "",
    "serviceCidr": "20.0.0.0/16" ,
    "dnsServiceIP": "20.0.0.10",
    "dockerBridgeCidr": "30.0.0.1/16"
  }
}
```

* Network Plugin: Azure CNI + Network Policy: Azure (Network Policy).

```json
"aks": {
  "value": {
    "version": "1.21.2",
    "networkPlugin": "azure" ,
    "networkPolicy": "azure",
    "podCidr": "",
    "serviceCidr": "20.0.0.0/16" ,
    "dnsServiceIP": "20.0.0.10",
    "dockerBridgeCidr": "30.0.0.1/16"
  }
}
```
### November 26, 2021

Added Azure Recovery Vault schema to enable the creation of a Recovery Vault in the generic Archtetype subscription
| Setting | Type | Description |
| ------- | ---- | ----------- |
| enabled | Boolean | Indicate whether or not to deploy Azure Recovery Vault (required) |
| name | String | The name of the Recovery Vault |


**Examples**

Enable recovery vault | Json (used in parameter files)
```json
    "backupRecoveryVault":{
            "value": {
                "enabled":true,
                "name":"bkupvault"
            }
        }
```

### November 25, 2021

* Remove `uuid` format check on `privateDnsManagedByHubSubscriptionId` for type `schemas/latest/landingzones/types/hubNetwork.json`

### November 23, 2021

Change in `sqldb` schema object to support Azure AD authentication.

| Setting | Type | Description |
| ------- | ---- | ----------- |
| enabled | Boolean | Indicate whether or not to deploy Azure SQL Database (required) |
| aadAuthenticationOnly | Boolean | Indicate that either AAD auth only or both AAD & SQL auth (required) |
| sqlAuthenticationUsername | String | The SQL authentication user name optional, required when `aadAuthenticationOnly` is false |
| aadLoginName | String | The name of the login or group in the format of first-name last-name |
| aadLoginObjectID | String | The object id of the Azure AD object whether it's a login or a group |
| aadLoginType | String | Represent the type of the object, it can be **User**, **Group** or **Application** (in case of service principal) |

**Examples**

SQL authentication only | Json (used in parameter files)

```json
"sqldb": {
  "value": {
    "aadAuthenticationOnly":false,
    "enabled": true,
    "sqlAuthenticationUsername": "azadmin"
  }
}
```
  
SQL authentication only | bicep (used when calling bicep module from another)
  
```bicep
{
  enabled: true
  aadAuthenticationOnly: false 
  sqlAuthenticationUsername: 'azadmin'
}
```
  
Azure AD authentication only | Json (used in parameters files)
  
```json
"sqldb": {
  "value": {
    "enabled":true,
    "aadAuthenticationOnly":true,
    "aadLoginName":"John Smith",
    "aadLoginObjectID":"88888-888888-888888-888888",
    "aadLoginType":"User"
  }
}
```

Azure AD authentication only | bicep (used when calling bicep module from another)
  
```bicep
{
  enabled: true
  aadAuthenticationOnly: true 
  aadLoginName:'John Smith',
  aadLoginObjectID:'88888-888888-888888-888888',
  aadLoginType:'User'
}
```
  
Mixed authentication |  Json (used in parameters files)

```json
  "sqldb": {
    "value": {
      "enabled":true,
      "aadAuthenticationOnly":false,
      "sqlAuthenticationUsername": "azadmin",
      "aadLoginName":"John Smith",
      "aadLoginObjectID":"88888-888888-888888-888888",
      "aadLoginType":"User"
    }
  }
 ```
  
  Mixed authentication | bicep (used when calling bicep module from another)
  
```bicep
  {
    enabled: true
    aadAuthenticationOnly: false
    sqlAuthenticationUsername: 'azadmin' 
    aadLoginName:'John Smith',
    aadLoginObjectID:'88888-888888-888888-888888',
    aadLoginType:'User'
  }
```

### November 12, 2021

* Initial version based on v0.1.0 of the schema definitions.
