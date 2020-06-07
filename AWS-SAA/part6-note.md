Multi-Region Failover with Amazon Route 53
Version A5L5

Region-wide events such as natural disasters can disrupt the availability of a region for an extended length of time, making cross-region availability a critical component to ensure that an application is highly available. Amazon Route 53 can help keep your web application available with a minimal amount of downtime.

Scenario

In this lab, you will configure and test a cross-region disaster recovery scenario.

Lab scenario
A web application has already been deployed in two different regions. You will:

Configure a domain in Amazon Route 53 to send traffic to the primary region.
Configure a Health Check on the primary region. If the health check fails, traffic will be sent to the secondary region.
Test the failover by stopping the instance in the primary region.
Objectives

After completing this lab, you will be able to:

Use Route 53 to configure cross-region failover of a web application.
Use Route 53 health checks to determine the health of a resource.
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

   

Task 1: Inspect Your Environment
Resources have already been deployed for you in two regions. You will start by inspecting the resources in your Primary region.

In the AWS Management Console, on the Services menu, click EC2.

In the navigation pane, click Instances.

Click on the instance named Web-Application-1.

Copy the IPv4 Public IP (shown in the bottom-right) and paste it into a text document for future reference.

This is the IP address of your Primary web server. You will configuring your domain to point to this instance by default.

Make a note of your Region, displayed in the top-right corner of the screen. This is the Region of your Primary web server.

Next, you will look at your resources in your Secondary region. You can determine your Secondary Region from this table:

table with data
Make a note of your Secondary Region.

Select your Secondary Region from the Region pull-down menu in the top-right of the screen.

Region selector
Click on the instance named Web-Application-2.

 If you do not see any instances listed, please refer to the above table to find the correct Secondary Region.

Copy the IPv4 Public IP (shown in the bottom-right) and paste it into a text document for future reference.

This is the IP address of your Secondary web server. You will configuring your domain to point to this instance if the Primary web server fails.

   

Task 2: Configure a Health Check
In this task, you will create a Health Check that will test the health of your Primary web server. AWS has health checkers located in multiple locations around the world that can test whether your website is accessible.

On the Services menu, click Route 53.

 If you see any error messages, you can safely ignore them. They will not impact your lab.

In the navigation pane, click Health checks.

Click Create health check.

In the Configure health check page, configure the following settings (and ignore any settings that aren't listed):

table with data
Expand Advanced configuration and then configure the following settings (and ignore any settings that aren't listed):

table with data
Click Next.

Click Create health check.

The health check will now start monitoring your primary web server.

   

Task 3: Configure your Domain in Route 53
In this task, you will configure your domain to point to your primary and secondary web servers.

In the navigation pane, click Hosted zones.

A random domain name has been created for you. It has a name similar to: XXXXXX_XXXXXXXXXX.training

Click the name of your domain.

Click Create Record Set.

You will now create a DNS A-record to point to your Primary web server. An A-record resolves a domain name by returning an IP address. You will also associate this Record Set with the Health Check you created earlier so that traffic will only be sent to your Primary web server if the Health Check indicates that the server is healthy.

In Create Record Set, configure the following settings (and ignore any settings that aren't listed):

table with data
Click Create.

An A-record should be listed. If the newly created record does not immediately appear in the table, periodically click the refresh icon to update the table until it appears.

You will now create another A-record to point to the Secondary web server. It will be used as the failover server if the Primary web server fails its Health Check.

Click Create Record Set again.

In Create Record Set, configure the following settings (and ignore any settings that aren't listed):

table with data
Click Create.

You are not associating this record with a Health Check because there is no third site available.

You can now check the status of your Health Check.

In the navigation pane, click Health checks.

Select check-1.

Click the Health checkers tab at the bottom of the page.

The health check is performed independently from multiple locations around the world, with each location requesting the page every 10 seconds.

Confirm that check-1 has a status of Healthy. If it is not showing as Healthy after a few minutes, ask your instructor for assistance in debugging the configuration.

You have now configured your web application to failover across two regions.

   

Task 4: Check the DNS Resolution
In this task, you will query DNS (Domain Name Service) to verify that Amazon Route 53 is correctly sending traffic to your Primary web server.

In the navigation pane, click Hosted zones.

Click the name of your domain (starting with vocareum).

Click Test Record Set.

 If the Test Record Set button is not visible, try making your window wider so that it appears.

In Check response from Route 53, configure the following settings (and ignore any settings that aren't listed):

table with data
Click Get response.

The DNS response will be tested, with the results appearing on the right side of the window.

Look at the Response returned by Route 53 value. Confirm that it is the same IP address as your Primary web server.

 If correct IP address is not displayed, ask your instructor for assistance in debugging the configuration.

The fact that your Domain resolved to the IP address of your Primary web server means that requests to your domain will be routed by default to that web server.

   

Task 5 - Test Your Failover
In this task, you will verify that Amazon Route 53 correctly fails over to your Secondary web server if your Primary web server fails. You will simulate a failure by manually stopping the instance in your primary region.

On the Services menu, click EC2.

Select your primary region from the region drop-down list in the top-right. (That is the region where you started the lab.)

In the navigation pane, click Instances.

Right-click Web-Application-1, click Instance State, and click Stop.

In the Stop Instances dialog box, click Yes, Stop.

Wait until the instance state changes to stopped.

On the Services menu, click Route 53.

In the navigation pane, click Health checks.

Select check-1 and click the Health checkers tab in the lower pane.

Wait until the status of check-1 is Unhealthy. If necessary, periodically click the refresh icon in the top-right corner.

The Health Check has now detected that your Primary web server has stopped responding. It should now be directing DNS requests to the Secondary web server.

In the navigation pane, click Hosted zones.

Click your domain name.

Click Test Record Set.

In Check response from Route 53, configure the following settings (and ignore any settings that aren't listed):

table with data
Click Get response.

Look at the Response returned by Route 53 value. Confirm that it is the same IP address as your Secondary web server.

You have now successfully confirmed that your application environment can fail over from a primary region to a secondary region if the server in the primary region fails.

Optional: Start your Primary web server again, wait for the Health Check to become Healthy and confirm that the DNS resolution now points back to your Primary web server. This demonstrates the ability to failover to a Secondary web server and then to failback to Primary web server when it becomes healthy again.

   

Lab Complete
Click End Lab at the top of this page and then click Yes to confirm that you want to end the lab.

A panel will appear, indicating that "DELETE has been initiated... You may close this message box now."

Click the X in the top right corner to close the panel.