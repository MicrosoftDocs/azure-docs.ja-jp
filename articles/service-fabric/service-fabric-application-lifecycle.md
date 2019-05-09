---
title: Service Fabric のアプリケーション ライフサイクル | Microsoft Docs
description: Service Fabric アプリケーションの開発、デプロイ、テスト、アップグレード、保守、および削除について説明します。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 08837cca-5aa7-40da-b087-2b657224a097
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/19/2018
ms.author: atsenthi
ms.openlocfilehash: 53cab3591ea11721e36b48438f35df016e2a9f3a
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664983"
---
# <a name="service-fabric-application-lifecycle"></a>Service Fabric アプリケーションのライフサイクル
通常は、その他のプラットフォームと同様に、Azure Service Fabric のアプリケーションは、デザイン、開発、テスト、デプロイ、アップグレード、保守、削除のフェーズを通過します。 Service Fabric は、開発からデプロイ、日常的な管理、保守、最終的な使用停止に至るまで、クラウド アプリケーションの完全なアプリケーション ライフサイクルに対して高度なサポートを提供します。 そのサービス モデルにより、アプリケーションのライフサイクルで個別に関与するさまざまな役割が有効になります。 この記事では、API の概要と、Service Fabric アプリケーション ライフサイクルのフェーズでさまざまなロールがその API をどのように使用するかを示します。

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="service-model-roles"></a>サービス モデルのロール
サービス モデルのロールは、次のとおりです。

* **サービス開発者**:同じ種類や別の種類の複数のアプリケーションで再利用、使用できるモジュールと汎用サービスを開発します。 たとえば、チケット発行のアプリケーション (ヘルプ デスク) または電子商取引アプリケーション (ショッピング カート) を作成するために Queue サービスを使用できます。
* **アプリケーション開発者**:特定の要件またはシナリオを満たすために、サービスのコレクションを統合してアプリケーションを作成します。 たとえば、電子商取引 Web サイトには、"JSON ステートレス フロントエンド サービス"、"オークション ステートフル サービス"、"ステートフル キュー サービス" を統合して、オークション ソリューションが構築されることがあります。
* **アプリケーション管理者**:アプリケーションの構成 (テンプレートの構成パラメーターを入力)、デプロイ (使用可能なリソースへのマッピング)、およびサービスの品質を決定します。 たとえば、アプリケーション管理者は、アプリケーションの言語ロケール (米国には英語、日本には日本語) を決定します。 別のデプロイされたアプリケーションには、さまざまな設定を含めることができます。
* **オペレーター**:アプリケーションの構成に基づくアプリケーションと、アプリケーション管理者が指定する要件をデプロイします。 たとえば、オペレーターはアプリケーションをプロビジョニングしてデプロイし、Azure で実行されていることを確認します。 オペレーターは、アプリケーションの正常性とパフォーマンスの情報を監視し、必要に応じて、物理インフラストラクチャを管理します。

## <a name="develop"></a>開発
1. "*サービス開発者*" は、[Reliable Actors](service-fabric-reliable-actors-introduction.md) または [Reliable Services](service-fabric-reliable-services-introduction.md) プログラミング モデルを使用してさまざまな種類のサービスを開発します。
2. *サービス開発者* は、1 つ以上のコード、構成、データのパッケージで構成されるサービス マニフェスト ファイルで、開発したサービスの種類について宣言によって記述します。
3. *アプリケーション開発者* は、異なる種類のサービスを使用するアプリケーションを構築します。
4. *アプリケーション開発者* は、構成サービスのサービス マニフェストを参照し、異なる構成と構成サービスのデプロイ設定を適切にオーバーライドしてパラメーター化し、アプリケーション マニフェストでアプリケーションの種類を宣言によって記述します。

例については、[「Reliable Actors の使用」](service-fabric-reliable-actors-get-started.md)と[「Reliable Services の使用」](service-fabric-reliable-services-quick-start.md)を参照してください。

## <a name="deploy"></a>デプロイ
1. *アプリケーション管理者* は、アプリケーション マニフェストで **ApplicationType** 要素を指定して、Service Fabric クラスターにデプロイされるように、特定のアプリケーションに合わせてアプリケーションの種類を調整します。
2. "*オペレーター*" は、[**CopyApplicationPackage** メソッド](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)または [**Copy-ServiceFabricApplicationPackage** コマンドレット](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps)を使用して、クラスター イメージ ストアにアプリケーション パッケージをアップロードします。 アプリケーション パッケージには、アプリケーション マニフェストとサービス パッケージのコレクションが含まれています。 Service Fabric は、Azure Blob ストアまたは Service Fabric のシステム サービスが対象となる、イメージ ストアに格納されているアプリケーション パッケージからアプリケーションをデプロイします。
3. "*オペレーター*" は、[**ProvisionApplicationAsync** メソッド](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)、[**Register-ServiceFabricApplicationType** コマンドレット](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype)、[**Provision an Application** REST 操作](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application)を使用して、アップロードされたアプリケーション パッケージから対象のクラスターでアプリケーションの種類をプロビジョニングします。
4. アプリケーションをプロビジョニングした後、"*オペレーター*" は [**CreateApplicationAsync** メソッド](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)、[**New-ServiceFabricApplication** コマンドレット](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication)、[**Create Application** REST 操作](https://docs.microsoft.com/rest/api/servicefabric/create-an-application)を使用して、"*アプリケーション管理者*" によって提供されるパラメーターでアプリケーションを起動します。
5. アプリケーションをデプロイした後、"*オペレーター*" は [**CreateServiceAsync** メソッド](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient)、[**New-ServiceFabricService** コマンドレット](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice)、[**Create Service** REST 操作](https://docs.microsoft.com/rest/api/servicefabric/create-a-service)を使用して、使用可能なサービスの種類に基づいて、アプリケーションの新しいサービス インスタンスを作成します。
6. これで、Service Fabric のクラスターで、アプリケーションが実行されます。

例については、「 [Deploy an application (アプリケーションをデプロイする)](service-fabric-deploy-remove-applications.md) 」を参照してください。

## <a name="test"></a>テスト
1. ローカル開発クラスターまたはテスト クラスターにデプロイした後、"*サービス開発者*" は [**FailoverTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters)、[**FailoverTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario) クラス、[**Invoke-ServiceFabricFailoverTestScenario** コマンドレット](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps)を使用して、組み込みのフェールオーバー テスト シナリオを実行します。 フェールオーバー テスト シナリオで、重要な切り替えとフェールオーバーを通して指定したサービスを実行して、引き続き利用可能で動作していることを確認します。
2. 次に、"*サービス開発者*" は、[**ChaosTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters) と [**ChaosTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario) クラスまたは [**Invoke-ServiceFabricChaosTestScenario** コマンドレット](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps)を使用して、組み込みの chaos テスト シナリオを実行します。 chaos テスト シナリオは、複数のノード、コード パッケージ、レプリカのエラーをクラスターにランダムに誘導します。
3. *service developer* [サービス対サービスの通信をテスト](service-fabric-testability-scenarios-service-communication.md) します。

詳細については、「 [Introduction to the Fault Analysis Service (Fault Analysis サービスの概要)](service-fabric-testability-overview.md) 」を参照してください。

## <a name="upgrade"></a>アップグレード
1. *サービス開発者* は、インスタンス化されたアプリケーションの構成サービスの更新、バグの修正を行い、サービス マニフェストの新しいバージョンを提供します。
2. *アプリケーション開発者* は、一貫性のあるサービスの構成とデプロイメント設定をオーバーライドしてパラメーター化し、新しいバージョンのアプリケーション マニフェストを提供します。 アプリケーション開発者は、アプリケーションに新しいバージョンのサービス マニフェストを組み込み、更新されたアプリケーション パッケージで新しいバージョンのアプリケーションの種類を提供します。
3. *アプリケーション管理者* は、適切なパラメーターを更新して、対象のアプリケーションに新しいバージョンのアプリケーションの種類を組み込みます。
4. "*オペレーター*" は、[**CopyApplicationPackage** メソッド](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)または [**Copy-ServiceFabricApplicationPackage** コマンドレット](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps)を使用して、クラスター イメージ ストアに更新されたアプリケーション パッケージをアップロードします。 アプリケーション パッケージには、アプリケーション マニフェストとサービス パッケージのコレクションが含まれています。
5. "*オペレーター*" は、[**ProvisionApplicationAsync** メソッド](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)、[**Register-ServiceFabricApplicationType** コマンドレット](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype)、[**Provision an Application** REST 操作](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application)を使用して、対象のクラスターで新しいバージョンのアプリケーションをプロビジョニングします。
6. "*オペレーター*" は、[**UpgradeApplicationAsync** メソッド](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)、[**Start-ServiceFabricApplicationUpgrade** コマンドレット](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade)、[**Upgrade an Application** REST 操作](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application)を使用して、対象のアプリケーションを新しいバージョンにアップグレードします。
7. "*オペレーター*" は、[**GetApplicationUpgradeProgressAsync** メソッド](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)、[**Get-ServiceFabricApplicationUpgrade** コマンドレット](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricapplicationupgrade)、[**Get Application Upgrade Progress** REST 操作](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1)を使用して、アップグレードの進行状況を確認します。
8. 必要に応じて、"*オペレーター*" は、[**UpdateApplicationUpgradeAsync** メソッド](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)、[**Update-ServiceFabricApplicationUpgrade** コマンドレット](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade)、[**Update Application Upgrade** REST 操作](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade)を使用して、現在のアプリケーション アップグレードのパラメーターを変更して再適用します。
9. 必要に応じて、"*オペレーター*" は、[**RollbackApplicationUpgradeAsync** メソッド](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)、[**Start-ServiceFabricApplicationRollback** コマンドレット](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback)、[**Rollback Application Upgrade** REST 操作](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade)を使用して、現在のアプリケーション アップグレードをロールバックします。
10. Service Fabric はその構成サービスのいずれかの可用性を失うことなく、クラスター内で実行されている対象アプリケーションをアップグレードします。

例については、「 [アプリケーション アップグレードのチュートリアル](service-fabric-application-upgrade-tutorial.md) 」を参照してください。

## <a name="maintain"></a>管理
1. オペレーティング システムのアップグレードと修正プログラムに対しては、Service Fabric はクラスター内で実行しているすべてのアプリケーションの可用性を保証する Azure インフラストラクチャとつなぐインターフェイスです。
2. Service Fabric プラットフォームへのアップグレードと修正プログラムに対しては、クラスターで実行されているアプリケーションの可用性が失われることなく、Service Fabric 自体でアップグレードされます。
3. *アプリケーション管理者* は、過去の容量使用率のデータと予測される将来の要求を分析した後、ノードの追加やクラスターからのノードの削除を承認します。
4. "*オペレーター*" は、"*アプリケーション管理者*" が指定したノードを追加、削除します。
5. 新しいノードをクラスターに追加する、または既存のノードをクラスターから削除するとき、Service Fabric は最適なパフォーマンスを実現するために、クラスター内のすべてのノード間でアプリケーションを実行している負荷を自動的に分散します。

## <a name="remove"></a>Remove
1. "*オペレーター*" は、[**DeleteServiceAsync** メソッド](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient)、[**Remove-ServiceFabricService** コマンドレット](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice)、[**Delete Service** REST 操作](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service)を使用して、アプリケーション全体を削除することなく、クラスター内の実行中のサービスの特定のインスタンスを削除できます。  
2. "*オペレーター*" は、[**DeleteApplicationAsync** メソッド](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)、[**Remove-ServiceFabricApplication** コマンドレット](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricapplication)、[**Delete Application** REST 操作](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application)を使用して、アプリケーションのインスタンスとそのすべてのサービスも削除できます。
3. アプリケーションとサービスが停止されると、"*オペレーター*" は [**UnprovisionApplicationAsync** メソッド](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)、[**Unregister-ServiceFabricApplicationType** コマンドレット](https://docs.microsoft.com/powershell/module/servicefabric/unregister-servicefabricapplicationtype)、[**Unprovision an Application** REST 操作](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application)を使用して、アプリケーションの種類のプロビジョニングを解除できます。 プロビジョニングを解除されたアプリケーションの種類の場合、ImageStore からアプリケーション パッケージが削除されません。 アプリケーション パッケージを手動で削除する必要があります。
4. "*オペレーター*" は、[**RemoveApplicationPackage** メソッド](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)または [**Remove-ServiceFabricApplicationPackage** コマンドレット](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps)を使用して、ImageStore からアプリケーション パッケージを削除します。

例については、「 [Deploy an application (アプリケーションをデプロイする)](service-fabric-deploy-remove-applications.md) 」を参照してください。

## <a name="next-steps"></a>次の手順
Service Fabric アプリケーションとサービスの開発、テスト、管理に関する詳細については、以下を参照してください。

* [Reliable Actor](service-fabric-reliable-actors-introduction.md)
* [Reliable Service](service-fabric-reliable-services-introduction.md)
* [アプリケーションをデプロイする](service-fabric-deploy-remove-applications.md)
* [アプリケーションのアップグレード](service-fabric-application-upgrade.md)
* [Testability の概要](service-fabric-testability-overview.md)
