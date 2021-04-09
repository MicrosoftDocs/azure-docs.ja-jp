---
title: ローカル Azure Service Fabric クラスター セットアップのトラブルシューティング
description: この記事には、ローカル開発クラスターのトラブルシューティングに関する推奨事項が記載されています。
ms.topic: conceptual
ms.date: 02/23/2018
ms.openlocfilehash: 20948cd1626c02d73fb6e9ef096b552bbab627fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575909"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>ローカル開発クラスターのセットアップをトラブルシューティングする
Azure Service Fabric のローカル開発クラスターとの対話中に問題が発生した場合は、次の推奨事項を確認して解決の参考にしてください。

## <a name="cluster-setup-failures"></a>クラスターのセットアップに関するエラー
### <a name="cannot-clean-up-service-fabric-logs"></a>Service Fabric のログをクリーンアップできない
#### <a name="problem"></a>問題
DevClusterSetup スクリプトの実行中に、次のエラーが表示されます。

```output
Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
At line:1 char:1 + .\DevClusterSetup.ps1
+ ~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
+ FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1
```

#### <a name="solution"></a>解決策
現在開いている PowerShell ウィンドウを閉じ、新しい PowerShell ウィンドウを管理者として開きます。 これで、スクリプトを正常に実行することができます。

## <a name="cluster-connection-failures"></a>クラスターの接続に関するエラー

### <a name="type-initialization-exception"></a>型の初期化で例外が発生する
#### <a name="problem"></a>問題
PowerShell でクラスターに接続する際に、System.Fabric.Common.AppTrace で TypeInitializationException エラーが発生します。

#### <a name="solution"></a>解決策
インストール時に Path 変数が正しく設定されませんでした。 Windows からサインアウトし、再度サインインします。 これでパスが更新されます。

### <a name="cluster-connection-fails-with-object-is-closed"></a>"object is closed" のエラーが発生してクラスターの接続が失敗する
#### <a name="problem"></a>問題
次のようなエラーが発生して、Connect-ServiceFabricCluster の呼び出しが失敗します。

```output
Connect-ServiceFabricCluster : The object is closed.
At line:1 char:1
+ Connect-ServiceFabricCluster
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
+ FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster
```

#### <a name="solution"></a>解決策
現在開いている PowerShell ウィンドウを閉じ、新しい PowerShell ウィンドウを管理者として開きます。

### <a name="fabric-connection-denied-exception"></a>FabricConnectionDeniedException が発生する
#### <a name="problem"></a>問題
Visual Studio を使用してデバッグしている際に、FabricConnectionDeniedException エラーが発生します。

#### <a name="solution"></a>解決策
このエラーは、サービス ホスト プロセスを手動で開始しようとしたときに発生します。

お使いのソリューションでサービス プロジェクトがスタートアップ プロジェクトに設定されていないことを確認します。 スタートアップ プロジェクトに設定できるプロジェクトは、Service Fabric アプリケーションのプロジェクトのみです。

> [!TIP]
> セットアップに従った結果、ローカル クラスターが正常に動作しなくなった場合は、ローカル クラスター マネージャーのシステム トレイ アプリケーションを使用してクラスターをリセットできます。 これで、既存のクラスターは削除され、新しいクラスターがセットアップされます。 デプロイ済みのアプリケーションと関連付けられたデータがすべて削除されることに注意してください。
> 
> 

## <a name="next-steps"></a>次のステップ
* [システム正常性レポートでクラスターを理解してトラブルシューティングする](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Service Fabric Explorer を使用したクラスターの視覚化](service-fabric-visualizing-your-cluster.md)

