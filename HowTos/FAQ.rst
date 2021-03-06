.. meta::
   :description: UCC Frequently Asked Questions
   :keywords: ucc, faq, frequently asked questions, ucc faq, aviatrix faq

===========================
Frequently Asked Questions
===========================

Aviatrix product consists of a controller and gateways. When the product is deployed in the
public cloud marketplace, what you launch is the controller instance and from controller console you launch gateways by using cloud provider APIs.

When the product is deployed as a virtual appliance in a virtualized datacenter environment, the controller and gateway is bundled into one virtual image, such as OVF and VHD.

The following FAQ discusses only the cloud deployment scenario.

Aviatrix Secure Cloud Network Platform
=======================================


What can Aviatrix platform do for me?
--------------------------------------


Aviatrix Cloud Gateway provides an end to end secure network solution
for AWS, Azure and Google GCloud. The solution includes `AWS Global Transit Network <http://docs.aviatrix.com/HowTos/transitvpc_faq.html>`_, an enterprise
`OpenVPN® <http://docs.aviatrix.com/HowTos/openvpn_faq.html>`_ access to VPC/VNet, `encrypted routing <http://docs.aviatrix.com/HowTos/peering.html>`_ for VPC/VNet to VPC/VNet traffic, `Stateful Firewall and Egress FQDN <http://docs.aviatrix.com/HowTos/FQDN_Whitelists_Ref_Design.html>`_ and
`monitoring and logging <http://docs.aviatrix.com/HowTos/AviatrixLogging.html>`_ of link status and latency. The solution enables
you to build a secure private network spanning one or more public clouds
where a user access any instance/VM with a private IP address directly.
No more bastion stations and jump hosts, the solution gives user the
seamless experience that they enjoy when using the on-prem network.

For an Aviatrix overview, check out `this document. <http://docs.aviatrix.com/StartUpGuides/aviatrix_overview.html>`_ 

In addition, the product interoperates with any third party IPSEC capable devices, including AWS VGW and Aviatrix's own on-prem virtual appliance CloudN.

Architecturally, Aviatrix solution is a centrally managed, loosely
coupled and globally deployed platform built for the cloud from the
ground up.

How do I launch the product?
--------------------------------

The product consists of two components, the controller and one or more
gateways. The gateway is launched from the controller.

The controller provides a central console for all provisioning,
monitoring and upgrades of the services.

The controller is available in AWS and Azure marketplace. It is also
available as a GCloud community image. For marketplace launch, search
for “Aviatrix” in marketplace.

Follow `Getting Started on AWS <http://docs.aviatrix.com/StartUpGuides/aviatrix-cloud-controller-startup-guide.html>`_ instructions to launch Controller on AWS.

Follow `Getting Started on Azure <http://docs.aviatrix.com/StartUpGuides/azure-aviatrix-cloud-controller-startup-guide.html>`_ instructions to launch Controller on Azure. 

Follow `Getting Started on Google <http://docs.aviatrix.com/StartUpGuides/google-aviatrix-cloud-controller-startup-guide.html>`_ instructions to launch Controller on Google.

How do I access the Controller?
--------------------------------


Once you have launched the instance, you access the Controller instance
via a web browser.

https://public\_IP\_address\_of\_the\_controller\_instance

Login with username “admin”. The first time password is the private IP
address of the controller instance. You are required to change the
password at your first login.

How do I secure the Controller access?
--------------------------------------

There are several ways to secure your Controller access, as discussed below. 

1. Reduce the access scope
############################

Only TCP port 443 needs to be opened for inbound traffic to the
controller. If you wish to reduce the scope of source addresses by
specifying custom IP address, you must include all gateway public IP
addresses, in addition to your own public IP address. This is because
gateways launched from the controller use its public IP address to
communicate back to controller.

.. Important::

 The Controller security group inbound rules must be open to all when a new gateway is launched as the EIP of the new gateway is unknown prior to launch. You can add that EIP after the gateway is launched for the Controller inbound security rule. 

..

If you deploy Aviatrix SAML clients for user VPN access, you can follow `this document <http://docs.aviatrix.com/HowTos/controller_security_for_SAML.html>`_ to add security to the Controller. 

2. Use signed certificate
##########################

Avitrix Controller is shipped with a self-signed certificate, therefore there is a "Note Secure" warning sign shown on your browser console. 

You can change that by importing your own signed certificate. To do so, go to Settings ->Advanced -> Security. At the panel "CERTIFICATE REQUEST - SEND TO CERTIFICATE AUTHORITY", send a CSR (certificate sign request), then follow the instruction to import CA and signed Controller certificate. 

3. Remove less secure TLS version
##################################

You can disable access from browser that runs TLSv1 and TLSv1.1 and only support TLSv1.2. To do so, go to Settings ->Advanced -> Security -> TLS VERSIONS SUPPORT. Uncheck TLSv1 and TLSv1.1. 

4. Enable LDAP or DUO second factor to login in
################################################

In addition to username and password login credentials to the Controller, you can also enable LDAP or DUO authentication. 

To enable LDAP authentication, go to Settings -> Controller -> LDAP Login and fill the form. 

To enable DUO authentication, go to Settings -> Controller -> 2FA Login and follow `the instructions <http://docs.aviatrix.com/HowTos/AdminUsers_DuoAuth.html#configuration-workflow-for-duo-authentication>`_ to setup DUO. 

5. Create Read-only accounts
#############################

You can create read_only accounts for your operations team. They can view and list pages but not making changes. Follow the `answer <http://docs.aviatrix.com/HowTos/FAQ.html#can-there-be-read-only-account-for-operation-team>`_ to have it setup. 

How do I ensure my Transit Network is secure when an Aviatrix gateway is deployed on a public subnet?
------------------------------------------------------------------------------------------------------

The customer concerns
######################

Some organizations have concerns about having public subnets in a VPC. 
The concern is that if there were a public subnet in a VPC, 
users may find ways to launch an instance on the public subnet and associate the instance with a 
public IP address, thus enabling the instance to access Internet without 
going through a proper egress firewall (in the cloud or on-prem).

The reality 
##############

However when deploying a `AWS Global Transit Network solution <https://aws.amazon.com/answers/networking/aws-global-transit-network/>`_, a vendor gateway must be deployed on a public subnet in the Transit VPC. This is true for all vendor appliances on the AWS marketplace. This is
because the vendor gateway in the Transit VPC establishes IPSEC tunnels with Spoke VPC 
over public IP address, wheather or not the Spoke VPC deploys a vendor gateway or VGW. 

Note that this connectivity between Transit VPC and Spoke VPC, although using public IP addresses 
as IPSEC tunnel end points, does not imply that traffic between Transit VPC and Spoke VPC go through the 
Internet. AWS recognizes that it owns these public IP addresses therefore always
try to route the traffic through its own backbone network without ever going out to Internet.   

The Aviatrix solution
######################

Aviatrix provides multiple featrues to ensure your Transit Network is secure, as described below.

 1. Enable `Gatewazy Subnet Monitoring <http://docs.aviatrix.com/HowTos/gateway.html#monitor-gateway-subnet>`_. When this feature is enabled, the Controller will monitor the selected public subnets periodically. When it detects any instances being launched on these subnets, the Controller will alert the admin and stop the instances. 

 #. Enable `VPC Egress Firewall`. If you need to optimize application performance, you should consider allowing instances to access Internet directly, rather than backhauling to on-prem. When this feature is enabled, any traffic initiated from instances on the private subnet must go through the inline and in VPC egress whitelists before going out to Internet.  

 #. Enable `Remote User VPN`. If you need to optimize developer experience (less latency, higher bandwidth), you should consider allowing users to access instances in VPC directly with SSL VPN. When this feature is enabled, all user traffic is tracked and logged for audit and tracking purpose. 

 #. Build `Zero Trust` Network. `Aviatrix Next-Gen Transit Network <http://docs.aviatrix.com/HowTos/transitvpc_faq.html#how-does-the-aviatrix-transit-network-solution-differ-from-cisco-s-csr-based-solution>`_ is a network connectivity by design solution that ensures Spoke VPCs that belong to different BUs or projects do not have connectivity by default. 

 #. Secure the Controller. Follow the guidelines `here <http://docs.aviatrix.com/HowTos/FAQ.html#how-do-i-secure-the-controller-access>`_ to secure the Controller access. 

 #. Log Everything. Enable `Logging` to send all events from gateways, Controllers and user activities to your favorite log service platform for audit and compliance. 



Is Aviatrix Cloud Gateway a SaaS offer?
------------------------------------------


No. Aviatrix Cloud Gateway is a software product that is deployed in
your own network perimeter.



Onboarding
==========



Where do I start?
-------------------


The first time when you login, complete Onboarding process. It takes a
few steps.

If you have a BYOL license or use a community image, you need to have a
customer ID provided by Aviatrix to be able to use the product. Contact
support@aviatrix.com if you do not have a customer ID.

What is an Aviatrix Cloud Account?
-----------------------------------


An Aviatrix Cloud Account is specific and unique on the controller. It
contains cloud credentials, for example, your AWS IAM Access Key ID and
Secret Key. The controller uses these credential to launch Aviatrix
gateways by using cloud APIs.

An Aviatrix Cloud Account can correspond to multiple cloud account. For
example, it can contain credentials for an AWS IAM account, Azure
account and GCloud account.

How do I upgrade software?
-------------------------------


Click Settings -> Upgrade. This upgrades to the latest release of the
controller software.

When a new release becomes available, an alert message appears on
Dashboard. An email will also be sent to the admin of the controller.

Is there a reference design example?
---------------------------------------

Check out docs.aviatrix.com.

What is the support model?
-----------------------------


For support, send email to
`support@aviatrix.com <mailto:support@aviatrix.com>`__. We also offer premium customers with 24x7 support.
To request a
feature, click Make a wish button at the bottom of each page.



Logging and Monitoring
======================


How do I forward syslog events to my Logstash server?
---------------------------------------------------------


Click on Settings-> Logging ->LogStash logging and input the required
parameters to enable forwarding of controller syslog events and all
gateways syslog and auth log to a Logstash server.

SUMO Logic, Splunk, DataDog and rSyslog are also supported.

What are the monitoring capabilities?
--------------------------------------

Encrypted tunnel (peering and site2cloud) status is monitored. When a tunnel status changes, an alert email is sent to the controller admin.

Active VPN users are displayed on the Dashboard. Click on any username,
the user VPN connectivity history is displayed.

You can also disconnect a user from the dashboard.

Can alert email be sent to a different email address?
------------------------------------------------------

Yes, you can choose an alternative email address to send alert messages.
This is useful if the controller admin is different from the operation team.


Administration
==============


Can there be multiple admins?
--------------------------------


Yes. Username “admin” is the default admin user. But you can create
multiple users with admin privilege.
Follow `the instructions <http://docs.aviatrix.com/HowTos/AdminUsers_DuoAuth.html>`_ to learn more about setting up multiple admin users.

Is there 2FA support to log in to the console?
------------------------------------------------


Yes. In addition to password login, DUO authentication and LDAP are supported.

Can there be read only account for operation team?
---------------------------------------------------

Yes. Accounts -> Account Users -> Add A NEW USER, at Account Name field, select "read_only" from the drop down menu. This user account will have views to all pages but cannot make changes to any configurations.


OpenVPN is a registered trademark of OpenVPN Inc.


.. |image1| image:: FAQ_media/image1.png

.. disqus::
