# Варианты использования в бизнесе<a name="IAM_UseCases"></a>

Просто пример вариант использования IAM в бизнесе поможет вам понять основные способы реализации управления доступами ваших пользователей в AWS\. Этот вариант использования описан в общих терминах, без развернутого описания механики IAM API, которое вы будете использовать для достижения желаемых резульатов\. 

В этом сценарии использования рассматриваются два типичных способа использования IAM вымышленной компанией Example Corp\. В первом сценарии рассматривается Amazon Elastic Compute Cloud \(Amazon EC2\)\. А во втором - Amazon Simple Storage Service \(Amazon S3\)\. 

Для получения дополнительной информации об использовании IAM с другими сервисами AWS, ознакомьтесь с документом [Сервисы AWS которые работают с IAM](reference_aws-services-that-work-with-iam.md)\.

**Темы**
+ [Начальная настройка для Example Corp](#InitSetupExampleCorp_IAM)
+ [Вариант использования IAM с Amazon EC2](#UseCase_EC2)
+ [Вариант использования IAM с Amazon S3](#UseCase_S3)

## Начальная настройка для Example Corp<a name="InitSetupExampleCorp_IAM"></a>

Джон - основатель Example Corp\. С момента основания компании, он создает себе учетную запись AWS и самостоятельно использует продукты AWS\. Затем он нанимает сотрудников для работы в качестве разработчиков, администраторов, тестировщиков, менеджеров и системных администраторов.\. 

Джон использует консоль управления AWS Management Console с корневой(root) учетной записью AWS для создания себе пользователя *John* и группы *Admins*\. Он предоставляте группе Admins разрешения на выполнение всех действий со всеми ресурсами AWS в пределах учетной записи,используя встроенную политику [AdministratorAccess](https://console.aws.amazon.com/iam/home#policies/arn:aws:iam::aws:policy/AdministratorAccess)\. Затем он добавляет пользователя John в группу Admins\. Пошаговое руководство по созданию группы администраторов и пользователя IAM для себя, а затем добавлению своего пользователя в группу администраторов см. в разделе [Создание первого административного пользователя и группы IAM](getting-started_create-admin-group.md)\. 

С этого момента Джон может перестать использовать корневые(root) учетные данные для работы с AWS, а, вместо этого, начать использовать свои личные учетные данные\.

Джон также создает группу *AllUsers* таким образом он может с лекгостью применять разрешения для всех пользователей в пределах аккаунта AWS\. Он добавляет себя в группу\. Затем он создает группу *Developers*, группу *Testers*, группу *Managers* и группу *SysAdmins*\. Он создает пользователей для каждого из сотрудников и помещает пользователей в соответствующие группы\. Кроме того, он добавляет их всех в группу AllUsers\. Для получения информации о создании групп, смотрите [Создание групп в IAm](id_groups_create.md)\. Для получения информации о создании пользователей, смотрите [Создание пользователя в IAM в аккаунте AWS](id_users_create.md)\. Для получения информации о добавлении пользователей в группы, смотрите [Управление группами IAM](id_groups_manage.md)\. 

## Вариант использования IAM с Amazon EC2<a name="UseCase_EC2"></a>

Такие компании, как Example Corp, обычно используют IAM для взаимодействия с такими сервисами, как Amazon EC2\. Для понимания этой части описания данного варианта использования, вам необходимо базовое понимание Amazon EC2\. Для получения информации об Amazon EC2, обратитесь к [Руководству пользователя по Amazon EC2 для использования инстансов с Linux](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/)\.

### Разрешения Amazon EC2 для групп<a name="EC2_PermissionsGroups"></a>

Для обеспечения контроля "периметра" Джон присоединяет политику к группе AllUsers\. Эта политика запрещает все запросы к AWS от пользователей, если его IP адрес не находится в пределах корпоративной сети Example Corp\.

В самой же Example Corp, разным группам необходимы разные разрешения:
+ **System administrators** – Необходимы разрешения создавать и управлять образами AMI, инстансами, мгновенными снимками, томами, группами безопасности и так далее\. Джон присоединяет встроенную управляему AWS политику `AmazonEC2FullAccess` к группе SysAdmins, что дает членам группы использовать все действия связанные с Amazon EC2\.
+ **Developers** – Need the ability to work with instances only\. John therefore creates and attaches a policy to the Developers group that allows developers to call `DescribeInstances`, `RunInstances`, `StopInstances`, `StartInstances`, and `TerminateInstances`\. 
**Note**  
Amazon EC2 uses SSH keys, Windows passwords, and security groups to control who has access to the operating system of specific Amazon EC2 instances\. There's no method in the IAM system to allow or deny access to the operating system of a specific instance\.
+ **Managers** – Should not be able to perform any Amazon EC2 actions except listing the Amazon EC2 resources currently available\. Therefore, John creates and attaches a policy to the Managers group that only lets them call Amazon EC2 "Describe" API operations\.

For examples of what these policies might look like, see [Example IAM Identity\-Based Policies](access_policies_examples.md) and [Using AWS Identity and Access Management](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/index.html?UsingIAM.html) in the *Amazon EC2 User Guide for Linux Instances*\.

### User's Job Function Change<a name="EC2_UserRoleChange"></a>

At some point, one of the developers, Paulo, changes job functions and becomes a manager\. John moves Paulo from the Developers group to the Managers group\. Now that he's in the Managers group, Paulo's ability to interact with Amazon EC2 instances is limited\. He can't launch or start instances\. He also can't stop or terminate existing instances, even if he was the user who launched or started the instance\. He can list only the instances that Example Corp users have launched\.

## Use Case for IAM with Amazon S3<a name="UseCase_S3"></a>

Companies like Example Corp would also typically use IAM with Amazon S3\. John has created an Amazon S3 bucket for the company called *aws\-s3\-bucket*\.

### Creation of Other Users and Groups<a name="S3_CreationOtherUsersGroups"></a>

As employees, Zhang and Mary each need to be able to create their own data in the company's bucket\. They also need to read and write shared data that all developers work on\. To enable this, John logically arranges the data in aws\-s3\-bucket using an Amazon S3 key prefix scheme as shown in the following figure\.

```
/aws-s3-bucket
    /home
        /zhang
        /mary
    /share
        /developers
        /managers
```

John divides the master `/aws-s3-bucket` into a set of home directories for each employee, and a shared area for groups of developers and managers\.

Now John creates a set of policies to assign permissions to the users and groups:
+ **Home directory access for Zhang** – John attaches a policy to Zhang that lets him read, write, and list any objects with the Amazon S3 key prefix `/aws-s3-bucket/home/Zhang/` 
+ **Home directory access for Mary** – John attaches a policy to Mary that lets her read, write, and list any objects with the Amazon S3 key prefix `/aws-s3-bucket/home/mary/`
+ **Shared directory access for the Developers group** – John attaches a policy to the group that lets developers read, write, and list any objects in `/aws-s3-bucket/share/developers/`
+ **Shared directory access for the Managers group** – John attaches a policy to the group that lets managers read, write, and list objects in `/aws-s3-bucket/share/managers/`

**Note**  
Amazon S3 doesn't automatically give a user who creates a bucket or object permission to perform other actions on that bucket or object\. Therefore, in your IAM policies, you must explicitly give users permission to use the Amazon S3 resources they create\.

For examples of what these policies might look like, see [Access Control](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingAuthAccess.html) in the *Amazon Simple Storage Service Developer Guide*\. For information on how policies are evaluated at runtime, see [Policy Evaluation Logic](reference_policies_evaluation-logic.md)\. 

### User's Job Function Change<a name="S3_UserRoleChange"></a>

At some point, one of the developers, Zhang, changes job functions and becomes a manager\. We assume that he no longer needs access to the documents in the `share/developers` directory\. John, as an admin, moves Zhang to the `Managers` group and out of the `Developers` group\. With just that simple reassignment, Zhang automatically gets all permissions granted to the `Managers` group, but can no longer access data in the `share/developers` directory\.

### Integration with a Third\-Party Business<a name="S3_3rdPartyBusiness"></a>

Organizations often work with partner companies, consultants, and contractors\. Example Corp has a partner called the Widget Company, and a Widget Company employee named Shirley needs to put data into a bucket for Example Corp's use\. John creates a group called *WidgetCo* and a user named `Shirley` and adds Shirley to the WidgetCo group\. John also creates a special bucket called *aws\-s3\-bucket1* for Shirley to use\.

John updates existing policies or adds new ones to accommodate the partner Widget Company\. For example, John can create a new policy that denies members of the WidgetCo group the ability to use any actions other than write\. This policy would be necessary only if there's a broad policy that gives all users access to a wide set of Amazon S3 actions\.
