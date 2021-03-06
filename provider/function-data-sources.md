---

copyright:
  years: 2017, 2020
lastupdated: "2020-03-31"

keywords: terraform provider plugin, terraform functions, terraform openwhisk, terraform function action

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

# Functions data sources
{: #function-data-sources}

Review the data sources that you can use to retrieve information about your {{site.data.keyword.cos_full_notm}} instance. All data sources are imported as read-only information. You can reference the output parameters for each data source by using Terraform interpolation syntax. 
{: shortdesc}

Before you start working with your data source, make sure to review the [required parameters](/docs/terraform?topic=terraform-provider-reference#required-parameters) that you need to specify in the `provider` block of your Terraform configuration file. 
{: important}


## `ibm_function_action`
{: #fn-action}

Retrieve information about an action. 
{: shortdesc}

### Sample Terraform code
{: #fn-action-sample}

The following example retrieves information about the `myaction` action. 
{: shortdesc}

```
data "ibm_function_action" "nodehello" {
    name = "myaction"		  
}
```

### Input parameters
{: #fn-action-input}

Review the input parameters that you can specify for your data source. 
{: shortdesc}

| Input parameter | Data type | Required/ optional | Description |
| ------------- |-------------| ----- | -------------- |
|`name`|String|Required|The name of the action.|
{: caption="Table. Available input parameters" caption-side="top"}

### Output parameters
{: #fn-action-output}

Review the output parameters that you can access after you retrieved your data source. 
{: shortdesc}

| Output parameter | Data type | Description |
| ------------- |-------------| -------------- |
|`id`| String| The ID of the action.|
|`version`|String|The version of the action. |
|`annotations`|List|Annotations to describe the action, including those set by you or by IBM Cloud Functions.|
|`parameters`|List| Parameters passed to the action when the action is invoked, including those set by you or by IBM Cloud Functions.|
|`limits`|A list of objects| A nested block to describe assigned limits. |
|`limits.timeout`|Integer|The timeout limit to terminate the action, specified in milliseconds. Default value: `60000`.    |
|`limits.memory`|Integer|The maximum memory for the action, specified in megabytes. Default value: `256`.    |
|`limits.log_size`|Integer|The maximum log size for the action, specified in megabytes. Default value: `10`.|
|`exec`|List of objects|A nested block to describe executable binaries.     |
|`exec.image`|String|When using the `blackbox` executable, the name of the container image name.    |
|`exec.init`|String|When using `nodejs`, the optional reference to the compressed file.    |
|`exec.code`|String|When not using the `blackbox` executable, the code to execute.     |
|`exec.kind`|String|The type of action. Accepted values: `nodejs`, `blackbox`, `swift`, `sequence`.    |
|`exec.main`|String|The name of the action entry point (function or fully-qualified method name, when applicable).    |
|`exec.components`|List|The list of fully qualified actions.|
|`publish`|Boolean|Action visibility.|
{: caption="Table 1. Available output parameters" caption-side="top"}

## `ibm_function_package`
{: #fn-package}

Retrieve information about an existing IBM Cloud Functions OpenWhisk package. 
{: shortdesc}

### Sample Terraform code
{: #fn-package-sample}

The following example retrieves information about the `mypackage` package. 
{: shortdesc}

```
data "ibm_function_package" "package" {
  name = "mypackage"
}
```

### Input parameters
{: #fn-package-input}

Review the input parameters that you can specify for your data source. 
{: shortdesc}

| Input parameter | Data type | Required/ optional | Description |
| ------------- |-------------| ----- | -------------- |
|`name`|String|Required|The name of the package.|
{: caption="Table. Available input parameters" caption-side="top"}

### Output parameters
{: #fn-package-output}

Review the output parameters that you can access after you retrieved your data source. 
{: shortdesc}

| Output parameter | Data type | Description |
| ------------- |-------------| -------------- |
|`id`|String|The ID of the package.|
|`version`|String|Semantic version of the package.|
|`publish`|Boolean|Package visibility.|
|`annotations`|List|All annotations to describe the package, including those set by you or by IBM Cloud Functions.|
|`parameters`|List|All parameters passed to the package, including those set by you or by IBM Cloud Functions.|
{: caption="Table 1. Available output parameters" caption-side="top"}


## `ibm_function_rule`
{: #fn-rule}

Retrieve information about an IBM Cloud Functions rule.
{: shortdesc}

### Sample Terraform code
{: #fn-rule-sample}

The following example retrieves information about the `myrule` rule. 

```
data "ibm_function_rule" "rule" {
	name = "myrule"
}
```

### Input parameters
{: #fn-rule-input}

Review the input parameters that you can specify for your data source. 
{: shortdesc}

| Input parameter | Data type | Required/ optional | Description |
| ------------- |-------------| ----- | -------------- |
|`name`|String|Required|The name of the rule.|
{: caption="Table. Available input parameters" caption-side="top"}

### Output parameters
{: #fn-rule-output}

Review the output parameters that you can access after you retrieved your data source. 
{: shortdesc}

| Output parameter | Data type | Description |
| ------------- |-------------| -------------- |
|`id`|String|The ID of the rule.|
|`publish`|Boolean|Rule visibility.|
|`version`|String|Semantic version of the rule.|
|`status`|String|The status of the rule.|
|`trigger_name`|String|The name of the trigger that the rule belongs to.|
|`action_name`|String|The name of the action that the rule belongs to.|
{: caption="Table 1. Available output parameters" caption-side="top"}


## `ibm_function_trigger`
{: #fn-trigger}

Retrieve information about an IBM Cloud Functions trigger. 
{: shortdesc}

### Sample Terraform code
{: #fn-trigger-sample}

The following example retrieves information about the `mytrigger` trigger. 

```
data "ibm_function_trigger" "trigger" {
  name = "mytrigger"		  
}
```

### Input parameters
{: #fn-trigger-input}

Review the input parameters that you can specify for your data source. 
{: shortdesc}

| Input parameter | Data type | Required/ optional | Description |
| ------------- |-------------| ----- | -------------- |
|`name`|String|Required|The name of the trigger.|
{: caption="Table. Available input parameters" caption-side="top"}

### Output parameters
{: #fn-trigger-output}

Review the output parameters that you can access after you retrieved your data source. 
{: shortdesc}

| Output parameter | Data type | Description |
| ------------- |-------------| -------------- |
|`id`|String|The ID of the trigger.|
|`publish`|Boolean|Trigger visibility.|
|`version`|String|Semantic version of the trigger.|
|`annotations`|List|All annotations to describe the trigger, including those set by you or by IBM Cloud Functions.|
|`parameters`|List|All parameters passed to the trigger, including those set by you or by IBM Cloud Functions.|
{: caption="Table 1. Available output parameters" caption-side="top"}
