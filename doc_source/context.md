# 런타임 컨텍스트<a name="context"></a>

컨텍스트 값은 스택 또는 컨스트럭트와 연관될 수 있는 키\-값의 쌍입니다\. AWS CDK는 컨텍스트를 사용하여 계정의 가용 영역 또는 인스턴스를 시작하는 데 사용되는 Amazon 머신 이미지 \(AMI\) ID와 같은 AWS 계정의 정보를 캐시 합니다\. [기능 플래그](featureflags.md)도 컨텍스트 값입니다. 앱이나 컨스트럭트에서 사용할 고유한 컨텍스트 값을 만들 수 있습니다.

## 컨스트럭트의 컨텍스트<a name="context_construct"></a>

컨텍스트 값은 6 가지 방법으로 AWS CDK 앱에 제공할 수 있습니다:
+ 현재 AWS 계정에서 자동으로\.
+ Cdk cli 명령줄의 \-\-context 옵션을 통해서\.
+ 프로젝트의 `cdk.context.json` 파일로부터\.
+ 프로젝트의 `cdk.json` 파일의 `context` 키에서\.
+ `~/cdk.json` 파일의 `context` 키에서\.
+ AWS CDK 앱에서 `construct.node.setContext` 메서드를 사용하여\.

AWS CDK는 프로젝트 내의 `cdk.context.json` 파일에 AWS 계정에서 검색 한 컨텍스트 값을 캐시 하여 저장합니다\. 이 방법은 배포에 예기치 않은 변경이 발생하지 않도록 하는데, 예를 들면, 새로운 Amazon Linux AMI가 릴리스되어 Auto Scaling 그룹을 변경하는 경우입니다\. AWS CDK는 목록에 있는 다른 파일에는 컨텍스트 데이터를 쓰지 않습니다\.

프로젝트의 컨텍스트 파일은 나머지 응용 프로그램과 함께 버전 제어하게 배치하는 것이 좋습니다. 해당 정보는 앱 상태의 일부로 취급하여 항상 일관성 있게 합성하고 배포할 수 있어야 합니다 \.

컨텍스트의 값은 컨텍스트를 생성하는 컨스트럭트의 범위 안에 있어야 합니다; 컨텍스트의 값은 자식 컨스트럭트에서는 보이고 하고, 형제 컨스트럭트에서는 보이지 않습니다\. AWS CDK Toolkit \(the cdk command\)를 통해  파일에서 자동으로 읽어오거나, \-\-context 옵션을 사용하여 설정된 컨텍스트의 값은, `App` 컨스트럭트에 암시적으로 설정되며, 앱 내부에 있는 모든 컨스트럭트에서 볼 수 있습니다\.

`construct.node.tryGetContext` 메서드를 사용하여 컨텍스트 값을 얻을 수 있습니다\. 요청된 엔트리가 현재 컨스트럭트나 부모 컨스트럭트에서 발견되지 않으면 `undefined` 가 반환됩니다\. \(혹은 Python의 `None`처럼 이 언어에 따라 그에 준하는 값으로 설정됩니다\.\)

## Context methods<a name="context_methods"></a>

The AWS CDK supports several context methods that enable AWS CDK apps to get contextual information\. For example, you can get a list of Availability Zones that are available in a given AWS account and AWS Region, using the [stack\.availabilityZones](https://docs.aws.amazon.com/cdk/api/latest/docs/@aws-cdk_core.Stack.html#availabilityzones) method\.

The following are the context methods:

[HostedZone\.fromLookup](https://docs.aws.amazon.com/cdk/api/latest/docs/@aws-cdk_aws-route53.HostedZone.html#static-from-wbr-lookupscope-id-query)  
Gets the hosted zones in your account\.

[stack\.availabilityZones](https://docs.aws.amazon.com/cdk/api/latest/docs/@aws-cdk_core.Stack.html#availabilityzones)  
Gets the supported Availability Zones\.

[StringParameter\.valueFromLookup](https://docs.aws.amazon.com/cdk/api/latest/docs/@aws-cdk_aws-ssm.StringParameter.html#static-value-wbr-from-wbr-lookupscope-parametername)  
Gets a value from the current Region's Amazon EC2 Systems Manager Parameter Store\.

[Vpc\.fromLookup](https://docs.aws.amazon.com/cdk/api/latest/docs/@aws-cdk_aws-ec2.Vpc.html#static-from-wbr-lookupscope-id-options)  
Gets the existing Amazon Virtual Private Clouds in your accounts\.

[LookupMachineImage](https://docs.aws.amazon.com/cdk/api/latest/docs/@aws-cdk_aws-ec2.LookupMachineImage.html)  
Looks up a machine image for use with a NAT instance in an Amazon Virtual Private Cloud\.

If a given context information isn't available, the AWS CDK app notifies the AWS CDK CLI that the context information is missing\. The CLI then queries the current AWS account for the information, stores the resulting context information in the `cdk.context.json` file, and executes the AWS CDK app again with the context values\.

Don't forget to add the `cdk.context.json` file to your source control repository to ensure that subsequent synth commands will return the same result, and that your AWS account won't be needed when synthesizing from your build system\.

## Viewing and managing context<a name="context_viewing"></a>

Use the cdk context command to view and manage the information in your `cdk.context.json` file\. To see this information, use the cdk context command without any options\. The output should be something like the following\.

```
Context found in cdk.json:
      
┌───┬─────────────────────────────────────────────────────────────┬─────────────────────────────────────────────────────────┐
│ # │ Key                                                         │ Value                                                   │
├───┼─────────────────────────────────────────────────────────────┼─────────────────────────────────────────────────────────┤
│ 1 │ availability-zones:account=123456789012:region=eu-central-1 │ [ "eu-central-1a", "eu-central-1b", "eu-central-1c" ]   │
├───┼─────────────────────────────────────────────────────────────┼─────────────────────────────────────────────────────────┤
│ 2 │ availability-zones:account=123456789012:region=eu-west-1    │ [ "eu-west-1a", "eu-west-1b", "eu-west-1c" ]            │
└───┴─────────────────────────────────────────────────────────────┴─────────────────────────────────────────────────────────┘

Run cdk context --reset KEY_OR_NUMBER to remove a context key. If it is a cached value, it will be refreshed on the next cdk synth.
```

To remove a context value, run cdk context \-\-reset, specifying the value's corresponding key or number\. The following example removes the value that corresponds to the second key in the preceding example, which is the list of availability zones in the Ireland region\.

```
$ cdk context --reset 2
```

```
Context value
availability-zones:account=123456789012:region=eu-west-1
reset. It will be refreshed on the next SDK synthesis run.
```

Therefore, if you want to update to the latest version of the Amazon Linux AMI, you can use the preceding example to do a controlled update of the context value and reset it, and then synthesize and deploy your app again\.

```
$ cdk synth
```

To clear all of the stored context values for your app, run cdk context \-\-clear, as follows\.

```
$ cdk context --clear
```

Only context values stored in `cdk.context.json` can be reset or cleared\. The AWS CDK does not touch other context files\. To protect a context value from being reset using these commands, then, you might copy the value to `cdk.json`\.

## Example<a name="context_example"></a>

Below is an example of importing an existing Amazon VPC using AWS CDK context\.

------
#### [ TypeScript ]

```
import * as cdk from '@aws-cdk/core';
import * as ec2 from '@aws-cdk/aws-ec2';

export class ExistsVpcStack extends cdk.Stack {

  constructor(scope: cdk.Construct, id: string, props?: cdk.StackProps) {
  
    super(scope, id, props);
    
    const vpcid = this.node.tryGetContext('vpcid');
    const vpc = ec2.Vpc.fromLookup(this, 'VPC', {
      vpcId: vpcid,
    });
    
    const pubsubnets = vpc.selectSubnets({subnetType: ec2.SubnetType.PUBLIC});
    
    new cdk.CfnOutput(this, 'publicsubnets', {
      value: pubsubnets.subnetIds.toString(),
    });
  }
}
```

------
#### [ JavaScript ]

```
const cdk = require('@aws-cdk/core');
const ec2 = require('@aws-cdk/aws-ec2');

class ExistsVpcStack extends cdk.Stack {

  constructor(scope, id, props) {
  
    super(scope, id, props);
    
    const vpcid = this.node.tryGetContext('vpcid');
    const vpc = ec2.Vpc.fromLookup(this, 'VPC', {
      vpcId: vpcid
    });
    
    const pubsubnets = vpc.selectSubnets({subnetType: ec2.SubnetType.PUBLIC});
    
    new cdk.CfnOutput(this, 'publicsubnets', {
      value: pubsubnets.subnetIds.toString()
    });
  }
}

module.exports = { ExistsVpcStack }
```

------
#### [ Python ]

```
import aws_cdk.core as cdk
import aws_cdk.aws_ec2 as ec2

class ExistsVpcStack(cdk.Stack):

    def __init__(scope: cdk.Construct, id: str, **kwargs):
  
        super().__init__(scope, id, **kwargs)
    
        vpcid = self.node.try_get_context("vpcid");
        vpc = ec2.Vpc.from_lookup(self, "VPC", vpc_id=vpcid)
    
        pubsubnets = vpc.select_subnets(subnetType=ec2.SubnetType.PUBLIC);
    
        cdk.CfnOutput(self, "publicsubnets",
            value=pubsubnets.subnet_ids.to_string())
```

------
#### [ Java ]

```
import software.amazon.awscdk.core.CfnOutput;

import software.amazon.awscdk.services.ec2.Vpc;
import software.amazon.awscdk.services.ec2.VpcLookupOptions;
import software.amazon.awscdk.services.ec2.SelectedSubnets;
import software.amazon.awscdk.services.ec2.SubnetSelection;
import software.amazon.awscdk.services.ec2.SubnetType;

public class ExistsVpcStack extends Stack {
    public ExistsVpcStack(App context, String id) {
        this(context, id, null);
    }
    
    public ExistsVpcStack(App context, String id, StackProps props) {
        super(context, id, props);
        
        String vpcId = (String)this.getNode().tryGetContext("vpcid");
        Vpc vpc = (Vpc)Vpc.fromLookup(this, "VPC", VpcLookupOptions.builder()
                .vpcId(vpcId).build());

        SelectedSubnets pubSubNets = vpc.selectSubnets(SubnetSelection.builder()
                .subnetType(SubnetType.PUBLIC).build());
        
        CfnOutput.Builder.create(this, "publicsubnets")
                .value(pubSubNets.getSubnetIds().toString()).build();                
    }
}
```

------
#### [ C\# ]

```
using Amazon.CDK;
using Amazon.CDK.AWS.EC2;

class ExistsVpcStack : Stack
{
    public ExistsVpcStack(App scope, string id, StackProps props) : base(scope, id, props)
    {
        var vpcId = (string)this.Node.TryGetContext("vpcid");
        var vpc = Vpc.FromLookup(this, "VPC", new VpcLookupOptions
        {
            VpcId = vpcId
        });

        SelectedSubnets pubSubNets = vpc.SelectSubnets([new SubnetSelection
        {
            SubnetType = SubnetType.PUBLIC
        }]);

        new CfnOutput(this, "publicsubnets", new CfnOutputProps {
            Value = pubSubNets.SubnetIds.ToString()
        });
    }
}
```

------

You can use cdk diff to see the effects of passing in a context value on the command line:

```
$ cdk diff -c vpcid=vpc-0cb9c31031d0d3e22
```

```
Stack ExistsvpcStack
Outputs
[+] Output publicsubnets publicsubnets: {"Value":"subnet-06e0ea7dd302d3e8f,subnet-01fc0acfb58f3128f"}
```

The resulting context values can be viewed as shown here\.

```
$ cdk context -j
```

```
{
  "vpc-provider:account=123456789012:filter.vpc-id=vpc-0cb9c31031d0d3e22:region=us-east-1": {
    "vpcId": "vpc-0cb9c31031d0d3e22",
    "availabilityZones": [
      "us-east-1a",
      "us-east-1b"
    ],
    "privateSubnetIds": [
      "subnet-03ecfc033225be285",
      "subnet-0cded5da53180ebfa"
    ],
    "privateSubnetNames": [
      "Private"
    ],
    "privateSubnetRouteTableIds": [
      "rtb-0e955393ced0ada04",
      "rtb-05602e7b9f310e5b0"
    ],
    "publicSubnetIds": [
      "subnet-06e0ea7dd302d3e8f",
      "subnet-01fc0acfb58f3128f"
    ],
    "publicSubnetNames": [
      "Public"
    ],
    "publicSubnetRouteTableIds": [
      "rtb-00d1fdfd823c82289",
      "rtb-04bb1969b42969bcb"
    ]
  }
}
```