# How To Use The scp Command to Copy a File From Local to Remote

1. The first step is to insure you are in your .ssh file where the eng130.pem file is.

2. Next step is you need to run the following command..

- command `scp -i eng130.pem -r /Users/faduma/Downloads/app_env/ ubuntu@ec2-3-250-219-99.eu-west-1.compute.amazonaws.com:/home/ubuntu`

format is `scp -i eng130.pem -r "location of file" ubuntu@ec2-3-250-219-99.eu-west-1.compute.amazonaws.com:/home/ubuntu`