---

copyright:
  years: 2017, 2020
lastupdated: "2020-05-27"

keywords: terraform provider plugin, terraform functions, terraform openwhisk, terraform function action, terraform serverless

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

# Functions resources
{: #function-resources}

Review the {{site.data.keyword.openwhisk_short}} resources that you can create, update, or delete.
{: shortdesc}

Before you start working with your resource, make sure to review the [required parameters](/docs/terraform?topic=terraform-provider-reference#required-parameters) that you need to specify in the `provider` block of your Terraform configuration file. 
{: important}


## `ibm_function_action`
{: #fn-action}

Create, update, or delete a {{site.data.keyword.openwhisk_short}} action. Actions are stateless code snippets that run on the {{site.data.keyword.openwhisk_short}} platform. An action can be written as a JavaScript, Swift, or Python function, a Java method, or a custom executable program packaged in a Docker container. To bundle and share related actions, use the `function_package` resource.


### Sample Terraform code
{: #fn-action-sample}

####  Simple JavaScript action
{: #js-action}

The following example creates a JavaScript action. 
{: shortdesc}

```
resource "ibm_function_action" "nodehello" {
  name = "myaction"

  exec {
    kind = "nodejs:6"
    code = file("hellonode.js")
  }
}

```
#### Passing parameters to an action
{: #parameter-action}

The following example shows how to pass parameters to an action. 
{: shortdesc}

```
resource "ibm_function_action" "nodehellowithparameter" {
  name = "hellonodeparam"

  exec {
    kind = "nodejs:6"
    code = file("hellonodewithparameter.js")
  }

  user_defined_parameters = <<EOF
        [
    {
        "key":"place",
        "value":"India"
    }
        ]
        EOF
```

#### Packaging an action as a Node.js module
{: #action-package}

The following example packages a JavaScript action to a module. 
{: shortdesc}

``` 
resource "ibm_function_action" "nodezip" {
  name = "nodezip"

  exec {
    kind = "nodejs:6"
    code = base64encode(file("nodeaction.zip"))
  }
}

```

#### Creating action sequences
{: #action-sequence}

The following example creates an action sequence. 
{: shortdesc}

``` 
resource "ibm_function_action" "swifthello" {
  name = "actionsequence"

  exec {
    kind = "sequence"
    components = ["/whisk.system/utils/split","/whisk.system/utils/sort"]
  }
}

```

### Creating Docker actions
{: #docker-action}

The following example creates a Docker action. 
{: shortdesc}

``` 
resource "ibm_function_action" "swifthello" {
  name = "dockeraction"

  exec {
    kind = "janesmith/blackboxdemo"
    image = file("helloSwift.swift")
  }
}

```

### Input parameters
{: #fn-action-input}

Review the input parameters that you can specify for your resource. 
{: shortdesc}

| Input parameter | Data type | Required/ optional | Description |
| ------------- |-------------| ----- | -------------- |
|`name`|String|Required|The name of the action.|
|`limits`|List of objects|Optional|A nested block to describe assigned limits. |
|`limits.timeout`|Integer|Optional|The timeout limit to terminate the action, specified in milliseconds. Default value: `60000`.    |
|`limits.memory`|Integer|Optional|The maximum memory for the action, specified in MBs. Default value: `256`.    |
|`limits.log_size`|Integer|Optional|The maximum log size for the action, specified in MBs. Default value: `10`.|
|`exec`|List of objects|Required|A nested block to describe executable binaries.  |
|`exec.image`|String|Optional| When using the `blackbox` executable, the name of the container image name.        **NOTE**: Conflicts with `exec.components`, `exec.code`.    |
|`exec.init`|String|Optional| When using `nodejs`, the optional zipfile reference.        **NOTE**: Conflicts with `exec.components`, `exec.image`.    |
|`exec.code`|String|Optional| When not using the `blackbox` executable, the code to execute.       **NOTE**: Conflicts with `exec.components`, `exec.image`.    |
|`exec.kind`|String|Required|The type of action. You can find supported kinds in the [IBM Cloud Functions docs](/docs/openwhisk?topic=openwhisk-runtimes).    |
|`exec.main`|String|Optional|The name of the action entry point (function or fully-qualified method name, when applicable).       **NOTE**: Conflicts with `exec.components`, `exec.image`.    |
|`exec.components`|String|Optional|The list of fully qualified actions. **NOTE**: Conflicts with `exec.code`, `exec.image`.|
|`publish`|Boolean|Optional|Action visibility.|
|`user_defined_annotations`|String|Optional|Annotations defined in key value format.|
|`user_defined_parameters`|String|Optional|Parameters defined in key value format. Parameter bindings included in the context passed to the action. Cloud Function backend/API.|
{: caption="Table. Available input parameters" caption-side="top"}

### Output parameters
{: #fn-action-output}

Review the output parameters that you can access after your resource is created. 
{: shortdesc}

| Output parameter | Data type | Description |
| ------------- |-------------| -------------- |
|`id`|String|The ID of the new action.|
|`version`|String|Semantic version of the item.|
|`annotations`|List|All annotations to describe the action, including those set by you or by IBM Cloud Functions.|
|`parameters`|List|All parameters passed to the action when the action is invoked, including those set by you or by IBM Cloud Functions.|
{: caption="Table 1. Available output parameters" caption-side="top"}

### Import
{: #fn-action-import}

`ibm_function_action` can be imported using the ID.

Example:

```
terraform import ibm_function_action.nodeAction hello
```
{: pre}

## `ibm_function_package`
{: #fn-package}

Create, update, or delete an IBM Cloud Functions package. You can the packages to bundle together a set of related actions, and share them with others. To create actions, use the `function_action` resource.

### Sample Terraform code
{: #fn-package-sample}

#### Create a package
{: #create-package}

The following example creates the `mypackage` package. 

```
resource "ibm_function_package" "package" {
  name = "mypackage"

  user_defined_annotations = <<EOF
        [
    {
        "key":"description",
        "value":"Count words in a string"
    },
    {
        "key":"sampleOutput",
        "value": {
                        "count": 3
                }
    },
    {
        "key":"final",
        "value": [
                        {
                                "description": "A string",
                                "name": "payload",
                                "required": true
                        }
                ]
    }
]
EOF
}
```

#### Create a package using a binding
{: #package-service-binding}

The following example shows how to bind a package. 
{: shortdesc}

``` 
resource "ibm_function_package" "bindpackage" {
  name              = "bindalaram"
  bind_package_name = "/whisk.system/alarms/alarm"

  user_defined_parameters = <<EOF
        [
    {
        "key":"cron",
        "value":"0 0 1 0 *"
    },
    {
        "key":"trigger_payload ",
        "value":"{'message':'bye old Year!'}"
    },
    {
        "key":"maxTriggers",
        "value":1
    },
    {
        "key":"userdefined",
        "value":"test"
    }
]
EOF
}

```

### Input parameters
{: #fn-package-input}

Review the input parameters that you can specify for your resource. 
{: shortdesc}

| Input parameter | Data type | Required/ optional | Description |
| ------------- |-------------| ----- | -------------- |
|`name`|String|Required|The name of the package.|
|`publish`|Boolean|Optional|Package visibility.|
|`user_defined_annotations`|String|Optional|Annotations defined in key value format.|
|`user_defined_parameters`|String| Optional|Parameters defined in key value format. Parameter bindings included in the context passed to the package.|
|`bind_package_name`|String|Optional| Name of package to be bound.|
{: caption="Table. Available input parameters" caption-side="top"}

### Output parameters
{: #fn-package-output}

Review the output parameters that you can access after your resource is created. 
{: shortdesc}

| Output parameter | Data type | Description |
| ------------- |-------------| -------------- |
|`id`|String|The ID of the new package.|
|`version`|String|Semantic version of the item.|
|`annotations`|String|All annotations to describe the package, including those set by you or by IBM Cloud Functions.|
|`parameters`|String|All parameters passed to the package, including those set by you or by IBM Cloud Functions.### Import`ibm_function_package` can be imported using the ID.Example:```$ terraform import ibm_function_package.sample hello```|
{: caption="Table 1. Available output parameters" caption-side="top"}




## `ibm_function_rule`
{: #fn-rule}

Create, update, or delete an IBM Cloud Functions rule. Events from external and internal event sources are channeled through a trigger, and rules allow your actions to react to these events. To set triggers, use the `function_trigger` resource.
{: shortdesc}

### Sample Terraform code
{: #fn-rule-sample}

The following example creates a rule for an action. 
{: shortdesc}

```
resource "ibm_function_action" "action" {
  name = "hello"

  exec {
    kind = "nodejs:6"
    code = file("test-fixtures/hellonode.js")
  }
}

resource "ibm_function_trigger" "trigger" {
  name = "alarmtrigger"

  feed = [
    {
      name = "/whisk.system/alarms/alarm"

      parameters = <<EOF
                    [
                        {
                            "key":"cron",
                            "value":"0 */2 * * *"
                        }
                    ]
                EOF
    },
  ]
}

resource "ibm_function_rule" "rule" {
  name         = "alarmrule"
  trigger_name = ibm_function_trigger.trigger.name
  action_name  = ibm_function_action.action.name
}

```

### Input parameters
{: #fn-rule-input}

Review the input parameters that you can specify for your resource. 
{: shortdesc}

| Input parameter | Data type | Required/ optional | Description |
| ------------- |-------------| ----- | -------------- |
|`name`|String|Required|The name of the rule.|
|`trigger_name`|String|Required|The name of the trigger.|
|`action_name`|String|Required|The name of the action.|
{: caption="Table. Available input parameters" caption-side="top"}

### Output parameters
{: #fn-rule-output}

Review the output parameters that you can access after your resource is created. 
{: shortdesc}

| Output parameter | Data type | Description |
| ------------- |-------------| -------------- |
|`id`|String|The ID of the new rule.|
|`publish`|Boolean|Rule visibility.|
|`version`|String|Semantic version of the item.|
|`status`|String|The status of the rule.|
{: caption="Table 1. Available output parameters" caption-side="top"}

### Import
{: #fn_rule-import}

`ibm_function_rule` can be imported using the ID.

Example: 
```
terraform import ibm_function_rule.sampleRule alarmrule
```
{: pre}


## `ibm_function_trigger`
{: #fn-trigger}

Create, update, or delete an IBM Cloud Functions trigger. Events from external and internal event sources are channeled through a trigger, and rules allow your actions to react to these events. To set rules, use the `function_rule` resource.
{: shortdesc}

### Sample Terraform code
{: #fn-trigger-sample}

#### Creating triggers
{: #create-trigger}

The following example creates the `mytrigger` trigger. 
{: shortdesc}

```
resource "ibm_function_trigger" "trigger" {
  name = "mytrigger"

  user_defined_parameters = <<EOF
                        [
                                {
                                        "key":"place",
                                        "value":"India"
                           }
                   ]
           EOF

  user_defined_annotations = <<EOF
           [
                   {
                          "key":"Description",
                           "value":"Sample code to display hello"
                  }
          ]
  EOF
}
```

#### Creating a trigger feed
{: #create-trigger-feed}

The following example creates a feed for the `alarmFeed` trigger. 
{: shortdesc}

```
resource "ibm_function_trigger" "feedtrigger" {
  name = "alarmFeed"

  feed = [
    {
      name = "/whisk.system/alarms/alarm"

      parameters = <<EOF
                [
                        {
                                "key":"cron",
                                "value":"0 */2 * * *"
                        }
                ]
                EOF
    },
  ]

  user_defined_annotations = <<EOF
                 [
         {
                 "key":"sample trigger",
                 "value":"Trigger for hello action"
         }
                 ]
                 EOF
}
```


### Input parameters
{: #fn-trigger-input}

Review the input parameters that you can specify for your resource. 
{: shortdesc}

| Input parameter | Data type | Required/ optional | Description |
| ------------- |-------------| ----- | -------------- |
|`name`|String|Required|The name of the trigger.|
|`feed`|List|Optional| A nested block to describe the feed.   |
|`feed.name`|String|Required|Trigger feed `ACTION_NAME`.    |
|`feed.parameters`|String|Optional|Parameters definitions in key value format. Parameter bindings are included in the context and passed when the action is invoked.|
|`user_defined_annotations`|String|Optional| Annotation definitions in key value format.|
|`user_defined_parameters`|String|Optional|Parameters definitions in key value format. Parameter bindings are included in the context and passed to the trigger.|
{: caption="Table. Available input parameters" caption-side="top"}

### Output parameters
{: #fn-trigger-output}

Review the output parameters that you can access after your resource is created. 
{: shortdesc}

| Output parameter | Data type | Description |
| ------------- |-------------| -------------- |
|`id`|String|The ID of the new trigger.|
|`publish`|Boolean|Trigger visibility.|
|`version`|String|Semantic version of the item.|
|`annotations`|String|All annotations to describe the trigger, including those set by you or by IBM Cloud Functions.|
|`parameters`|String|All parameters passed to the trigger, including those set by you or by IBM Cloud Functions.|
{: caption="Table 1. Available output parameters" caption-side="top"}

### Import
{: #fn-trigger-import}

`ibm_function_trigger` can be imported using the ID.

Example:
```
terraform import ibm_function_trigger.trigger alaram
```
{: pre}
