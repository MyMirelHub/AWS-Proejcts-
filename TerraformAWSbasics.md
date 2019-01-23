# Getting Started with terraform and AWS

Creating a VPC with public, private subnets, route tables and internet getaway
-------------------------------------------
Using Windows and VS code with official terraform extension as editor
________________________________________
Configuration 
- Create a terraform directorty and a new file with extension .tf 

Open with VS code 
 provider "aws" {
  region     = "eu-west-1"
  secret key 
  access key 
}

#create VPC 
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
  enable_dns_hostnames = "true"
  tags = {
    Name = "Terra1VPC"
  }
}
#create subnets 
#public 
resource "aws_subnet" "main" {
  vpc_id     = "${aws_vpc.main.id}"
  cidr_block = "10.0.0.0/24"
  map_public_ip_on_launch = "true" 
  tags = {
    Name = "Public1"
  }
}
#private 
resource "aws_subnet" "main2" {
  vpc_id     = "${aws_vpc.main.id}"
  cidr_block = "10.0.1.0/24"
  tags = {
    Name = "Private"
  }
}
#Internet Gateway
resource "aws_internet_gateway" "igw" {
  vpc_id = "${aws_vpc.main.id}"
  tags = {
    Name = "TerraIGW"
  }
}

resource "aws_route_table" "public" {
  vpc_id = "${aws_vpc.main.id}"

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = "${aws_internet_gateway.igw.id}"
  }
  tags = {
    Name = "RTPublic"
  }
depends_on = ["aws_internet_gateway.igw"]

}

resource "aws_route_table" "Private" {
  vpc_id = "${aws_vpc.main.id}"
  tags = {
    Name = "RTPrivate"
  }
}

resource "aws_route_table_association" "private" {
  subnet_id      = "${aws_subnet.main.id}"
  route_table_id = "${aws_route_table.Private.id}"
}

resource "aws_route_table_association" "Public" {
  subnet_id      = "${aws_subnet.main2.id}"
  route_table_id = "${aws_route_table.public.id}"
}
______________________________________________________________
Save the file, 

To run on command promt for the first time

Terraform init 
Terraform plan 
Terraform apply 
________________________________________________________________
Results 
_____________________
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create
-/+ destroy and then create replacement

Terraform will perform the following actions:

  + aws_internet_gateway.igw
      id:                                          <computed>
      owner_id:                                    <computed>
      tags.%:                                      "1"
      tags.Name:                                   "TerraIGW"
      vpc_id:                                      "${aws_vpc.main.id}"

  + aws_route_table.Private
      id:                                          <computed>
      owner_id:                                    <computed>
      propagating_vgws.#:                          <computed>
      route.#:                                     <computed>
      tags.%:                                      "1"
      tags.Name:                                   "RTPrivate"
      vpc_id:                                      "${aws_vpc.main.id}"

  + aws_route_table.public
      id:                                          <computed>
      owner_id:                                    <computed>
      propagating_vgws.#:                          <computed>
      route.#:                                     "1"
      route.~3624129189.cidr_block:                "0.0.0.0/0"
      route.~3624129189.egress_only_gateway_id:    ""
      route.~3624129189.gateway_id:                "${aws_internet_gateway.igw.id}"
      route.~3624129189.instance_id:               ""
      route.~3624129189.ipv6_cidr_block:           ""
      route.~3624129189.nat_gateway_id:            ""
      route.~3624129189.network_interface_id:      ""
      route.~3624129189.transit_gateway_id:        ""
      route.~3624129189.vpc_peering_connection_id: ""
      tags.%:                                      "1"
      tags.Name:                                   "RTPublic"
      vpc_id:                                      "${aws_vpc.main.id}"

-/+ aws_route_table_association.Public (new resource required)
      id:                                          "rtbassoc-02bc776d92cf5db17" => <computed> (forces new resource)
      route_table_id:                              "rtb-02e13d4ffc7325096" => "${aws_route_table.public.id}"
      subnet_id:                                   "subnet-0c03a70882778edbe" => "${aws_subnet.main2.id}" (forces new resource)

-/+ aws_route_table_association.private (new resource required)
      id:                                          "rtbassoc-0e7b3f4cf61d044ff" => <computed> (forces new resource)
      route_table_id:                              "rtb-00528e1f87a85c29b" => "${aws_route_table.Private.id}"
      subnet_id:                                   "subnet-0e057936ea5c34547" => "${aws_subnet.main.id}" (forces new resource)

  + aws_subnet.main
      id:                                          <computed>
      arn:                                         <computed>
      assign_ipv6_address_on_creation:             "false"
      availability_zone:                           <computed>
      availability_zone_id:                        <computed>
      cidr_block:                                  "10.0.0.0/24"
      ipv6_cidr_block:                             <computed>
      ipv6_cidr_block_association_id:              <computed>
      map_public_ip_on_launch:                     "true"
      owner_id:                                    <computed>
      tags.%:                                      "1"
      tags.Name:                                   "Public1"
      vpc_id:                                      "${aws_vpc.main.id}"

  + aws_subnet.main2
      id:                                          <computed>
      arn:                                         <computed>
      assign_ipv6_address_on_creation:             "false"
      availability_zone:                           <computed>
      availability_zone_id:                        <computed>
      cidr_block:                                  "10.0.1.0/24"
      ipv6_cidr_block:                             <computed>
      ipv6_cidr_block_association_id:              <computed>
      map_public_ip_on_launch:                     "false"
      owner_id:                                    <computed>
      tags.%:                                      "1"
      tags.Name:                                   "Private"
      vpc_id:                                      "${aws_vpc.main.id}"

  + aws_vpc.main
      id:                                          <computed>
      arn:                                         <computed>
      assign_generated_ipv6_cidr_block:            "false"
      cidr_block:                                  "10.0.0.0/16"
      default_network_acl_id:                      <computed>
      default_route_table_id:                      <computed>
      default_security_group_id:                   <computed>
      dhcp_options_id:                             <computed>
      enable_classiclink:                          <computed>
      enable_classiclink_dns_support:              <computed>
      enable_dns_hostnames:                        "true"
      enable_dns_support:                          "true"
      instance_tenancy:                            "default"
      ipv6_association_id:                         <computed>
      ipv6_cidr_block:                             <computed>
      main_route_table_id:                         <computed>
      owner_id:                                    <computed>
      tags.%:                                      "1"
      tags.Name:                                   "Terra1VPC"
