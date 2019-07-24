---
title: 高度なアプリケーション アップグレードに関するトピック | Microsoft Docs
description: この記事では、Service Fabric アプリケーションのアップグレードに関連する高度なトピックについて説明します。
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: e29585ff-e96f-46f4-a07f-6682bbe63281
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 3cdddac74552b56dfe3567adf30f1a05b6eb8e24
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663793"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Service Fabric アプリケーションのアップグレード: 高度なトピック
## <a name="adding-or-removing-service-types-during-an-application-upgrade"></a>アプリケーション アップグレード中のサービスの種類の追加と削除
アップグレードの一環として、発行したアプリケーションに新しいサービスの種類を追加した場合、その新しいサービスの種類はデプロイされているアプリケーションに追加されます。 このようなアップグレードは、既にアプリケーションの一部であるサービス インスタンスには影響しませんが、追加されたサービスの種類のインスタンスを作成して、新しいサービスの種類をアクティブにする必要があります (「[New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)」を参照)。

同様に、アップグレードの一環として、アプリケーションからサービスの種類を削除することもできます。 ただし、アップグレードに進む前に、削除するサービスの種類のサービス インスタンスをすべて削除する必要があります (「[Remove-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)」を参照)。

## <a name="manual-upgrade-mode"></a>手動アップグレード モード
> [!NOTE]
> すべての Service Fabric アップグレードで、*Monitored* アップグレード モードをお勧めします。
> *UnmonitoredManual* アップグレード モードは、アップグレードが失敗または中断した場合にのみ検討してください。 
>
>

*Monitored* モードでは、Service Fabric が正常性ポリシーを適用して、アップグレードの進行中にアプリケーションの正常性を確認します。 正常性ポリシーに違反すると、指定した *FailureAction* に応じて、アップグレードが中断されるか、自動的にロールバックされます。

*UnmonitoredManual* モードでは、アプリケーション管理者がアップグレードの進行を完全に制御します。 このモードは、カスタムの正常性評価ポリシーを適用する場合や、一般的でないアップグレードを実行して、正常性の監視を完全にバイパスする場合 (アプリケーションが既にデータ損失の状態にある場合など) に便利です。 このモードでアップグレードを実行すると、各 UD の完了後にアップグレードが中断し、[Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) を使用して明示的に再開する必要があります。 アップグレードが中断し、ユーザーが再開できる状態になると、そのアップグレードの状態は *RollforwardPending* と表示されます (「[UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)」を参照)。

最後に、*UnmonitoredAuto* モードは、ユーザーの入力が不要で、アプリケーションの正常性ポリシーが評価されないため、サービスの開発時やテスト時に迅速にアップグレードを繰り返し実行する場合に便利です。

## <a name="upgrade-with-a-diff-package"></a>差分のパッケージを使用したアップグレード
完全なアプリケーション パッケージをプロビジョニングする代わりに、完全なアプリケーション マニフェストと完全なサービス マニフェストと共に、更新されたコード/構成/データ パッケージのみを含む差分パッケージをプロビジョニングして、アップグレードを実行することもできます。 完全なアプリケーション パッケージは、アプリケーションをクラスターに最初にインストールするときにのみ必要です。 後続のアップグレードは、完全なアプリケーション パッケージと差分パッケージのどちらからでも実行できます。  

アプリケーション パッケージ内に見つからない、差分パッケージのアプリケーション マニフェストまたはサービス マニフェスト内の参照はすべて、現在プロビジョニングされているバージョンに自動的に置き換えられます。

差分パッケージを使用するシナリオは次のとおりです。

* 複数のサービス マニフェスト ファイルや複数のコード パッケージ、構成パッケージ、データ パッケージを参照する大規模なアプリケーション パッケージがある場合。
* アプリケーションのビルド プロセスから直接ビルドのレイアウトを生成するデプロイ システムがある場合。 この場合、コードは変更されていませんが、新しくビルドされたアセンブリはさまざまなチェックサムを取得します。 完全なアプリケーション パッケージを使用すると、すべてのコード パッケージのバージョンを更新する必要があります。 差分のパッケージを使用すると、変更されたファイルとバージョンが変更されているマニフェスト ファイルのみが提供されます。

Visual Studio を使ってアプリケーションをアップグレードすると、差分パッケージが自動的に発行されます。 差分パッケージを手動で作成するには、アプリケーション マニフェストとサービス マニフェストを更新する必要がありますが、最終的なアプリケーション パッケージには、変更のあったパッケージだけを追加します。

たとえば、次のようなアプリケーションがあるとします (理解しやすいようバージョン番号を単純化しています)。

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

差分パッケージを使用して、service1 のコード パッケージのみを更新すると仮定しましょう。 更新後のアプリケーションのバージョンは、次のように変更されます。

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

このケースでは、アプリケーション マニフェストを 2.0.0 に更新したうえで、service1 のサービス マニフェストに、コード パッケージの更新内容を反映することになります。 アプリケーション パッケージのフォルダー構造は、次のようになります。

```text
app1/
  service1/
    code/
```

つまり、通常どおり完全なアプリケーション パッケージを作成した後、バージョンが変更されなかったコード/構成/データ パッケージ フォルダーをすべて削除します。

## <a name="rolling-back-application-upgrades"></a>アプリケーションのアップグレードのロールバック

アップグレードは 3 つのモード (*Monitored*、*UnmonitoredAuto*、*UnmonitoredManual*) のいずれかでロールフォワードできますが、ロールバックできるのは *UnmonitoredAuto* または *UnmonitoredManual* モードのみです。 *UnmonitoredAuto* モードでのロールバックは、*UpgradeReplicaSetCheckTimeout* の既定値が異なる点を除き、ロールフォワードと同じように動作します (「[アプリケーション アップグレードのパラメーター](service-fabric-application-upgrade-parameters.md)」を参照)。 *UnmonitoredManual* モードでのロールバックは、ロールフォワードと同じように動作します。つまり、ロールバックは各 UD の完了後に中断し、[Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) を使用して明示的に再開してロールバックを続行する必要があります。

*Monitored* モードで *FailureAction* に *Rollback* を指定し、アップグレードの正常性ポリシーに違反した場合 (「[アプリケーション アップグレードのパラメーター](service-fabric-application-upgrade-parameters.md)」を参照)、または [Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps) を明示的に使用した場合に、ロールバックが自動的にトリガーされます。

ロールバック中、*UpgradeReplicaSetCheckTimeout* の値とモードは、[Update-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps) を使用していつでも変更できます。

## <a name="next-steps"></a>次の手順
[Visual Studio を使用したアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial.md) に関する記事では、Visual Studio を使用してアプリケーションをアップグレードする方法について説明します。

[PowerShell を使用したアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial-powershell.md) に関する記事では、PowerShell を使用したアプリケーションのアップグレードについて説明します。

[アップグレード パラメーター](service-fabric-application-upgrade-parameters.md)を使用して、アプリケーションのアップグレード方法を制御します。

[データのシリアル化](service-fabric-application-upgrade-data-serialization.md)の方法を学ぶことで、アプリケーションのアップグレードに互換性を持たせます。

「 [アプリケーションのアップグレードのトラブルシューティング](service-fabric-application-upgrade-troubleshooting.md)」の手順を参照して、アプリケーションのアップグレードでの一般的な問題を修正します。
