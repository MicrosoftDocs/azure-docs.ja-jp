---
title: "ローカル Service Fabric クラスター セットアップのトラブルシューティング | Microsoft Docs"
description: "この記事には、ローカル開発クラスターのトラブルシューティングに関する推奨事項が記載されています。"
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: 97f4feaa-bba0-47af-8fdd-07f811fe2202
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/02/2017
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0f0db912ac3cee02f4268996bf2409440afade86
ms.lasthandoff: 11/17/2016


---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>ローカル開発クラスターのセットアップをトラブルシューティングする
Azure Service Fabric のローカル開発クラスターとの対話中に問題が発生した場合は、次の推奨事項を確認して解決の参考にしてください。

## <a name="cluster-setup-failures"></a>クラスターのセットアップに関するエラー
### <a name="cannot-clean-up-service-fabric-logs"></a>Service Fabric のログをクリーンアップできない
#### <a name="problem"></a>問題点
DevClusterSetup スクリプトの実行中に、次のようなエラーが発生します。

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>解決策
現在開いている PowerShell ウィンドウを閉じ、新しい PowerShell ウィンドウを管理者として開きます。 これでスクリプトを正常に実行できるようになります。

## <a name="cluster-connection-failures"></a>クラスターの接続に関するエラー
### <a name="service-fabric-powershell-cmdlets-are-not-recognized-in-azure-powershell"></a>Service Fabric PowerShell コマンドレットが Azure PowerShell で認識されない
#### <a name="problem"></a>問題点
Azure PowerShell ウィンドウで `Connect-ServiceFabricCluster` などの Service Fabric PowerShell コマンドレットを実行しようとすると失敗し、コマンドレットが認識されないというエラーが表示されます。 これは、Azure PowerShell は (OS が 64 ビット版の場合でも) 32 ビット版の Windows PowerShell を使用するのに対し、Service Fabric コマンドレットは 64 ビット環境でしか機能しないためです。

#### <a name="solution"></a>解決策
Service Fabric コマンドレットは常に Windows PowerShell から直接実行します。

> [!NOTE]
> Azure PowerShell の最新バージョンでは特別なショートカットが作成されないため、このエラーは発生しなくなります。
> 
> 

### <a name="type-initialization-exception"></a>型の初期化で例外が発生する
#### <a name="problem"></a>問題点
PowerShell でクラスターに接続する際に、System.Fabric.Common.AppTrace で TypeInitializationException エラーが発生します。

#### <a name="solution"></a>解決策
インストール時に Path 変数が正しく設定されませんでした。 Windows からサインアウトし、再びサインインしてください。 これでパスが最新の状態に更新されます。

### <a name="cluster-connection-fails-with-object-is-closed"></a>"object is closed" のエラーが発生してクラスターの接続が失敗する
#### <a name="problem"></a>問題点
次のようなエラーが発生して、Connect-ServiceFabricCluster の呼び出しが失敗します。

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>解決策
現在開いている PowerShell ウィンドウを閉じ、新しい PowerShell ウィンドウを管理者として開きます。 これで正常に接続できるようになります。

### <a name="fabric-connection-denied-exception"></a>FabricConnectionDeniedException が発生する
#### <a name="problem"></a>問題点
Visual Studio を使用してデバッグしている際に、FabricConnectionDeniedException エラーが発生します。

#### <a name="solution"></a>解決策
このエラーは通常、ランタイムサービス ホスト プロセスを Service Fabric からではなく手動で開始すると発生します。

お使いのソリューションでサービス プロジェクトがスタートアップ プロジェクトに設定されていないことを確認します。 スタートアップ プロジェクトに設定できるプロジェクトは、Service Fabric アプリケーションのプロジェクトのみです。

> [!TIP]
> セットアップに従った結果、ローカル クラスターが正常に動作しなくなった場合は、ローカル クラスター マネージャーのシステム トレイ アプリケーションを使用してクラスターをリセットできます。 これにより、既存のクラスターは削除され、新しいクラスターがセットアップされます。 デプロイ済みのアプリケーションと関連付けられたデータがすべて削除されることに注意してください。
> 
> 

## <a name="next-steps"></a>次のステップ
* [システム正常性レポートでクラスターを理解してトラブルシューティングする](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Service Fabric Explorer を使用したクラスターの視覚化](service-fabric-visualizing-your-cluster.md)


