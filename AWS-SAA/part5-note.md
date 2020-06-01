Introduction to Amazon CloudFront
Version 1.0.6 (spl85)

Overview
This guide introduces you to Amazon CloudFront. In this lab you will create an Amazon CloudFront distribution that will use a CloudFront domain name in the url to distribute a publicly accessible image file stored in an Amazon S3 bucket.

Topics covered
By the end of this lab, you will be able to:

Create a new Amazon CloudFront distribution
Use your Amazon CloudFront distribution to serve an image file
Delete your Amazon CloudFront distribution when it is no longer required
Amazon CloudFront

Amazon CloudFront is a content delivery web service. It integrates with other Amazon Web Services products to give developers and businesses an easy way to distribute content to end users with low latency, high data transfer speeds, and no minimum usage commitments.

   

Accessing the AWS Management Console
At the top of these instructions, click Start Lab to launch your lab.

A Start Lab panel opens displaying the lab status.

Wait until you see the message "Lab status: ready", then click the X to close the Start Lab panel.

At the top of these instructions, click AWS

This will open the AWS Management Console in a new browser tab. The system will automatically log you in.

Tip: If a new browser tab does not open, there will typically be a banner or icon at the top of your browser indicating that your browser is preventing the site from opening pop-up windows. Click on the banner or icon and choose "Allow pop ups."

Arrange the AWS Management Console tab so that it displays along side these instructions. Ideally, you will be able to see both browser tabs at the same time, to make it easier to follow the lab steps.

   

Task 1: Store a Publicly Accessible Image File in an Amazon S3 Bucket
In this task, you will store the file that you wish to distribute using Amazon CloudFront in a publicly accessible location. You will store the image file in a publically accessible Amazon S3 bucket.

In the AWS Management Console, on the Services menu, click S3.

In the Amazon S3 console, click  Create bucket then configure:

Bucket name: cfBUCKET

Replace BUCKET with a random number

Click Create

Note: If you receive an error saying that your bucket name is not available, try a different bucket name. For your bucket to work with CloudFront, the name must conform to DNS naming requirements. For more information, go to Bucket Restrictions and Limitations in the Amazon Simple Storage Service Developer Guide.

Click on the S3 bucket you created and then click the Permissions tab.

With Block public access selected, click Edit and

Uncheck the Block all public access. All five boxes should now be unchecked. Click Save.

In the Edit public access settings for this bucket dialog box, type confirm and click Confirm to update the settings.

Click Overview tab.

Click  Upload

Click Add files

Select a file that you would like to upload.

If you don’t have a file prepared, visit a favorite website in your browser and download an image from the website to your desktop. Then choose that file for this step.

Click Next then configure:

Under Manage public permissions, select Grant public read access to this object(s)

Click Upload

Copy the name of your file to your text editor for later use.

e.g. The name of your file could be myimage.png

Click the file that you uploaded.

Under S3 Object URL, copy the link to your clipboard.

Paste the link in a new browser tab, then press Enter.

This will display your image. It also proves that your content is publicly accessible. However, this is not the URL you will use when you are ready to distribute your content.

   

Task 2: Create an Amazon CloudFront Web Distribution
In this task, you will create an Amazon CloudFront web distribution that distributes the file stored in the publicly accessible Amazon S3 bucket.

In the AWS Management Console, on the Services menu, click CloudFront.

Click Create Distribution

On the Select a delivery method for your content page, in the Web section, click Get Started then configure:

Origin Domain Name: Select the S3 bucket you created

Scroll to the bottom of the page, then click Create Distribution

The Status column shows  In Progress for your distribution. After Amazon CloudFront has created your distribution, the value of the Status column for your distribution will change to Deployed. At this point, it will be ready to process requests. This should take around 15-20 minutes. The domain name that Amazon CloudFront assigns to your distribution appears in the list of distributions. It will look similar to dm2afjy05tegj.cloudfront.net

Amazon CloudFront now knows where your Amazon S3 origin server is, and you know the domain name associated with the distribution. You can create a link to your Amazon S3 bucket content with that domain name, and have Amazon CloudFront serve it.

   

Task 3: Create a Link to Your Object
Copy the following HTML into a new text file:

<html>
<head>My CloudFront Test</head>
<body>
<p>My text content goes here.</p>
<p><img src="http://DOMAIN/OBJECT" alt="my test image" /></p>
</body>
</html>
In your text file:

Replace DOMAIN with your Amazon CloudFront Domain Name for your distribution. You should see this on the CloudFront Distributions page.

Replace OBJECT with the name of the file that you uploaded to your Amazon S3 bucket

Save the text file to your computer as myimage.html

Open the web page you just created in a browser to ensure that you can see your content.

The browser returns your page with the embedded image file, served from the edge location that Amazon CloudFront determined was appropriate to serve the object.

   

Task 4: Delete Your Amazon CloudFront Distribution
You can clean up your resources by deleting the Amazon CloudFront distribution and the Amazon S3 bucket.

In the AWS Management Console, select the check box  for your CloudFront distribution.

At the top of the screen, click Disable

Click Yes, Disable

Click Close

The value of the State column immediately changes to Disabled.

Wait until the value of the Status column changes to Deployed.

Select the check box  for your CloudFront distribution, then configure:

Click Delete then:

Click Yes, Delete

Click Close

   

Task 5: Delete Your Amazon S3 Bucket
On the Services menu, click S3.

Click the area to the right of your bucket so that you highlight your bucket.

Do not click the name of your bucket. You only need to highlight your bucket.

Click Delete then:

Enter the name of your bucket

Click Confirm

You have now released the resources used by your CloudFront distribution and Amazon S3 bucket.

   

Lab Complete
Click End Lab at the top of this page and then click Yes to confirm that you want to end the lab.

A panel will appear, indicating that "DELETE has been initiated... You may close this message box now."

Click the X in the top right corner to close the panel.

Conclusion
 Congratulations! You now have successfully:

Created a new Amazon CloudFront distribution

Used your Amazon CloudFront distribution to serve an image file

Deleted your Amazon CloudFront distribution when it is no longer required

Additional resources
Amazon CloudFront

AWS Training & Certification

For feedback, suggestions, or corrections, please email us at aws-course-feedback@amazon.com.