---
title: "アプリケーションのアップグレード: アップグレードのパラメーター | Microsoft Docs"
description: "Service Fabric アプリケーションのアップグレードに関連するパラメーターについて説明します。正常性確認やアップグレードを自動的に取り消すポリシーなどが含まれます。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: a4170ac6-192e-44a8-b93d-7e39c92a347e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/02/2017
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: aa26d72524f035cca92cd39062873ceb15bffac1
ms.lasthandoff: 03/11/2017


---
# <a name="application-upgrade-parameters"></a>アプリケーション アップグレードのパラメーター
この記事では、Azure Service Fabric アプリケーションのアップグレード中に適用されるさまざまなパラメーターについて説明します。 パラメーターには、アプリケーションの名前とバージョンが含まれています。 パラメーターは、アップグレード時に適用されるタイムアウトと正常性チェックを制御するノブです。また、パラメーターには、アップグレードの失敗時に適用する必要があるポリシーを指定します。

<br>

| パラメーター | Description |
| --- | --- |
| ApplicationName |アップグレードするアプリケーションの名前です。 例: fabric:/VisualObjects、fabric:/ClusterMonitor |
| TargetApplicationTypeVersion |ターゲットをアップグレードするアプリケーションの種類のバージョンです。 |
| FailureAction |アップグレードが失敗したときに、Service Fabric が実行するアクション。 アプリケーションを更新前のバージョンにロールバックするか (ロールバック)、現在のアップグレード ドメインでアップグレードを停止できます。 後者の場合、アップグレード モードは手動に変更できます。 使用できる値は、ロールバックと手動です。 |
| HealthCheckWaitDurationSec |Service Fabric でアプリケーションの正常性を評価する前に、アップグレード ドメインでのアップグレード完了後の待機時間を秒単位で指定します。 この期間は、アプリケーションが正常と見なされる前に、アプリケーションが実行されている必要がある時間としても考慮されます。 正常性チェックに合格すると、アップグレード プロセスは次のアップグレード ドメインに進みます。  正常性チェックに合格しない場合、Service Fabric は HealthCheckRetryTimeout に達するまで、一定期間 (UpgradeHealthCheckInterval) 待機してから、正常性チェックをもう一度実行します。 既定値と推奨値は、0 秒です。 |
| HealthCheckRetryTimeoutSec |アップグレードを失敗として宣言する前に、Service Fabric が正常性評価を実行し続ける期間 (秒) です。 既定値は 600 秒です。 この期間は HealthCheckWaitDuration に達した後に開始します。 Service Fabric は、この HealthCheckRetryTimeout 内で、アプリケーションの正常性についての複数のチェックを実行することがあります。 既定値は 10 分です。アプリケーションに合わせてカスタマイズしてください。 |
| HealthCheckStableDurationSec |次のアップグレード ドメインへの移行またはアップグレードが完了する前に、アプリケーションが安定していることを確認するための時間 (秒)。 この待機期間を使用して、正常性チェックが実行された直後に、正常性の変更が未検出にならないようにします。 既定値は 120 秒です。アプリケーションに合わせてカスタマイズしてください。 |
| UpgradeDomainTimeoutSec |1 つのアップグレード ドメインをアップグレードするための最大時間 (秒)。 このタイムアウトに達すると、アップグレードは停止され、UpgradeFailureAction の設定に基づいて操作が続行されます。 既定値はなし (無限) です。アプリケーションに合わせてカスタマイズしてください。 |
| UpgradeTimeout |全体のアップグレードに適用されるタイムアウト (秒)。 このタイムアウトに達すると、アップグレードは停止され、UpgradeFailureAction がトリガーされます。 既定値はなし (無限) です。アプリケーションに合わせてカスタマイズしてください。 |
| UpgradeHealthCheckInterval |正常性状態をチェックする頻度。 このパラメーターは、"*クラスター* *マニフェスト*" の ClusterManager セクションで指定します。アップグレード コマンドレットでは指定されません。 既定値は 60 秒です。 |

<br>

## <a name="service-health-evaluation-during-application-upgrade"></a>アプリケーション アップグレード中のサービス正常性評価
<br>
正常性の評価基準は省略可能です。 アップグレードを開始するときに、正常性評価の基準が指定されていない場合、Service Fabric はアプリケーション インスタンスの ApplicationManifest.xml で指定されているアプリケーションの正常性ポリシーを使用します。

<br>

| パラメーター | Description |
| --- | --- |
| ConsiderWarningAsError |既定値は False です。 アップグレード中に、アプリケーションの正常性を評価するときに、アプリケーションの警告正常性状態をエラーとして扱います。 既定では、Service Fabric は警告イベントがある場合でもアップグレードを続行できるように、エラーになるイベントの警告正常性のイベントは評価しません。 |
| MaxPercentUnhealthyDeployedApplications |既定値と推奨値は、0 です。 アプリケーションが正常でないと見なされ、アップグレードに失敗する前にデプロイされるアプリケーションの最大数 ([正常性のセクション](service-fabric-health-introduction.md)を参照してください) を指定します。 このパラメーターは、ノード上のアプリケーションの正常性を定義するため、アップグレード中の問題を検出するうえで役立ちます。 通常、アプリケーションのレプリカの負荷はその他のノードに分散されます。これにより、アプリケーションは正常に表示され、アップグレードを続行することができます。 厳密な MaxPercentUnhealthyDeployedApplications を指定すると、Service Fabric はアプリケーション パッケージで高速に問題を検出し、高速アップグレードに失敗します。 |
| MaxPercentUnhealthyServices |既定値と推奨値は、0 です。 アップグレードが正常でないと見なされてアップグレードに失敗する前に、正常性がなくなることを許可するアプリケーション インスタンス内のサービスの最大数を指定します。 |
| MaxPercentUnhealthyPartitionsPerService |既定値と推奨値は、0 です。 サービス内のパーティション最大数に指定すると、サービスが正常でないと見なされる前に、正常でなくなる可能性があります。 |
| MaxPercentUnhealthyReplicasPerPartition |既定値と推奨値は、0 です。 パーティションが正常でないと見なされる前に、正常でない状態になるパーティション内のレプリカの最大数を指定します。 |
| UpgradeReplicaSetCheckTimeout |**ステートレス サービス**-- Service Fabric は単一のアップグレード ドメイン内で、サービスの追加インスタンスを確実に使用できるようにしようとします。 ターゲット インスタンス数が複数ある場合、最大タイムアウト値になるまで、複数のインスタンスが使用可能になるまで待機します。 このタイムアウトは、UpgradeReplicaSetCheckTimeout プロパティを使用して指定されます。 タイムアウトになると、サービス インスタンス数にかかわらず、Service Fabric はアップグレードを続行します。 ターゲット インスタンス数が&1; つの場合、Service Fabric は待機せずに、すぐにアップグレードを実行します。 **ステートフル サービス**-- Service Fabric は&1; つのアップグレード ドメイン内で、レプリカ セットに確実にクォーラムが含まれるようにしようとします。 Service Fabric は UpgradeReplicaSetCheckTimeout プロパティで指定した最大タイムアウト値になるまで、クォーラムが使用可能になるまで待機します。 タイムアウトになると、クォーラムにかかわらずアップグレードを続行します。 この設定は、ロール フォワード時には、"しない (無限)" に設定され、ロールバック時には 900 秒に設定されます。 |
| ForceRestart |サービス コードを更新せずに構成やデータ パッケージを更新する場合、サービスは、ForceRestart プロパティが true に設定されている場合にのみ再起動されます。 更新が完了すると、Service Fabric は新しい構成パッケージやデータ パッケージを使用可能なサービスを知らせます。 サービスは、変更を適用する役割を担います。 必要に応じて、サービス自体を再起動できます。 |

<br>
<br>
MaxPercentUnhealthyServices、MaxPercentUnhealthyPartitionsPerService、MaxPercentUnhealthyReplicasPerPartition の基準は、アプリケーション インスタンスのサービスの種類ごとに指定できます。 このパラメーター per-service を設定すると、アプリケーションにさまざまなサービスの種類を含め、それぞれ異なる評価ポリシーを指定することができます。 たとえば、ステートレスなゲートウェイ サービスの種類には、特定のアプリケーション インスタンスのステートフルなエンジン サービスの種類とは異なる MaxPercentUnhealthyPartitionsPerService を含めることができます。

## <a name="next-steps"></a>次のステップ
[Visual Studio を使用したアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial.md) に関する記事では、Visual Studio を使用してアプリケーションをアップグレードする方法について説明します。

[PowerShell を使用したアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial-powershell.md) に関する記事では、PowerShell を使用したアプリケーションのアップグレードについて説明します。

[Linux での Azure CLI を使用したアプリケーションのアップグレード](service-fabric-azure-cli.md#upgrading-your-application)に関する記事では、Azure CLI を使用してアプリケーションをアップグレードする方法について説明します。

[Service Fabric Eclipse プラグインを使用したアプリケーションのアップグレード](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

[データのシリアル化](service-fabric-application-upgrade-data-serialization.md)の方法を学ぶことで、アプリケーションのアップグレードに互換性を持たせます。

[高度なトピック](service-fabric-application-upgrade-advanced.md)を参照して、アプリケーションをアップグレードするときの高度な機能の使用方法を学習します。

「 [アプリケーションのアップグレードのトラブルシューティング](service-fabric-application-upgrade-troubleshooting.md)」の手順を参照して、アプリケーションのアップグレードでの一般的な問題を修正します。

