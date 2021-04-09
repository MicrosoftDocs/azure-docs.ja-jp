---
title: Network Performance Monitor から接続モニターに移行する
titleSuffix: Azure Network Watcher
description: Network Performance Monitor から接続モニターに移行する方法について説明します。
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: e95f6fdff164a6f5f9d4af4f19b1876d1483a70c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038715"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Network Performance Monitor から接続モニターに移行する

> [!IMPORTANT]
> 2021 年 7 月 1 日以降、既存のワークスペースへの新しいテストの追加や、新しいワークスペースでの Network Performance Monitor の有効化は実行できなくなります。 2021 年 7 月 1 日より前に作成されたテストは使い続けることができます。 現在のワークロードに対するサービスの中断を最小限に抑えるには、2024 年 2 月 29 日より前に、Network Performance Monitor から Azure Network Watcher の新しい接続モニターにテストを移行します。

Network Performance Monitor (NPM) から機能が向上した新しい接続モニターに、1 回のクリックで、ダウンタイムなしにテストを移行できます。 利点の詳細については、[接続モニター](./connection-monitor-overview.md)に関する記事を参照してください。


## <a name="key-points-to-note"></a>注意する点

この移行は、次の結果を生み出すために役立ちます。

* オンプレミスのエージェントとファイアウォールの設定はそのままで動作します。 変更の必要はありません。 Azure 仮想マシンにインストールされている Log Analytics エージェントを、[Network Watcher 拡張機能](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)に置き換える必要があります。
* 既存のテストは、[接続モニター] > [テストグループ] > [テスト形式] にマップされます。 **[編集]** を選択すると、新しい接続モニターのプロパティを表示して変更したり、テンプレートをダウンロードして変更したり、Azure Resource Manager 経由でテンプレートを送信したりすることができます。
* エージェントによって、Log Analytics ワークスペースとメトリックの両方にデータが送信されます。
* データの監視:
   * **Log Analytics 内のデータ**: 移行まで、データは NetworkMonitoring テーブルで NPM が構成されているワークスペースに残っています。 移行後、データは、同じワークスペース内の NetworkMonitoring テーブル、NWConnectionMonitorTestResult テーブル、および NWConnectionMonitorPathResult テーブルに移ります。 NPM でテストが無効にされた後、データは NWConnectionMonitorTestResult テーブルと NWConnectionMonitorPathResult テーブルにのみ格納されるようになります。
   * **ログ ベースのアラート、ダッシュボード、統合**: 新しい NWConnectionMonitorTestResult テーブルと NWConnectionMonitorPathResult テーブルに基づいて、クエリを手作業で編集する必要があります。 メトリックでアラートを作成し直すには、「[接続モニターによるネットワーク接続の監視](./connection-monitor-overview.md#metrics-in-azure-monitor)」を参照してください。
    
## <a name="prerequisites"></a>前提条件

* お使いのサブスクリプションと Log Analytics ワークスペースのリージョンで Network Watcher を確実に有効にします。 
* Log Analytics ワークスペースとは異なるリージョン/サブスクリプションに属している Azure VM がエンドポイントとして使用されている場合、そのサブスクリプションとリージョンで Network Watcher が有効になっていることを確認します。   
* Log Analytics エージェントがインストールされている Azure 仮想マシンで、Network Watcher 拡張機能を有効にする必要があります。

## <a name="migrate-the-tests"></a>テストを移行する

Network Performance Monitor から接続モニターにテストを移行するには、次のようにします。

1. Network Watcher で、 **[Connection Monitor]\(接続モニター\)** を選択し、 **[Migrate tests from NPM]\(NPM からのテストの移行\)** タブを選択します。 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Network Performance Monitor から接続モニターにテストを移行する" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. ドロップダウン リストで、お使いのサブスクリプションとワークスペースを選択してから、移行する NPM 機能を選択します。 
1. **[インポート]** を選択してテストを移行します。

移行が開始されると、次の変更が行われます。 
* 新しい接続モニター リソースが作成されます。
   * リージョンとサブスクリプションごとに 1 つの接続モニターが作成されます。 オンプレミスのエージェントを使用するテストの場合、新しい接続モニターの名前は `<workspaceName>_"workspace_region_name"` という形式になります。 Azure のエージェントを使用するテストの場合、新しい接続モニターの名前は `<workspaceName>_<Azure_region_name>` という形式になります。
   * 監視データは、NPM が有効になっている Log Analytics ワークスペースと同じ場所の新しいテーブル (NWConnectionMonitorTestResult テーブルと NWConnectionMonitorPathResult テーブル) に格納されるようになりました。 
   * テスト名はテスト グループ名として引き継がれます。 テストの説明は移行されません。
   * 移行元と移行先のエンドポイントが作成され、新しいテスト グループで使用されます。 オンプレミスのエージェントの場合、エンドポイントは `<workspaceName>_<FQDN of on-premises machine>` という形式になります。
   * 宛先ポートとプローブ間隔は、`TC_<protocol>_<port>` と `TC_<protocol>_<port>_AppThresholds` というテスト構成に移動されます。 ポートの値に基づいて、プロトコルが設定されます。 ICMP の場合、テスト構成の名前は `TC_<protocol>` と `TC_<protocol>_AppThresholds` になります。 成功のしきい値とその他のオプションのプロパティが設定されている場合は、それらが移行されます。設定されていない場合は空白のままになります。
   * 移行するテストに、実行されていないエージェントが含まれている場合は、エージェントを有効にして再度移行する必要があります。
* NPM は無効化されないため、移行後のテストで、NetworkMonitoring テーブル、NWConnectionMonitorTestResult テーブル、および NWConnectionMonitorPathResult テーブルにデータを送り続けることができます。 このアプローチにより、既存のログ ベースのアラートと統合が影響を受けないことが保証されます。
* 新しく作成された接続モニターが接続モニターに表示されます。

移行後は、必ず次のようにしてください。
* NPM のテストを手動で無効にします。 それまでの間、それらに対して引き続き同じ料金が発生します。 
* NPM を無効にしている間に、NWConnectionMonitorTestResult テーブルと NWConnectionMonitorPathResult テーブルのアラートを再作成するか、メトリックを使用します。 
* 外部統合を NWConnectionMonitorTestResult テーブルと NWConnectionMonitorPathResult テーブルに移行します。 外部統合の例は、Power BI や Grafana のダッシュボードや、セキュリティ情報イベント管理 (SIEM) システムとの統合などです。


## <a name="next-steps"></a>次の手順

接続モニターの詳細については、以下を参照してください。
* [接続モニター (クラシック) から接続モニターに移行する](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Azure portal を使用して接続モニターを作成する](./connection-monitor-create-using-portal.md)
