Using Notifications to Trigger AWS Lambda
Version A5L3

Many AWS services can automatically generate notifications when events occur. These notifications can be used to trigger automated actions without requiring human intervention.

In this lab, you will create an AWS Lambda function that will automatically snapshot and tag new Amazon EC2 instances launched by Auto Scaling.

The lab scenario is:

An Auto Scaling group has already been configured.

You will trigger Auto Scaling to scale-out and launch a new Amazon EC2 instance.

This will send a notification to an Amazon Simple Notification Service (SNS) topic.

The SNS topic will trigger an AWS Lambda function which will:

Create a snapshot of the Amazon EBS volumes attached to the instance.

Add a tag to the instance.

Sent log information to Amazon CloudWatch Logs.

Lab Overview
In this lab, you will:

Create an Amazon Simple Notification Service (Amazon SNS) topic as a notification target for Auto Scaling events.

Configure your Auto Scaling group to send notifications when new Amazon EC2 instances are launched.

Create an AWS Lambda function that will be invoked when it receives a message from your Amazon SNS topic that an Auto Scaling event has occurred.

Objectives

After completing this lab, you will be able to:

Configure Auto Scaling to send notifications.

Create an AWS Lambda function to respond to notifications.

Duration

This lab will require approximately 30 minutes to complete.

   

Accessing the AWS Management Console
At the top of these instructions, click Start Lab to launch your lab.

A Start Lab panel opens displaying the lab status.

Wait until you see the message "Lab status: ready", then click the X to close the Start Lab panel.

At the top of these instructions, click AWS

This will open the AWS Management Console in a new browser tab. The system will automatically log you in.

Tip: If a new browser tab does not open, there will typically be a banner or icon at the top of your browser indicating that your browser is preventing the site from opening pop-up windows. Click on the banner or icon and choose "Allow pop ups."

Arrange the AWS Management Console tab so that it displays along side these instructions. Ideally, you will be able to see both browser tabs at the same time, to make it easier to follow the lab steps.

   

Task 1: Create an SNS Topic
In this task, you will create an Amazon Simple Notification Service (SNS) topic that the Auto Scaling group will use as a notification target.

In the AWS Management Console, on the Services menu, click Simple Notification Service.

On the left side of the screen, click on  to reveal the Amazon SNS menu, and then click Topics.

Click Create topic.

In the Create topic dialog box, configure the following settings:

Name: ScaleEvent

Display name: ScaleEvent

Click Create topic.

The topic is now ready to receive notifications.

   

Task 2: Configure Auto Scaling to Send Events
In this task, you will configure an Auto Scaling group to send notifications to the SNS topic when new Amazon EC2 instances are launched in the group.

In the AWS Management Console, on the Services menu, click EC2.

In the left navigation pane, click Auto Scaling Groups (you might need to scroll down to see it).

You will now see the Auto Scaling group that was created for you automatically for this lab.

 If you do not see a list of groups, click Auto Scaling Group: 1.

Click the Notifications tab at the bottom of the screen.

Click Create notification.

 You can drag the dividing line upwards to make the lower window pane bigger.

For Send a notification to, confirm that ScaleEvent is selected. (This is the notification topic you just created.)

For Whenever instances, ensure that only launch is selected. All other options should be deselected.

Click Save.

Auto Scaling will now send a message to your SNS topic whenever a new instance is launched in the Auto Scaling group.

   

An IAM Role for the Lambda function
An IAM role named SnapAndTagRole that has permission to perform operations on EC2 instances and to log messages in Amazon CloudWatch Logs has been pre-created for you. You will later associate this role with your Lambda function.

   

Task 4: Create a Lambda Function
Lambda function    
In this task, you will create an AWS Lambda function that will be invoked by Amazon SNS when Auto Scaling launches a new EC2 instance. The Lambda function will create a snapshot of the Amazon EBS volumes attached to the instance and then add a tag to the instance.

On the Services menu, click Lambda.

Click Create a function.

 Blueprints are code templates for writing Lambda functions. Blueprints are provided for standard Lambda triggers such as creating Alexa skills and processing Amazon Kinesis Firehose streams. This lab provides you with a pre-written Lambda function, so you will Author from scratch.

Configure the following:

Name: Snap_and_Tag

Runtime: Python 2.7

Role: Use an existing role

Existing role: SnapAndTagRole

This role grants permission to the Lambda function to create an EBS Snapshot and to tag the EC2 instance.

Click Create function.

A page will be displayed with your function configuration.

Scroll down to the Function code section, then delete all of the code that appears in the code editor.

Copy the code below, and paste it into the code editor:

 When pasting code into the code editor, use keyboard shortcuts (Ctrl+V / ⌘V) rather than right-clicking and pasting.

# Snap_and_Tag Lambda function
#
# This function is triggered when Auto Scaling launches a new instance.
# A snapshot of EBS volumes will be created and a tag will be added.

from __future__ import print_function

import json, boto3

def lambda_handler(event, context):
    print("Received event: " + json.dumps(event, indent=2))

    # Extract the EC2 instance ID from the Auto Scaling event notification
    message = event['Records'][0]['Sns']['Message']
    autoscalingInfo = json.loads(message)
    ec2InstanceId = autoscalingInfo['EC2InstanceId']

    # Snapshot all EBS volumes attached to the instance
    ec2 = boto3.resource('ec2')
    for v in ec2.volumes.filter(Filters=[{'Name': 'attachment.instance-id', 'Values': [ec2InstanceId]}]):
        description = 'Autosnap-%s-%s' % ( ec2InstanceId, v.volume_id )

        if v.create_snapshot(Description = description):
            print("\t\tSnapshot created with description [%s]" % description)

    # Add a tag to the EC2 instance: Key = Snapshots, Value = Created
    ec2 = boto3.client('ec2')
    response = ec2.create_tags(
        Resources=[ec2InstanceId],
        Tags=[{'Key': 'Snapshots', 'Value': 'Created'}]
    )
    print ("***Tag added to EC2 instance with id: " + ec2InstanceId)

    # Finished!
    return ec2InstanceId
Examine the code. It is performing the following steps:

Extract the EC2 instance ID from the notification message

Create a snapshot of all EBS volumes attached to the instance

Add a tag to the instance to indicate that snapshots were created

In the Basic settings section at the bottom of the page, configure the following:

Description: Snapshot and tag EC2 instance

Timeout: 3 min 0 sec

You will now configure the trigger that will activate the Lambda function.

Scroll up to Add triggers at the top of the page.

Under Add triggers, click SNS.

Scroll down to Configure triggers and use these settings:

SNS topic: ScaleEvent
Note: the topic may already be pre-populated in the text box.

Amazon SNS will invoke this Lambda function when the ScaleEvent topic receives a notification from Auto Scaling.

Click Add.

Click Save at the top of the page.

Your Lambda function will now automatically execute whenever Auto Scaling launches a new instance.

   

Task 5: Scale-Out the Auto Scaling Group to Trigger the Lambda function
In this task, you will increase the desired capacity of the Auto Scaling group. This will cause the Auto Scaling group to launch a new Amazon EC2 instance to meet the increased capacity requirement. Auto Scaling will then send a notification to the ScaleEvent SNS topic. Amazon SNS will then invoke the Snap_and_Tag Lambda function.

On the Services menu, click EC2.

In the left navigation pane, click Auto Scaling Groups (you might need to scroll down to see it).

On the Details tab at the bottom of the screen, click Edit (you may need to scroll to the right to see the button).

For Desired Capacity, enter: 2

Click Save.

This will cause Auto Scaling to launch an additional Amazon EC2 instance.

Click the Activity History tab and monitor the progress of the new EC2 instance that is being launched.

 Wait for the status to change to show 2 rows with a Status of Successful. You can occasionally click refresh  to update the status.

Once the status has updated, you can confirm that the Lambda function executed correctly.

In the left navigation pane (not the tab at the bottom of the page), click Instances.

Click the row for the instance that has the most recent launch time. You might have to scroll to the right to view the Launch Time column for your instance.

Click the Tags tab at the bottom of the screen.

You should see a tag with Snapshots as the key, and Created as the value. This tag was added to the EC2 instance by your Lambda function.

In the navigation pane, click Snapshots.

In the snapshot window, you should see two snapshots that were created by the Lambda function.

 If the tag or snapshots were not created, then your Lambda function either had a failure or was not triggered. Ask your instructor for assistance in diagnosing the configuration.

Your Auto Scaling group successfully triggered the Lambda function, which created the tag and snapshots. This provides an example serverless solution on AWS.

   

Lab Complete
Congratulations! You have completed the lab.

Click End Lab at the top of this page and then click Yes to confirm that you want to end the lab.

A panel will appear, indicating that "DELETE has been initiated... You may close this message box now."

Click the X in the top right corner to close the panel.

For feedback, suggestions, or corrections, please email us at: aws-course-feedback@amazon.com