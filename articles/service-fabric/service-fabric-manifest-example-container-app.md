---
title: Azure Service Fabric コンテナー アプリケーション マニフェストの例
description: 複数コンテナーの Service Fabric アプリケーションのアプリケーションとサービス マニフェストの設定を構成する方法について説明します。
author: peterpogorski
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: pepogors
ms.openlocfilehash: 5efdbd17db20e69ace33b379ddbb99b2c4a20e69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84701168"
---
# <a name="multi-container-application-and-service-manifest-examples"></a>複数コンテナーのアプリケーション マニフェストとサービス マニフェストの例
複数コンテナーの Service Fabric アプリケーションのアプリケーションとサービス マニフェストの例を次に示します。 これらの例の目的は、利用できる設定とその使用方法を示すことです。 これらのアプリケーションとサービス マニフェストは、[Windows Server 2016 コンテナー サンプル](https://github.com/Azure-Samples/service-fabric-containers/tree/master/Windows) のマニフェストに基づいています。

次の機能が示されます。

|Manifest|特徴|
|---|---|
|[アプリケーション マニフェスト](#application-manifest)| [環境変数のオーバーライド](service-fabric-get-started-containers.md#configure-and-set-environment-variables)、[コンテナー ポートからホストへのマッピングの構成](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)、[コンテナー レジストリ認証の構成](service-fabric-get-started-containers.md#configure-container-repository-authentication)、[リソース ガバナンス](service-fabric-resource-governance.md)、[分離モードの設定](service-fabric-get-started-containers.md#configure-isolation-mode)、[OS ビルド固有のコンテナー イメージの指定](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)| 
|[FrontEndService サービス マニフェスト](#frontendservice-service-manifest)| [環境変数の設定](service-fabric-get-started-containers.md#configure-and-set-environment-variables)、[エンドポイントの構成](service-fabric-get-started-containers.md#configure-communication)、コマンドをコンテナーに渡す、[証明書のコンテナーへのインポート](service-fabric-securing-containers.md)| 
|[BackEndService サービス マニフェスト](#backendservice-service-manifest)|[環境変数の設定](service-fabric-get-started-containers.md#configure-and-set-environment-variables)、[エンドポイントの構成](service-fabric-get-started-containers.md#configure-communication)、[ボリューム ドライバーの構成](service-fabric-containers-volume-logging-drivers.md)| 

特定の XML 要素の詳細については、「[アプリケーション マニフェスト要素](#application-manifest-elements)」、「[FrontEndService サービス マニフェスト要素](#frontendservice-service-manifest-elements)」、および「[BackEndService サービス マニフェスト要素](#backendservice-service-manifest-elements)」を参照してください。

## <a name="application-manifest"></a>アプリケーション マニフェスト

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Container.ApplicationType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="BackEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="FrontEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="MaximumIOps" DefaultValue="20"/>
    <Parameter Name="MemoryFront" DefaultValue="4084" />
    <Parameter Name="MemoryBack" DefaultValue="2048" />
    <Parameter Name="CertThumbprint" DefaultValue=""/>
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="BackEndServicePkg" ServiceManifestVersion="1.0.0" />    
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryFront]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[MemoryFront]" BlockIOWeight="[BlockIOWeight]" MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" MemorySwapInMB="[MemorySwapInMB]"/>
      
      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
        
        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>
        
        <!-- This binds the port the container is listening on (8905 in this sample) to an endpoint resource named "BackEndServiceTypeEndpoint", which is defined in the service manifest.  -->
        <PortBinding ContainerPort="8905" EndpointRef="BackEndServiceTypeEndpoint"/>
        
        <!-- Configure the Azure Files volume plugin.  Bind the source folder on the host VM or a remote share to the destination folder within the running container. -->
        <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
          <!-- Driver options to be passed to driver. The Azure Files volume plugin supports the following driver options:
            shareName (the Azure Files file share that provides the volume for the container), storageAccountName (the Azure storage account
            that contains the Azure Files file share), storageAccountKey (Access key for the Azure storage account that contains the Azure Files file share).
            These three driver options are required. -->
          <DriverOption Name="shareName" Value="" />
          <DriverOption Name="storageAccountName" Value="MY-STORAGE-ACCOUNT-NAME" />
          <DriverOption Name="storageAccountKey" Value="MY-STORAGE-ACCOUNT-KEY" />
        </Volume>
        
        <!-- Windows Server containers may not be compatible across different versions of the OS.  You can specify multiple OS images per container and tag 
        them with the build versions of the OS. Get the build version of the OS by running "winver" at a Windows command prompt. -->
        <ImageOverrides>
          <!-- If the underlying OS is build version 16299 (Windows Server version 1709), Service Fabric picks the container image tagged with Os="16299". -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_1709" Os="16299" />
          
          <!-- An untagged container image is assumed to work across all versions of the OS and overrides the image specified in the service manifest. -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_default" />          
        </ImageOverrides>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <!-- Policies to be applied to the imported service manifest. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    
    <!-- This enables you to provide different values for environment variables when creating a FrontEndService
         Theses environment variables are declared in the FrontEndServiceType service manifest-->
    <EnvironmentOverrides CodePackageRef="Code">
      <EnvironmentVariable Name="BackendServiceName" Value="Container.Application/BackEndService"/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentOverrides>
    
    <!-- This policy maps the  port of the container (80) to the endpoint declared in the service, 
         FrontEndServiceTypeEndpoint which is exposed as port 80 on the host-->    
    <Policies>

      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryBack]"/>

      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">

        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>

        <!-- Binds an endpoint resource (declared in the service manifest) to the exposed container port. -->
        <PortBinding ContainerPort="80" EndpointRef="FrontEndServiceTypeEndpoint"/>

        <!-- Import a certificate into the container.  The certificate must be installed in the LocalMachine store of all the cluster nodes.
          When the application starts, the runtime reads the certificate and generates a PFX file and password (on Windows) or a PEM file (on Linux).
          The PFX file and password are accessible in the container using the Certificates_ServicePackageName_CodePackageName_CertName_PFX and 
          Certificates_ServicePackageName_CodePackageName_CertName_Password environment variables. The PEM file is accessible in the container using the 
          Certificates_ServicePackageName_CodePackageName_CertName_PEM and Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey environment variables.-->
        <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[CertThumbprint]" />

        <!-- If the certificate is already in PFX or PEM form, you can create a data package inside your application and reference that certificate here. -->
        <CertificateRef Name="MyCert2" DataPackageRef="Data" DataPackageVersion="1.0.0" RelativePath="MyCert2.PFX" Password="ENCRYPTED-PASSWORD" IsPasswordEncrypted="true"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
        
    <Service Name="FrontEndService" >
      <StatelessService ServiceTypeName="FrontEndServiceType" InstanceCount="[FrontEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
        <Service Name="BackEndService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="BackEndServiceType" InstanceCount="[BackEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="frontendservice-service-manifest"></a>FrontEndService サービス マニフェスト

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="FrontEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="FrontEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricfrontendservice:v1</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container or exe.  These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="IsContainer" Value=""/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Data package is the contents of the Data directory under PackageRoot that contains an 
       independently-updateable and versioned static data that's consumed by the process at runtime. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme and port 80 -->
      <Endpoint Name="FrontEndServiceTypeEndpoint" UriScheme="http" Port="80"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="backendservice-service-manifest"></a>BackEndService サービス マニフェスト

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="BackEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="BackEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricbackendservice:v1</ImageName>
        
        <!-- Pass comma delimited commands to your container. -->
        <Commands> dotnet, myproc.dll, 5 </Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container. These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the host port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme. In this case since no port is specified, one is created and assigned dynamically
           to the service. This dynamically assigned host port is mapped to the container port (8905 in this sample),
            which was specified in the application manifest.-->
      <Endpoint Name="BackEndServiceTypeEndpoint" UriScheme="http" />
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

### <a name="policies-element"></a>Policies 要素
インポートされたサービス マニフェストに適用されるポリシー (エンドポイント バインディング、パッケージ共有、実行アカウント、セキュリティ アクセス) について説明します。 詳細については、「[Policies 要素](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)」を参照してください。

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy 要素
サービス パッケージ全体のレベルで適用するリソース ガバナンス ポリシーを定義します。 詳細については、「[ServicePackageResourceGovernancePolicy 要素](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)」を参照してください。

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy 要素
コード パッケージのリソース制限を指定します。 詳細については、「[ResourceGovernancePolicy 要素](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)」を参照してください。

### <a name="containerhostpolicies-element"></a>ContainerHostPolicies 要素
コンテナー ホストをアクティブ化するためのポリシーを指定します。 詳細については、「[ContainerHostPolicies 要素](service-fabric-service-model-schema-elements.md#ContainerHostPoliciesElementContainerHostPoliciesTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)」を参照してください。

### <a name="repositorycredentials-element"></a>RepositoryCredentials 要素
イメージをプロトコルするコンテナー イメージ リポジトリの資格情報。 詳細については、「[RepositoryCredentials 要素](service-fabric-service-model-schema-elements.md#RepositoryCredentialsElementRepositoryCredentialsTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)」を参照してください。

### <a name="portbinding-element"></a>PortBinding 要素
公開されているコンテナー ポートにバインドするエンドポイント リソースを指定します。 詳細については、「[PortBinding 要素](service-fabric-service-model-schema-elements.md#PortBindingElementPortBindingTypeComplexTypeDefinedInServicePackageContainerPolicyTypecomplexTypeDefinedInContainerHostPoliciesTypecomplexType)」を参照してください。

### <a name="volume-element"></a>Volume 要素
コンテナーにバインドするボリュームを指定します。 詳細については、「[Volume 要素](service-fabric-service-model-schema-elements.md#VolumeElementContainerVolumeTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)」を参照してください。

### <a name="driveroption-element"></a>DriverOption 要素
ドライバーに渡されるドライバーのオプション。 詳細については、「[DriverOption 要素](service-fabric-service-model-schema-elements.md#DriverOptionElementDriverOptionTypeComplexTypeDefinedInContainerLoggingDriverTypecomplexTypeDefinedInContainerVolumeTypecomplexType)」を参照してください。

### <a name="imageoverrides-element"></a>ImageOverrides 要素
Windows Server コンテナーは、OS の異なるバージョン間では互換性がない場合があります。  コンテナーごとに複数の OS イメージを指定し、OS のビルド バージョンでタグ付けすることができます。 Windows コマンド プロンプトで "winver" を実行して、OS のビルド バージョンを取得してください。 基になる OS のビルド バージョンが 16299 (Windows Server バージョン 1709) である場合、Service Fabric は Os = "16299" とタグ付けされているコンテナー イメージを取得します。 タグ付けされていないコンテナー イメージは OS のすべてのバージョンで動作すると見なされ、サービス マニフェストで指定されたイメージをオーバーライドします。 詳細については、「[ImageOverrides 要素](service-fabric-service-model-schema-elements.md#ImageOverridesElementImageOverridesTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)」を参照してください。

### <a name="image-element"></a>Image 要素
起動する OS のビルド バージョン番号に対応するコンテナー イメージ。 Os 属性が指定されていない場合、コンテナー イメージは OS のすべてのバージョンで動作すると見なされ、サービス マニフェストで指定されたイメージをオーバーライドします。 詳細については、「[Image 要素](service-fabric-service-model-schema-elements.md#ImageElementImageTypeComplexTypeDefinedInImageOverridesTypecomplexType)」を参照してください。

### <a name="environmentoverrides-element"></a>EnvironmentOverrides 要素
 詳細については、「[EnvironmentOverrides 要素](service-fabric-service-model-schema-elements.md#EnvironmentOverridesElementEnvironmentOverridesTypeComplexTypeDefinedInServiceManifestImportelement)」を参照してください。

### <a name="environmentvariable-element"></a>EnvironmentVariable 要素
環境変数。 詳細については、「[EnvironmentVariable 要素](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)」を参照してください。

### <a name="certificateref-element"></a>CertificateRef 要素
コンテナー環境に公開される X509 証明書に関する情報を指定します。 証明書は、すべてのクラスター ノードの LocalMachine ストアにインストールする必要があります。
アプリケーションの起動時に、ランタイムは証明書を読み取り、PFX ファイルとパスワード (Windows の場合) または PEM ファイル (Linux の場合) を生成します。
PFX ファイルとパスワードには、コンテナーで Certificates_ServicePackageName_CodePackageName_CertName_PFX および Certificates_ServicePackageName_CodePackageName_CertName_Password 環境変数を使用してアクセスできます。 PEM ファイルには、コンテナーで Certificates_ServicePackageName_CodePackageName_CertName_PEM および Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey 環境変数を使用してアクセスできます。 詳細については、「[CertificateRef 要素](service-fabric-service-model-schema-elements.md#CertificateRefElementContainerCertificateTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)」を参照してください。

### <a name="defaultservices-element"></a>DefaultServices 要素
アプリケーションがこのアプリケーションの種類に対してインスタンス化されるたびに自動的に作成されるサービス インスタンスを宣言します。 詳細については、「[DefaultServices 要素](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)」を参照してください。

### <a name="service-element"></a>Service 要素
アプリケーションのインスタンス化時に自動的に作成するサービスを宣言します。 詳細については、「[Service 要素](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)」を参照してください。

### <a name="statelessservice-element"></a>StatelessService 要素
ステートレス サービスを定義します。 詳細については、「[StatelessService 要素](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)」を参照してください。


## <a name="frontendservice-service-manifest-elements"></a>FrontEndService サービス マニフェスト要素
### <a name="servicemanifest-element"></a>ServiceManifest 要素
宣言によって、サービスの種類とバージョンについて説明します。 1 つ以上のサービスの種類をサポートするよう、サービス パッケージを構成するアップグレード可能なコード、構成、データ パッケージの一覧を別に列挙します。 リソース、診断設定、サービス メタデータ (サービスの種類、正常性のプロパティ、負荷分散のメトリックなど) も指定します。 詳細については、「[ServiceManifest 要素](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)」を参照してください。

### <a name="servicetypes-element"></a>ServiceTypes 要素
このマニフェストの CodePackage でサポートされているサービスの種類を定義します。 これらのサービスの種類のいずれかに対してサービスがインスタンス化されると、このマニフェストで宣言されているすべてのコード パッケージは、エントリ ポイントを実行してアクティブ化されます。 サービスの種類は、コード パッケージ レベルではなく、マニフェスト レベルで宣言されます。 詳細については、「[ServiceTypes 要素](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)」を参照してください。

### <a name="statelessservicetype-element"></a>StatelessServiceType 要素
ステートレス サービスの種類について説明します。 詳細については、「[StatelessServiceType 要素](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)」を参照してください。

### <a name="codepackage-element"></a>CodePackage 要素
定義済みのサービスの種類をサポートするコード パッケージについて説明します。 これらのサービスの種類のいずれかに対してサービスがインスタンス化されると、このマニフェストで宣言されているすべてのコード パッケージは、エントリ ポイントを実行してアクティブ化されます。 実行時に、サポートされているサービスの種類を登録するために、結果のプロセスが必要になります。 複数のコード パッケージがあるとき、システムが宣言されたサービスの種類のいずれかを検索するときは常に、すべてアクティブ化されます。 詳細については、「[CodePackage 要素](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)」を参照してください。

### <a name="entrypoint-element"></a>EntryPoint 要素
EntryPoint によって指定された実行可能ファイルは通常は実行時間の長いサービス ホストです。 別々にセットアップされたエントリ ポイントの存在により、長期にわたって高い権限でサービス ホストを実行する必要がなくなります。 EntryPoint で指定された実行可能ファイルは、SetupEntryPoint が正常に終了した後に実行されます。 結果のプロセスは、終了またはクラッシュした場合に、監視されて再起動されます (SetupEntryPoint で再起動)。 詳細については、「[EntryPoint 要素](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)」を参照してください。

### <a name="containerhost-element"></a>ContainerHost 要素
 詳細については、「[ContainerHost 要素](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)」を参照してください。

### <a name="imagename-element"></a>ImageName 要素
[https://hub.docker.com](https://hub.docker.com) または Azure Container Registry のリポジトリおよびイメージ。 詳細については、「[ImageName 要素](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)」を参照してください。

### <a name="environmentvariables-element"></a>EnvironmentVariables 要素
コンテナーまたは exe に環境変数を渡します。  詳細については、「[EnvironmentVariables 要素](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)」を参照してください。

### <a name="environmentvariable-element"></a>EnvironmentVariable 要素
環境変数。 詳細については、「[EnvironmentVariable 要素](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)」を参照してください。

### <a name="configpackage-element"></a>ConfigPackage 要素
Settings.xml ファイルを含む Name 属性を使用して名前が付けられたフォルダーを宣言します。 このファイルには、実行時にプロセスが読み取ることができるユーザー定義のキー値ペアの設定のセクションが含まれています。 アップグレード中に ConfigPackage のバージョンのみが変更された場合、実行中のプロセスは再起動されません。 代わりに、コールバックは構成設定が変更されたことをプロセスに通知して、動的に再読み込みされるようにします。 詳細については、「[ConfigPackage 要素](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)」を参照してください。

### <a name="datapackage-element"></a>DataPackage 要素
静的データ ファイルを含む Name 属性を使用して名前が付けられたフォルダーを宣言します。 サービス マニフェストに含まれるデータ パッケージのいずれかがアップグレードされると、Service Fabric は、ホストおよびサポート パッケージに指定されているすべての EXE と DLLHOST をリサイクルします。 詳細については、「[DataPackage 要素](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)」を参照してください。

### <a name="resources-element"></a>Resources 要素
このサービスに使用されるリソースについて説明します。これは、コンパイル済みのコードを変更することなく宣言できます。また、サービスのデプロイ時に変更できます。 これらのリソースへのアクセスは、アプリケーション マニフェストの Principals および Policies セクションで制御されます。 詳細については、「[Resources 要素](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)」を参照してください。

### <a name="endpoints-element"></a>Endpoints 要素
サービスのエンドポイントを定義します。 詳細については、「[Endpoints 要素](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)」を参照してください。

### <a name="endpoint-element"></a>Endpoint 要素
詳細については、「[Endpoint 要素](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)」を参照してください。


## <a name="backendservice-service-manifest-elements"></a>BackEndService サービス マニフェスト要素
### <a name="servicemanifest-element"></a>ServiceManifest 要素
宣言によって、サービスの種類とバージョンについて説明します。 1 つ以上のサービスの種類をサポートするよう、サービス パッケージを構成するアップグレード可能なコード、構成、データ パッケージの一覧を別に列挙します。 リソース、診断設定、サービス メタデータ (サービスの種類、正常性のプロパティ、負荷分散のメトリックなど) も指定します。 詳細については、「[ServiceManifest 要素](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)」を参照してください。

### <a name="servicetypes-element"></a>ServiceTypes 要素
このマニフェストの CodePackage でサポートされているサービスの種類を定義します。 これらのサービスの種類のいずれかに対してサービスがインスタンス化されると、このマニフェストで宣言されているすべてのコード パッケージは、エントリ ポイントを実行してアクティブ化されます。 サービスの種類は、コード パッケージ レベルではなく、マニフェスト レベルで宣言されます。 詳細については、「[ServiceTypes 要素](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)」を参照してください。

### <a name="statelessservicetype-element"></a>StatelessServiceType 要素
ステートレス サービスの種類について説明します。 詳細については、「[StatelessServiceType 要素](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)」を参照してください。

### <a name="codepackage-element"></a>CodePackage 要素
定義済みのサービスの種類をサポートするコード パッケージについて説明します。 これらのサービスの種類のいずれかに対してサービスがインスタンス化されると、このマニフェストで宣言されているすべてのコード パッケージは、エントリ ポイントを実行してアクティブ化されます。 実行時に、サポートされているサービスの種類を登録するために、結果のプロセスが必要になります。 複数のコード パッケージがあるとき、システムが宣言されたサービスの種類のいずれかを検索するときは常に、すべてアクティブ化されます。 詳細については、「[CodePackage 要素](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)」を参照してください。

### <a name="entrypoint-element"></a>EntryPoint 要素
EntryPoint によって指定された実行可能ファイルは通常は実行時間の長いサービス ホストです。 別々にセットアップされたエントリ ポイントの存在により、長期にわたって高い権限でサービス ホストを実行する必要がなくなります。 EntryPoint で指定された実行可能ファイルは、SetupEntryPoint が正常に終了した後に実行されます。 結果のプロセスは、終了またはクラッシュした場合に、監視されて再起動されます (SetupEntryPoint で再起動)。 詳細については、「[EntryPoint 要素](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)」を参照してください。

### <a name="containerhost-element"></a>ContainerHost 要素
詳細については、「[ContainerHost 要素](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)」を参照してください。

### <a name="imagename-element"></a>ImageName 要素
[https://hub.docker.com](https://hub.docker.com) または Azure Container Registry のリポジトリおよびイメージ。 詳細については、「[ImageName 要素](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)」を参照してください。

### <a name="commands-element"></a>Commands 要素
コマンドのコンマ区切りリストをコンテナーに渡します。 詳細については、「[Commands 要素](service-fabric-service-model-schema-elements.md#CommandsElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)」を参照してください。

### <a name="environmentvariables-element"></a>EnvironmentVariables 要素
コンテナーまたは exe に環境変数を渡します。  詳細については、「[EnvironmentVariables 要素](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)」を参照してください。

### <a name="environmentvariable-element"></a>EnvironmentVariable 要素
環境変数。 詳細については、「[EnvironmentVariable 要素](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)」を参照してください。

### <a name="configpackage-element"></a>ConfigPackage 要素
Settings.xml ファイルを含む Name 属性を使用して名前が付けられたフォルダーを宣言します。 このファイルには、実行時にプロセスが読み取ることができるユーザー定義のキー値ペアの設定のセクションが含まれています。 アップグレード中に ConfigPackage のバージョンのみが変更された場合、実行中のプロセスは再起動されません。 代わりに、コールバックは構成設定が変更されたことをプロセスに通知して、動的に再読み込みされるようにします。 詳細については、「[ConfigPackage 要素](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)」を参照してください。

### <a name="resources-element"></a>Resources 要素
このサービスに使用されるリソースについて説明します。これは、コンパイル済みのコードを変更することなく宣言できます。また、サービスのデプロイ時に変更できます。 これらのリソースへのアクセスは、アプリケーション マニフェストの Principals および Policies セクションで制御されます。 詳細については、「[Resources 要素](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)」を参照してください。

### <a name="endpoints-element"></a>Endpoints 要素
サービスのエンドポイントを定義します。 詳細については、「[Endpoints 要素](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)」を参照してください。

### <a name="endpoint-element"></a>Endpoint 要素
 詳細については、「[Endpoint 要素](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)」を参照してください。

