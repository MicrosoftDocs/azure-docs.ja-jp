---
title: Network Performance Monitor から接続モニター (プレビュー) に移行する
titleSuffix: Azure Network Watcher
description: Network Performance Monitor から接続モニター (プレビュー) に移行する方法について説明します。
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: 69dbb1dd4017c5acf9c195f5104741caee38c2b7
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701309"
---
# <a name="migrate-to-connection-monitor-preview-from-network-performance-monitor"></a>Network Performance Monitor から接続モニター (プレビュー) に移行する

1 回のクリックで、Network Performance Monitor から新しい接続モニター (プレビュー) にテストをダウンタイムなしで移行できます。 利点の詳細については、「[接続モニター (プレビュー)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview)」を参照してください。

>[!NOTE]
> 接続モニター (プレビュー) にはサービス接続モニターからのテストのみを移行できます。
>

## <a name="key-points-to-note"></a>注意する点

* オンプレミスのエージェントとファイアウォールの設定は現状のままです。 変更は必要ありません。 Azure Virtual Machines にインストールされている Log Analytics エージェントは、Network Watcher 拡張機能に置き換える必要があります。
* 既存のテストは、接続モニター (プレビュー) -> テストグループ -> テスト形式にマップされます。 ユーザーは *[編集]* をクリックして新しい接続モニターのプロパティを表示して変更したり、テンプレートをダウンロードして接続モニターの変更を行い、それを Azure Resource Manager 経由で送信したりできます。
* エージェントから、Log Analytics ワークスペースとメトリックの両方にデータが送信されます。
* データの監視
    * Log Analytics のデータ – 移行前のデータはすべて、NetworkMonitoring テーブルで NPM が構成されているワークスペースに引き続き存在します。 移行後のデータは、同じワークスペース内の NetworkMonitoring テーブルと ConnectionMonitor_CL テーブルに移動されます。 NPM からのテストが無効になると、データは ConnectionMonitor_CL テーブルにのみ格納されます。
    * ログベースのアラート、ダッシュボード、および統合 – 新しい ConnectionMonitor_CL テーブルに基づいて、手動でクエリを編集する必要があります。 このリンクを使用して、メトリックでアラートを再作成することもできます。 間もなく、移行の一環として NetworkMonitoring テーブルで自動的にログベースのアラートをメトリックベースのアラートに移行できるようになります。
    
## <a name="prerequisites"></a>前提条件

*   Log Analytics ワークスペースのサブスクリプションとリージョンで Network Watcher が有効になっていることを確認する
*   Log Analytics エージェントがインストールされている Azure 仮想マシンで、Network Watcher 拡張機能を有効にする必要があります。

## <a name="steps-to-migrate-tests-from-network-performance-monitor-to-connection-monitor-preview"></a>Network Performance Monitor から接続モニター (プレビュー) にテストを移行するためのステップ

1.  [接続モニター] をクリックし、[Migrate tests from NPM]\(NPM からのテストの移行\) に移動して、接続モニター (プレビュー) にテストを移行します。

    ![NPM から接続モニター (プレビュー) にテストを移行する例を示すスクリーンショット](./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png)
    
1.  移行するサブスクリプション、ワークスペース、NPM 機能を選択します。 現時点では、サービス接続モニターからのテストのみを移行できます。  
1.  [インポート] をクリックしてテストを移行します。
1.  移行が開始されると、次のような変更が行われます。 
    1. 新しい接続モニター リソースが作成されます。
        1. リージョンとサブスクリプションごとに 1 つの接続モニターが作成されます。 オンプレミスのエージェントを使用したテストの場合は、新しい接続モニター名の形式が <workspaceName> _"on-premises" になります。Azure のエージェントを使用したテストの場合は、新しい接続モニター名の形式が <workspaceName>_ <Azure_region_name> になります。
        1. 監視データは、NPM が有効になっている Log Analytics ワークスペースと同じ場所に、Connectionmonitor_CL テーブルという新しいテーブルで格納されるようになりました。 
        1. テスト名はテスト グループ名に引き継がれます。 テストの説明は移行されません。
        1. 移行元と移行先のエンドポイントが作成され、作成されたテスト グループで使用されます。 オンプレミスのエージェントの場合は、エンドポイント名の形式が <workspaceName> _"endpoint"_ <FQDN of on-premises machine> になります。Azure の場合は、移行するテストに実行されていないエージェントが含まれている場合に、エージェントを有効にしてから再度移行する必要があります。
        1. 宛先ポートとプローブ間隔は、テスト構成 (つまり、"TC _<testname>" と "TC"_ <testname>_"AppThresholds") に移動されます。 ポートの値に基づいて、プロトコルが設定されます。 成功のしきい値などのオプションのプロパティは、空白のままです。
    1. NPM は無効になっていません。 そのため、移行されたテストでも引き続き NetworkMonitoring テーブルおよび ConnectionMonitor_CL テーブルにデータが送信されます。 このステップでは、既存のログベースのアラートと統合は影響を受けません。 間もなく、移行の一環として NetworkMonitoring テーブルで自動的にログベースのアラートをメトリックベースのアラートに移行できるようになります。
    1. 新しく作成された接続モニターが接続モニター (プレビュー) に表示されます。
1.  移行後に、NPM でテストを手動で無効にする必要があります。 それまでの間は、引き続き同じ料金が発生します。 NPM を無効にしている間は、ConnectionMonitor_CL テーブルでアラートを再作成するか、メトリックを使用してください。 また、外部統合 (Power BI のダッシュボード、Grafana、SIEM システムとの統合など) は、ConnectionMonitor_CL テーブルに移行する必要があります。


## <a name="next-steps"></a>次のステップ

* [接続モニターから接続モニター (プレビュー) に移行する方法](migrate-to-connection-monitor-preview-from-connection-monitor.md)について説明する
* [Azure portal を使用して接続モニター (プレビュー) を作成する方法](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal)について説明する
