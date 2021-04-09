---
title: Azure Monitor で IIS 用に Blob Storage を使用し、イベント用にテーブル ストレージを使用する | Microsoft Docs
description: Azure Monitor は、テーブル ストレージに診断を書き込む Azure サービスのログ、または Blob Storage に書き込まれた IIS ログを読み取ることができます。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: deb6b5f3718c1a7c84e3591bf9abcceb72b785da
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054159"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>Azure Diagnostics 拡張機能から Azure Monitor ログにデータを収集する
Azure Diagnostics 拡張機能は、仮想マシンを含む Azure コンピューティング リソースのゲスト オペレーティング システムから監視データを収集する、[Azure Monitor のエージェント](../agents/agents-overview.md)です。 この記事では、診断拡張機能によって収集されたデータを Azure Storage から Azure Monitor ログに収集する方法について説明します。

> [!NOTE]
> Azure Monitor の Log Analytics エージェントは、ゲスト オペレーティング システムから Azure Monitor ログにデータを収集するために一般的に推奨される方法です。 エージェントの詳細な比較については、「[Azure Monitor エージェントの概要](../agents/agents-overview.md)」を参照してください。

## <a name="supported-data-types"></a>サポートされるデータ型
Azure Diagnostics 拡張機能では、データは Azure Storage アカウントに格納されます。 このデータを収集する Azure Monitor ログは、次の場所にある必要があります。

| ログの種類 | リソースの種類 | 場所 |
| --- | --- | --- |
| IIS ログ |Virtual Machines <br> Web ロールの比較 <br> Worker ロール |wad-iis-logfiles (Blob Storage) |
| syslog |Virtual Machines |LinuxsyslogVer2v0 (Table Storage) |
| Service Fabric の操作イベント |Service Fabric ノード |WADServiceFabricSystemEventTable |
| Service Fabric Reliable Actor のイベント |Service Fabric ノード |WADServiceFabricReliableActorEventTable |
| Service Fabric Reliable Service のイベント |Service Fabric ノード |WADServiceFabricReliableServiceEventTable |
| Windows イベント ログ |Service Fabric ノード <br> Virtual Machines <br> Web ロールの比較 <br> Worker ロール |WADWindowsEventLogsTable (Table Storage) |
| Windows ETW のログ |Service Fabric ノード <br> Virtual Machines <br> Web ロールの比較 <br> Worker ロール |WADETWEventTable (Table Storage) |

## <a name="data-types-not-supported"></a>サポートされていないデータ型

- ゲスト オペレーティング システムからのパフォーマンス データ
- Azure Web サイトからの IIS ログ


## <a name="enable-azure-diagnostics-extension"></a>Azure Diagnostics 拡張機能を有効にする
診断拡張機能のインストールと構成の詳細については、「[Windows Azure Diagnostic 拡張機能 (WAD) のインストールと構成](../agents/diagnostics-extension-windows-install.md)」または「[Linux Diagnostic 拡張機能を使用して、メトリックとログを監視する](../../virtual-machines/extensions/diagnostics-linux.md)」を参照してください。 これにより、ストレージ アカウントを指定したり、Azure Monitor ログに転送するデータの収集を構成したりできます。


## <a name="collect-logs-from-azure-storage"></a>Azure Storage からログを収集する
Azure Storage アカウントからの診断拡張機能データの収集を有効にするには、次の手順に従います。

1. Azure portal で **Log Analytics ワークスペース** に移動し、ご自分のワークスペースを選択します。
1. メニューの **[ワークスペースのデータ ソース]** セクションで、 **[ストレージ アカウント ログ]** をクリックします。
2. **[追加]** をクリックします。
3. 収集するデータが含まれている **ストレージ アカウント** を選択します。
4. 収集する **[データ型]** を選択します。
5. ソースの値はデータ型に応じて自動的に設定されます。
6. **[OK]** をクリックして構成を保存します。
7. 追加のデータ型についても同じ手順を繰り返します。

約 30 分で、ストレージ アカウントからのデータを Log Analytics ワークスペースで確認できるようになります。 表示されるのは、構成の適用後にストレージに書き込まれたデータだけです。 ワークスペースは、ストレージ アカウントから既存のデータを読み取りません。

> [!NOTE]
> ストレージ アカウントにソースが存在するかどうかや、新しいデータが書き込まれているかどうかの確認は、ポータルでは実行されません。



## <a name="next-steps"></a>次のステップ

* サポートされている Azure サービスの[ログとメトリックを収集](../essentials/resource-logs.md#send-to-log-analytics-workspace)します。
* [ソリューションを有効](../insights/solutions.md) にして、データに対する洞察を得ます。
* [検索クエリを使用](../logs/log-query-overview.md) して、データを分析します。