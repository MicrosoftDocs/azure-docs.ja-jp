---
title: Azure Migrate Server Assessment での依存関係の分析
description: Azure Migrate Server Assessment を使用して、評価のために依存関係の分析を使用する方法について説明します。
ms.topic: conceptual
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 09/15/2020
ms.openlocfilehash: 378a85ed77a6eedeb928dee541046db1909da553
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491993"
---
# <a name="dependency-analysis"></a>依存関係の分析

この記事では、Azure Migrate での依存関係の分析について説明します。Server Assessment を使用して作成する方法について説明します。


依存関係の分析では、検出されたオンプレミス マシン間の依存関係を特定します。 これには以下のような利点があります。 

- マシンをグループ化して、より正確で信頼性が高い評価を実現できます。
- 一緒に移行する必要があるマシンを特定できます。 これは特に、どのマシンが、Azure に移行するアプリのデプロイの一部になっているか明確でない場合に便利です。
- マシンが使用中かどうかや、移行の代わりにどのマシンの使用を停止できるかを特定できます。
- 依存関係の分析は、作業漏れがないようにする助けになるため、移行後の突然の停止を避けられます。
- 依存関係の分析に関する一般的な質問を[確認](common-questions-discovery-assessment.md#what-is-dependency-visualization)してください。


## <a name="analysis-types"></a>分析の種類

依存関係の分析をデプロイする場合は、2 つのオプションがあります

**オプション** | **詳細** | **パブリック クラウド** | **Azure Government**
----  |---- | ---- 
**エージェントレス** | vSphere API を使用して VMware VM のデータをポーリングします。<br/><br/> VM にエージェントをインストールする必要はありません。<br/><br/> このオプションは現在プレビュー段階であり、VMware VM のみが対象です。 | サポートされています。 | サポートされています。
**エージェントベースの分析** | 依存関係の視覚化と分析を可能にするため、Azure Monitor の [Service Map ソリューション](../azure-monitor/insights/service-map.md)を使用します。<br/><br/> 分析するオンプレミスのマシンそれぞれに、エージェントをインストールする必要があります。 | サポートされています | サポートされていません。


## <a name="agentless-analysis"></a>エージェントレスの分析

エージェントレスの依存関係の分析は、それが有効になっているマシンから TCP 接続データをキャプチャすることで機能します。 VM にはエージェントがインストールされません。 同一のソース サーバーとプロセスを使用する接続が、宛先のサーバー、プロセス、およびポートと、1 つの依存関係へと論理的にグループ化されます。 キャプチャした依存関係データは、マップ ビューで視覚化したり、CSV としてエクスポートしたりできます。 分析するマシンにエージェントがインストールされている必要はありません。

### <a name="dependency-data"></a>依存関係データ

依存関係データの検出が開始された後、ポーリングが開始されます。

- Azure Migrate アプライアンスは 5 分ごとにマシンから TCP 接続のデータをポーリングし、データを収集します。
- データは、vSphere API を使用して、vCenter Server 経由でゲスト VM から収集されます。
- ポーリングでは、以下のデータが収集されます。

    - アクティブな接続が含まれるプロセスの名前。
    - アクティブな接続が含まれるプロセスを実行するアプリケーションの名前。
    - アクティブな接続に関する宛先のポート。

- 収集されたデータは、ID 情報推定のために Azure Migrate アプライアンスで処理され、6 時間ごとに Azure Migrate に送信されます


## <a name="agent-based-analysis"></a>エージェントベースの分析

エージェントベースの分析の場合、Server Assessment で Azure Monitor の [Service Map ソリューション](../azure-monitor/insights/service-map.md)を使用します。 分析するマシンそれぞれに、[Microsoft Monitoring Agent/Log Analytics エージェント](../azure-monitor/platform/agents-overview.md#log-analytics-agent)と [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent) をインストールします。

### <a name="dependency-data"></a>依存関係データ

エージェントベースの分析では、以下のデータが提供されます。

- ソース マシンのサーバー名、プロセス、アプリケーション名。
- ターゲット マシンのサーバー名、プロセス、アプリケーション名、ポート。
- 接続数、待機時間、データ転送に関する情報が収集され、Log Analytics クエリで使用できます。 



## <a name="compare-agentless-and-agent-based"></a>エージェントレスとエージェントベースを比較する

エージェントレスの視覚化とエージェントベースの視覚化の違いを次の表にまとめます。

**要件** | **エージェントレス** | **エージェント ベース**
--- | --- | ---
**サポート** | VMware VM のみが対象のプレビュー段階です。 サポートされているオペレーティング システムについては[こちらを確認してください](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)。 | 一般提供 (GA) 中。
**エージェント** | 分析対象のマシンにエージェントは必要ありません。 | 分析するオンプレミスのマシンそれぞれにエージェントが必要です。
**Log Analytics** | 不要。 | Azure Migrate では、依存関係の分析のために [Azure Monitor ログ](../azure-monitor/log-query/log-query-overview.md)の [Service Map](../azure-monitor/insights/service-map.md) ソリューションを使用します。<br/><br/> Log Analytics ワークスペースを Azure Migrate プロジェクトに関連付けます。 ワークスペースは、米国東部リージョン、東南アジア リージョン、または西ヨーロッパ リージョンに存在する必要があります。 ワークスペースは、[Service Map がサポートされている](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)リージョンに存在する必要があります。
**処理** | TCP 接続データをキャプチャします。 検出後は、5 分間隔でデータが収集されます。 | マシンにインストールされている Service Map エージェントにより、TCP プロセスと、各プロセスの受信/送信接続に関するデータが収集されます。
**データ** | ソース マシンのサーバー名、プロセス、アプリケーション名。<br/><br/> ターゲット マシンのサーバー名、プロセス、アプリケーション名、ポート。 | ソース マシンのサーバー名、プロセス、アプリケーション名。<br/><br/> ターゲット マシンのサーバー名、プロセス、アプリケーション名、ポート。<br/><br/> 接続数、待機時間、データ転送に関する情報が収集され、Log Analytics クエリで使用できます。 
**視覚化** | 1 つのサーバーの依存関係マップを、1 時間から 30 日までの範囲で表示できます。 | 1 つのサーバーの依存関係マップ。<br/><br/> サーバーのグループの依存関係マップ。<br/><br/>  マップは 1 時間についてのみ表示できます。<br/><br/> マップ ビューからグループのサーバーを追加および削除します。
データのエクスポート | 過去 30 日間のデータを CSV 形式でダウンロードできます。 | データは Log Analytics で照会できます。



## <a name="next-steps"></a>次のステップ

- エージェントベースの依存関係の視覚化を[設定する](how-to-create-group-machine-dependencies.md)。
- VMware VM のエージェントレスの依存関係の視覚化を[試す](how-to-create-group-machine-dependencies-agentless.md)。
- 依存関係の視覚化について[よく寄せられる質問](common-questions-discovery-assessment.md#what-is-dependency-visualization)を確認する。
