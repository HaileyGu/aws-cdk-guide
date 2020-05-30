# AWS CDK란 무엇입니까?<a name="home"></a>

*AWS Cloud Development Kit \(AWS CDK\) 개발자 안내서* 에 오신 것을 환영합니다\. 이 문서는 코드에서 클라우드 인프라를 정의하고 AWS CloudFormation을 통해 프로비저닝하기 위한 소프트웨어 개발 프레임워크인 AWS CDK에 대한 정보를 제공합니다\.

AWS CloudFormation을 통해 다음을 수행할 수 있습니다:
+ 예측 가능하고 반복 가능한 AWS 인프라 배포의 생성 및 프로비저닝\.
+ Amazon EC2, Amazon Elastic Block Store, Amazon SNS, Elastic Load Balancing 및 Auto Scaling과 같은 AWS 제품 활용\.
+ 기본 AWS 인프라 생성 및 구성에 대한 걱정 없이 클라우드에서 안정성, 확장성이 뛰어나고 비용 효율적인 애플리케이션 구축\.
+ 템플릿 파일을 사용하여 리소스들의 모음을 단일 자원\(Stack\)으로 생성 및 삭제\.

AWS CDK를 사용하여 익숙한 프로그래밍 언어로 클라우드 리소스를 정의하십시오. AWS CDK는 TypeScript, JavaScript, Python, Java 및 C\#/\.Net을 지원합니다\.

개발자는 지원되는 프로그래밍 언어 중 하나를 사용하여 [Constructs](constructs.md)라는 재사용 가능한 클라우드 구성 요소를 정의할 수 있습니다\. 이것들을 [Stacks](stacks.md) 및 [Apps](apps.md)을 구성하는데 사용할 수 있습니다\.

![\[이미지를 찾을 수 없습니다\]](http://docs.aws.amazon.com/cdk/latest/guide/images/AppStacks.png)

## AWS CDK를 사용해야 하는 이유<a name="why_use_cdk"></a>

AWS CDK의 강력한 기능을 살펴보겠습니다. 다음은 AWS Fargate 서비스를 생성하는 AWS CDK 프로젝트의 코드입니다. 다음은 \([AWS CDK를 사용한 AWS Fargate 서비스 생성](ecs_example.md)\)에 사용된 코드입니다\.

------
#### [ TypeScript ]

```
export class MyEcsConstructStack extends core.Stack {
  constructor(scope: core.App, id: string, props?: core.StackProps) {
    super(scope, id, props);

    const vpc = new ec2.Vpc(this, "MyVpc", {
      maxAzs: 3 // Default is all AZs in region
    });

    const cluster = new ecs.Cluster(this, "MyCluster", {
      vpc: vpc
    });

    // Create a load-balanced Fargate service and make it public
    new ecs_patterns.ApplicationLoadBalancedFargateService(this, "MyFargateService", {
      cluster: cluster, // Required
      cpu: 512, // Default is 256
      desiredCount: 6, // Default is 1
      taskImageOptions: { image: ecs.ContainerImage.fromRegistry("amazon/amazon-ecs-sample") },
      memoryLimitMiB: 2048, // Default is 512
      publicLoadBalancer: true // Default is false
    });
  }
}
```

------
#### [ JavaScript ]

```
class MyEcsConstructStack extends core.Stack {
  constructor(scope, id, props) {
    super(scope, id, props);

    const vpc = new ec2.Vpc(this, "MyVpc", {
      maxAzs: 3 // Default is all AZs in region
    });

    const cluster = new ecs.Cluster(this, "MyCluster", {
      vpc: vpc
    });

    // Create a load-balanced Fargate service and make it public
    new ecs_patterns.ApplicationLoadBalancedFargateService(this, "MyFargateService", {
      cluster: cluster, // Required
      cpu: 512, // Default is 256
      desiredCount: 6, // Default is 1
      taskImageOptions: { image: ecs.ContainerImage.fromRegistry("amazon/amazon-ecs-sample") },
      memoryLimitMiB: 2048, // Default is 512
      publicLoadBalancer: true // Default is false
    });
  }
}

module.exports = { MyEcsConstructStack }
```

------
#### [ Python ]

```
class MyEcsConstructStack(core.Stack):

    def __init__(self, scope: core.Construct, id: str, **kwargs) -> None:
        super().__init__(scope, id, **kwargs)

        vpc = ec2.Vpc(self, "MyVpc", max_azs=3)     # default is all AZs in region

        cluster = ecs.Cluster(self, "MyCluster", vpc=vpc)

        ecs_patterns.ApplicationLoadBalancedFargateService(self, "MyFargateService",
            cluster=cluster,            # Required
            cpu=512,                    # Default is 256
            desired_count=6,            # Default is 1
            task_image_options=ecs_patterns.ApplicationLoadBalancedTaskImageOptions(
                image=ecs.ContainerImage.from_registry("amazon/amazon-ecs-sample")),
            memory_limit_mib=2048,      # Default is 512
            public_load_balancer=True)  # Default is False
```

------
#### [ Java ]

```
public class MyEcsConstructStack extends Stack {

    public MyEcsConstructStack(final Construct scope, final String id) {
        this(scope, id, null);
    }

    public MyEcsConstructStack(final Construct scope, final String id,
            StackProps props) {
        super(scope, id, props);

        Vpc vpc = Vpc.Builder.create(this, "MyVpc").maxAzs(3).build();

        Cluster cluster = Cluster.Builder.create(this, "MyCluster")
                .vpc(vpc).build();

        ApplicationLoadBalancedFargateService.Builder.create(this, "MyFargateService")
                .cluster(cluster)
                .cpu(512)
                .desiredCount(6)
                .taskImageOptions(
                       ApplicationLoadBalancedTaskImageOptions.builder()
                               .image(ContainerImage
                                       .fromRegistry("amazon/amazon-ecs-sample"))
                               .build()).memoryLimitMiB(2048)
                .publicLoadBalancer(true).build();
    }
}
```

------
#### [ C\# ]

```
using Amazon.CDK;
using Amazon.CDK.AWS.EC2;
using Amazon.CDK.AWS.ECS;
using Amazon.CDK.AWS.ECS.Patterns;

public class MyEcsConstructStack : Stack
{
    public MyEcsConstructStack(Construct scope, string id, IStackProps props=null) : base(scope, id, props)
    {
        var vpc = new Vpc(this, "MyVpc", new VpcProps
        {
            MaxAzs = 3
        });

        var cluster = new Cluster(this, "MyCluster", new ClusterProps
        {
            Vpc = vpc
        });

        new ApplicationLoadBalancedFargateService(this, "MyFargateService", 
            new ApplicationLoadBalancedFargateServiceProps
        {
            Cluster = cluster,
            Cpu = 512,
            DesiredCount = 6,
            TaskImageOptions = new ApplicationLoadBalancedTaskImageOptions
            {
                Image = ContainerImage.FromRegistry("amazon/amazon-ecs-sample")
            },
            MemoryLimitMiB = 2048,
            PublicLoadBalancer = true,
        });
    }
}
```

------

이 클래스는 다음의 AWS CloudFormation을 생성합니다\. [500 줄 이상의 템플릿](https://github.com/awsdocs/aws-cdk-guide/blob/master/doc_source/my_ecs_construct-stack.yaml); AWS CDK 앱을 배포하면 다음 유형의 리소스가 50개 이상 생성됩니다\.
+  [AWS::EC2::EIP](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-eip.html) 
+  [AWS::EC2::InternetGateway](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-internetgateway.html) 
+  [AWS::EC2::NatGateway](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-natgateway.html) 
+  [AWS::EC2::Route](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-route.html) 
+  [AWS::EC2::RouteTable](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-route-table.html) 
+  [AWS::EC2::SecurityGroup](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-security-group.html) 
+  [AWS::EC2::Subnet](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-subnet.html) 
+  [AWS::EC2::SubnetRouteTableAssociation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-subnet-route-table-assoc.html) 
+  [AWS::EC2::VPCGatewayAttachment](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-vpc-gateway-attachment.html) 
+  [AWS::EC2::VPC](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-vpc.html) 
+  [AWS::ECS::Cluster](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ecs-cluster.html) 
+  [AWS::ECS::Service](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ecs-service.html) 
+  [AWS::ECS::TaskDefinition](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ecs-taskdefinition.html) 
+  [AWS::ElasticLoadBalancingV2::Listener](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-elasticloadbalancingv2-listener.html) 
+  [AWS::ElasticLoadBalancingV2::LoadBalancer](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-elasticloadbalancingv2-loadbalancer.html) 
+  [AWS::ElasticLoadBalancingV2::TargetGroup](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-elasticloadbalancingv2-targetgroup.html) 
+  [AWS::IAM::Policy](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-iam-policy.html) 
+  [AWS::IAM::Role](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-iam-role.html) 
+  [AWS::Logs::LogGroup](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-logs-loggroup.html) 

AWS CDK의 다른 장점은 다음과 같습니다:
+ 인프라를 정의할 때 조건문 \(if 문, for 루프문 등\) 사용
+ 객체 지향 기술을 사용하여 시스템 모델 생성
+ 고 수준의 추상화를 정의하고, 공유할 수 있으며, 팀, 회사 또는 커뮤니티에 배포 가능
+ 프로젝트를 논리 모듈로 구성
+ 인프라를 라이브러리로 공유 및 재사용
+ 산업 표준 프로토콜을 사용하여 인프라 코드 테스트
+ 기존 코드 리뷰 워크 플로우 사용
+ IDE 내에서 코드 완성  
![\[이미지를 찾을 수 없습니다\]](http://docs.aws.amazon.com/cdk/latest/guide/images/CodeCompletion.png)

## AWS CDK로 개발하는 방법<a name="developing"></a>

코드 스니펫과 더 긴 예제는 AWS CDK에서 지원되는 프로그래밍 언어 \(TypeScript, JavaScript, Python, Java 및 C\#\)으로 제공됩니다\. 예제 목록은 [AWS CDK 예제](about_examples.md)를 참조하십시오\.

[AWS CDK 도구](tools.md)는 CDK 앱과 상호 작용하기 위한 커맨드 라인 도구입니다\. 개발자는 AWS CloudFormation 템플릿과 같은 아티팩트를 합성하고 스택을 AWS 개발 계정에 배포할 수 있으며, 배포된 스택과 비교하여 코드 변경의 영향을 이해할 수 있습니다\.

[AWS Construct 라이브러리](constructs.md)에는 각 AWS 서비스에 대한 모듈이 포함되어 있으며, Amazon 또는 AWS 서비스를 위한 리소스를 생성하는 방법에 대한 세부 정보를 캡슐화하는 풍부한 API를 제공합니다\. AWS Construct 라이브러리의 목표는 다양한 AWS 서비스를 통합하여 AWS 목표를 달성할 때 요구되는 복잡성과 로직의 결합도를 낮추는 것입니다\.

**노트**  
AWS CDK 사용에 대한 요금은 없지만 Amazon EC2 인스턴스 실행 또는 Amazon S3 스토리지 사용과 같은 AWS의 [요금이 부과되는 리소스](http://docs.aws.amazon.com/general/latest/gr/glos-chap.html#chargeable-resources)의 생성 또는 사용에 대한 AWS 요금이 발생할 수 있습니다\. 다양한 AWS 리소스 사용 요금을 추정하려면 [AWS 요금 계산기] (https://calculator.aws/#/)를 사용하십시오\.

## AWS CDK에 기여하기<a name="contributing"></a>

AWS CDK는 오픈소스이며 AWS CDK팀은 당신이 기여를 통해 CDK를 더 나은 도구로 만드는 것을 권장하고 있습니다\. 자세한 내용은 [기여하기](https://github.com/awslabs/aws-cdk/blob/master/CONTRIBUTING.md)를 참조하십시오\.

## 추가 문서 및 자료<a name="additional_docs"></a>

이 안내서 외에도, AWS CDK 사용자를 위한 다음과 같은 추가 자료들이 있습니다:
+ [API 참조 문서](https://docs.aws.amazon.com/cdk/api/latest)
+ [re:Invent 2018에서의 AWS CDK 데모](https://www.youtube.com/watch?v=Lh-kVC2r2AU)
+ [AWS CDK 워크숍](https://cdkworkshop.com/)
+ [AWS CDK 예제](https://github.com/aws-samples/aws-cdk-examples)
+ [AWS 개발자 블로그](https://aws.amazon.com/blogs/developer)
+ [Gitter 채널](https://gitter.im/awslabs/aws-cdk)
+ [스택 오버플로우](https://stackoverflow.com/questions/tagged/aws-cdk)
+ [GitHub 저장소](https://github.com/awslabs/aws-cdk)
  + [이슈 목록](https://github.com/awslabs/aws-cdk/issues)
  + [예제](https://github.com/aws-samples/aws-cdk-examples)
  + [문서 저장소](https://github.com/awsdocs/aws-cdk-user-guide/tree/master/doc_source)
  + [라이선스](https://github.com/awslabs/aws-cdk/blob/master/LICENSE)
  + [릴리스](https://github.com/awslabs/aws-cdk/releases)
    + [AWS CDK OpenPGP 키](pgp-keys.md#cdk_pgp_key)
    + [JSII OpenPGP 키](pgp-keys.md#jsii_pgp_key)
+ [Cloud9로 작업하는 AWS CDK 샘플](https://docs.aws.amazon.com/cloud9/latest/user-guide/sample-cdk.html)
+ [AWS CloudFormation 개념](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-whatis-concepts.html)
+ [AWS 용어 사전](https://docs.aws.amazon.com/general/latest/gr/glos-chap.html)

## Amazon Web Services에 대하여<a name="about_aws"></a>

Amazon Web Services \(AWS\)는 개발자가 애플리케이션을 개발할 때 사용할 수 있는 디지털 인프라 서비스 모음입니다\. 서비스에는 컴퓨팅, 스토리지, 데이터베이스 및 애플리케이션 동기화\(메시징 및 대기열\)가 포함됩니다\.

AWS는 pay\-as\-you\-go 서비스 모델을 사용합니다\. 귀하 또는 귀하의 응용 프로그램이 사용하는 서비스에 대해서만 요금이 청구됩니다. 또한, AWS를 프로토 타이핑 및 실험을 위한 플랫폼으로 유용하게 사용할 수 있도록, 특정 조건에서는 요금이 청구되지 않는 무료 사용 등급을 제공합니다\. AWS 비용 및 무료 사용 등급에 대한 자세한 내용은 [무료 사용 등급에서의 AWS 테스트\-드라이빙](http://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-free-tier.html)을 참조하십시오\.

AWS 계정을 얻으려면 [aws\.amazon\.com](https://aws.amazon.com)으로 이동한 다음 **AWS 계정 생성**을 선택하십시오\.