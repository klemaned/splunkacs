# splunkacs
A Command for managing the Splunk Cloud Admin Config Service (ACS).

Functionality is confirmed for the [Victoria Experience](https://docs.splunk.com/Documentation/SplunkCloud/9.0.2209/Admin/Experience) only.

## Getting Started
- Make sure Python3 is installed and clone the repository.
- Running `./splunkacs help` will return a list of command options.
- Running `./splunkacs [command] help` will return the options for that command.

### Initial Setup
The first time you run `splunkacs` it will ask you for your Splunk Cloud Stack name and Authentication Token.

Your Splunk Cloud Stack name is the hostname of your Splunk Cloud URL. In the URL example.splunkcloud.com, ***example*** is the stack name. 

For the Authentication Token, using the Splunk Cloud web portal, you will need to create one for a local Splunk account that has the 'sc_admin' role. For more information on setting up token authentication go [here](https://docs.splunk.com/Documentation/SplunkCloud/9.0.2209/Config/ACSusage#Create_an_authentication_token).

# Commands
Functionality yet to be included:
- [ ] View maintenance windows
- [ ] Splunk restarts
- [ ] Outbound Ports

Functionality of `splunkacs` is focused on the activities that you cannot perform within the web portal such as IP Access Lists, but some functionality is included for the purposes of testing and convenience. Below is a list of available commands.

## ACL
The `ACL` command allows an admin to view and manage the inboud IP access lists of the various Splunk Cloud features.

```
$ ./splunkacs acl help

Command: acl
Description: Manage Splunk Cloud IP access-lists

Format:
splunkacs acl [feature] (show|add|remove) <cidr network>

Splunk ACL Features:
idm-api, idm-ui, hec, s2s, search-api, search-ui
```

### Example Uses:

Show current acl for search-api
```
$ ./splunkacs acl search-api show

{
  "subnets": [
    "8.8.8.8/32"
  ]
}
```
#### Add network to to search-ip acl
```
$ ./splunkacs acl search-api add 1.1.1.1/32

{
  "warnings": [
    "IP allow list subnets creation request submitted successfully. Note that it can take several minutes for the subnet update to be applied to your Splunk Cloud Platform stack."
  ]
}
```
> Note that the "Warning" is more informational than it is an indicator of something wrong.

#### Remove network from search-ip acl
```
$ ./splunkacs acl search-api remove 1.1.1.1/32

{
  "warnings": [
    "IP allow list subnets creation request submitted successfully. Note that it can take several minutes for the subnet update to be applied to your Splunk Cloud Platform stack."
  ]
}
```
> Again, the warning is informational

## Limits
The `Limits` command allows an admin to view and modify the limits settings of a Splunk Cloud environment.

```
$ ./splunkacs limits help

Command: limits
Description: Manage the limtis.conf settings of Splunk Cloud

Format:
splunkacs limits (defaults|set|show) <stanza> <setting> <value>

defaults        Displays default limits.conf settings
set             Modify the Splunk Cloud limits. Requires a Stanza, Setting, and Value
show            Displays the current limits.conf settings
```

> If you provide a stanza when using the `defaults` or `show` option, only the details for that staza will be displayed. Omission of a stanza value will display the details for all stanzas.

### Example Uses:
#### Show the default configuration for the pdf stanza
```
$ ./splunkacs limits defaults pdf

{
  "settings": [
    {
      "defaultValue": 1000,
      "maxValue": 5000,
      "minValue": 500,
      "setting": "max_rows_per_table"
    }
  ],
  "stanza": "pdf"
}
```
> In this example, the pdf stanza only has the `max_rows_per_table` setting aviailable. The the setting has the default value of 1000, a max value of 5000, and a minimum value of 500.

#### Show the current configuration for the pdf stanza
```
$ ./splunkacs limits show pdf

{
  "max_rows_per_table": "1000"
}
```

#### Set pdf max_rows_per_table to 2000
> Note that it can take up to a minute for the changes to reflect in the `show` command.
```
$ ./splunkacs limits set pdf max_rows_per_table 2000

{
  "settings": {
    "max_rows_per_table": 2000
  }
}
```


> All input validation is handled by Splunk's api. For example, if we try to set the pdf max_rows_per_table to 6000, we receive the following error.
```
{
  "code": "403-forbidden",
  "message": "request value 6000.000000 outside of bounds min: 500.000000, max: 5000.000000. Please refer https://docs.splunk.com/Documentation/SplunkCloud/latest/Config/ACSerrormessages for general troubleshooting tips."
}
```
A list of available Stanzas and Settings can be found [here](https://docs.splunk.com/Documentation/SplunkCloud/9.0.2209/Config/ManageLimits)

## Maintenance
The `maintenance` command displays previous and upcoming maintenace for a given period of time.
> If no "From Date" is provided, the command will default to a window of +-30 days. If a "From Date" is provided but no "To Date" is given, the "To Date" will be set to today's date.

```
$ ./splunkacs maintenance help

Command: maintenance
Description: View previous and upcoming maintance windows

Format:
splunkacs maintenance (show) <From Date> <To Date>

Date Format: YYYY-MM-DD
```

### Example Uses:
#### Show Maintenance during default +-30 day window
```
$ ./splunkacs maintenance show

{
  "nextLink": "",
  "schedules": []
}
```
> There are no scheduled maintenance for the default window


#### Show Maintenance from 2023-01-01 to 2023-04-10 
```
$ ./splunkacs maintenance show 2023-01-01 2023-04-10

{
  "nextLink": "",
  "schedules": [
    {
      "duration": "3h0m0s",
      "lastModifiedTimestamp": "2023-03-11T02:07:54.485060828Z",
      "lastSummary": "",
      "mwType": "Service Update",
      "operations": [
        {
          "operationDescription": "Splunk version upgrade to latest/stable release",
          "operationStatus": "Success",
          "operationType": "Splunk Upgrade",
          "targetVersion": "9.0.2209.4"
        },
        {
          "SFDCTickets": [
            "1"
          ],
          "notes": [
            "8"
          ],
          "operationDescription": "Rightsize Stack",
          "operationStatus": "Success",
          "operationType": "Repaver",
          "targetVersion": "m6i.4xlarge"
        }
      ],
      "requestedEntity": "Splunk",
      "scheduleId": "56fb3ca2-88c5-4f4e-8b7e-45821a38a2ce",
      "scheduleStartTimestamp": "2023-03-10T04:00:00Z",
      "status": "Completed"
    }
  ]
}
```


## Tokens
The `tokens` command allows an admin to view all configured authentication tokens. The command does not enable an admin to view the actual tokens, only the tokens properties.
> Creating and Deleting tokens is not currently supported by splunkacs. Use the Splunk web portal for token management.
```
$ ./splunkacs tokens help

Command: tokens
Description: View active Splunk Cloud Authentication Tokens

Format:
splunkacs tokens (show)
```
### Example Uses:
#### Show currently configured authentication tokens
```
$ ./splunkacs tokens show

[
  {
    "id": "[fe5e55d13a1a2502316f37a0ca435273ceb61b392ed28ae52a486064ce36c266]",
    "user": "api_test",
    "audience": "api testing",
    "status": "enabled",
    "expiresOn": "2023-02-02T14:03:17Z",
    "notBefore": "2023-01-03T14:03:17Z",
    "lastUsed": "2023-01-27T14:12:43Z",
    "lastUsedIP": "[redated]"
  }
]
```

# Commands not related to the API
## Configure
The `configure` command will prompt for the Stack name and Authentication Token just as it did on the first run. If the settings allow for a successful authentication to the Splunk Cloud ACS API, then the configuration will be saved to `settings.config` and the previous configuration will be saved to `settings.config.old`
```
$ ./splunkacs configure

Please provide the following items to begin using splunkacs:

Splunk Stack Name (https://[stack].splunkcloud.com): example 
Splunk Authentication Token: notmytoken

Validating connection...

Failed to verify connection. Configuration NOT saved
```
## Settings
The `settings` command will display the current configuration
```
$ ./splunkacs settings

Current Settings:

Splunk Stack : [redacted]
Bearer Token : [redacted]
```











