---

copyright:
  years: 2019, 2021
lastupdated: "2021-08-18"

keywords: image, stock image, custom image, virtual private cloud, virtual server, power, generation 2, gen 2

subcollection: vpc

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:new_window: target="_blank"}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:external: target="_blank" .external}


# Images
{: #about-images}

When you provision {{site.data.keyword.vsi_is_full}}, you can select from the supported stock images or a custom image that you import from {{site.data.keyword.cos_full_notm}}. The image that you select determines the operating system that is provisioned for your instance.
{:shortdesc}

## Stock images
{: #stock-images}

The following operating systems are available as stock images when you create a virtual server.

### Supported x86_64 stock image operating systems
{: #x86-supported-os}

| Image | Architectures |
|---------|---------|
| CentOS 7.x, 8.x | x86-64 |
| Debian 9.x, 10.x | x86-64 |
| Red Hat Enterprise Linux 7.x, 8.x | x86-64 |
| Ubuntu 16.04.x, 18.04.x, 20.04.x | x86-64 |
| Windows 2012, 2012 R2, 2016, 2019 | x86-64 |  |
{: caption="Table 1. Supported x86_64 stock image operating systems" caption-side="top"}

### Supported LinuxONE (s390x processor architecture) stock image operating systems
{: #s390x-supported-os}

| Image | Architectures |
|---------|---------|
|  Ubuntu 20.04.x | s390x |
|  SUSE Linux Enterprise server (SLES) 15 SP1 | s390x |
|  Red Hat Enterprise Linux 8.x | s390x |
{: caption="Table 2. Supported s390x stock image operating systems" caption-side="top"}

When you order an instance, the images are cloud-init enabled to optimize creation times. With a cloud-init enabled image, you can provide user data. In the **User Data** field on the order form, you can enter optional cloud-init user data for the server. For more information about user data and automation, see [User data](/docs/vpc?topic=vpc-user-data).

You can access details about each operating system, such as the url for the operating system, by using the API call, [Retrieves all operating systems](https://cloud.ibm.com/apidocs/vpc#retrieves-all-operating-systems){: external}.  
{: tip}

### Stock image naming conventions
{: #image-naming-conventions}

All IBM-provided stock, public images are named by using the following convention:

```
ibm-<family>-<version>-<type>-<architecture>-<build>
```

For example,

```
ibm-centos-7-6-minimal-amd64-2
```

The following list explains the variables that make up the components of the image name:
* The leading prefix of `ibm-` is used for IBM-provided images. Custom images cannot be named with this prefix.
* The `family` component provides the operating system family, such as *redhat*, *debian* or *windows-server*.
* The `version` component provides the operating system version, such as *18-04* for Ubuntu 18.04, or *2012-r2* for Windows 2012 R2.
* The `type` component provides the minimization level of the operating system image, such as *minimal* or *full*.
* The `architecture` component provides the vCPU architecture that is supported by the operating system image, such as *amd64*.
* The `build` component is a small, non-negative integer that is incremented each time a new build of the operating system is created. For image names that are otherwise identical, the image with the highest build value is the most recent image for that operating system.

You can obtain the current list of images, including stock images, by running the following command in the command-line interface: [ibmcloud is images](/docs/vpc?topic=vpc-infrastructure-cli-plugin-vpc-reference#images).

The image naming convention is subject to change. The list of image names is not intended to be programmatically parsed or interpreted. You can use the [GET /images](/apidocs/vpc#get-image) API to obtain metadata in a structured format.
{: important}

## Custom images
{: #custom-images}

You can import an image from {{site.data.keyword.cos_full_notm}} to use for creating a new virtual server instance.

### Requirements
{: #custom-image-reqs}

Custom images must meet the following requirements:
- Contain a single file or volume
- Is in qcow2 format
- Is cloud-init enabled
- The operating system is supported as a stock image
- Size doesn't exceed 250 GB
- Size isn't less than 10 GB, images less than 10 GB are rounded up to 10 GB

For more information about custom images, see [Planning for custom images](/docs/vpc?topic=vpc-planning-custom-images).

<!--### Storage costs
{: #custom-image-storage}

Storage costs are incurred for storing custom images. This charge is separate from charges for storing images in {{site.data.keyword.cos_full_notm}}.-->

 ## Next steps
{: #next-steps-images}

After you choose a profile, it's time to plan for and create an instance.
* [Planning for instances](/docs/vpc?topic=vpc-vsi_best_practices)
* [Creating an instance by using the UI](/docs/vpc?topic=vpc-creating-virtual-servers)
* [Creating an instance by using the CLI](/docs/vpc?topic=vpc-creating-virtual-servers-cli)
