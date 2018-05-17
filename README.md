# Network Security on AWS

## Prepare

```
export AWS_PROFILE=widdix-michael
export AWS_DEFAULT_REGION=eu-west-1
```

## Set up

```
$ aws cloudformation create-stack --stack-name netsec --template-body file://setup.yaml --capabilities CAPABILITY_IAM --parameters ParameterKey=DBMasterUserPassword,ParameterValue=test1234
```

## SSH to InstanceAPublic via BastionHost

```
$ ssh -J michael@$BastionHostIpAddressPublic $InstanceAPublicIpAddressPrivate
```

## SQL to Database from BastionHost

```
$ ssh michael@$BastionHostIpAddressPublic
$ nc -z $DatabaseDNSName 5432
Connection to $DatabaseDNSName 5432 port [tcp/postgres] succeeded!
```

## SQL to Database from InstanceAPublic

```
$ ssh -J michael@$BastionHostIpAddressPublic $InstanceAPublicIpAddressPrivate
$ sudo -i
# yum install postgresql96
# psql -h $DatabaseDNSName -U master -W -d netsec 
```

Not working? How can we fix it?

> Hint: Add DatabaseClient Security Group to InstanceA

## Talk from InstanceAPrivate to InstanceBPrivate

```
$ ssh -J michael@$BastionHostIpAddressPublic $InstanceAPrivateIpAddressPrivate
$ curl http://InstanceBPrivateIpAddressPrivate
```

How can you avoid that InstanceAPrivate can communicate with InstanceBPrivate?

> Hint: Add ACL rule to block traffic from subnets 10.0.16.0/20 and 10.0.48.0/20. Keep in mind that ENi within one subnet can still communicate with each other!

## Talk From VPC to OtherVPC

```
$ ssh -J michael@$BastionHostIpAddressPublic $InstanceAPublicIpAddressPrivate
curl http://$InstanceAPublicIpAddressPrivate
```