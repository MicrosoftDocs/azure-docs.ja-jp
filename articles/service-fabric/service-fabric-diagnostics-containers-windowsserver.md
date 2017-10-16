---
title: "Azure Service Fabric コンテナーの監視と診断 | Microsoft Docs"
description: "OMS のコンテナー ソリューションを使用して、Microsoft Azure Service Fabric で調整されたコンテナーの監視と診断を行う方法について説明します。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/10/2017
ms.author: dekapur
ms.openlocfilehash: 874c1a5c4b399ff2254072b7282f05d83a005cc3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-windows-server-containers-with-oms"></a>OMS を使用した Windows Server コンテナーの監視

## <a name="oms-containers-solution"></a>OMS コンテナー ソリューション

Operations Management Suite (OMS) チームは、コンテナーの診断と監視のためのコンテナー ソリューションを公開しています。 Service Fabric ソリューションとともに、このソリューションは、Service Fabric で調整されたコンテナーのデプロイを監視するための優れたツールです。 ソリューションのダッシュボードの外観の単純な例を次に示します。

![基本的な OMS のダッシュボード](./media/service-fabric-diagnostics-containers-windowsserver/oms-containers-dashboard.png)

OMS Log Analytics ツールでクエリを実行したり、生成される任意のメトリックまたはイベントの視覚化に使用できるさまざまな種類のログも収集します。 収集されるログの種類は次のとおりです。

1. ContainerInventory: コンテナーの場所、名前、およびイメージに関する情報を表示
2. ContainerImageInventory: ID やサイズなど、デプロイ済みのイメージに関する情報
3. ContainerLog: 特定のエラー ログ、Docker ログ (stdout など)、およびその他のエントリ
4. ContainerServiceLog: 実行されている Docker デーモン コマンド
5. Perf: コンテナーの CPU、メモリ、ネットワーク トラフィック、ディスク I/O、およびホスト コンピューターからのカスタム メトリックなどのパフォーマンス カウンター

この記事では、クラスターを監視するようにコンテナーを設定するために必要な手順について説明します。 OMS のコンテナー ソリューションの詳細については、ソリューションの[ドキュメント](../log-analytics/log-analytics-containers.md)を参照してください。

## <a name="1-set-up-a-service-fabric-cluster"></a>1.Service Fabric クラスターの設定

[ここ](https://github.com/dkkapur/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Sample)にある Azure Resource Manager テンプレートを使用して、クラスターを作成します。 必ず一意の OMS ワークスペース名を追加してください。 このテンプレートは、Service Fabric (v255.255) のプレビュー ビルドでクラスターをデプロイするための既定でもあります。つまり、これは運用環境で使用できず、別の Service Fabric バージョンにアップグレードできないことを意味します。 このテンプレートを長期間使用したり運用環境で使用する場合は、バージョンを安定したバージョン番号に変更してください。

クラスターを設定したら、適切な証明書がインストールされていることを確認し、クラスターに接続できることを確認します。

リソース グループが [Azure ポータル](https://portal.azure.com/)に正しく設定されていることを確認し、デプロイを検索します。 リソース グループには、すべての Service Fabric リソースが含まれている必要があります。また、Log Analytics ソリューションと Service Fabric ソリューションも必要です。

既存の Service Fabric クラスターを変更するには、次の手順を実行します。
* 診断が有効になっていることを確認します (なっていない場合は、[仮想マシンのスケール セットを更新](/rest/api/virtualmachinescalesets/create-or-update-a-set)することで有効にします)。
* Azure Marketplace を通じて、"Service Fabric Analytics" ソリューションを作成して、OMS ワークスペースを追加します。
* クラスターが属するリソース グループ内の適切な Azure Storage テーブル (WAD によって設定) からデータを取得するように、Service Fabric のソリューションのデータ ソースを編集します。
* PowerShell を使用または仮想マシンのスケール セットを更新する (上記の Resource Manager テンプレートを変更するのと同じリンク) ことで、エージェントを[拡張として仮想マシンのスケール セットに](/powershell/module/azurerm.compute/add-azurermvmssextension)追加します。

## <a name="2-deploy-a-container"></a>2.コンテナーをデプロイする

クラスターの準備ができ、クラスターにアクセスできることを確認したら、そこにコンテナーをデプロイします。 テンプレートで設定されているプレビュー バージョンを使う場合は、Service Fabric の新しい Docker Compose の機能を確認することもできます。 コンテナー イメージがクラスターに初めてデプロイされる場合には、イメージのサイズによっては、ダウンロードに数分かかることに注意してください。

## <a name="3-add-the-containers-solution"></a>手順 3.コンテナー ソリューションを追加する

Azure ポータルで、Azure Marketplace を通じて (監視 + 管理カテゴリの下に) コンテナー リソースを作成します。 

![コンテナー ソリューションの追加](./media/service-fabric-diagnostics-containers-windowsserver/containers-solution.png)

作成手順では、OMS ワークスペースが要求されます。 上記のデプロイで作成したワークスペースを選択します。 この手順は、OMS ワークスペース内にコンテナー ソリューションを追加し、テンプレートでデプロイされた OMS エージェントによって自動的に検出されます。 エージェントはクラスター内のコンテナー プロセスでデータの収集を開始し、10 分から 15 分で、上記のダッシュ ボードのイメージのように、ソリューションがデータとともにを表示されるはずです。

## <a name="4-next-steps"></a>4.次のステップ

OMS には、ワークスペースをより便利にするさまざまなツールが用意されています。 次のオプションを確認して、ニーズに合わせてソリューションをカスタマイズします。
- Log Analytic の一部として提供されている[ログ検索とクエリ](../log-analytics/log-analytics-log-searches.md)機能に詳しくなる
- 特定のパフォーマンス カウンターを選択するように OMS エージェントを構成する (ワークスペースの [ホーム]、[設定]、[データ]、[Windows パフォーマンス カウンター] の順に移動)
- OMS を構成して、検出と診断に役立つ[自動アラート](../log-analytics/log-analytics-alerts.md) ルールを設定する