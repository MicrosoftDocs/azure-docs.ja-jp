---
title: "Linux と Windows での Azure Service Fabric の違い | Microsoft Docs"
description: "Linux での Azure Service Fabric プレビューと Windows での Azure Service Fabric の違いについて説明します。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2017
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 5faf7dc0b544f6fe2f83565cc368e218c6df35af
ms.lasthandoff: 03/29/2017


---
# <a name="differences-between-service-fabric-on-linux-preview-and-windows-generally-available"></a>Linux での Service Fabric (プレビュー) と Windows での Service Fabric (一般公開) の違い

Linux での Service Fabric はプレビュー版であるため、Windows ではサポートされているものの Linux ではサポートされていない機能が一部存在します。 いずれ Linux での Service Fabric が一般公開されるときに、これらの一連の機能は Windows と同様に使用できるようになります。

* Reliable Collection (と信頼できるステートフル サービス) は Linux ではサポートされていません。
* ReverseProxy は Linux では利用できません。
* スタンドアロンのインストーラーは Linux では使用できません。
* マニフェスト ファイルの XML スキーマ検証は、Linux では実行されません。 
* コンソールのリダイレクトは、Linux ではサポートされていません。 
* Fault Analysis Service (FAS) は、Linux では利用できません。
* Azure Active Directory のサポートは、Linux では利用できません。
* PowerShell コマンドと同等の一部の CLI コマンドは使用できません。
* Linux クラスターに対して実行できる PowerShell コマンドは、一部のものに限られています (詳しくは次のセクションを参照)。

>[!NOTE]
>運用環境クラスターでのコンソールのリダイレクトは、Windows でもサポートされていません。

使用される開発ツールが異なります。Windows では Visual Studio、PowerShell、VSTS、ETW が使用され、Linux では Yeoman、Eclipse、Jenkins、LTTng が使用されます。

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>Linux Service Fabric クラスターに対して実行できない PowerShell コマンドレット

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-ServiceFabricPartitionQuorumLoss
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-ServiceFabricImageStoreContent
* Get-ServiceFabricChaosReport
* Get-ServiceFabricNodeTransitionProgress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-ServiceFabricPartitionQuorumLoss
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Cmd
* Get-ServiceFabricNodeConfiguration
* Get-ServiceFabricClusterConfiguration
* Get-ServiceFabricClusterConfigurationUpgradeStatus
* Get-ServiceFabricPackageDebugParameters
* New-ServiceFabricPackageDebugParameter
* New-ServiceFabricPackageSharingPolicy
* Add-ServiceFabricNode
* Copy-ServiceFabricClusterPackage
* Get-ServiceFabricRuntimeSupportedVersion
* Get-ServiceFabricRuntimeUpgradeVersion
* New-ServiceFabricCluster
* New-ServiceFabricNodeConfiguration
* Remove-ServiceFabricCluster
* Remove-ServiceFabricClusterPackage
* Remove-ServiceFabricNodeConfiguration
* Test-ServiceFabricClusterManifest
* Test-ServiceFabricConfiguration
* Update-ServiceFabricNodeConfiguration
* Approve-ServiceFabricRepairTask
* Complete-ServiceFabricRepairTask
* Get-ServiceFabricRepairTask
* Invoke-ServiceFabricDecryptText
* Invoke-ServiceFabricEncryptSecret
* Invoke-ServiceFabricEncryptText
* Invoke-ServiceFabricInfrastructureCommand
* Invoke-ServiceFabricInfrastructureQuery
* Remove-ServiceFabricRepairTask
* Start-ServiceFabricRepairTask
* Stop-ServiceFabricRepairTask
* Update-ServiceFabricRepairTaskHealthPolicy



## <a name="next-steps"></a>次のステップ
* [Linux で開発環境を準備する](service-fabric-get-started-linux.md)
* [OSX で開発環境を準備する](service-fabric-get-started-mac.md)
* [Yeoman を使用して Linux で最初の Service Fabric Java アプリケーションを作成してデプロイする](service-fabric-create-your-first-linux-application-with-java.md)
* [Eclipse 用の Service Fabric プラグインを使用して Linux で最初の Service Fabric Java アプリケーションを作成してデプロイする](service-fabric-get-started-eclipse.md)
* [Linux で最初の CSharp アプリケーションを作成する](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Azure CLI を使用した Service Fabric アプリケーションの管理](service-fabric-azure-cli.md)

