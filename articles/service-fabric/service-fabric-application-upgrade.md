---
title: Service Fabric アプリケーションのアップグレード
description: この記事では、アップグレード モードの選択や正常性チェックの実行など、Service Fabric アプリケーションのアップグレードの概要を紹介します。
ms.topic: conceptual
ms.date: 8/5/2020
ms.openlocfilehash: f3fad8d0ede92004706d9a1f4e14353715361b63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98792016"
---
# <a name="service-fabric-application-upgrade"></a>Service Fabric アプリケーションのアップグレード
Service Fabric アプリケーションは、サービスのコレクションです。 アップグレードの際、Service Fabric は新しい [アプリケーション マニフェスト](service-fabric-application-and-service-manifests.md) を以前のバージョンと比較し、アプリケーション内でアップグレードの必要があるサービスを決定します。 Service Fabric は、サービス マニフェスト内のバージョン番号を、以前のバージョンのバージョン番号と比較します。 サービスが変更されていない場合は、そのサービスはアップグレードされません。

> [!NOTE]
> [ApplicationParameter](/dotnet/api/system.fabric.description.applicationdescription.applicationparameters#System_Fabric_Description_ApplicationDescription_ApplicationParameters) は、アプリケーションをアップグレードすると保持されなくなります。 現在のアプリケーション パラメーターを保持するには、まずパラメーターを取得して、次のようにアップグレード API 呼び出しに渡します。
```powershell
$myApplication = Get-ServiceFabricApplication -ApplicationName fabric:/myApplication
$appParamCollection = $myApplication.ApplicationParameters

$applicationParameterMap = @{}
foreach ($pair in $appParamCollection)
{
    $applicationParameterMap.Add($pair.Name, $pair.Value);
}

Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/myApplication -ApplicationTypeVersion 2.0.0 -ApplicationParameter $applicationParameterMap -Monitored -FailureAction Rollback
```

## <a name="rolling-upgrades-overview"></a>ローリング アップグレードの概要
アプリケーションのローリング アップグレードでは、アップグレードは段階的に実行されます。 各段階で、アップグレードは、更新ドメインと呼ばれる、クラスター内のノードのサブセットに適用されます。 その結果、アプリケーションはアップグレード全体を通して引き続き使用できます。 アップグレード中に、クラスターに古いバージョンと新しいバージョンが混在することがあります。

そのため、その 2 つのバージョンには上位互換性と下位互換性がある必要があります。 互換性がない場合は、アプリケーション管理者が複数フェーズのアップグレードを担当して可用性を維持します。 複数フェーズのアップグレードでは、最初に、以前のバージョンと互換性のあるアプリケーションの中間バージョンにアップグレードします。 次に、更新前のバージョンとの互換性を破る最終バージョンにアップグレードします。この最終バージョンは、中間バージョンに対応しています。

更新ドメインは、クラスターを構成するときに、クラスター マニフェストで指定されます。 更新ドメインが更新を受け取る順序は特に決まってはいません。 更新ドメインは、アプリケーションのデプロイの論理単位です。 更新ドメインを使用すると、アップグレード中、サービスの可用性は高く維持されます。

アップグレードがクラスター内のすべてのノードに適用される場合 (アプリケーションに含まれている更新ドメインが 1 つのみの場合) は、非ローリング アップグレードを行うことができます。 このアプローチは推奨されません。サービスがダウンして、アップグレード時に利用可能にならないためです。 さらに、Azure では、クラスターが 1 つだけの更新ドメインでセットアップされた場合、一切保証しません。

アップグレードが完了したら、すべてのサービスおよびレプリカ (インスタンス) は同じバージョンに保管されます。つまり、アップグレードに成功すると、サービスおよびレプリカも新しいバージョンに更新されます。また、アップグレードに失敗してロールバックされると、これらも古いバージョンにロールバックされます。

## <a name="health-checks-during-upgrades"></a>アップグレード時の正常性チェック
アップグレードには、正常性ポリシーを設定する必要があります (既定値を使用することもできます)。 指定されたタイムアウト内にすべての更新ドメインがアップグレードされた場合や、すべての更新ドメインが正常であると判断された場合は、アップグレードが成功したことになります。  正常な更新ドメインとは、その更新ドメインが、正常性ポリシーで指定されているすべての正常性チェックに合格したことを意味します。 たとえば、正常性ポリシーでは、Service Fabric で正常性が定義されているように、アプリケーション インスタンス内のすべてのサービスが *正常* であることが要求される場合があります。

アップグレード中の Service Fabric による正常性ポリシーと正常性チェックはサービスとアプリケーションに依存しません。 つまり、サービス固有のテストは実行されません。  たとえば、サービスにスループット要件が必要になる場合がありますが、Service Fabric にはスループットを確認するための情報がありません。 実行されるチェックについては、 [正常性に関する記事](service-fabric-health-introduction.md) をご覧ください。 アップグレード中に実行されるチェックには、アプリケーション パッケージが正しくコピーされたかどうか、インスタンスが開始されたかどうかなどのテストが含まれます。

アプリケーションの正常性は、アプリケーションの子エンティティの集約です。 つまり、Service Fabric では、アプリケーションで報告された正常性によってアプリケーションの正常性が評価されます。 また、アプリケーションのすべてのサービスの正常性もそのように評価されます。 Service Fabric では、アプリケーション サービスの正常性が、サービスのレプリカなど、それらの子の正常性を集約することでさらに評価されます。 アプリケーションの正常性ポリシーが満たされると、アップグレードを続行できます。 正常性ポリシーに違反すると、アプリケーションのアップグレードは失敗します。

## <a name="upgrade-modes"></a>アップグレード モード
アプリケーションのアップグレードに推奨されるモードは、監視対象モードです。これは、よく使用されるモードです。 監視対象モードでは、1 つの更新ドメインでアップグレードを実行し、(指定されたポリシーに従って) すべての正常性チェックに合格すると、自動的に次の更新ドメインに移ります。  正常性チェックが失敗した場合やタイムアウトに達した場合は、更新ドメインのアップグレードはロールバックされるか、監視対象外手動モードに切り替えられます。 アップグレードを構成するときに、この 2 つのモードのいずれかを、アップグレードが失敗した場合のモードとして選択できます。 

監視対象外手動モードでは、更新ドメインですべてのアップグレードが行われた後、次の更新ドメインでアップグレードを開始するために、手動操作が必要になります。 Service Fabric の正常性チェックは実行されません。 管理者は、次の更新ドメインでアップグレードを開始する前に、正常性または状態のチェックを実行します。

## <a name="upgrade-default-services"></a>既定のサービスをアップグレードする
アプリケーションのアップグレードの一環として、[アプリケーション マニフェスト](service-fabric-application-and-service-manifests.md)で定義されている一部の既定のサービス パラメーターもアップグレードできます。 アップグレードの一環として変更できるのは、[Update-ServiceFabricService](/powershell/module/servicefabric/update-servicefabricservice) による変更がサポートされているサービス パラメーターのみです。 アプリケーションのアップグレード時に既定のサービス パラメーターを変更する動作は次のとおりです。

1. クラスターに既に存在していない新しいアプリケーション マニフェストの既定のサービスは、作成されます。
2. 以前のアプリケーション マニフェストと新しいアプリケーション マニフェストの両方に存在している既定のサービスは、更新されます。 新しいアプリケーション マニフェストの既定のサービスのパラメーターによって、既存のサービスのパラメーターが上書きされます。 既定のサービスの更新が失敗した場合、アプリケーションのアップグレードは自動的にロールバックされます。
3. 新しいアプリケーション マニフェストに存在しない既定のサービスがクラスターに存在する場合は、削除されます。 **既定のサービスを削除すると、そのサービスの状態がすべて削除され、元には戻せないことに注意してください。**

アプリケーションのアップグレードがロールバックされるときは、既定のサービス パラメーターがアップグレード開始前の古い値に戻されますが、削除されたサービスを以前の状態で再作成することはできません。

> [!TIP]
> 上記のルール 2) および 3) (既定のサービスの更新と削除) を有効にするには、[EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) クラスター構成設定を *true* にする必要があります。 この機能は、Service Fabric バージョン 5.5 以降でサポートされています。

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>HTTPS エンドポイントを持つ複数のアプリケーションのアップグレード
HTTP **S** の使用時は、同じアプリケーションの異なるインスタンスに対して **同じポート** を使用しないように注意する必要があります。 その理由は、Service Fabric は任意の 1 つのアプリケーション インスタンスの証明書をアップグレードできないためです。 たとえば、アプリケーション 1 とアプリケーション 2 で証明書 1 を証明書 2 にアップグレードする必要があるとします。 アップグレードが発生した場合、もう一方のアプリケーションが使用中であるのにもかかわらず、Service Fabric が http.sys により証明書 1 の登録をクリーンアップしてている可能性があります。 これを回避するために、Service Fabric は、証明書を持つポートに登録された他のアプリケーション インスタンスが既にあることを検出し (http.sys により)、その操作を失敗させます。

そのため、Service Fabric では、異なるアプリケーション インスタンスで **同じポート** を使用して 2 つの異なるサービスをアップグレードすることはできません。 つまり、同じポート上の異なるサービスで同じ証明書を使用することはできません。 同じポートで証明書を共有する必要がある場合は、必ずサービスを配置の制約がある異なるコンピューターに配置するようにしてください。 または、各アプリケーション インスタンスの各サービスに対して、可能であれば、Service Fabric 動的ポートを使用することを検討してください。 

https でアップグレードが失敗した場合、"Windows HTTP Server API が、ポートを共有するアプリケーションに対して複数の証明書をサポートしていない" ことを示す警告が表示されます。

## <a name="application-upgrade-flowchart"></a>アプリケーション アップグレードのフローチャート
以下に示すフローチャートは、Service Fabric アプリケーションのアップグレード プロセスをわかりやすく示しています。 特に、このフローでは 1 つの更新ドメインのアップグレードが成功または失敗と見なされるときに、*HealthCheckStableDuration*、*HealthCheckRetryTimeout*、*UpgradeHealthCheckInterval* などのタイムアウトが制御にどのように役立つかを説明します。

![Service Fabric アプリケーションのアップグレード プロセス][image]

## <a name="next-steps"></a>次のステップ
[Visual Studio を使用したアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial.md) に関する記事では、Visual Studio を使用してアプリケーションをアップグレードする方法について説明します。

[PowerShell を使用したアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial-powershell.md)に関する記事では、PowerShell を使用したアプリケーションのアップグレードについて説明します。

[アップグレード パラメーター](service-fabric-application-upgrade-parameters.md)を使用して、アプリケーションのアップグレード方法を制御します。

[データのシリアル化](service-fabric-application-upgrade-data-serialization.md)の方法を学ぶことで、アプリケーションのアップグレードに互換性を持たせます。

「 [高度なトピック](service-fabric-application-upgrade-advanced.md)」を参照して、アプリケーションをアップグレードするときの高度な機能の使用方法を学習します。

「[アプリケーションのアップグレードのトラブルシューティング](service-fabric-application-upgrade-troubleshooting.md)」の手順を参照して、アプリケーションのアップグレードでの一般的な問題を修正します。

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
