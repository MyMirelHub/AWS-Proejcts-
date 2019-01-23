## Elastic load balacing over two private instances
- https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/elb-security-groups.html#recommended-sg-rules
-------------------------------------------------
- VPC 
  - Subnets
    - Private 1 
    - Private 2 
    - Public 1
    - Public 2  
    - Each pair in a separate availablity zone 
   
Route Tables 
- Public RT 
  - Assigned to Internet Gateway
- Private RT 
  - Assigned to ELB security group
   
WebServerSG 
- Inbond 
  - TCP 80 
  - SSH 22
  
Private instances inside web server SG in different availability zones 
   
ELB SG
- Inbound HTTP ALL - instance listener
- Outbound ALL 
- Port Configuration
  - 80 (HTTP) forwarding to 80 (HTTP)
  - Assing public subnets - internet facing 
