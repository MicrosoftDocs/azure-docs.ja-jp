---
title: アプリケーションのアップグレードに関する高度なトピック
description: この記事では、Service Fabric アプリケーションのアップグレードに関連する高度なトピックについて説明します。
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 6604300328f2d243077ba341a9028221438dce9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98792050"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Service Fabric アプリケーションのアップグレード:高度なトピック

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>アプリケーションのアップグレード中にサービスの種類を追加または削除する

アップグレードの一環として、発行したアプリケーションに新しいサービスの種類を追加した場合、その新しいサービスの種類はデプロイされているアプリケーションに追加されます。 このようなアップグレードは、既にアプリケーションの一部であるサービス インスタンスには影響しませんが、追加されたサービスの種類のインスタンスを作成して、新しいサービスの種類をアクティブにする必要があります (「[New-ServiceFabricService](/powershell/module/servicefabric/new-servicefabricservice)」を参照)。

同様に、アップグレードの一環として、アプリケーションからサービスの種類を削除することもできます。 ただし、アップグレードに進む前に、削除するサービスの種類のサービス インスタンスをすべて削除する必要があります (「[Remove-ServiceFabricService](/powershell/module/servicefabric/remove-servicefabricservice)」を参照)。

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime"></a>ステートレス サービスの計画的なダウンタイム中に接続がドロップされないようにする

アプリケーションやクラスターのアップグレードまたはノードの非アクティブ化など、ステートレス インスタンスの計画的なダウンタイムの場合、インスタンスがダウンして接続が強制的に終了した後、公開されたエンドポイントが削除されるため、接続が解除される場合があります。

これを回避するには、サービス構成に *インスタンスの終了遅延期間* を追加する、*RequestDrain* 機能を構成して、公開されているエンドポイントでクラスター内の既存の要求がドレインされるようにします。 これにより、ステートレス インスタンスによってアドバタイズされたエンドポイントが、インスタンスの終了前の延期期間の開始 *前* に削除されます。 この延期期間により、インスタンスが実際に停止する前に、既存の要求を適切にドレインすることができます。 クライアントには、延期期間開始時点でコールバック関数によってエンドポイントの変更が通知されるため、エンドポイントを解決し、停止するインスタンスへの新しい要求の送信を回避できます。 これらの要求は、[リバース プロキシ](./service-fabric-reverseproxy.md)を使用するクライアントや、エンドポイントを更新する通知モデル ([ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription)) をサービス エンドポイントの解決 API と共に使用しているクライアントから送信されたものである場合があります。

### <a name="service-configuration"></a>サービス構成

サービス側で延期期間を構成するには、いくつかの方法があります。

 * **新しいサービスを作成する場合** は、`-InstanceCloseDelayDuration` を指定します。

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>
    ```

 * **アプリケーション マニフェストの既定のセクションでサービスを定義する場合** は、`InstanceCloseDelayDurationSeconds` プロパティを割り当てます。

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **既存のサービスを更新する場合** は、`-InstanceCloseDelayDuration` を指定します。

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

 * **ARM テンプレートを使用して既存のサービスを作成または更新する場合** は、`InstanceCloseDelayDuration` 値を指定します (サポートされている最小 API バージョン:2019-11-01-preview):

    ```ARM template to define InstanceCloseDelayDuration of 30seconds
    {
      "apiVersion": "2019-11-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications/services",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
      "location": "[variables('clusterLocation')]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
      ],
      "properties": {
        "provisioningState": "Default",
        "serviceKind": "Stateless",
        "serviceTypeName": "[parameters('serviceTypeName')]",
        "instanceCount": "-1",
        "partitionDescription": {
          "partitionScheme": "Singleton"
        },
        "serviceLoadMetrics": [],
        "servicePlacementPolicies": [],
        "defaultMoveCost": "",
        "instanceCloseDelayDuration": "00:00:30.0"
      }
    }
    ```

### <a name="client-configuration"></a>クライアントの構成

エンドポイントが変更されたときに通知を受け取るには、クライアントはコールバックを登録する必要があります ([ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription) を参照)。
変更通知は、エンドポイントが変更されたことを示しており、クライアントでは、エンドポイントを再び解決する必要があります。また、アドバタイズされなくなったエンドポイントはすぐに停止するため、使用しないようにします。

### <a name="optional-upgrade-overrides"></a>省略可能なアップグレードのオーバーライド

サービスごとに既定の延期期間を設定するだけでなく、同じ (`InstanceCloseDelayDurationSec`) オプションを使用して、アプリケーションまたはクラスターのアップグレード中に延期期間をオーバーライドすることもできます。

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

オーバーライドされた延期期間は、呼び出されたアップグレード インスタンスにのみ適用されます。それ以外の場合、個々のサービスの延期期間構成は変更されません。 たとえば、これを利用すると、事前に構成されたアップグレードの延期期間をスキップするために `0` の延期期間を指定できます。

> [!NOTE]
> * 要求をドレインする設定では、ドレイン中のエンドポイントへの Azure ロード バランサーによる新しい要求の送信が阻止できません。
> * サービスの解決は、エラーの発生後にトリガーされるため、クレームベースの解決メカニズムでは要求は正常にドレインされません。 前述のように、これは代わりに [ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription) を使用してエンドポイントの変更通知をサブスクライブするように拡張する必要があります。
> * アップグレードが影響を受けないものである場合、つまり アップグレード時にレプリカが停止されない場合、この設定は受け入れられません。
>
>

> [!NOTE]
> この機能は、クラスター コードのバージョンが 7.1.XXX 以上の場合、前述のように Update-ServiceFabricService コマンドレットまたは ARM テンプレートを使用して既存のサービスに構成することができます。
>
>

## <a name="manual-upgrade-mode"></a>手動アップグレード モード

> [!NOTE]
> すべての Service Fabric アップグレードで、*Monitored* アップグレード モードをお勧めします。
> *UnmonitoredManual* アップグレード モードは、アップグレードが失敗または中断した場合にのみ検討してください。 
>
>

*Monitored* モードでは、Service Fabric が正常性ポリシーを適用して、アップグレードの進行中にアプリケーションの正常性を確認します。 正常性ポリシーに違反すると、指定した *FailureAction* に応じて、アップグレードが中断されるか、自動的にロールバックされます。

*UnmonitoredManual* モードでは、アプリケーション管理者がアップグレードの進行を完全に制御します。 このモードは、カスタムの正常性評価ポリシーを適用する場合や、一般的でないアップグレードを実行して、正常性の監視を完全にバイパスする場合 (アプリケーションが既にデータ損失の状態にある場合など) に便利です。 このモードでアップグレードを実行すると、各 UD の完了後にアップグレードが中断し、[Resume-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/resume-servicefabricapplicationupgrade) を使用して明示的に再開する必要があります。 アップグレードが中断し、ユーザーが再開できる状態になると、そのアップグレードの状態は *RollforwardPending* と表示されます (「[UpgradeState](/dotnet/api/system.fabric.applicationupgradestate)」を参照)。

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

## <a name="upgrade-application-parameters-independently-of-version"></a>バージョンとは無関係にアプリケーション パラメーターをアップグレードする

マニフェストのバージョンを変更せずに Service Fabric アプリケーションのパラメーターを変更することが望ましい場合があります。 これは、Azure Service Fabric の **Start-ServiceFabricApplicationUpgrade** という PowerShell コマンドレットで **-ApplicationParameter** フラグを使用することで簡単に実行できます。 次のプロパティを持つ Service Fabric アプリケーションを想定します。

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

ここで、**Start-ServiceFabricApplicationUpgrade** コマンドレットを使用してアプリケーションをアップグレードします。 この例では監視付きのアップグレードを示していますが、監視されていないアップグレードも使用できます。 このコマンドレットに指定できるフラグの詳細については、[Azure Service Fabric PowerShell モジュール リファレンス](/powershell/module/servicefabric/start-servicefabricapplicationupgrade#parameters)をご覧ください。

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

アップグレード後、アプリケーションのパラメーターが更新されていること、またバージョンが同じであることを確認します。

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="roll-back-application-upgrades"></a>アプリケーションのアップグレードをロールバックする

アップグレードは 3 つのモード (*Monitored*、*UnmonitoredAuto*、*UnmonitoredManual*) のいずれかでロールフォワードできますが、ロールバックできるのは *UnmonitoredAuto* または *UnmonitoredManual* モードのみです。 *UnmonitoredAuto* モードでのロールバックは、*UpgradeReplicaSetCheckTimeout* の既定値が異なる点を除き、ロールフォワードと同じように動作します (「[アプリケーション アップグレードのパラメーター](service-fabric-application-upgrade-parameters.md)」を参照)。 *UnmonitoredManual* モードでのロールバックは、ロールフォワードと同じように動作します。つまり、ロールバックは各 UD の完了後に中断し、[Resume-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/resume-servicefabricapplicationupgrade) を使用して明示的に再開してロールバックを続行する必要があります。

*Monitored* モードで *FailureAction* に *Rollback* を指定し、アップグレードの正常性ポリシーに違反した場合 (「[アプリケーション アップグレードのパラメーター](service-fabric-application-upgrade-parameters.md)」を参照)、または [Start-ServiceFabricApplicationRollback](/powershell/module/servicefabric/start-servicefabricapplicationrollback) を明示的に使用した場合に、ロールバックが自動的にトリガーされます。

ロールバック中、*UpgradeReplicaSetCheckTimeout* の値とモードは、[Update-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/update-servicefabricapplicationupgrade) を使用していつでも変更できます。

## <a name="next-steps"></a>次のステップ
[Visual Studio を使用したアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial.md) に関する記事では、Visual Studio を使用してアプリケーションをアップグレードする方法について説明します。

[PowerShell を使用したアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial-powershell.md) に関する記事では、PowerShell を使用したアプリケーションのアップグレードについて説明します。

[アップグレード パラメーター](service-fabric-application-upgrade-parameters.md)を使用して、アプリケーションのアップグレード方法を制御します。

[データのシリアル化](service-fabric-application-upgrade-data-serialization.md)の方法を学ぶことで、アプリケーションのアップグレードに互換性を持たせます。

「[アプリケーションのアップグレードのトラブルシューティング](service-fabric-application-upgrade-troubleshooting.md)」の手順を参照して、アプリケーションのアップグレードでの一般的な問題を修正します。
