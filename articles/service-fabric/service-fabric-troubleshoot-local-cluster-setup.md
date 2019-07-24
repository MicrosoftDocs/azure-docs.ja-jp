---
title: ローカル Azure Service Fabric クラスター セットアップのトラブルシューティング | Microsoft Docs
description: この記事には、ローカル開発クラスターのトラブルシューティングに関する推奨事項が記載されています。
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: chackdan
editor: ''
ms.assetid: 97f4feaa-bba0-47af-8fdd-07f811fe2202
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 8bb32b2bded061bd19bcd7cfda4ef259a75b0626
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662807"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>ローカル開発クラスターのセットアップをトラブルシューティングする
Azure Service Fabric のローカル開発クラスターとの対話中に問題が発生した場合は、次の推奨事項を確認して解決の参考にしてください。

## <a name="cluster-setup-failures"></a>クラスターのセットアップに関するエラー
### <a name="cannot-clean-up-service-fabric-logs"></a>Service Fabric のログをクリーンアップできない
#### <a name="problem"></a>問題点
DevClusterSetup スクリプトの実行中に、次のエラーが表示されます。

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>解決策
現在開いている PowerShell ウィンドウを閉じ、新しい PowerShell ウィンドウを管理者として開きます。 これで、スクリプトを正常に実行することができます。

## <a name="cluster-connection-failures"></a>クラスターの接続に関するエラー

### <a name="type-initialization-exception"></a>型の初期化で例外が発生する
#### <a name="problem"></a>問題点
PowerShell でクラスターに接続する際に、System.Fabric.Common.AppTrace で TypeInitializationException エラーが発生します。

#### <a name="solution"></a>解決策
インストール時に Path 変数が正しく設定されませんでした。 Windows からサインアウトし、再度サインインします。 これでパスが更新されます。

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
現在開いている PowerShell ウィンドウを閉じ、新しい PowerShell ウィンドウを管理者として開きます。

### <a name="fabric-connection-denied-exception"></a>FabricConnectionDeniedException が発生する
#### <a name="problem"></a>問題点
Visual Studio を使用してデバッグしている際に、FabricConnectionDeniedException エラーが発生します。

#### <a name="solution"></a>解決策
このエラーは、サービス ホスト プロセスを手動で開始しようとしたときに発生します。

お使いのソリューションでサービス プロジェクトがスタートアップ プロジェクトに設定されていないことを確認します。 スタートアップ プロジェクトに設定できるプロジェクトは、Service Fabric アプリケーションのプロジェクトのみです。

> [!TIP]
> セットアップに従った結果、ローカル クラスターが正常に動作しなくなった場合は、ローカル クラスター マネージャーのシステム トレイ アプリケーションを使用してクラスターをリセットできます。 これで、既存のクラスターは削除され、新しいクラスターがセットアップされます。 デプロイ済みのアプリケーションと関連付けられたデータがすべて削除されることに注意してください。
> 
> 

## <a name="next-steps"></a>次の手順
* [システム正常性レポートでクラスターを理解してトラブルシューティングする](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Service Fabric Explorer を使用したクラスターの視覚化](service-fabric-visualizing-your-cluster.md)

