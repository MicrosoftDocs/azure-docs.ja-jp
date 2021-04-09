---
title: Reliable Services のアプリ マニフェストの例
description: Reliable Services Service Fabric アプリケーションのアプリケーションとサービス マニフェストの設定を構成する方法について説明します。
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: f40e54f5260f827f0b18c833d23d1f57b5ebc3a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84701100"
---
# <a name="reliable-services-application-and-service-manifest-examples"></a>Reliable Services のアプリケーション マニフェストとサービス マニフェストの例
ASP.NET Core Web フロント エンドとステートフルなバックエンドを持つ Service Fabric アプリケーションのアプリケーションとサービス マニフェストの例を次に示します。 これらの例の目的は、利用できる設定とその使用方法を示すことです。 これらのアプリケーションとサービス マニフェストは、[Service Fabric .NET クイック スタート](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) のマニフェストに基づいています。

次の機能が示されます。

|Manifest|特徴|
|---|---|
|[アプリケーション マニフェスト](#application-manifest)| [リソース ガバナンス](service-fabric-resource-governance.md)、[ローカル管理者アカウントとしてサービスを実行する](service-fabric-application-runas-security.md)、[すべてのサービス コード パッケージに既定のポリシーを適用する](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)、[ユーザーとグループのプリンシパルを作成する](service-fabric-application-runas-security.md)、サービス インスタンス間でデータ パッケージを共有する、[サービス エンドポイントをオーバーライドする](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)| 
|FrontEndService サービス マニフェスト| [サービスの起動時にスクリプトを実行する](service-fabric-run-script-at-service-startup.md)、[HTTPS エンドポイントを定義する](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest) | 
|BackEndService サービス マニフェスト| [構成パッケージを宣言する](service-fabric-application-and-service-manifests.md)、[データ パッケージを宣言する](service-fabric-application-and-service-manifests.md)、[エンドポイントを構成する](service-fabric-service-manifest-resources.md)| 

特定の XML 要素の詳細については、「[アプリケーション マニフェスト要素](#application-manifest-elements)」、「[VotingWeb サービス マニフェスト要素](#votingweb-service-manifest-elements)」、および「[VotingData サービス マニフェスト要素](#votingdata-service-manifest-elements)」を参照してください。

## <a name="application-manifest"></a>アプリケーション マニフェスト

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="Memory" DefaultValue="4084" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="Port" DefaultValue="8081" />
    <Parameter Name="Protocol" DefaultValue="tcp" />
    <Parameter Name="Type" DefaultValue="internal" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <!-- Override endpoints declared in the service manifest. -->
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="DataEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
      </Endpoints>
    </ResourceOverrides>

    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[Memory]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[Memory]" BlockIOWeight="[BlockIOWeight]" 
                                MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" 
                                MemorySwapInMB="[MemorySwapInMB]"/>

      <!-- Share the data package across multiple instances of the VotingData service-->
      <PackageSharingPolicy PackageRef="Data"/>

      <!-- Give read rights on the "DataEndpoint" endpoint to the Customer2 account.-->
      <SecurityAccessPolicy GrantRights="Read" PrincipalRef="Customer2" ResourceRef="DataEndpoint" ResourceType="Endpoint"/>         
    </Policies>
  </ServiceManifestImport>
  
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Run the setup entry point (defined in the imported service manifest) as the SetupAdminUser account 
      (declared in the following Principals section). -->
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      
    </Policies>

  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <!-- Define users and groups required to run the services and access resources.  Principals are used in the Policies section(s). -->
  <Principals>
    <!-- Declare a set of groups as security principals, which can be referenced in policies. Groups are useful if there are multiple users 
    for different service entry points and they need to have certain common privileges that are available at the group level. -->
    <Groups>
      <!-- Create a group that has administrator privileges. -->
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <!-- Declare a user and add the user to the Administrators system group. The SetupAdminUser account is used to run the 
      setup entry point of the VotingWebPkg code package (described in the preceding Policies section).-->
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
      <!-- Create a user. Local user accounts are created on the machines where the application is deployed. By default, these accounts 
      do not have the same names as those specified here. Instead, they are dynamically generated and have random passwords. -->
      <User Name="Customer1" >
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as a local user with the specified account name and password. Local user accounts are created on the machines 
      where the application is deployed. -->
      <User Name="Customer2" AccountType="LocalUser" AccountName="Customer1" Password="MyPassword">
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as NetworkService. -->
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <!-- Policies applied at the application level. -->
  <Policies>
    <!-- Specify a default user account for all code packages that don’t have a specific RunAsPolicy defined in 
    the ServiceManifestImport section(s). -->
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
    
  </Policies>
</ApplicationManifest>

```

## <a name="votingweb-service-manifest"></a>VotingWeb サービス マニフェスト

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <!-- A privileged entry point that by default runs with the same credentials as Service Fabric (typically the NetworkService account) before 
    any other entry point. Use the setup entry point to set system environment variables, give the account running the service (NETWORK SERVICE, by default) 
    access to a certificate's private key, or perform other setup tasks. In the application manifest, you can change the security permissions to run the startup script 
    under a local system account or an administrator account.  -->
    <SetupEntryPoint>
      <ExeHost>
        <!-- The setup script to run. -->
        <Program>Setup.bat</Program>
        <!-- Pass arguments to the script when it runs.-->
        <Arguments>MyValue</Arguments>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
        <!-- Warning! Do not use console redirection in a production application, only use it for local development and debugging. Redirects console output from the startup
        script to an output file in the application folder called "log" on the cluster node where the application is deployed and run. Also set the number of output files
        to retain and the maximum file size (in KB). -->
        <ConsoleRedirection FileRetentionCount="10" FileMaxSizeInKb="20480"/>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- Configure a HTTPS endpoint on port 443. This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

## <a name="votingdata-service-manifest"></a>VotingData サービス マニフェスト

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingDataPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="VotingDataType"  HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingData.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Declares a folder, named by the Name attribute, under PackageRoot that contains a Settings.xml file. This file contains sections of user-defined, 
  key-value pair settings that the process can read back at run time. During an upgrade, if only the ConfigPackage version has changed, 
  then the running process is not restarted. Instead, a callback notifies the process that configuration settings have changed so they can be reloaded dynamically. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Declares a folder, named by the Name attribute, under PackageRoot which contains static data files to be consumed by the process at run time. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- Define an internal (used for intra-application communication) TCP endpoint. Since no port is specified, one is created and assigned dynamically
           to the service.-->
      <Endpoint Name="DataEndpoint" Protocol="tcp" Type="Internal" />
    </Endpoints>
  </Resources>

</ServiceManifest>

```

## <a name="application-manifest-elements"></a>アプリケーション マニフェスト要素
### <a name="applicationmanifest-element"></a>ApplicationManifest 要素
宣言によって、アプリケーションの種類とバージョンについて説明します。 構成要素のサービスの 1 つまたは複数のサービス マニフェストを参照して、アプリケーションの種類を構成します。 構成要素のサービスの構成設定は、パラメーター化されたアプリケーション設定を使用してオーバーライドできます。 既定のサービス、サービス テンプレート、プリンシパル、ポリシー、診断のセットアップ、および証明書もアプリケーション レベルで宣言できます。 詳細については、「[ApplicationManifest 要素](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType)」を参照してください。

### <a name="parameters-element"></a>Parameters 要素
このアプリケーション マニフェストで使用されるパラメーターを宣言します。 これらのパラメーターの値は、アプリケーションのインストール時に指定できます。また、このパラメーターを使用して、アプリケーションまたはサービスの構成設定をオーバーライドできます。 詳細については、「[Parameters 要素](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)」を参照してください。

### <a name="parameter-element"></a>Parameter 要素
このマニフェストで使用されるアプリケーション パラメーター。 アプリケーションのインストール時にパラメーター値を変更できます。または、値が指定されない場合、既定値が使用されます。 詳細については、「[Parameter 要素](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement)」を参照してください。

### <a name="servicemanifestimport-element"></a>ServiceManifestImport 要素
サービス開発者が作成したサービス マニフェストをインポートします。 アプリケーションの構成要素の各サービスについて、サービス マニフェストをインポートする必要があります。 サービス マニフェストの構成のオーバーライドとポリシーを宣言できます。 詳細については、「[ServiceManifestImport 要素](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)」を参照してください。

### <a name="servicemanifestref-element"></a>ServiceManifestRef 要素
サービス マニフェストを参照渡しでインポートします。 現在、サービス マニフェスト ファイル (ServiceManifest.xml) はビルド パッケージに存在します。 詳細については、「[ServiceManifestRef 要素](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement)」を参照してください。

### <a name="resourceoverrides-element"></a>ResourceOverrides 要素
サービス マニフェスト リソースで宣言されたエンドポイントのリソースのオーバーライドを指定します。 詳細については、「[ResourceOverrides 要素](service-fabric-service-model-schema-elements.md#ResourceOverridesElementResourceOverridesTypeComplexTypeDefinedInServiceManifestImportelement)」を参照してください。

### <a name="endpoints-element"></a>Endpoints 要素
オーバーライドするエンドポイント。 詳細については、「[Endpoints 要素](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourceOverridesTypecomplexType)」を参照してください。

### <a name="endpoint-element"></a>Endpoint 要素
オーバーライドする、サービス マニフェストで宣言されているエンドポイント。 詳細については、「[Endpoint 要素](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)」を参照してください。

### <a name="policies-element"></a>Policies 要素
インポートされたサービス マニフェストに適用されるポリシー (エンドポイント バインディング、パッケージ共有、実行アカウント、セキュリティ アクセス) について説明します。 詳細については、「[Policies 要素](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)」を参照してください。

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy 要素
サービス パッケージ全体のレベルで適用するリソース ガバナンス ポリシーを定義します。 詳細については、「[ServicePackageResourceGovernancePolicy 要素](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)」を参照してください。

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy 要素
コードパッケージのリソース制限を指定します。 詳細については、「[ResourceGovernancePolicy 要素](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)」を参照してください。

### <a name="packagesharingpolicy-element"></a>PackageSharingPolicy 要素
コード、構成、またはデータ パッケージを、同じサービスの種類のサービス インスタンス間で共有するかどうかを示します。 詳細については、「[PackageSharingPolicy 要素](service-fabric-service-model-schema-elements.md#PackageSharingPolicyElementPackageSharingPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexType)」を参照してください。

### <a name="securityaccesspolicy-element"></a>SecurityAccessPolicy 要素
サービス マニフェストに定義されているリソース (エンドポイントなど) のプリンシパルにアクセス許可を付与します。 通常、セキュリティ リスクを最小限に抑えるために、さまざまなリソースに対するサービスのアクセスを制御および制限する場合にとても便利です。 これは、複数の開発者が開発した、マーケットプレースから入手したサービスのコレクションから、アプリケーションを構築した場合に特に重要です。 詳細については、「[SecurityAccessPolicy 要素](service-fabric-service-model-schema-elements.md#SecurityAccessPolicyElementSecurityAccessPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInSecurityAccessPolicieselementDefinedInDigestedEndpointelement)」を参照してください。

### <a name="runaspolicy-element"></a>RunAsPolicy 要素
サービス コード パッケージを実行するローカル ユーザーまたはローカル システム アカウントを指定します。 ドメイン アカウントは、Azure Active Directory を使用できる Windows Server デプロイでサポートされます。 既定では、アプリケーションは Fabric.exe プロセスを実行しているアカウントで実行されます。 また、他のアカウントとしてアプリケーションを実行することもできます。これは、Principals セクションで宣言する必要があります。 サービスに RunAs ポリシーを適用し、サービス マニフェストで HTTP プロトコルを使用するエンドポイント リソースが宣言されている場合は、これらのエンドポイントに割り当てられているポートに、サービスが実行する RunAs ユーザー アカウントのアクセス制御リストが正しく適用されるように、SecurityAccessPolicy も指定する必要があります。 HTTPS エンドポイントの場合、クライアントに返す証明書の名前を示す EndpointBindingPolicy も定義する必要があります。 詳細については、「[RunAsPolicy 要素](service-fabric-service-model-schema-elements.md#RunAsPolicyElementRunAsPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)」を参照してください。

### <a name="defaultservices-element"></a>DefaultServices 要素
アプリケーションがこのアプリケーションの種類に対してインスタンス化されるたびに自動的に作成されるサービス インスタンスを宣言します。 詳細については、「[DefaultServices 要素](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)」を参照してください。

### <a name="service-element"></a>Service 要素
アプリケーションのインスタンス化時に自動的に作成するサービスを宣言します。 詳細については、「[Service 要素](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)」を参照してください。

### <a name="statefulservice-element"></a>StatefulService 要素
ステートフル サービスを定義します。 詳細については、「[StatefulService 要素](service-fabric-service-model-schema-elements.md#StatefulServiceElementStatefulServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)」を参照してください。

### <a name="statelessservice-element"></a>StatelessService 要素
ステートレス サービスを定義します。 詳細については、「[StatelessService 要素](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)」を参照してください。

### <a name="principals-element"></a>Principals 要素
このアプリケーションがサービスを実行し、リソースのセキュリティを保護するために必要なセキュリティ プリンシパル (ユーザー、グループ) について説明します。 プリンシパルは、ポリシー セクションで参照されます。 詳細については、「[Principals 要素](service-fabric-service-model-schema-elements.md#PrincipalsElementSecurityPrincipalsTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)」を参照してください。

### <a name="groups-element"></a>Groups 要素
一連のグループをセキュリティ プリンシパルとして宣言します。これはポリシーで参照できます。 グループは、異なるサービス エントリ ポイントに対して複数のユーザーが存在し、グループ レベルで使用できる特定の共通の特権が必要な場合に便利です。 詳細については、「[Groups 要素](service-fabric-service-model-schema-elements.md#GroupsElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)」を参照してください。

### <a name="group-element"></a>Group 要素
グループをセキュリティ プリンシパルとして宣言します。これはポリシーで参照できます。 詳細については、「[Group 要素](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInGroupselement)」を参照してください。

### <a name="membership-element"></a>Membership 要素
 詳細については、「[Membership 要素](service-fabric-service-model-schema-elements.md#MembershipElementanonymouscomplexTypeComplexTypeDefinedInGroupelement)」を参照してください。

### <a name="systemgroup-element"></a>SystemGroup 要素
 詳細については、「[SystemGroup 要素](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMembershipelement)」を参照してください。

### <a name="users-element"></a>Users 要素
一連のユーザーをセキュリティ プリンシパルとして宣言します。これはポリシーで参照できます。 詳細については、「[Users 要素](service-fabric-service-model-schema-elements.md#UsersElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)」を参照してください。

### <a name="user-element"></a>User 要素
ユーザーをセキュリティ プリンシパルとして宣言します。これはポリシーで参照できます。 詳細については、「[User 要素](service-fabric-service-model-schema-elements.md#UserElementanonymouscomplexTypeComplexTypeDefinedInUserselement)」を参照してください。

### <a name="memberof-element"></a>MemberOf 要素
ユーザーを既存の任意のメンバーシップ グループに追加して、そのメンバーシップ グループのすべてのプロパティとセキュリティ設定を継承するようにすることができます。 別のサービスまたは同じサービス (異なるマシン) がアクセスする必要がある外部リソースをセキュリティで保護するために、メンバーシップ グループを使用することができます。 詳細については、「[MemberOf 要素](service-fabric-service-model-schema-elements.md#MemberOfElementanonymouscomplexTypeComplexTypeDefinedInUserelement)」を参照してください。

### <a name="systemgroup-element"></a>SystemGroup 要素
ユーザーを追加するシステム グループ。  システム グループは Groups セクションで定義する必要があります。 詳細については、「[SystemGroup 要素](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)」を参照してください。

### <a name="group-element"></a>Group 要素
ユーザーを追加するグループ。  グループは Groups セクションで定義する必要があります。 詳細については、「[Group 要素](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)」を参照してください。

### <a name="policies-element"></a>Policies 要素
アプリケーション レベルで適用されるポリシー (ログ コレクション、既定の実行アカウント、正常性、セキュリティ アクセス) について説明します。 詳細については、「[Policies 要素](service-fabric-service-model-schema-elements.md#PoliciesElementApplicationPoliciesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)」を参照してください。

### <a name="defaultrunaspolicy-element"></a>DefaultRunAsPolicy 要素
ServiceManifestImport セクションに特定の RunAsPolicy が定義されていないすべてのサービス コード パッケージ用に既定のユーザー アカウントを指定します。 詳細については、「[DefaultRunAsPolicy 要素](service-fabric-service-model-schema-elements.md#DefaultRunAsPolicyElementanonymouscomplexTypeComplexTypeDefinedInApplicationPoliciesTypecomplexType)」を参照してください。




## <a name="votingweb-service-manifest-elements"></a>VotingWeb サービス マニフェスト要素
### <a name="servicemanifest-element"></a>ServiceManifest 要素
宣言によって、サービスの種類とバージョンについて説明します。 1 つ以上のサービスの種類をサポートするよう、サービス パッケージを構成するアップグレード可能なコード、構成、データ パッケージの一覧を別に列挙します。 リソース、診断設定、サービス メタデータ (サービスの種類、正常性のプロパティ、負荷分散のメトリックなど) も指定します。 詳細については、「[ServiceManifest 要素](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)」を参照してください。

### <a name="servicetypes-element"></a>ServiceTypes 要素
このマニフェストの CodePackage でサポートされているサービスの種類を定義します。 これらのサービスの種類のいずれかに対してサービスがインスタンス化されると、このマニフェストで宣言されているすべてのコード パッケージは、エントリ ポイントを実行してアクティブ化されます。 サービスの種類は、コード パッケージ レベルではなく、マニフェスト レベルで宣言されます。 詳細については、「[ServiceTypes 要素](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)」を参照してください。

### <a name="statelessservicetype-element"></a>StatelessServiceType 要素
ステートレス サービスの種類について説明します。 詳細については、「[StatelessServiceType 要素](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)」を参照してください。

### <a name="codepackage-element"></a>CodePackage 要素
定義済みのサービスの種類をサポートするコード パッケージについて説明します。 これらのサービスの種類のいずれかに対してサービスがインスタンス化されると、このマニフェストで宣言されているすべてのコード パッケージは、エントリ ポイントを実行してアクティブ化されます。 実行時に、サポートされているサービスの種類を登録するために、結果のプロセスが必要になります。 複数のコード パッケージがあるとき、システムが宣言されたサービスの種類のいずれかを検索するときは常に、すべてアクティブ化されます。 詳細については、「[CodePackage 要素](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)」を参照してください。

### <a name="setupentrypoint-element"></a>SetupEntryPoint 要素
他のエントリ ポイントの前に、既定では Service Fabric と同じ資格情報で実行する特権を持つエントリ ポイント (通常は NETWORKSERVICE アカウント)。 EntryPoint によって指定された実行可能ファイルは通常は実行時間の長いサービス ホストです。 別々にセットアップされたエントリ ポイントの存在により、長期にわたって高い権限でサービス ホストを実行する必要がなくなります。 詳細については、「[SetupEntryPoint 要素](service-fabric-service-model-schema-elements.md#SetupEntryPointElementanonymouscomplexTypeComplexTypeDefinedInCodePackageTypecomplexType)」を参照してください。

### <a name="exehost-element"></a>ExeHost 要素
 詳細については、「[ExeHost 要素](service-fabric-service-model-schema-elements.md#ExeHostElementExeHostEntryPointTypeComplexTypeDefinedInSetupEntryPointelement)」を参照してください。

### <a name="program-element"></a>Program 要素
実行可能ファイル名。  たとえば、"MySetup.bat"、"MyServiceHost.exe" などです。 詳細については、「[Program 要素](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)」を参照してください。

### <a name="arguments-element"></a>Arguments 要素
 詳細については、「[Arguments 要素](service-fabric-service-model-schema-elements.md#ArgumentsElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)」を参照してください。

### <a name="workingfolder-element"></a>WorkingFolder 要素
アプリケーションがデプロイされるクラスター ノード上のコード パッケージ内のプロセス用の作業ディレクトリ。 Work (既定値)、CodePackage、または CodeBase の 3 つの値を指定することができます。 CodeBase は、作業ディレクトリがコード パッケージで EXE が定義されるディレクトリに設定されることを指定します。 CodePackage は、コード パッケージ ディレクトリで EXE が定義されている場所にかかわらず、作業ディレクトリがコード パッケージのルートになるように設定します。 Work は、作業ディレクトリをノード上に作成された一意のフォルダーに設定します。  このフォルダーは、アプリケーション インスタンス全体で同じです。 既定では、アプリケーションのすべてのプロセスの作業ディレクトリは、アプリケーションの作業フォルダーに設定されます。 ここにプロセスがデータを書き込むことができます。 コード パッケージまたはコード ベースのフォルダーは、さまざまなアプリケーション インスタンス間で共有されたり削除されたりする可能性があるため、これらのフォルダーにデータを書き込むことはお勧めできません。 詳細については、「[WorkingFolder 要素](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)」を参照してください。

### <a name="consoleredirection-element"></a>ConsoleRedirection 要素

> [!WARNING]
> 実稼動アプリケーションでは、コンソール リダイレクションを使用しないでください。これはローカル開発とデバッグにのみ使用します。 スタートアップ スクリプトからのコンソール出力を、アプリケーションがデプロイおよび実行されるクラスター ノード上の "log" と呼ばれるアプリケーション フォルダー内の出力ファイルにリダイレクトします。 詳細については、「[ConsoleRedirection 要素](service-fabric-service-model-schema-elements.md#ConsoleRedirectionElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)」を参照してください。

### <a name="entrypoint-element"></a>EntryPoint 要素
EntryPoint によって指定された実行可能ファイルは通常は実行時間の長いサービス ホストです。 別々にセットアップされたエントリ ポイントの存在により、長期にわたって高い権限でサービス ホストを実行する必要がなくなります。 EntryPoint で指定された実行可能ファイルは、SetupEntryPoint が正常に終了した後に実行されます。 結果のプロセスは、終了またはクラッシュした場合に、監視されて再起動されます (SetupEntryPoint で再起動)。 詳細については、「[EntryPoint 要素](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)」を参照してください。

### <a name="exehost-element"></a>ExeHost 要素
 詳細については、「[ExeHost 要素](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)」を参照してください。

### <a name="configpackage-element"></a>ConfigPackage 要素
Settings.xml ファイルを含む PackageRoot の下で、Name 属性を使用して名前が付けられたフォルダーを宣言します。 このファイルには、実行時にプロセスが読み取ることができるユーザー定義のキー値ペアの設定のセクションが含まれています。 アップグレード中に ConfigPackage のバージョンのみが変更された場合、実行中のプロセスは再起動されません。 代わりに、コールバックは構成設定が変更されたことをプロセスに通知して、動的に再読み込みされるようにします。 詳細については、「[ConfigPackage 要素](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)」を参照してください。

### <a name="resources-element"></a>Resources 要素
このサービスに使用されるリソースについて説明します。これは、コンパイル済みのコードを変更することなく宣言できます。また、サービスのデプロイ時に変更できます。 これらのリソースへのアクセスは、アプリケーション マニフェストの Principals および Policies セクションで制御されます。 詳細については、「[Resources 要素](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)」を参照してください。

### <a name="endpoints-element"></a>Endpoints 要素
サービスのエンドポイントを定義します。 詳細については、「[Endpoints 要素](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)」を参照してください。

### <a name="endpoint-element"></a>Endpoint 要素
オーバーライドする、サービス マニフェストで宣言されているエンドポイント。 詳細については、「[Endpoint 要素](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)」を参照してください。



## <a name="votingdata-service-manifest-elements"></a>VotingData サービス マニフェスト要素
### <a name="servicemanifest-element"></a>ServiceManifest 要素
宣言によって、サービスの種類とバージョンについて説明します。 1 つ以上のサービスの種類をサポートするよう、サービス パッケージを構成するアップグレード可能なコード、構成、データ パッケージの一覧を別に列挙します。 リソース、診断設定、サービス メタデータ (サービスの種類、正常性のプロパティ、負荷分散のメトリックなど) も指定します。 詳細については、「[ServiceManifest 要素](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)」を参照してください。

### <a name="servicetypes-element"></a>ServiceTypes 要素
このマニフェストの CodePackage でサポートされているサービスの種類を定義します。 これらのサービスの種類のいずれかに対してサービスがインスタンス化されると、このマニフェストで宣言されているすべてのコード パッケージは、エントリ ポイントを実行してアクティブ化されます。 サービスの種類は、コード パッケージ レベルではなく、マニフェスト レベルで宣言されます。 詳細については、「[ServiceTypes 要素](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)」を参照してください。

### <a name="statefulservicetype-element"></a>StatefulServiceType 要素
ステートフル サービスの種類について説明します。 詳細については、「[StatefulServiceType 要素](service-fabric-service-model-schema-elements.md#StatefulServiceTypeElementStatefulServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)」を参照してください。

### <a name="codepackage-element"></a>CodePackage 要素
定義済みのサービスの種類をサポートするコード パッケージについて説明します。 これらのサービスの種類のいずれかに対してサービスがインスタンス化されると、このマニフェストで宣言されているすべてのコード パッケージは、エントリ ポイントを実行してアクティブ化されます。 実行時に、サポートされているサービスの種類を登録するために、結果のプロセスが必要になります。 複数のコード パッケージがあるとき、システムが宣言されたサービスの種類のいずれかを検索するときは常に、すべてアクティブ化されます。 詳細については、「[CodePackage 要素](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)」を参照してください。

### <a name="entrypoint-element"></a>EntryPoint 要素
EntryPoint によって指定された実行可能ファイルは通常は実行時間の長いサービス ホストです。 別々にセットアップされたエントリ ポイントの存在により、長期にわたって高い権限でサービス ホストを実行する必要がなくなります。 EntryPoint で指定された実行可能ファイルは、SetupEntryPoint が正常に終了した後に実行されます。 結果のプロセスは、終了またはクラッシュした場合に、監視されて再起動されます (SetupEntryPoint で再起動)。 詳細については、「[EntryPoint 要素](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)」を参照してください。

### <a name="exehost-element"></a>ExeHost 要素
 詳細については、「[ExeHost 要素](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)」を参照してください。

### <a name="program-element"></a>Program 要素
実行可能ファイル名。  たとえば、"MySetup.bat"、"MyServiceHost.exe" などです。 詳細については、「[Program 要素](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)」を参照してください。

### <a name="workingfolder-element"></a>WorkingFolder 要素
アプリケーションがデプロイされるクラスター ノード上のコード パッケージ内のプロセス用の作業ディレクトリ。 Work (既定値)、CodePackage、または CodeBase の 3 つの値を指定することができます。 CodeBase は、作業ディレクトリがコード パッケージで EXE が定義されるディレクトリに設定されることを指定します。 CodePackage は、コード パッケージ ディレクトリで EXE が定義されている場所にかかわらず、作業ディレクトリがコード パッケージのルートになるように設定します。 Work は、作業ディレクトリをノード上に作成された一意のフォルダーに設定します。  このフォルダーは、アプリケーション インスタンス全体で同じです。 既定では、アプリケーションのすべてのプロセスの作業ディレクトリは、アプリケーションの作業フォルダーに設定されます。 ここにプロセスがデータを書き込むことができます。 コード パッケージまたはコード ベースのフォルダーは、さまざまなアプリケーション インスタンス間で共有されたり削除されたりする可能性があるため、これらのフォルダーにデータを書き込むことはお勧めできません。 詳細については、「[WorkingFolder 要素](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)」を参照してください。

### <a name="configpackage-element"></a>ConfigPackage 要素
Settings.xml ファイルを含む PackageRoot の下で、Name 属性を使用して名前が付けられたフォルダーを宣言します。 このファイルには、実行時にプロセスが読み取ることができるユーザー定義のキー値ペアの設定のセクションが含まれています。 アップグレード中に ConfigPackage のバージョンのみが変更された場合、実行中のプロセスは再起動されません。 代わりに、コールバックは構成設定が変更されたことをプロセスに通知して、動的に再読み込みされるようにします。 詳細については、「[ConfigPackage 要素](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)」を参照してください。

### <a name="datapackage-element"></a>DataPackage 要素
実行時にプロセスで消費される静的データ ファイルを含む PackageRoot の下で、Name 属性を使用して名前が付けられたフォルダーを宣言します。 サービス マニフェストに含まれるデータ パッケージのいずれかがアップグレードされると、Service Fabric は、ホストおよびサポート パッケージに指定されているすべての EXE と DLLHOST をリサイクルします。 詳細については、「[DataPackage 要素](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)」を参照してください。

### <a name="resources-element"></a>Resources 要素
このサービスに使用されるリソースについて説明します。これは、コンパイル済みのコードを変更することなく宣言できます。また、サービスのデプロイ時に変更できます。 これらのリソースへのアクセスは、アプリケーション マニフェストの Principals および Policies セクションで制御されます。 詳細については、「[Resources 要素](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)」を参照してください。

### <a name="endpoints-element"></a>Endpoints 要素
サービスのエンドポイントを定義します。 詳細については、「[Endpoints 要素](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)」を参照してください。

### <a name="endpoint-element"></a>Endpoint 要素
オーバーライドする、サービス マニフェストで宣言されているエンドポイント。 詳細については、「[Endpoint 要素](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)」を参照してください。

