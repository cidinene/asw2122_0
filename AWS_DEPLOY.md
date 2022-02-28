# Amazon Academy

Amazon Academy is a platform created by Amazon to prepare students to works with amazon Service. 
For our pourposes, well use Amazon Academy Labs to :
    1.  Create an EC2 instance
    2.  Connect by SSH to our instance and install Docker

## Creating an EC2 Instance
    
   - Log In at [LMS AWS Academy | https://awsacademy.instructure.com/login/canvas] with your student user/passwd
   - At the DashBoard, click on the Lab Course. At the Menu, click  Module ->
   - Click on Start Lab , and once your Lab Started, Click on ->
   - Go to AWS and select services EC2
## Connect by ssh 
 
  To connect by ssh you need your host address. You can get it in following ways
    1. In you Amazon Web Console execute :
  ```
    ec2 describe-instances
  ```
  ```json
  {
                ...
                    "PrivateDnsName": "ip-172-31-5-59.ec2.internal",
                    "PrivateIpAddress": "172.31.5.59",
                    "ProductCodes": [],
                    "PublicDnsName": "ec2-34-200-249-48.compute-1.amazonaws.com",
                    "PublicIpAddress": "34.200.249.48",
                    "State": {
                        "Code": 16,
                        "Name": "running"
                    },
                    "StateTransitionReason": "",
                    "SubnetId": "subnet-099628a784d42cf88",
                    "VpcId": "vpc-0b4a450cd9cec1aee",
                    "Architecture": "x86_64",
                    "BlockDeviceMappings": [
                        {
                            "DeviceName": "/dev/xvda",
                            "Ebs": {
                                "AttachTime": "2022-02-26T23:54:35+00:00",
                                "DeleteOnTermination": true,
                                "Status": "attached",
                                "VolumeId": "vol-0a256e4d34cb00b0d"
                            }
                        }
                    ],
                 .........
                   
      }
      ```
    2. In you Amazon Web Console execute :
    
    
     
       
       
