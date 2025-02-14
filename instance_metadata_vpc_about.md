---

copyright:
  years: 2021
lastupdated: "2021-08-23"

keywords: metadata, virtual private cloud, instance, virtual server

subcollection: vpc


---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:external: target="_blank" .external}
{:pre: .pre}
{:tip: .tip}
{:note: .note}
{:beta: .beta}
{:table: .aria-labeledby="caption"}
{:DomainName: data-hd-keyref="APPDomain"}
{:DomainName: data-hd-keyref="DomainName"}

# About Instance Metadata for VPC (Beta)
{: #imd-about}

Use the Instance metadata for VPC service to access information about your VPC resources. The metadata service is a REST API that you invoke using a well-known URI to retrieve instance-specific information from the metadata server. Access to API services is made available from within the instance itself and is inaccessible from outside the instance. 
The service is free.
{:shortdesc}

This service is available only to accounts with special approval to preview this beta feature. 
{:beta}

There are no backward-compatibility guarantees as a feature progresses through its beta phase, or from the final beta release to its initial GA release. Using beta features could introduce the risk of corrupting resources in your account. IBM strongly recommends that you do not use non-GA-mature features on production accounts.
{: important}

## Metadata service concepts
{: #imd-concepts}

You can programmatically access metadata about a virtual server instance and use it to initialize new instances. Metadata provided by API services pertains only to the instance from which the request is made. You can't use the metadata service within an instance to obtain information about another instance or to obtain information concerning resources not currently associated with the instance.

The instance metadata service uses a REST API and well-known IP address to retrieve metadata such as the instance name, CRN, resource groups, user data, as well as SSH keys and placement groups. For a list of all metadata returned from the service, [Summary of data returned by the metadata service](/docs/vpc?topic=vpc-imd-metadata-summary).

The instance metadata service consists of two components:

* **Instance identity token service** that lets you aquire an [access token](#imd-vpc-access-token) to access the VPC metadata service. To access other IBM Cloud IAM-enabled services, you can exchange the instance identity token for an IAM token. For example, starting an instance might require a call to IBM Cloud Database to set up a database server.

* **Instance [metadata service](#imd-service)** that lets you retrieve the instance metadata. By calling the metadata service APIs, you can get instance initialization data, network interface, volume attachment, public SSH key, and placement group information.

### Limitations for the Beta release
{: imd-limitations}

By default, the metadata service is enabled when your account is added to the allow list to preview this feature. If you want to disable the service, you must disable it from an account not on the allow list. For information about disabling the metadata service, see [Enable or disable the metadata service](/docs/vpc?topic=vpc-imd-configure-service#imd-metadata-service-enable).

### Instance identity token service
{: #imd-vpc-access-token}

The instance identity token service lets you generate an access token (a JSON web token) to use that token to access the metadata service. You generate it by calling this endpoint: `http://169.254.169.254/instance_identity/v1/token`. This endpoint is accessible to all commands, processes, and software applications running within a virtual server instance. Access to the API endpoint is not available outside the virtual server instance.

Use the instance identity access token when calling the metadata service. The token is valid until it reaches expiration, which by default is 5 minutes. You can optionally exchange this token for an IAM token to call any IAM-enabled service. After you exchange the token, it can't be used to access the metadata service.

For more information, see [Aquire an access token](/docs/vpc?topic=vpc-imd-configure-service#imd-json-token).

### Metadata service
{: #imd-service}

You [configure the metadata service and enable it](/docs/vpc?topic=vpc-imd-configure-service) on an instance to retrieve data about the instance. Use this information to configure and launch new instances.

Metadata is obtained by calling REST APIs that provide instance, SSH key, and placement group information at the following  at the following endpoints: 

* `http://169.254.169.254/metadata/v1/instance`
* `http://169.254.169.254/metadata/v1/keys`
* `http://169.254.169.254/metadata/v1/placement_groups`

These endpoints are accessible only within a running virtual server instance.

You [enable the metadata service](/docs/vpc?topic=vpc-imd-get-metadata#imd-metadata-service-enable) by setting a toggle in the VPC API when creating a new instance (`POST /instance`) or updating an existing instance (`PATCH /instance`). 

Be default, the service is enabled for virtual server instances created on accounts participating in the Beta release. You can disable the service from existing instances or when creating new ones. For information, see [Enable or disable the instance metadata service](/docs/vpc?topic=vpc-imd-configure-service&interface=ui#imd-metadata-service-enable).
{:note}

The metadata service intercepts all requests to the service's IP and then routes them to the specific services to handle these requests. As part of the request to the metadata service, you include the instance identity access token.

Through IAM, you can also assign access rights to instances by creating a [compute resource identity](/docs/vpc?topic=vpc-imd-trusted-profile-metadata), and then configure access rights to IBM-enabled services using that identity.

The metadata service API lets you [retrieve the metadata](/docs/vpc?topic=vpc-imd-get-metadata) from the virtual server instance. This includes _user data_, the same user data available from cloud-init for VPC instances. User data is data you specified when provisioning the instance and is available when provisioning new instances. User data provides valuable data for controlling how the instance is initialized. For example, information to load database software, custom software for worker nodes, or information to make decisions about how to initialize the instance. For more information, see [User data](/docs/vpc?topic=vpc-user-data).

User data is obtained by making a GET call to the metadata service:

```
user_data=`curl -X GET "http://169.254.169.254/metadata/v1/instance/initialization
```
{:pre}

The metadata you access from within the instance is not protected by any encryption method. Any user with access to the instance and metadata service can potentially see the metadata. As a precaution, do not store sensitive data, such as passwords or customer encryption keys, as user data.
{:note}

## Scenarios for using the metadata service
{: #imd-metadata-scenarios}

You can use the metadata service in two ways:

* Access the metadata from within the instance
  
  In this scenario, you retrieve metadata from within the instance to bootstrap the instance. For example, you might want to specify custom user data when creating the instance and then retrieve that custom user data when initializing the instance. For more information, see [Accessing metadata from an instance](/docs/vpc?topic=vpc-imd-access-instance-metadata).

* Access an instance identity IAM token and call IAM-enabled services

  In this scenario, you create a trusted profile for compute resource identity and assign a virtual server instance access rights for IAM-enabled services. Make a call to create a new instance, configured with the metadata service. Link the instance to a trusted profile. Call the metadata service to get an access token, then exchange the token for an IAM token. IAM-enabled  services can then be called from an instance without having to manage and distribute IAM secrets to the instance. Use this option when you want to call IAM-enabled services as part of instance bootstrapping an instance. For example, you might want to set up a new COS bucket to be used by the instance workload. For more information, see [Using trusted profiles to access instance metadata](/docs/vpc?topic=vpc-imd-trusted-profile-metadata).

Figure 1 illustrates these scenarios.

![Figure showing the instance metadata user workflows.](/images/metadata-service-user-workflow.png "Figure showing the instance metadata user workflows."){: caption="Figure 1. User scenarios for the instance metadata service" caption-side="bottom"}

## Data security
{: #imd-security}

IBM takes precaution to assure your data is secure. Metadata is retrieved only from the instance to which you have access. Data common to multiple instances will be retrieved but specific IP addresses of other instances are not exposed. 

Calls to the metadata service are secure prior to leaving the compute host from which they originate. While access is over HTTP, data is wrapped into a secure protocol prior to leaving the compute host.

The initial call to the instance metadata service is over an HTTP connection. The purpose of this call is to acquire an identity token to access the metadata service. These services use a well-known URL. Although you see a non-secure connection, the VPC compute service takes additional actions to secure the token and metadata services.

The instance metadata service is not intended for sensitive data. The service end point is open to all processes on the instance. Information exposed through this service should be considered as shared information to all applications running inside the virtual server instance.
{:note}

For additional security measures you can take, see [Security best practices for the Instance Metadata service](/docs/vpc?topic=vpc-imd-security-best-practices).

## Next steps
{: #imd-next-steps-about}

* [Create a JSON web token for accessing the metadata service](/docs/vpc?topic=vpc-imd-configure-service#imd-get-token).

* [Retrieve data using the metadata service](/docs/vpc?topic=vpc-imd-get-metadata).
