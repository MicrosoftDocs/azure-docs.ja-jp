---
title: Azure Service Fabric を使用した正常性のレポートとチェック
description: サービス コードから正常性レポートを送信し、Azure Service Fabric に用意されている正常性監視ツールを使用してサービスの正常性をチェックする方法について説明します。
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.custom: devx-track-csharp
ms.openlocfilehash: 59c8202b03bf1be2be5a68b75a1d7c7404b2213d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95998245"
---
# <a name="report-and-check-service-health"></a>サービス正常性のレポートとチェック
サービスで問題が発生した場合、インシデントと停止に対処して修正する能力は、問題を迅速に検出できるかどうかに依存します。 問題とエラーをサービス コードから Service Fabric Health Manager にレポートすれば、正常性状態を確認するために Service Fabric に用意されている標準の正常性監視ツールを使用できます。

サービスから正常性をレポートできる 3 つの方法があります。

* [Partition](/dotnet/api/system.fabric.istatefulservicepartition) オブジェクトまたは [CodePackageActivationContext](/dotnet/api/system.fabric.codepackageactivationcontext) オブジェクトを使用します。  
  `Partition` と `CodePackageActivationContext` オブジェクトを使用し、現在のコンテキストに含まれる要素の正常性をレポートできます。 たとえば、レプリカの一部として実行されるコードでは、そのレプリカ、所属パーティション、含まれるアプリケーションのみの正常性をレポートできます。
* `FabricClient`を使用します。   
  クラスターが[セキュリティで保護](service-fabric-cluster-security.md)されていない場合やサービスが管理者特権で実行されている場合、`FabricClient` を使用し、サービス コードから正常性をレポートできます。 ほとんどの現実のシナリオでは、セキュリティで保護されていないクラスターを使用しません。また、管理者特権を付与しません。 `FabricClient`では、クラスターの一部であるすべてのエンティティの正常性をレポートできます。 ただし、サービス コードで独自の正常性に関するレポートのみを送信するのが理想的です。
* クラスター、アプリケーション、デプロイされたアプリケーション、サービス、サービス パッケージ、パーティション、レプリカ、またはノード レベルで、REST API を使用します。 これは、コンテナー内から正常性レポートを取得するために使用できます。

この記事では、サービス コードから正常性をレポートするサンプルを紹介します。 このサンプルでは、Service Fabric が提供するツールで正常性を確認する方法についても紹介します。 この記事は、Service Fabric の正常性状態を監視する機能を簡単に説明することを目的としています。 詳細については、この記事の最後にあるリンクから始まる、正常性に関する一連の解説記事を参照してください。

## <a name="prerequisites"></a>前提条件
以下のものがインストールされている必要があります。

* Visual Studio 2015 または Visual Studio 2019
* Service Fabric SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>セキュリティで保護されたローカル開発クラスターを作成するには
* 管理者特権で PowerShell を起動し、次のコマンドを実行します。

![セキュリティ保護された開発クラスターを作成する方法を表示するコマンド](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>アプリケーションをデプロイしてその正常性をチェックするには
1. Visual Studio を管理者として開きます。
1. **ステートフル サービス** テンプレートを利用し、プロジェクトを作成します。
   
    ![Create a Service Fabric application with Stateful Service](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
1. **F5** キーを押してデバッグ モードでアプリケーションを実行します。 アプリケーションは、ローカル クラスターにデプロイされます。
1. アプリケーションが実行されたら、通知領域のローカル クラスター マネージャー アイコンを右クリックし、ショートカット メニューから **[ローカル クラスターの管理]** を選択し、Service Fabric Explorer を起動します。
   
    ![通知領域から Service Fabric Explorer を開く](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
1. アプリケーションの正常性がこの画像のように表示されます。 この時点では、アプリケーションはエラーなしで、正常です。
   
    ![Healthy application in Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
1. また、PowerShell を使用して正常性をチェックすることもできます。 ```Get-ServiceFabricApplicationHealth``` を利用し、アプリケーションの正常性を確認できます。```Get-ServiceFabricServiceHealth``` を利用し、サービスの正常性を確認できます。 PowerShell の同じアプリケーションの正常性レポートがこの画像にあります。
   
    ![Healthy application in PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>サービス コードにカスタム正常性イベントを追加するには
Visual Studio の Service Fabric プロジェクト テンプレートには、サンプル コードが含まれています。 次の手順では、サービス コードからカスタム正常性イベントをレポートする方法を説明します。 このようなレポートは、Service Fabric に用意されている正常性監視用の標準ツール (Service Fabric Explorer、Azure Portal の正常性ビュー、PowerShell など) に自動的に表示されます。

1. Visual Studio で前に作成したアプリケーションを再度開くか、Visual Studio テンプレートの **ステートフル サービス** を使用して新しいアプリケーションを作成します。
1. Stateful1.cs ファイルを開き、`RunAsync` メソッドの `myDictionary.TryGetValueAsync` 呼び出しを見つけます。 カウンターの現在の値を保持している `result` がこのメソッドにより返されることがわかります。これは、このアプリケーションのキー ロジックが実行回数を保持するためです。 このアプリケーションが実際のアプリケーションであり、結果がないとエラーになる場合は、そのイベントにフラグを設定します。
1. 結果がなくてエラーになるときに正常性イベントを報告するには、さらに次の手順を実行します。
   
    a. `System.Fabric.Health` 名前空間を Stateful1.cs ファイルに追加します。
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. 次のコードを `myDictionary.TryGetValueAsync` 呼び出しの後ろに追加します。
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    ステートフル サービスからレポートされているため、レプリカの正常性がレポートされます。 `HealthInformation` パラメーターには、レポートされている正常性の問題に関する情報が格納されます。
   
    ステートレス サービスを作成した場合は、次のコードを使用します。
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
1. サービスが管理者特権で実行されている場合、またはクラスターが[セキュリティで保護](service-fabric-cluster-security.md) されていない場合は、次の手順に示すように、`FabricClient` を利用して正常性をレポートすることもできます。  
   
    a. `var myDictionary` 宣言の後に `FabricClient` インスタンスを作成します。
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. 次のコードを `myDictionary.TryGetValueAsync` 呼び出しの後ろに追加します。
   
    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.Context.PartitionId,
            this.Context.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```
1. このエラーをシミュレートし、正常性監視ツールに表示されるところを見てみましょう。 エラーをシミュレートするには、前に追加した正常性レポート コードの最初の行をコメント アウトします。 最初の行をコメント アウトすると、コードは次の例のようになります。
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   このコードでは、`RunAsync` が実行されるたびに正常性レポートが実行されます。 変更後、 **F5** を押し、アプリケーションを実行します。
1. アプリケーションの実行後、Service Fabric Explorer を開いて、アプリケーションの正常性をチェックします。 今度は、Service Fabric Explorer に、アプリケーションが異常であることが表示されます。 アプリケーションは、異常として表示されます。これは、前に追加したコードからレポートされたエラーが原因です。
   
    ![Unhealthy application in Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
1. Service Fabric Explorer のツリー ビューでプライマリ レプリカを選択すると、 **正常性状態** にエラーが示されていることもわかります。 Service Fabric Explorer には、コードで `HealthInformation` パラメーターに追加した正常性レポートの詳細も表示されます。 同じ正常性レポートを、PowerShell でも、Azure Portal でも見ることができます。
   
    ![Replica health in Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

このレポートは、別のレポートで置き換えられるか、このレプリカが削除されるまで、Health Manager に残されます。 `HealthInformation` オブジェクトのこの正常性レポートに `TimeToLive` を設定しなかったため、レポートには期限切れがありません。

最も細かいレベルで正常性をレポートすることをお勧めします (この例の場合はレプリカ)。 `Partition`の正常性をレポートすることもできます。

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

`Application`、`DeployedApplication`、`DeployedServicePackage` の正常性をレポートするには、`CodePackageActivationContext` を使用します。

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>次のステップ
* [Service Fabric の正常性の詳細情報](service-fabric-health-introduction.md)
* [サービスの正常性をレポートするための REST API](/rest/api/servicefabric/report-the-health-of-a-service)
* [アプリケーションの正常性をレポートするための REST API](/rest/api/servicefabric/report-the-health-of-an-application)
