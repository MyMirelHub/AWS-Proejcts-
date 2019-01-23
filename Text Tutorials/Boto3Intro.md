# https://boto3.amazonaws.com/v1/documentation/api/latest/index.html
```python
import boto3
# https://boto3.amazonaws.com/v1/documentation/api/latest/guide/configuration.html
# Do not hard code credentials
# A low-level client representing Amazon Elastic Compute Cloud (EC2):
# Hard coded strings as credentials, not recommended.
client = boto3.client('ec2', aws_access_key_id='#########',
    aws_secret_access_key='###########')
```


# Enter AWS Credentials
# use both client and resource
ec2 = boto3.resource('ec2')

# create VPC
vpc = ec2.create_vpc(CidrBlock='10.0.0.0/16')
# we can assign a name to vpc, or any resource, by using tag
vpc.create_tags(Tags=[{"Key": "Name", "Value": "Boto3VPC"}])
# enable dns hostnames
client.modify_vpc_attribute(EnableDnsHostnames={'Value': True,},VpcId='vpc-0e1317219ea037e1e')
vpc.wait_until_available()
print(vpc.id)

# create internet gateway
igw = ec2.create_internet_gateway()
igw.create_tags(Tags=[{"Key": "Name", "Value": "Boto3IGW"}])

# attach gateway to VPC
vpc.attach_internet_gateway(InternetGatewayId=igw.id)

# create subnet
subnet = vpc.create_subnet(CidrBlock='10.0.0.0/25', VpcId=vpc.id)
subnet.create_tags(Tags=[{"Key": "Name", "Value": "MirelPythonSubnet"}])

# enable auto assign IP
client.modify_subnet_attribute(SubnetId=subnet.id, MapPublicIpOnLaunch={"Value": True})

# create a route table and a public route

route_table = vpc.create_route_table()
route = route_table.create_route(
    DestinationCidrBlock='0.0.0.0/0',
    GatewayId=igw.id
)

route_table.create_tags(Tags=[{"Key": "Name", "Value": "Boto3Route"}])

# associate the route table with the subnet
route_table.associate_with_subnet(SubnetId=subnet.id)

# create sg
sec_group = ec2.create_security_group(
    GroupName='Boto3', Description='Boto3SecurityGroup', VpcId=vpc.id)
sec_group.authorize_ingress(
    CidrIp='0.0.0.0/0',
    IpProtocol='icmp',
    FromPort=-1,
    ToPort=-1
)

ec2 = boto3.resource('ec2')
security_group = ec2.SecurityGroup('id')

# Create instance
instances = ec2.create_instances(
    ImageId='ami-08935252a36e25f85', InstanceType='t2.micro', MaxCount=1, MinCount=1,
    NetworkInterfaces=[{'SubnetId': subnet.id, 'DeviceIndex': 0, 'AssociatePublicIpAddress': True, 'Groups': [sec_group.group_id]}])
instances[0].wait_until_running()
print(instances[0].id)
