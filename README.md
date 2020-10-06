```yaml
AWSTemplateFormatVersion: 2010-09-09
Parameters:
  KeyName:
    Description: Name of existing EC2 KeyPair to enable SSH access into the Server
    Type: AWS::EC2::KeyPair::KeyName
Mappings:
  RegionMap:
    us-east-1:
      AMI: ami-1853ac65
    us-west-1:
      AMI: ami-bf5548df
    eu-west-1:
      AMI: ami-3bfab942
    ap-southeast-1:
      AMI: ami-e2adf99e
    ap-southeast-2:
      AMI: ami-43874721
    ap-south-1:
      AMI: ami-0e306788ff2473ccb
Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      Tags:
      - Key: Name
        Value: Lab VPC
  
  WebServerSG:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupDescription: Enable SSH access via port 22 and 80
      VpcId: !Ref VPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: '22'
          ToPort: '22'
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: '80'
          ToPort: '80'
          CidrIp: 0.0.0.0/0

  EC2Instance:
    Type: 'AWS::EC2::Instance'              
    Properties:
      InstanceType: t2.micro
      ImageId:
        Fn::FindInMap:
          - RegionMap
          - !Ref AWS::Region
          - AMI
      SecurityGroupIds:
        - !Ref WebServerSG
      Tags:
        - Key: "Name"
          Value: Lab EC2 
      KeyName: !Ref KeyName
      
Outputs:
  Website:
    Description: Public DNS for EC2 EC2Instance
    Value: !Sub 'http://${EC2Instance.PublicDnsName}'

```
