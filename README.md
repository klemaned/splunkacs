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

# splunkacs Commands
Functionality of `splunkacs` is focused on the activities that you cannot perform within the web portal such as IP Access Lists, but some functionality is included for the purposes of testing and convenience. Below is a list of available commands.

## ACL
The ACL command allows an admin to view and manage the inboud IP access lists of the various Splunk Cloud features.

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
Add network to to search-ip acl
```
$ ./splunkacs acl search-api add 1.1.1.1/32

{
  "warnings": [
    "IP allow list subnets creation request submitted successfully. Note that it can take several minutes for the subnet update to be applied to your Splunk Cloud Platform stack."
  ]
}

# Note that the "Warning" is more informational than it is an indicator of something wrong.
```

Remove network from search-ip acl
```
$ ./splunkacs acl search-api remove 1.1.1.1/32

{
  "warnings": [
    "IP allow list subnets creation request submitted successfully. Note that it can take several minutes for the subnet update to be applied to your Splunk Cloud Platform stack."
  ]
}

# Again, the warning is informational
```




Splunk Reference: https://docs.splunk.com/Documentation/SplunkCloud/9.0.2209/Config/ConfigureIPAllowList





