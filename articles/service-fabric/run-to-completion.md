---
title: Service Fabric での RunToCompletion セマンティクス
description: Service Fabric での RunToCompletion セマンティクスについて説明します。
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: 6f2f6aa4380fcf6909957118bf682275350ce68c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000268"
---
# <a name="runtocompletion"></a>RunToCompletion

Service Fabric バージョン 7.1 以降では、[コンテナー][containers-introduction-link]と [ゲスト実行可能ファイル][guest-executables-introduction-link] アプリケーション用の **RunToCompletion** セマンティクスがサポートされています。 これらのセマンティクスにより、常に実行され続けるアプリケーションやサービスに対し、タスクを完了して終了するアプリケーションやサービスが可能になります。

この記事を読み進める前に、[Service Fabric のアプリケーション モデル][application-model-link]と [Service Fabric のホスティング モデル][hosting-model-link]を十分に理解しておくことをお勧めします。

> [!NOTE]
> 現在、RunToCompletion セマンティクスは、[Reliable Services][reliable-services-link] プログラミング モデルを使用して作成されたサービスではサポートされていません。
 
## <a name="runtocompletion-semantics-and-specification"></a>RunToCompletion のセマンティクスと仕様
RunToCompletion セマンティクスは、[ServiceManifest のインポート][application-and-service-manifests-link]時に **ExecutionPolicy** として指定できます。 指定されたポリシーは、ServiceManifest を構成するすべての CodePackage によって継承されます。 次の ApplicationManifest.xml スニペットは、その例を示しています。

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**ExecutionPolicy** では、次の 2 つの属性を使用できます。
* **種類:** 現在、この属性で許可されている唯一の値は **RunToCompletion** です。
* **Restart:** この属性では、エラー発生時に、ServicePackage を構成する CodePackage に適用される再起動ポリシーを指定します。 CodePackage が **0 以外の終了コード** で終了すると、失敗したと見なされます。 この属性に使用できる値は **OnFailure** と **Never** であり、既定値は **OnFailure** です。

再起動ポリシーが **OnFailure** に設定されていて、いずれかの CodePackage が失敗した場合 **(0 以外の終了コード)** は、再起動され、繰り返し発生するエラー間でバックオフが発生します。 再起動ポリシーが **Never** に設定されていて、いずれかの CodePackage が失敗した場合、DeployedServicePackage のデプロイ状態は **Failed** としてマークされますが、他の CodePackage は実行の継続が許可されます。 ServicePackage を構成するすべての CodePackage が実行されて正常に完了した場合 **(終了コード 0)** 、DeployedServicePackage のデプロイ状態は **RanToCompletion** としてマークされます。 

## <a name="complete-example-using-runtocompletion-semantics"></a>RunToCompletion セマンティクスを使用した完全な例

RunToCompletion セマンティクスを使用した完全な例を見てみましょう。

> [!IMPORTANT]
> 次の例では、Service Fabric と Docker を使用した [Windows コンテナー アプリケーションの作成][containers-getting-started-link]に精通していることを前提としています。
>
> この例では、mcr.microsoft.com/windows/nanoserver:1809 を参照します。 Windows Server コンテナーは、ホスト OS のすべてのバージョンで互換性はありません。 詳細については、「[Windows コンテナーのバージョンの互換性](/virtualization/windowscontainers/deploy-containers/version-compatibility)」を参照してください。

次の ServiceManifest.xml では、コンテナーを表す 2 つの CodePackage で構成される ServicePackage を記述しています。 *RunToCompletionCodePackage1* では、単に **stdout** へのメッセージをログに記録して終了します。 *RunToCompletionCodePackage2* では、しばらくの間ループバック アドレスに ping を実行し、終了コード **0**、**1**、または **2** で終了します。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsRunToCompletionServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows RunToCompletion Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsRunToCompletionServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="RunToCompletionCodePackage1" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from RunToCompletionCodePackage1 &amp;&amp; exit 0</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="RunToCompletionCodePackage2" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/v,/c,ping 127.0.0.1 &amp;&amp; set /a exitCode=%random% % 3 &amp;&amp; exit !exitCode!</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

次の ApplicationManifest.xml では、前述の ServiceManifest.xml を基にしたアプリケーションを記述しています。 再起動ポリシー **OnFailure** を使用して、*WindowsRunToCompletionServicePackage* に **RunToCompletion** **ExecutionPolicy** を指定します。 *WindowsRunToCompletionServicePackage* をアクティブ化すると、その構成要素 CodePackage が起動されます。 *RunToCompletionCodePackage1* は、最初のアクティブ化で正常に終了する必要があります。 ただし、*RunToCompletionCodePackage2* が失敗した場合 **(0 以外の終了コード)** 、再起動ポリシーが **OnFailure** であるため、再起動されます。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsRunToCompletionApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows RunToCompletion Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsRunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsRunToCompletionService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsRunToCompletionServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>DeployedServicePackage のデプロイ状態のクエリ
DeployedServicePackage のデプロイ状態は、[Get-ServiceFabricDeployedServicePackage][deployed-service-package-link] を使用して PowerShell から、または [FabricClient][fabric-client-link] API [GetDeployedServicePackageListAsync(String, Uri, String)][deployed-service-package-fabricclient-link] を使用して C# からクエリできます。

## <a name="considerations-when-using-runtocompletion-semantics"></a>RunToCompletion セマンティクスを使用する際の考慮事項

現在の RunToCompletion のサポートについては、次の点に注意してください。
* これらのセマンティクスは、[コンテナー][containers-introduction-link]と[ゲスト実行可能ファイル][guest-executables-introduction-link] アプリケーションでのみサポートされています。
* RunToCompletion セマンティクスを使用するアプリケーションのアップグレード シナリオは許可されません。 ユーザーは、必要に応じて、このようなアプリケーションを削除して再作成する必要があります。
* フェールオーバー イベントが発生すると、CodePackage が正常に完了した後、同じノード上またはクラスターの他のノード上で再実行される可能性があります。 フェールオーバー イベントの例としては、ノードの再起動や、ノードでの Service Fabric ランタイム アップグレードがあります。

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
[application-and-service-manifests-link]: service-fabric-application-and-service-manifests.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
[deployed-service-package-working-with-link]: service-fabric-hosting-model.md#work-with-a-deployed-service-package
[deployed-code-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedservicepackage
[fabric-client-link]: /dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: /dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync
