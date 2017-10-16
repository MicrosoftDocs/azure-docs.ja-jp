---
title: "Azure Stack の診断 | Microsoft Docs"
description: "Azure Stack の診断のログ ファイルを収集する方法"
services: azure-stack
documentationcenter: 
author: adshar
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: adshar
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: d8f39d921222a3642e3da6e288b4ca11ae1ccaeb
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---
# <a name="azure-stack-diagnostics-tools"></a>Azure Stack の診断ツール

*適用対象: Azure Stack 統合システムおよび Azure Stack 開発キット*
 
Azure Stack は、相互に連携して作用する複数のコンポーネントによって構成された大規模なコレクションです。 これらすべてのコンポーネントで独自のログが生成されるため、相互作用する Azure Stack の複数コンポーネントからのエラーについては特に、問題の診断がたちまち困難になる可能性があります。 

Azure Stack の診断ツールを使用すれば、ログ収集のメカニズムを簡単かつ効率的にすることができます。 次の図は、Azure Stack のログ収集ツールのしくみを示しています。

![ログ収集ツール](media/azure-stack-diagnostics/image01.png)
 
 
## <a name="trace-collector"></a>トレース コレクター
 
トレース コレクターは、既定で有効になっています。 継続的にバックグラウンドで実行され、Azure Stack のコンポーネント サービスから Windows イベント トレーシング (ETW) のすべてのログを収集し、共通のローカル共有に格納します。 

トレース コレクターについて知っておくべき重要事項を次に示します。
 
* トレース コレクターは、既定のサイズ制限で継続的に実行されます。 各ファイルに対して許容される既定の最大サイズ (200 MB) は、サイズの上限では**ありません**。 定期的にサイズのチェックが行われ (現時点では 10 分ごと)、その時点で 200 MB 以上のファイルは保存されて、新しいファイルが生成されます。 また、イベント セッションごとに生成されるファイルの合計サイズには、8 GB (構成可能) の制限が設定されています。 この制限に達した場合、新しいファイルが作成されると最も古いファイルは削除されます。
* ログの保存期間には 5 日間の制限があります。 この制限も構成可能です。 
* 各コンポーネントは、JSON ファイルでトレースの構成プロパティを定義します。 JSON ファイルは `C:\TraceCollector\Configuration` に格納されています。 必要に応じてこれらのファイルを編集し、収集したログの保存期間とサイズの制限を変更できます。 ファイルへの変更を有効にするには、*Microsoft Azure Stack トレース コレクター* サービスを再起動する必要があります。
* 次の例は、XRP VM の FabricRingServices 操作に対する、トレース構成の JSON ファイルです。 

```
{
    "LogFile": 
    {
        "SessionName": "FabricRingServicesOperationsLogSession",
        "FileName": "\\\\SU1FileServer\\SU1_ManagementLibrary_1\\Diagnostics\\FabricRingServices\\Operations\\AzureStack.Common.Infrastructure.Operations.etl",
        "RollTimeStamp": "00:00:00",
        "MaxDaysOfFiles": "5",
        "MaxSizeInMB": "200",
        "TotalSizeInMB": "5120"
    },
    "EventSources":
    [
        {"Name": "Microsoft-AzureStack-Common-Infrastructure-ResourceManager" },
        {"Name": "Microsoft-OperationManager-EventSource" },
        {"Name": "Microsoft-Operation-EventSource" }
    ]
}
```

* **MaxDaysOfFiles**

    このパラメーターは、ファイルの保存期間を制御します。 古いログ ファイルは削除されます。
* **MaxSizeInMB**

    このパラメーターは、1 つのファイルのサイズのしきい値を制御します。 サイズが上限に達すると、新しい .etl ファイルが作成されます。
* **TotalSizeInMB**

    このパラメーターは、イベント セッションから生成された .etl ファイルの合計サイズを制御します。 ファイルの合計サイズがこのパラメーターの値よりも大きくなると、古いファイルは削除されます。
  
## <a name="log-collection-tool"></a>ログ収集ツール
 
PowerShell コマンド `Get-AzureStackLog` を使用して、Azure Stack 環境のすべてのコンポーネントからログを収集できます。 これらは、ユーザーの定義した場所に zip ファイルで保存されます。 テクニカル サポート チームが問題を解決するためにお客様のログを必要とする場合は、このツールを実行するようにお願いすることがあります。

> [!CAUTION]
> これらのログ ファイルには個人を特定できる情報 (PII) が含まれている可能性があります。 ログ ファイルを公開する前に、この点を考慮してください。
 
現時点では、次の種類のログが収集されます。
*   **Azure Stack デプロイ ログ**
*   **Windows イベント ログ**
*   **Panther ログ**

     VM 作成の問題をトラブルシューティングします。
*   **クラスター ログ**
*   **Storage 診断ログ**
*   **ETW ログ**

    これらはトレース コレクターによって収集され、`Get-AzureStackLog` によって取得された場所から共有に格納されます。
 
すべてのコンポーネントから収集されたすべてのログを識別するには、`C:\EceStore\<Guid>\<GuidWithMaxFileSize>` にある顧客構成ファイルの `<Logs>` タグを参照します。
 
**Get-AzureStackLog を実行するには**
1.  ホストに AzureStack\AzureStackAdmin でログインします。
2.  管理者として PowerShell ウィンドウを開きます。
3.  `Get-AzureStackLog` を実行します。  

    **例**

    - すべてのロールのすべてのログを収集します。

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs`

    - VirtualMachines ロールと BareMetal ロールからログを収集します。

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal`

    - ログ ファイルに対する過去 8 時間以内の日付範囲のフィルター処理で、VirtualMachines ロールと BareMetal ロールからログを収集します。

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date)`

`FromDate` および `ToDate` パラメーターが指定されない場合、既定では過去 4 時間以内のログが収集されます。

現時点では `FilterByRole` パラメーターを使用して、次のロールごとにログ収集をフィルター処理できます。

|   |   |   |
| - | - | - |
| `ACSMigrationService`     | `ACSMonitoringService`   | `ACSSettingsService` |
| `ACS`                     | `ACSFabric`              | `ACSFrontEnd`        |
| `ACSTableMaster`          | `ACSTableServer`         | `ACSWac`             |
| `ADFS`                    | `ASAppGateway`           | `BareMetal`          |
| `BRP`                     | `CA`                     | `CPI`                |
| `CRP`                     | `DeploymentMachine`      | `DHCP`               |
|`Domain`                   | `ECE`                    | `ECESeedRing`        |        
| `FabricRing`              | `FabricRingServices`     | `FRP`                |
|` Gateway`                 | `HealthMonitoring`       | `HRP`                |               
| `IBC`                     | `InfraServiceController` | `KeyVaultAdminResourceProvider`|
| `KeyVaultControlPlane`    | `KeyVaultDataPlane`      | `NC`                 |            
| `NonPrivilegedAppGateway` | `NRP`                    | `SeedRing`           |
| `SeedRingServices`        | `SLB`                    | `SQL`                |     
| `SRP`                     | `Storage`                | `StorageController`  |
| `URP`                     | `UsageBridge`            | `VirtualMachines`    |  
| `WAS`                     | `WASPUBLIC`              | `WDS`                |


注意点を次に示します。

* このコマンドでは、どのロールのログが収集されるかに基づいて、ログの収集に時間がかかります。 関連する要素には、ログ収集に指定した期間と、Azure Stack 環境のノード数が含まれます。
* ログの収集が完了したら、コマンドで指定した `-OutputPath` パラメーターに作成された新しいフォルダーを確認します。
* zip ファイルを含むフォルダーに `Get-AzureStackLog_Output.log` という名前のファイルが作成され、このファイルには、ログ収集のエラーを解決するのに使用できるコマンドの出力が含まれています。
* ロールごとに、個々の zip ファイル内にログがあります。 
* 特定のエラーを調べるには、複数のコンポーネントのログが必要な場合があります。
    -   インフラストラクチャのすべての VM のシステム ログとイベント ログは、*VirtualMachines* ロールで収集されます。
    -   すべてのホストのシステム ログとイベント ログは、*BareMetal* ロールで収集されます。
    -   フェールオーバー クラスターおよび Hyper-V のイベント ログは、*Storage* ロールで収集されます。
    -   ACS ログは、*Storage* ロールと *ACS* ロールで収集されます。
* 詳細については、顧客構成ファイルをご覧ください。 各種ロールの `<Logs>` タグを調べます。

> [!NOTE]
> 記憶域スペースを効率的に使用し、ログで占領されることがないようにすることは極めて重要なため、収集したログにはサイズと保存期間の制限が適用されます。 ただし、問題を診断するときに、これらの制限が適用されることによってもはや存在しないログが必要になってくる場合があります。 このため、外部の記憶域スペース (Azure のストレージ アカウント、オンプレミスの追加の記憶装置など) に、8 - 12 時間ごとにログをオフロードし、要件に応じて 1 - 3 か月間そこに保存することを**強くお勧めします**。

### <a name="multi-node-considerations"></a>マルチノードに関する考慮事項
マルチノード環境でログを収集する場合は、次の相違点に注意してください。
* `get-date` 関数は、マルチノード環境ではホワイトリストに含まれません。 そのため、日付を明示的に指定する必要があります。 For example:

   `-FromDate "Friday, August 18, 2017 6:34:48 AM" -ToDate "Friday, August 18, 2017 7:35:25 AM"`
* ハードウェア ライフサイクル ホスト上の共有フォルダーまたはアクセス可能なその他の共有フォルダーに出力するための UNC パスを指定します。 For example:

   `Get-AzureStackLog -OutputSharePath \\10.193.128.250\logs -OutputShareCredential $sharecred`

   `-OutputShareCredential`パラメーターで、共有フォルダーにアクセスするための資格情報を入力するよう求められます。

## <a name="next-steps"></a>次のステップ
[Microsoft Azure Stack のトラブルシューティング](azure-stack-troubleshooting.md)

