---

copyright:
  years: 2017, 2020
lastupdated: "2020-06-01"

keywords: terraform provider plugin, terraform data source cos, terraform data source object storage, terraform get cos bucket, terraform get object storage resources

subcollection: terraform

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:deprecated: .deprecated}
{:download: .download}
{:preview: .preview}
{:external: target="_blank" .external}

# Object Storage resources
{: #object-storage-resources}

Review the [{{site.data.keyword.cos_full_notm}}](/docs/cloud-object-storage?topic=cloud-object-storage-about-cloud-object-storage) resources that you can create, modify, or delete. You can reference the output parameters for each resource in other resources or data sources by using [Terraform interpolation syntax](https://www.terraform.io/docs/configuration-0-11/interpolation.html){: external}. 
{: shortdesc}

Before you start working with your resource, make sure to review the [required parameters](/docs/terraform?topic=terraform-provider-reference#required-parameters) that you need to specify in the `provider` block of your Terraform configuration file. 
{: important}


## `ibm_cos_bucket`
{: #cos-bucket}

Create or delete an {{site.data.keyword.cos_full_notm}} bucket. The bucket is used to store your data. For more information about configuration options, see [Create some buckets to store your data](/docs/cloud-object-storage?topic=cloud-object-storage-getting-started-cloud-object-storage#gs-create-buckets). 

To create a bucket, you must provision an {{site.data.keyword.cos_full_notm}} instance first by using the [`ibm_resource_instance`](/docs/terraform?topic=terraform-resource-mgmt-resources#resource-instance) resource.
{: note}

### Sample Terraform code
{: #cos-bucket-sample}

The following example creates an instance of {{site.data.keyword.cos_full_notm}}. Then, one bucket with a `flex` profile and one bucket with the `cold` profile are created for the service instance. 
{: shortdesc}

```
data "ibm_resource_group" "cos_group" {
  name = "cos-resource-group"
}

resource "ibm_resource_instance" "cos_instance" {
  name              = "cos-instance"
  resource_group_id = data.ibm_resource_group.cos_group.id
  service           = "cloud-object-storage"
  plan              = "standard"
  location          = "global"
}

resource "ibm_cos_bucket" "standard-ams03" {
  bucket_name = "a-standard-bucket-at-ams"
  resource_instance_id = ibm_resource_instance.cos_instance.id
  single_site_location = "ams03"
  storage_class = "standard"
}

resource "ibm_cos_bucket" "flex-us-south" {
  bucket_name = "a-flex-bucket-at-us-south"
  resource_instance_id = ibm_resource_instance.cos_instance.id
  region_location = "us-south"
  storage_class = "flex"
}

resource "ibm_cos_bucket" "cold-ap" {
  bucket_name = "a-cold-bucket-at-ap"
  resource_instance_id = ibm_resource_instance.cos_instance.id
  cross_region_location = "ap"
  storage_class = "cold"
}
```

### Input parameters
{: #hpvs-cos-bucket-input}

Review the input parameters that you can specify for your resource. 
{: shortdesc}

| Input parameter | Data type | Required/ optional | Description |
| ------------- |-------------| ----- | -------------- |
| `bucket_name` | String | Required | The name of the bucket. |
| `cross_region_location` | String | Optional | The location for a cross-regional bucket. Supported values are `us`, `eu`, and `ap`. If you this parameter, do not set `single_site_location` or `region_location` at the same time. |
| `key_protect` | String | Optional | The CRN of the {{site.data.keyword.keymanagementservicelong_notm}} instance where your root key is stored. You use this root key to encrypt data that is sent and stored in {{site.data.keyword.cos_full_notm}}. Before you can enable {{site.data.keyword.keymanagementservicelong_notm}} encryption, you must provision an instance of {{site.data.keyword.keymanagementservicelong_notm}} and authorize the service to access {{site.data.keyword.cos_full_notm}}. For more information, see [Server-Side Encryption with IBM Key Protect or Hyper Protect Crypto Services (SSE-KP)](/docs/services/cloud-object-storage?topic=cloud-object-storage-encryption#encryption-kp). |
| `resource_instance_id` | String | Required | The ID of the {{site.data.keyword.cos_full_notm}} service instance for which you want to create a bucket. |
| `region_location` | String | Optional | The location of a regional bucket. Supported values are `au-syd`, `eu-de`, `eu-gb`, `jp-tok`, `us-east`, `us-south`. If you set this parameter, do not set `single_site_location` or `cross_region_location` at the same time. 
| `single_site_location` | String | Optional | The location for a single site bucket. Supported values are: `ams03`, `che01`, `hkg02`, `mel01`, `mex01`, `mil01`, `mon01`, `osl01`, `sjc04`, `sao01`, `seo01`, and `tor01`. If you set this parameter, do not set `region_location` or `cross_region_location` at the same time. 
| `storage_class` | String | Required | The storage class that you want to use for the bucket. Supported values are `standard`, `vault`, `cold`, `flex`, and `smart`. For more information about storage classes, see [Use storage classes](/docs/services/cloud-object-storage?topic=cloud-object-storage-classes). 

Make sure that you set `cross_region_location`, `region_location`, or `single_site_location` to specify that location where you want to create the bucket. 
{: note}


### Output parameters
{: #hpvs-cos-bucket-output}

Review the output parameters that you can access after your resource is created. 
{: shortdesc}

| Output parameter | Data type | Description |
| ------------- |-------------| -------------- |
| `crn` | String | The CRN of the bucket. |
| `cross_region_location` | String | The location if you created a cross-regional bucket. |
| `id` | String | The ID of the bucket. | 
| `key_protect` | String | The CRN of the {{site.data.keyword.keymanagementservicelong_notm}} instance that you use to encrypt your data in {{site.data.keyword.cos_full_notm}}. |
| `region_location` | String | The location if you created a regional bucket. |
| `resource_instance_id` | String | The ID of {site.data.keyword.cos_full_notm}} instance. | 
| `single_site_location` | String | The location if you created a single site bucket. |
| `storage_class` | String | The storage class of the bucket. |

### Import

The `ibm_cos_bucket` resource can be imported using the `id`. The ID is formed from the `CRN` (Cloud Resource Name), the `bucket type` which must be `ssl` for single_site_location, `rl` for region_location or `crl` for cross_region_location, and the bucket location. The `CRN` and bucket location can be found on the portal.

id = $CRN:meta:$buckettype:$bucketlocation


```
$ terraform import ibm_cos_bucket.mybucket <crn>

$ terraform import ibm_cos_bucket.mybucket crn:v1:bluemix:public:cloud-object-storage:global:a/4ea1882a2d3401ed1e459979941966ea:31fa970d-51d0-4b05-893e-251cba75a7b3:bucket:mybucketname:meta:crl:eu
```
