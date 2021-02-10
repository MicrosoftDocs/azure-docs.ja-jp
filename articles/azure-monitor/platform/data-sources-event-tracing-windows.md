---
title: Azure Monitor Logs で分析する Windows イベント トレーシング (ETW) イベントの収集
description: Azure Monitor Logs での分析のために Windows イベント トレーシング (ETW) を収集する方法について説明します。
services: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: jamesfit
author: jimmyfit
ms.date: 01/29/2021
ms.openlocfilehash: 6239cf48794c74c5dd810fda42476df399300578
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2021
ms.locfileid: "99223757"
---
# <a name="collecting-event-tracing-for-windows-etw-events-for-analysis-azure-monitor-logs"></a>Azure Monitor Logs で分析する Windows イベント トレーシング (ETW) イベントの収集

*Windows イベント トレーシング (ETW)* は、ユーザーモード アプリケーションやカーネルモード ドライバーをインストルメント化するためのメカニズムを提供します。 Log Analytics エージェントは、Administrative (管理) および Operational (運用) の [ETW チャネル](https://docs.microsoft.com/windows/win32/wes/eventmanifestschema-channeltype-complextype)に書き込まれた [Windows イベントを収集](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events)するために使用されます。 ただし、Analytic (分析) チャネルに書き込まれたものなど、その他のイベントをキャプチャして分析することが必要な場合もあります。  

## <a name="event-flow"></a>イベント フロー

Azure Monitor Logs で分析する[マニフェストベースの ETW イベント](https://docs.microsoft.com/windows/win32/etw/about-event-tracing#types-of-providers)を正しく収集するには、Windows 用の [Azure Diagnostics 拡張機能](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview) (WAD) を使用する必要があります。 このシナリオでは、Diagnostics 拡張機能は ETW コンシューマーとして機能し、中間ストアの Azure Storage (テーブル) にイベントを書き込みます。 ここでは、**WADETWEventTable** という名前のテーブルに格納されます。 その後、Log Analytics によって Azure Storage からテーブル データが収集され、**ETWEvent** という名前のテーブルとして表示されます。

![イベント フロー](./media/data-sources-event-tracing-windows/event-flow.png)

## <a name="configuring-etw-log-collection"></a>ETW ログ収集の構成

### <a name="step-1-locate-the-correct-etw-provider"></a>手順 1: 正しい ETW プロバイダーを見つける

次のいずれかのコマンドを使用して、ソース Windows システム上の ETW プロバイダーを列挙します。

コマンド ライン:

```
logman query providers
```

PowerShell:
```
Get-NetEventProvider -ShowInstalled | Select-Object Name, Guid
```
必要に応じて、ナビゲーションを支援するために、この PowerShell 出力を Out-Gridview にパイプすることを選択できます。

ETW プロバイダーの名前と GUID を記録します。これは、イベント ビューアーに表示される分析またはデバッグ ログ、あるいはイベント データの収集対象のモジュールと整合します。

### <a name="step-2-diagnostics-extension"></a>手順 2:診断拡張機能

*Windows 診断拡張機能* がすべてのソース システムに[インストール](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-windows-install#install-with-azure-portal)されていることを確認します。

### <a name="step-3-configure-etw-log-collection"></a>手順 3: ETW ログ収集を構成する

1. 仮想マシンの **[診断設定]** ブレードに移動します

2. **[ログ]** タブを選択します

3. 下にスクロールして、 **[Windows イベント トレーシング (ETW) イベント]** オプションを有効にします ![診断設定のスクリーンショット](./media/data-sources-event-tracing-windows/enable-event-tracing-windows-collection.png)

4. コレクションの構成対象のプロバイダーに応じて、プロバイダー GUID またはプロバイダー クラスを設定します

5. 必要に応じて [ **[ログ レベル]** ](https://docs.microsoft.com/windows/win32/etw/configuring-and-starting-an-event-tracing-session) 設定します

6. 表示されているプロバイダーの横にある省略記号をクリックして **[構成]** をクリックします

7. **[Default destination table]\(既定の宛先テーブル\)** が **etweventtable** に設定されていることを確認します

8. 必要に応じて [ **[キーワード フィルター]** ](https://docs.microsoft.com/windows/win32/wes/defining-keywords-used-to-classify-types-of-events) を設定します

9. プロバイダーとログの設定を保存します

一致するイベントが生成されると、Azure Storage の **WADetweventtable** テーブルに ETW イベントが表示されるようになります。 Azure Storage Explorer を使用してこれを確認できます。

### <a name="step-4-configure-log-analytics-storage-account-collection"></a>手順 4:Log Analytics ストレージ アカウント コレクションを構成する

[この手順](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-logs#collect-logs-from-azure-storage)に従って、Azure Storage からログを収集します。 構成が完了すると、ETW イベント データが Log Analytics の **ETWEvent** テーブルに表示されます。

## <a name="next-steps"></a>次のステップ
- [カスタム フィールド](https://docs.microsoft.com/azure/azure-monitor/platform/custom-fields)を使用して ETW イベントの構造を作成します
- [ログ クエリ](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)について学習し、データ ソースとソリューションから収集されたデータを分析します。
