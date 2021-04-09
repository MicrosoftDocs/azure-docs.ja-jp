---
title: システムおよびローカル セキュリティ アカウントのもとでサービスを実行する
description: システムおよびローカル セキュリティ アカウントのもとで Service Fabric アプリケーションを実行する方法について説明します。  サービスを安全に実行するために、セキュリティ プリンシパルを作成し、RunAs ポリシーを適用します。
ms.topic: conceptual
ms.date: 03/29/2018
ms.openlocfilehash: 53212f8636602705899834b6db1d3f0d80b5fe4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75610125"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>ローカル ユーザー アカウントまたはローカル システム アカウントとしてサービスを実行する
Azure Service Fabric を使用すると、別のユーザー アカウントを使用してクラスターで実行しているアプリケーションをセキュリティで保護することができます。 既定では、Service Fabric アプリケーションは、Fabric.exe プロセスを実行しているアカウントで実行されます。 Service Fabric には、ローカル ユーザーまたはシステム アカウントでアプリケーションを実行する機能もあります。 サポートされているローカル システム アカウントの種類は、**LocalUser**、**NetworkService**、**LocalService**、**LocalSystem** です。  Windows スタンドアロン クラスターで Service Fabric を実行している場合は、[Active Directory ドメイン アカウント](service-fabric-run-service-as-ad-user-or-group.md)または[グループ管理サービス アカウント](service-fabric-run-service-as-gmsa.md)でサービスを実行することができます。

アプリケーション マニフェストの **Principals** セクションで、サービスの実行またはリソースの保護に必要なユーザー アカウントを定義します。 1 人以上のユーザーをまとめて管理できるように、ユーザー グループを定義および作成することもできます。 これは、異なるサービス エントリ ポイントに対して複数のユーザーが存在し、それらのユーザーがグループ レベルで使用できる共通の特権を必要とする場合に便利です。  ユーザーは、アプリケーション内の特定のサービスまたはすべてのサービスに適用される RunAs ポリシーで参照されます。 

既定では、RunAs ポリシーはメイン エントリ ポイントに適用されます。  [システム アカウントで特定の高特権セットアップ操作を実行する](service-fabric-run-script-at-service-startup.md)必要がある場合は、RunAs ポリシーをセットアップ エントリ ポイントに適用することもでき、メイン エントリ ポイントとセットアップ エントリポイントの両方に適用することもできます。  

> [!NOTE] 
> サービスに RunAs ポリシーを適用し、サービス マニフェストで HTTP プロトコルのエンドポイント リソースを宣言する場合は、**SecurityAccessPolicy** を指定する必要があります。  詳細については、[HTTP と HTTPS エンドポイントのセキュリティ アクセス ポリシーを割り当てる](service-fabric-assign-policy-to-endpoint.md)をご覧ください。 
>

## <a name="run-a-service-as-a-local-user"></a>ローカル ユーザーとしてのサービスの実行
アプリケーション内のサービスをセキュリティで保護するために使用できるローカル ユーザーを作成できます。 アプリケーション マニフェストの Principals セクションでアカウントの種類として **LocalUser** が指定されている場合、Service Fabric はアプリケーションがデプロイされているコンピューターにローカル ユーザー アカウントを作成します。 既定では、これらのアカウントにアプリケーション マニフェストで指定されているものと同じ名前は付けられません (たとえば、次のアプリケーション マニフェストの例では *Customer3*)。 代わりに、動的に生成され、ランダムなパスワードが与えられます。

**ServiceManifestImport** の **RunAsPolicy** セクションでは、**Principals** セクションからユーザー アカウントを指定してサービス コード パッケージを実行します。  次の例は、ローカル ユーザーを作成し、メイン エントリ ポイントに RunAs ポリシーを適用する方法を示しています。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="Customer3" />
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="create-a-local-user-group"></a>ローカル ユーザー グループを作成する
ユーザー グループを作成し、グループに 1 人以上のユーザーを追加できます。 これは、異なるサービス エントリ ポイントに対して複数のユーザーが存在し、グループ レベルで使用できる特定の共通の特権が必要な場合に便利です。 次のアプリケーション マニフェスト例は、管理者特権を持つ *LocalAdminGroup* というローカル グループを示しています。 2 人のユーザー、*Customer1* と *Customer2* がこのローカル グループのメンバーになっています。 **ServiceManifestImport** セクションで、*Stateful1Pkg* コード パッケージを *Customer2* として実行する RunAs ポリシーが適用されます。  *Web1Pkg* コード パッケージを *Customer1* として実行する別の RunAs ポリシーが適用されます。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer2" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="Stateful1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Groups>
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <User Name="Customer1">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <User Name="Customer2">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>すべてのサービス コード パッケージに既定のポリシーを適用する
**DefaultRunAsPolicy** セクションを使用して、特定の **RunAsPolicy** が定義されていないすべてのコード パッケージに既定のユーザー アカウントを指定します。 アプリケーションによって使用されるサービス マニフェストで指定されているほとんどのコード パッケージが同じユーザーで実行される必要がある場合、そのユーザー アカウントで既定の RunAs ポリシーのみを定義することができます。 次の例では、コード パッケージで **RunAsPolicy** が指定されていない場合、コード パッケージは Principals セクションで指定されている **MyDefaultAccount** ユーザーを使用して実行する必要があります。  サポートされているアカウントの種類は、LocalUser、NetworkService、LocalSystem、LocalService です。  ローカル ユーザーまたはサービスを使用する場合は、アカウント名とパスワードも指定します。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <Policies>
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
  </Policies>
</ApplicationManifest>
```

## <a name="debug-a-code-package-locally-using-console-redirection"></a>コンソール リダイレクトを使用してローカルでコード パッケージをデバッグする
場合によっては、デバッグ目的で実行中のサービスの実行結果をコンソールに出力し、確認すると便利です。 サービス マニフェストのエントリ ポイントでは、コンソール リダイレクト ポリシーを設定できます。これにより出力がファイルに書き込まれます。 ファイル出力はアプリケーションがデプロイおよび実行されるクラスター ノード上の **log** と呼ばれるアプリケーション フォルダーに書き込まれます。 

> [!WARNING]
> アプリケーションのフェールオーバーに影響する可能性があるため、運用環境でデプロイされたアプリケーションのコンソール リダイレクト ポリシーは決して使用しないでください。 これは、ローカル デプロイおよびデバッグの目的のため "*だけ*" に使用します。  
> 
> 

次のサービス マニフェストの例は、FileRetentionCount 値を使用したコンソール リダイレクトの有効化を示しています。

```xml
<CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
        <ConsoleRedirection FileRetentionCount="10"/>
      </ExeHost>
    </EntryPoint>
</CodePackage>

```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>次のステップ
* [アプリケーション モデルを理解する](service-fabric-application-model.md)
* [サービス マニフェストにリソースを指定する](service-fabric-service-manifest-resources.md)
* [アプリケーションをデプロイする](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
