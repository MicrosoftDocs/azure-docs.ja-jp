---
title: Service Fabric の初期化子 CodePackage
description: Service Fabric の初期化子 CodePackage について説明します。
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 3be079b97c2660437344f88203fdda06cc6d6740
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86258982"
---
# <a name="initializer-codepackages"></a>初期化子 CodePackage

Service Fabric では、バージョン 7.1 以降、[コンテナー][containers-introduction-link]と [ゲスト実行可能ファイル][guest-executables-introduction-link] アプリケーション用の **初期化子 CodePackage** をサポートします。 初期化子 CodePackage を使用すると、他の CodePackage の実行を開始する前に、ServicePackage スコープで初期化を実行できます。 ServicePackage との関係は、[SetupEntryPoint][setup-entry-point-link] と CodePackage の関係に似ています。

この記事を読み進める前に、[Service Fabric のアプリケーション モデル][application-model-link]と [Service Fabric のホスティング モデル][hosting-model-link]を十分に理解しておくことをお勧めします。

> [!NOTE]
> 現在、初期化子 CodePackage は、[Reliable Services][reliable-services-link] プログラミング モデルを使用して作成されたサービスではサポートされていません。
 
## <a name="semantics"></a>セマンティクス

初期化子 CodePackages は、**正常に終了する (終了コード 0)** まで実行することが想定されています。 失敗した初期化子 CodePackage は、正常に終了するまで再起動されます。 複数の初期化子 CodePackage を使用することができます。これらが **指定された順序で**、**順番に**、**正常に終了する** と、ServicePackage 内の他の CodePackage の実行が開始されます。

## <a name="specifying-initializer-codepackages"></a>初期化子 CodePackage を指定する
ServiceManifest で **Initializer** 属性を **true** に設定することにより、CodePackage を初期化子としてマークすることができます。 複数の初期化子 CodePackage がある場合、その実行の順序は、**ExecOrder** 属性で指定することができます。 **ExecOrder** は、負ではない整数である必要があり、初期化子 CodePackage に対してのみ有効です。 **ExecOrder** の値が小さい初期化子 CodePackage から順に実行されます。 初期化子 CodePackage に対して **ExecOrder** を指定しない場合、既定値の 0 と見なされます。 **ExecOrder** の値が同じ初期化子 CodePackage の相対的な実行の順序は未確定です。

次の ServiceManifest のスニペットでは、3 つの CodePackage が記述されており、そのうちの 2 つが初期化子としてマークされています。 この ServicePackage がアクティブになると、*InitCodePackage0* の **ExecOrder** 値が最も小さいため、これが最初に実行されます。 *InitCodePackage0* が正常に終了すると (終了コード 0)、*InitCodePackage1* が実行されます。 最後に、*InitCodePackage1* が正常に終了すると、*WorkloadCodePackage* が実行されます。

```xml
<CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
  ...
</CodePackage>

<CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
  ...
</CodePackage>

<CodePackage Name="WorkloadCodePackage" Version="1.0">
  ...
</CodePackage>
```
## <a name="complete-example-using-initializer-codepackages"></a>初期化子 CodePackage を使用した完全な例

初期化子 CodePackage を使用した完全な例を見てみましょう。

> [!IMPORTANT]
> 次の例では、[Service Fabric と Docker を使用した Windows コンテナー アプリケーション][containers-getting-started-link]の作成に精通していることを前提としています。
>
> この例では、mcr.microsoft.com/windows/nanoserver:1809 を参照します。 Windows Server コンテナーは、ホスト OS のすべてのバージョンで互換性はありません。 詳細については、「[Windows コンテナーのバージョンの互換性](/virtualization/windowscontainers/deploy-containers/version-compatibility)」を参照してください。

次の ServiceManifest.xml は、前述の ServiceManifest のスニペットを基にビルドされています。 *InitCodePackage0*、*InitCodePackage1*、*WorkloadCodePackage* は、コンテナーを表す CodePackage です。 アクティブになると、*InitCodePackage0* が最初に実行されます。 これは、メッセージをファイルに記録して終了します。 次に、*InitCodePackage1* が実行されます。これもメッセージをファイルに記録して終了します。 最後に、*WorkloadCodePackage* が実行を開始します。 これもメッセージをファイルに記録し、ファイルの内容を **stdout** に出力して、永続的に ping を実行します。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsInitCodePackageServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows Init CodePackage Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsInitCodePackageServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage0. &gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage1. &gt;&gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="WorkloadCodePackage" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from WorkloadCodePackage. &gt;&gt; C:\WorkspaceOnContainer\log.txt &amp;&amp; type C:\WorkspaceOnContainer\log.txt &amp;&amp; ping -t 127.0.0.1 &gt; nul</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

次の ApplicationManifest.xml では、前述の ServiceManifest.xml を基にしたアプリケーションを記述しています。 これは、すべてのコンテナーに対して同じ **Volume** マウントが指定されています。つまり、3 つのすべてのコンテナーの **C:\WorkspaceOnContainer** に、**C:\WorkspaceOnHost** がマウントされます。 実際には、すべてのコンテナーが、アクティブ化された順に、同じログ ファイルに書き込みを行うことになります。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsInitCodePackageApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows Init CodePackage Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsInitCodePackageServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ContainerHostPolicies CodePackageRef="InitCodePackage0" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

     <ContainerHostPolicies CodePackageRef="InitCodePackage1" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

      <ContainerHostPolicies CodePackageRef="WorkloadCodePackage" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsInitCodePackageService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsInitCodePackageServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
ServicePackage が正常にアクティブ化されると、**C:\WorkspaceOnHost\log.txt** の内容は次のようになります。

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

## <a name="next-steps"></a>次のステップ

関連情報については、次の記事を参照してください。

* [Service Fabric とコンテナー。][containers-introduction-link]
* [Service Fabric とゲスト実行可能ファイル。][guest-executables-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
