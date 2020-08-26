---
title: 診断を設定する
titleSuffix: Azure Digital Twins
description: 診断設定を使用してログ記録を有効にする方法について確認します。
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 5091edbf9138cb8ff03df193dcbeed692aaf13e3
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612403"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Azure Digital Twins のトラブルシューティング: 診断ログ

Azure Digital Twins では、リソースの状態に関する情報を提供する、サービス インスタンスの[メトリック](troubleshoot-metrics.md)が収集されます。 これらのメトリックを使用すると、Azure Digital Twins サービスやそれに接続されているリソースの全体的な正常性を評価できます。 これらのユーザー向けの統計情報は、Azure Digital Twins で何が起きているかを確認するのに役立ち、Azure サポートに連絡することなく問題の根本原因の分析を実行する助けとなります。

この記事では、Azure Digital Twins インスタンスからメトリック データの**診断ログ**を有効にする方法について説明します。 これらのログを使用して、サービスの問題をトラブルシューティングしたり、Azure Digital Twins のメトリックを異なる宛先に送信するように診断設定を構成したりできます。 これらの設定の詳細については、「[*プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する*](../azure-monitor/platform/diagnostic-settings.md)」を参照してください。

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Azure portal 上で診断設定を有効にする

Azure Digital Twins インスタンスの診断設定を有効にする方法は次のとおりです。

1. [Azure portal](https://portal.azure.com) にサインインし、Azure Digital Twins インスタンスに移動します。 その名前をポータルの検索バーに入力して、検索することができます。 

2. メニューから **[診断設定]** 、 **[診断設定を追加する]** の順に選択します。

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="[診断設定] ページと追加するボタンが表示されたスクリーンショット":::

3. 次のページで、以下の値を入力します。
     * **診断設定の名前**:診断設定の名前を付けます。
     * **カテゴリの詳細**:監視する操作を選択し、チェック ボックスをオンにしてそれらの操作の診断を有効にします。 診断設定では、次の操作をレポートできます。
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        これらのオプションの詳細については、下の「[*カテゴリの詳細*](#category-details)」を参照してください。
     * **宛先の詳細**:ログの送信先を選択します。 3 つのオプションを自由に組み合わせて選択できます。
        - Log Analytics への送信
        - ストレージ アカウントへのアーカイブ
        - イベント ハブへのストリーミング

        宛先の選択のために追加の詳細情報が必要な場合に、入力を求められることがあります。  
    
4. 新しい設定を保存します。 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="[診断設定] ページと追加するボタンが表示されたスクリーンショット":::

新しい設定は、10 分ほどで有効になります。 その後、インスタンスの **[診断設定]** ページ上の構成されたターゲットにログが表示されます。 

## <a name="category-details"></a>カテゴリの詳細

診断設定を構成するとき **[カテゴリの詳細]** で選択できるログ カテゴリの詳細を次に示します。

| ログのカテゴリ | 説明 |
| --- | --- |
| ADTModelsOperation | モデルに関連するすべての API 呼び出しをログに記録します |
| ADTQueryOperation | クエリに関連するすべての API 呼び出しをログに記録します |
| ADTEventRoutesOperation | イベント ルートに関連するすべての API 呼び出しに加え、Azure Digital Twins から Event Grid、Event Hubs、Service Bus などのエンドポイント サービスへのイベントの送信をログに記録します |
| ADTDigitalTwinsOperation | Azure Digital Twins に関連するすべての API 呼び出しをログに記録します |

各ログ カテゴリは、書き込み、読み取り、削除、およびアクションの操作で構成されます。  これらは、次のように REST API 呼び出しにマップされます。

| イベントの種類 | REST API の操作 |
| --- | --- |
| Write | PUT と PATCH |
| Read | GET |
| 削除 | DELETE |
| アクション | POST |

各カテゴリに記録される操作と対応する [Azure Digital Twins REST API 呼び出し](https://docs.microsoft.com/rest/api/azure-digitaltwins/)の一覧を次に示します。 

>[!NOTE]
> 各ログ カテゴリには、複数の操作/REST API 呼び出しが含まれています。 次の表では、各ログ カテゴリがすべての操作/REST API 呼び出しにマップされています (次のログ カテゴリが表示されるまで)。 

| ログのカテゴリ | 操作 | REST API 呼び出しとその他のイベント |
| --- | --- | --- |
| ADTModelsOperation | Microsoft.DigitalTwins/models/write | デジタル ツイン モデル更新 API |
|  | Microsoft.DigitalTwins/models/read | デジタル ツイン モデルの ID による取得とリスト API |
|  | Microsoft.DigitalTwins/models/delete | デジタル ツイン モデルの削除 API |
|  | Microsoft.DigitalTwins/models/action | デジタル ツイン モデルの追加 API |
| ADTQueryOperation | Microsoft.DigitalTwins/query/action | ツインのクエリ API |
| ADTEventRoutesOperation | Microsoft.DigitalTwins/eventroutes/write | イベント ルート追加 API |
|  | Microsoft.DigitalTwins/eventroutes/read | イベント ルートの ID による取得とリスト API |
|  | Microsoft.DigitalTwins/eventroutes/delete | イベント ルートの削除 API |
|  | Microsoft.DigitalTwins/eventroutes/action | エンドポイント サービスにイベントを発行しようとしてエラーが発生した (API 呼び出しではない) |
| ADTDigitalTwinsOperation | Microsoft.DigitalTwins/digitaltwins/write | Digital Twins の追加、リレーションシップの追加、更新、コンポーネントの更新 |
|  | Microsoft.DigitalTwins/digitaltwins/read | Digital Twins の ID による取得、コンポーネントの取得、ID によるリレーションシップの取得、受信リレーションシップのリスト、リレーションシップのリスト |
|  | Microsoft.DigitalTwins/digitaltwins/delete | Digital Twins の削除、リレーションシップの削除 |
|  | Microsoft.DigitalTwins/digitaltwins/action | Digital Twins のコンポーネント テレメトリの送信、テレメトリの送信 |

## <a name="next-steps"></a>次のステップ

* 診断の構成に関する詳細については、"[*Azure リソースからのログ データの収集と使用*](../azure-monitor/platform/platform-logs-overview.md)" に関するページを参照してください。
* Azure Digital Twins のメトリックについては、[*トラブルシューティング: Azure Monitor でメトリックを表示する方法*](troubleshoot-metrics.md)に関するページを参照してください。
* メトリックのアラートを有効にする方法については、[*トラブルシューティング:アラートの設定*](troubleshoot-alerts.md)に関するページを参照してください。
