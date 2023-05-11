---
title: Interact with Greenfield
order: 4
---

# Interact with Greenfield

With tBNBs on Greenfield Testnet, you can interact with Greenfield, and start your decentralized data journey.

## DCellar

[DCellar](http://dcellar.io) is a file management tool and an ultimate client for Greenfield, which is developed by
our community member [Nodereal](https://nodereal.io/). With DCellar, you can store and manage your files in a
decentralized way, fully control your data, and even make profits out of them. Meanwhile, you can also use DCellar to
manage your account, balance, and billings.

## Greenfield Command

[Greenfield Command](https://github.com/bnb-chain/greenfield-cmd) is a powerful command line to interact with Greenfield,
by which you can manage your resources on Greenfield. 

### Command Tool Guide

This command tool supports basic storage functions, including creating buckets, uploading and downloading files, and deleting resources, 
and it also supports related operations such as groups, permissions, banks, and cross-chain. To make the command display clearer, commands of different categories are implemented as subcommands of different categories.
You can use "gnfd-cmd -h" to view the supported command categories.

The command should run with "-c filePath" to load the config file and the config should be toml format(default:"config.toml").
The configuration file include three parts of information, including rpcAddr, chainId, and passwordFile.

Below is an example of config file, where the rpcAddr and chainId should be consistent with the Greenfield network, for Greenfield Testnet, 
you can refer to  [Greenfield Testnet RPC Endpoints](https://greenfield.bnbchain.org/docs/guide/resources.html#rpc-endpoints). The rpcAddr indicates the Tendermint RPC address with the port info.
The configuration for passwordFile is the path to the file containing the password required to generate the keystore or parse the keystore. The content of the keystore is the encrypted private key information and the passwordFile is used for encrypting/decrypting the private key.
```
rpcAddr = "https://gnfd-testnet-fullnode-tendermint-us.bnbchain.org:443"
chainId = "greenfield_5600-1"
passwordFile = "password.txt"
```

The command has ability to intelligently select the correct Storage provider to respond to the request. The User only need to set the storage provider operator-address
if they want to create a bucket on a specific SP. For example, the user can run "gnfd-cmd storage put test gnfd://bucket1/object1" to upload file to bucket1 and then run "gnfd-cmd storage put test gnfd://bucket2/object" to upload file to bucket2 which is stored on another SP without changing the config.

#### Generate the keystore file

Before using the rich features of the command tool, you need to generate a keystore file by following the steps below.

1) Export your private key from Metamask, and write it into a file in local as plaintext.

2) Set your password. It should be set by the "passwordFile" field in config file. 

3) Generate keystore by the "gen-key" command. The "-privKeyFile" flag is used to set the private key file path which is created by step1. 
The following command can be used to generate a keystore file called key.json:
```
gnfd-cmd gen-key -privKeyFile key.txt  key.json
```

4) Delete set the private key file which is created in step1, it is not needed after key store has generated.

 After the keystore file is generated, other commands need to be run with the addition of "-k keystore-path".
The default keystore file is "key.json". When executing commands with "-k keystore-file",
 the private key content will be fetched from keystore in a safe way.

#### List Storage providers

Before making a bucket and uploading files, we need to select a storage provider to store the files in the bucket. 
By executing the following command, we can obtain a list of storage providers on Greenfield.

```
gnfd-cmd ls-sp
```
You can take note of the operator-address information for the storage provider which is intended to be uploaded to. 
This parameter will be required for making the bucket in the next step.

#### Make Bucket

You can run "./gnfd-cmd storage -h " to get the help of the storage operations.

The below command can be used to create a new bucket called testBucket
```
gnfd-cmd storage make-bucket  gnfd://testBucket
```
The command support "-primarySP" flag to select the storage provider on which you what to create bucket. 
The content of the flag should be the operator-address of the storage provider.
If this value is not set, the first SP in the storage provider list will be selected as the upload target by default.

The user can update the bucket meta buy the "storage update-buckets" command.
It supports updating bucket visibility, charged quota or payment address
```
// update bucket charged quota 
gnfd-cmd storage update-bucket --chargedQuota 50000 gnfd://testBucket
```

#### Upload/Download Files

(1) put Object

The user can upload the local file to bucket by the "storage put" command.
The following command example uploads an object named 'testObject' to the 'testBucket' bucket.
The file payload for the upload is read from the local file indicated by 'file-path'.
```
gnfd-cmd storage put --contentType "text/xml"  file-path  gnfd://testBucket/testObject
```
After the command is executed, it will send createObject txn to chain and uploads the payload of object to the storage provider.
The command will return the uploading info after the objects has been sealed.

(2) download object

The user can download the object into the local file by the "storage get" command. The following command example 
downloads 'testObject' from 'testBucket' to the local 'file-path' and prints the length of the downloaded file.

```
gnfd-cmd storage get gnfd://testBucket/testObject file-path 
```
After the command is executed, it will send download request to the storage provider.

#### Group 

Users can run "./gnfd-cmd group -h " to get the help of group operations.

The user can create a new group by the "group make-group" command. 
Note that this command can set the initialized group member through the --initMembers parameter.
After command execute successfully, the group ID and transaction hash information will be returned.

You can add or remove members from a group using the "update-group" command. 
The user can use'--addMembers' to specify the addresses of the members to be added or '--removeMembers' to specify the addresses of the members to be removed.
```
// create 
gnfd-cmd group make-group gnfd://testGroup
// update
gnfd-cmd group update-group --groupOwner 0x.. --addMembers 0x.. gnfd://testGroup
```

#### Permission 
Users can run "./gnfd-cmd permission -h " to get the help of permission operations.

Users can use the "put-obj-policy" command to assign object permissions to other account or groups(called principal), 
such as the permission to delete objects.
After command execute successfully, the object policy information of the principal will be returned.
The principal is set by --groupId which indicates the group or --granter which indicates the account.
```
gnfd-cmd permission put-obj-policy --groupId  --actions get,delete  gnfd://testBucket/testObject
```

Users can use the 'permission put-bucket-policy' command to assign bucket permissions to other account or groups, 
such as the permission to update the bucket.
After command execute successfully, the bucket policy information of principal will be returned.

```
gnfd-cmd permission put-bucket-policy --granter --actions delete  gnfd://testBucket
```

In addition to the basic commands mentioned above, the Greenfield Command also supports functions such as transferring token and cross-chain operations. You can find more examples in the readme file.

## SDK

If you are a developer, you can build your projects and interact with Greenfield base on SDK, here are several
resources you can refer to:
1. [Greenfield Go SDK](https://github.com/bnb-chain/greenfield-go-sdk)
2. [Greenfield JS SDK](https://github.com/bnb-chain/greenfield-js-sdk)
3. [Build DApps](./dapp)
