## Automation on AWS

- Automation is made very easy on AWS. 
- First 1: When creating an EC2 instance navigate down to the bottom where you'll find `Advanced details` 
- First 2: Navigate to `User data`. Here you're able to write a bash script to automate whatever you need to do.
- An example.....

<img width="348" alt="Screenshot 2022-11-02 at 12 09 59" src="https://user-images.githubusercontent.com/115224560/199672027-7767ccf7-3e89-44b3-98cc-b3f38cf0d7e9.png">


# AWSs AMIs

- In the case you would like to keep an EC2 instance but not want to keep it running or in a stopped state you are able to store it as an AMI image. This stores all the properties of the EC2 at a fraction of the cost.

- Step 1: Select the Ec2 instance you would like to `Actions` on the tool bar at the top.
- Step 2: Select `Image and Templates` > `Create Image`
- Step 3: Name the image appropriately and save it as shown in the example below...