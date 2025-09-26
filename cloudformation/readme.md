# Cloudformation

AWS CloudFormation gives developers and systems administrators an easy way to create and manage a collection of related AWS resources, provisioning and updating them in an orderly and predictable fashion.

You can use AWS CloudFormation sample templates or create your own templates to describe the AWS resources, and any associated dependencies or runtime parameters, required to run your application. You don’t need to figure out the order for provisioning AWS services or the subtleties of making those dependencies work. AWS CloudFormation takes care of this for you. After the AWS resources are deployed, you can modify and update them in a controlled and predictable way, in effect applying version control to your AWS infrastructure the same way you do with your software.

You can deploy and update a template and its associated collection of resources (called a stack) by using the AWS Management Console, AWS Command Line Interface, or APIs. AWS CloudFormation is available at no additional charge, and you pay only for the AWS resources needed to run your applications.

An  _AWS CloudFormation template_  is a declaration of the AWS resources that make up a  _stack_. The template is stored as a text file in either  _JavaScript Object Notation (JSON)_  or  _YAML_  format. Because they are just text files, you can create and edit them in any text editor and manage them in your source control system with the rest of your source code.

In an AWS CloudFormation template you can declare the following six top-level objects:

-   AWSTemplateFormatVersion and Description: Optional identifiers and human-readable purpose for the template, aiding documentation and versioning in repositories.
    
-   Parameters: Inputs that make templates reusable; each parameter has a Type and can include constraints like AllowedValues, MinLength, and Default.
    
-   Mappings: Static key–value lookups used for conditional selections (for example, per-Region AMI IDs) without code branching.
    
-   Conditions: Boolean expressions to create or configure resources only when certain criteria are met (e.g., IsProd).
    
-   Resources: The heart of the template; each resource must have a Type (AWS::Service::Resource) and Properties specific to that type.
    
-   Outputs: Export values (like VPC IDs) for visibility or cross-stack references via exports.
    
-   Metadata: Structured, arbitrary data attached to resources or the template, commonly used by helper tools or transforms.
    

The only required top-level object is the **Resources** object, which must declare at least one resource.

  ```
AWSTemplateFormatVersion: 2010-09-09
Description: Basic S3 Bucket CloudFormation template
Resources:
  S3BucketForWebsiteContent:
    Type: AWS::S3::Bucket
    Properties:
      PublicAccessBlockConfiguration:
        BlockPublicAcls: false
        BlockPublicPolicy: false
        IgnorePublicAcls: false
        RestrictPublicBuckets: false
Outputs:
  BucketName:
    Value: !Ref S3BucketForWebsiteContent
    Description: Name of the newly created Amazon S3 Distribution
```

Each resource declared in the list of resources contains a type, which defines the kind of AWS resource you want to create.
Usually, a property for a resource is simply a **string value**. For example, the following template specifies a canned ACL for the **PublicAccessBlockConfiguration** property of the bucket.
```
AWS::ProductIdentifier::ResourceType
```

Key resource attributes in AWS CloudFormation are special settings added alongside a resource’s Type and Properties to control lifecycle, ordering, and behavior during updates and deletions.

-   DependsOn: Explicitly declare creation order when implicit dependencies are not enough, ensuring a resource is created after another.
    
-   Metadata: Attach JSON/YAML data to resources for tooling or documentation needs. Metadata attaches arbitrary structured data (YAML/JSON) to a resource to assist tools, hooks, or documentation workflows. A common pattern is using AWS::CloudFormation::Init to bootstrap instances with packages, files, and services.

```
Resources:
  WebInstance:
    Type: AWS::EC2::Instance
    Metadata:
      AWS::CloudFormation::Init:
        config:
          packages:
            yum:
              httpd: []
          files:
            /var/www/html/index.html:
              content: "Hello from cfn-init"
              mode: '000644'
              owner: ec2-user
              group: ec2-user
    Properties:
      ImageId: ami-0123456789abcdef0
      InstanceType: t3.micro

``` 
    
-   DeetionPolicy: Control what happens to a resource on stack delete (Retain, Snapshot, Delete).
	-    Retain: keep the resource and its data, preventing accidental loss.
	- Snapshot: take a point-in-time snapshot (where supported, e.g., certain databases or volumes) before deletion.
	- Delete: remove the resource (default behavior if not specified).
    
-   UpdateReplacePolicy: Specify behavior when updates require replacement (Retain, Snapshot, Delete).
In the  **Outputs**  section, you can optionally define custom values that are returned in response to the  **cfn-describe-stacks**  command. These output values can include information based on literals, resources, parameters, pseudo parameters, and intrinsic functions.

Once a stack is deleted the resources created with the stack are also deleted. For example, if you created a RDS database or an S3 bucket with the template, once you delete the created stack, by default, the bucket will be deleted. To avoid that, set DeletionPolicy.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Basic S3 Bucket CloudFormation template
Resources:
  S3BucketForWebsiteContent:
    Type: AWS::S3::Bucket
    DeletionPolicy: Retain
    Properties:
      PublicAccessBlockConfiguration:
        BlockPublicAcls: false
        BlockPublicPolicy: false
        IgnorePublicAcls: false
        RestrictPublicBuckets: false
Outputs:
  BucketName:
    Value: !Ref S3BucketForWebsiteContent
    Description: Name of the newly created Amazon S3 Distribution

```
Intrinsic functions in AWS CloudFormation are built-in helpers used inside templates to dynamically compose values at deployment time, reference resources and parameters, apply conditional logic, and reuse data without external scripts.


-   Ref: Returns  a parameter’s value or a  resource’s primary  identifier,  forming the  simplest way  to connect template  parts without  hardcoding.
    
-   Fn::GetAtt:  Retrieves a  specific attribute  from a declared  resource (for  example, Arn, DomainName, AllocationId) to pass downstream.
    
-   Fn::Sub: Performs  string interpolation, letting templates  embed parameters, attributes, and pseudo-parameters to  construct  names and  ARNs.

```
Parameters:
  Env:
    Type: String
    Default: dev

Resources:
  Queue:
    Type: AWS::SQS::Queue

Outputs:
  QueueName:
    Value: !Ref Queue
  QueueArn:
    Value: !GetAtt Queue.Arn
  LabeledName:
    Value: !Sub '${Env}-sqs-${Queue}'

```

-   Fn::If: Chooses between two values based on a defined Condition.
    
-   Fn::Equals, Fn::And, Fn::Or, Fn::Not: Logical operators to build reusable Conditions.
    
-   Condition on resources: Creates resources only when a Condition evaluates to true.
```
Parameters:
  EnableNat:
    Type: String
    AllowedValues: [true, false]
    Default: false

Conditions:
  CreateNat: !Equals [ !Ref EnableNat, true ]

Resources:
  NatGateway:
    Type: AWS::EC2::NatGateway
    Condition: CreateNat
    Properties:
      AllocationId: !GetAtt NatEip.AllocationId
      SubnetId: !Ref PublicSubnet

Outputs:
  NatInfo:
    Value: !If [ CreateNat, "NAT enabled", "NAT disabled" ]
```
-   Fn::ImportValue: Consumes exported Outputs from another stack for modular architectures.
    
-   Exports: Outputs with Export names that other stacks can refer to.
    


```
# Producer
Outputs:
  VpcId:
    Value: !Ref Vpc
    Export:
      Name: GlobalVpcId

# Consumer
Resources:
  AppSg:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: shared sg
      VpcId: !ImportValue GlobalVpcId

 ```
-   Pseudo-parameters: Built-ins like AWS::Region, AWS::AccountId, and AWS::StackName for environment-aware values.

Bootstrap means the initial process of getting something ready to run by automatically installing, configuring, and starting what it needs. -   A bootstrap often runs at first boot to install packages, write config files, create users, and start services so the machine is production‑ready.


AWS CloudFormation can download packages (such as RPMs or RubyGems), as well as reference individual files and expand .zip and .tar files to create the application artifacts on the Amazon EC2 instance.

-   cfn-init: Reads the metadata block and applies packages, files, commands, and services in order, often invoked from user data at first boot. Applies what is defined under the AWS::CloudFormation::Init metadata of a resource, in a prescribed order: packages → groups/users → sources → files → commands → services. This turns a fresh instance into a configured server deterministically.
```
Resources:
  WebInstance:
    Type: AWS::EC2::Instance
    Metadata:
      AWS::CloudFormation::Init:
        configSets:
          default:
            - base
            - app
        base:
          packages:
            yum:
              httpd: []
          files:
            /var/www/html/index.html:
              content: "Hello from cfn-init"
              mode: '000644'
              owner: ec2-user
              group: ec2-user
        app:
          services:
            sysvinit:
              httpd:
                enabled: true
                ensureRunning: true
    Properties:
      ImageId: ami-0123456789abcdef0
      InstanceType: t3.micro
      UserData:
        Fn::Base64: !Sub |
          #!/bin/bash -xe
          yum install -y aws-cfn-bootstrap
          /opt/aws/bin/cfn-init -v \
            --stack ${AWS::StackName} \
            --resource WebInstance \
            --region ${AWS::Region} \
            --configsets default

```
    
-   cfn-signal: Reports success or failure back to the stack, allowing the deployment to wait until bootstrapping finishes before moving on. Notifies the stack whether the instance finished bootstrapping successfully, letting CloudFormation wait before marking the resource complete. This prevents the stack from proceeding while software setup is still in progress.
```
UserData:
  Fn::Base64: !Sub |
    #!/bin/bash -xe
    yum install -y aws-cfn-bootstrap
    /opt/aws/bin/cfn-init -v \
      --stack ${AWS::StackName} \
      --resource WebInstance \
      --region ${AWS::Region} \
      --configsets default
    /opt/aws/bin/cfn-signal \
      --stack ${AWS::StackName} \
      --resource WebInstance \
      --region ${AWS::Region} \
      --exit-code $?

Resources:
  WebInstance:
    Type: AWS::EC2::Instance
    CreationPolicy:
      ResourceSignal:
        Count: 1
        Timeout: PT15M


```
    
-   cfn-hup: A small daemon that watches for metadata changes and re-runs cfn-init when updates are detected, enabling in-place config tweaks without replacing the instance. A lightweight daemon on the instance that watches the resource’s Metadata for changes after the stack is updated. When it detects changes, it triggers a hook (commonly re-running cfn-init) to apply updates without replacing the instance. Rolling out config tweaks (files, package versions, service flags) by updating template metadata; instances “pull” and apply the change automatically.


The template enables and configures the  **cfn-hup**  daemon to listen for changes to the configuration defined in the  **metadata**  for the Amazon EC2 instance. By using the  **cfn-hup**  daemon, you can update application software, such as the version of Apache or PHP, or you can update the PHP application file itself from AWS CloudFormation.

```
    Metadata:
      Comment: Install a simple PHP application
      AWS::CloudFormation::Init:
        config:
        ...
              
        files:
        ...

            /etc/cfn/hooks.d/cfn-auto-reloader.conf:
              content: !Sub |
                [cfn-auto-reloader-hook]
                triggers=post.update
                path=Resources.WebServerHost.Metadata.AWS::CloudFormation::Init
                action=/opt/aws/bin/cfn-init -s ${AWS::StackName} -r WebServerHost --region ${AWS::Region}
                runas=root
              mode: 000644
              owner: apache
              group: apache

            /etc/cfn/cfn-hup.conf:
              content: !Sub |
                [main]
                stack=${AWS::StackId}
                region=${AWS::Region}
                interval=1
              mode: 000400
              owner: root
              group: root

        services:
          sysvinit:
            httpd:
              enabled: true
              ensureRunning: true
            cfn-hup:
              enabled: true
              ensureRunning: true
              files:
                - /etc/cfn/cfn-hup.conf
                - /etc/cfn/hooks.d/cfn-auto-reloader.conf
```
