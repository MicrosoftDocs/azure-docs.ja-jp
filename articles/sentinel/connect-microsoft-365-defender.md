---
title: Microsoft 365 Defender のデータを Azure Sentinel に接続する | Microsoft Docs
description: Microsoft 365 Defender から Azure Sentinel にインシデント、アラート、および未加工のイベント データを取り込む方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 6500805a4dc7e26f5e1bc601df9ea78279ae17e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709344"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>Microsoft 365 Defender から Azure Sentinel にデータを接続する

> [!IMPORTANT]
>
> **Microsoft 365 Defender** は、以前は **Microsoft Threat Protection** または **MTP** と呼ばれていました。
>
> **Microsoft Defender for Endpoint** は、以前は **Microsoft Defender Advanced Threat Protection** または **MDATP** と呼ばれていました。
>
> 以前の名前がしばらくの間使用されることがあります。

## <a name="background"></a>背景

インシデント統合と共に Azure Sentinel の [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) コネクタを使用すると、すべての M365D インシデントとアラートを Azure Sentinel にストリーミングし、両方のポータル間でインシデントの同期を維持することができます。 M365D インシデントには、すべてのアラート、エンティティ、およびその他の関連情報が含まれています。これらは M365D のコンポーネント サービスである **Microsoft Defender for Endpoint**、**Microsoft Defender for Identity**、**Microsoft Defender for Office 365**、および **Microsoft Cloud App Security** によって強化され、アラートがグループ化されています。

このコネクタを使用すると、Microsoft Defender for Endpoint から Azure Sentinel に **詳細な捜索** イベントをストリーミングできます。これにより、MDE の詳細な捜索クエリを Azure Sentinel にコピーすること、Sentinel アラートを MDE の未加工のイベント データを使用して強化し、追加の分析情報を提供すること、リテンション期間を長くしてログを Log Analytics に格納することができます。

インシデント統合と詳細な捜索イベントの収集の詳細については、「[Microsoft 365 Defender と Azure Sentinel の統合](microsoft-365-defender-sentinel-integration.md)」を参照してください。

> [!IMPORTANT]
>
> 現在、Microsoft 365 Defender コネクタは **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

- 「[Microsoft 365 Defender の前提条件](/microsoft-365/security/mtp/prerequisites)」で説明されているように、Microsoft 365 Defender の有効なライセンスが必要です。 

- Azure Active Directory の **全体管理者** または **セキュリティ管理者** である必要があります。

## <a name="connect-to-microsoft-365-defender"></a>Microsoft 365 Defender に接続する

1. Azure Sentinel で、 **[データ コネクタ]** を選択し、ギャラリーから **[Microsoft 365 Defender (プレビュー)]** を選択して、 **[コネクタを開く]** ページを選択します。

1. **[Connect incidents & alerts]\(インシデントとアラートの接続\)** セクションの **[構成]** で、 **[Connect incidents & alerts]\(インシデントとアラートの接続\)** ボタンをクリックします。

1. インシデントの重複を避けるために、 **[Turn off all Microsoft incident creation rules for these products]\(これらの製品のすべての Microsoft インシデント作成規則を無効にする\)** というラベルの付いたチェック ボックスをオンにすることをお勧めします。

    > [!NOTE]
    > Microsoft 365 Defender コネクタを有効にすると、すべての M365D コンポーネントのコネクタ (この記事の冒頭で説明したもの) がバックグラウンドで自動的に接続されます。 コンポーネントのコネクタのいずれかを切断するには、最初に Microsoft 365 Defender コネクタを切断する必要があります。

1. M365 Defender インシデント データのクエリを実行するには、クエリ ウィンドウで次のステートメントを使用します。
    ```kusto
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    ```

1. Microsoft Defender for Endpoint から詳細な捜索イベントを収集する場合は、対応する詳細な捜索テーブルから次の種類のイベントを収集できます。

    1. 収集するイベントの種類があるテーブルのチェック ボックスをオンにします。

       | テーブル名 | イベントの種類 |
       |-|-|
       | DeviceInfo | マシン情報 (OS 情報を含む) |
       | DeviceNetworkInfo | マシンのネットワーク プロパティ |
       | DeviceProcessEvents | プロセスの作成と関連イベント |
       | DeviceNetworkEvents | ネットワーク接続と関連イベント |
       | DeviceFileEvents | ファイルの作成、変更、およびその他のファイル システム イベント |
       | DeviceRegistryEvents | レジストリ エントリの作成と変更 |
       | DeviceLogonEvents | サインインとその他の認証イベント |
       | DeviceImageLoadEvents | DLL 読み込みイベント |
       | DeviceEvents | その他のイベントの種類 |
       | DeviceFileCertificateInfo | 署名されたファイルの証明書情報 |
       |

    1. **[変更の適用]** をクリックします。

    1. Log Analytics で高度なハンティング テーブルに対してクエリを実行するには、クエリ ウィンドウの上の一覧からテーブル名を入力します。

## <a name="verify-data-ingestion"></a>データ インジェストを検証する

コネクタ ページのデータ グラフは、データを取り込んでいることを示しています。 インシデント、アラート、およびイベントごとに 1 行ずつ表示されており、イベント行は有効なすべてのテーブル全体のイベント ボリュームの集計であることがわかります。 コネクタを有効にすると、次の KQL クエリを使用して、より具体的なグラフを生成できます。

受信 M365 Defender インシデントのグラフには、次の KQL クエリを使用します。

```kusto
let Now = now(); 
(range TimeGenerated from ago(14d) to Now-1d step 1d 
| extend Count = 0 
| union isfuzzy=true ( 
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now) 
) 
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now) 
| sort by TimeGenerated 
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events") 
| render timechart 
```

次の KQL クエリを使用して、1 つのテーブルのイベント ボリュームのグラフを生成します (*DeviceEvents* テーブルを、必要な選択したテーブルに変更します)。

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

**[次のステップ]** タブには、便利なブック、サンプル クエリ、含まれている分析ルール テンプレートがいくつか表示されます。 これらのクエリは、そこから実行するか、変更して保存することができます。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Microsoft 365 Defender コネクタを使用して、Microsoft 365 Defender インシデントと Microsoft Defender for Endpoint からの詳細な捜索イベント データを Azure Sentinel に統合する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](./tutorial-detect-threats-built-in.md)の概要。
