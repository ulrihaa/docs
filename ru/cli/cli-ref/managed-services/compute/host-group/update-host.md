---
editable: false
sourcePath: en/_cli-ref/cli-ref/managed-services/compute/host-group/update-host.md
---

# yc compute host-group update-host

Update host of the specified host group

#### Command Usage

Syntax: 

`yc compute host-group update-host <HOST-GROUP-NAME>|<HOST-GROUP-ID> --host-id <HOST-ID> [Flags...] [Global Flags...]`

#### Flags

| Flag | Description |
|----|----|
|`--id`|<b>`string`</b><br/>Host group id.|
|`--name`|<b>`string`</b><br/>Host group name.|
|`--async`|Display information about the operation in progress, without waiting for the operation to complete.|
|`--host-id`|<b>`string`</b><br/>Host id to update.|
|`--replacement-deadline`|<b>`timestamp`</b><br/>New replacement deadline.|

#### Global Flags

| Flag | Description |
|----|----|
|`--profile`|<b>`string`</b><br/>Set the custom configuration file.|
|`--debug`|Debug logging.|
|`--debug-grpc`|Debug gRPC logging. Very verbose, used for debugging connection problems.|
|`--no-user-output`|Disable printing user intended output to stderr.|
|`--retry`|<b>`int`</b><br/>Enable gRPC retries. By default, retries are enabled with maximum 5 attempts.<br/>Pass 0 to disable retries. Pass any negative value for infinite retries.<br/>Even infinite retries are capped with 2 minutes timeout.|
|`--cloud-id`|<b>`string`</b><br/>Set the ID of the cloud to use.|
|`--folder-id`|<b>`string`</b><br/>Set the ID of the folder to use.|
|`--folder-name`|<b>`string`</b><br/>Set the name of the folder to use (will be resolved to id).|
|`--endpoint`|<b>`string`</b><br/>Set the Cloud API endpoint (host:port).|
|`--token`|<b>`string`</b><br/>Set the OAuth token to use.|
|`--impersonate-service-account-id`|<b>`string`</b><br/>Set the ID of the service account to impersonate.|
|`--format`|<b>`string`</b><br/>Set the output format: text (default), yaml, json, json-rest.|
|`-h`,`--help`|Display help for the command.|
