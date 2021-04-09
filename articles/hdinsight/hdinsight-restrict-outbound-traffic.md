---
title: 送信ネットワーク トラフィック制限を構成する - Azure HDInsight
description: Azure HDInsight クラスターの送信ネットワーク トラフィック制限を構成する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: 297c1d4afca5a1d605a046d69b086a05a9322bc7
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104872083"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>ファイアウォールを使用して Azure HDInsight クラスターのアウトバウンド ネットワーク トラフィックを構成する

この記事では、Azure Firewall を使用して HDInsight クラスターからの送信トラフィックをセキュリティで保護するための手順を説明します。 次の手順では、既存のクラスター用に Azure Firewall を構成することを前提としています。 新しいクラスターをファイアウォールの背後にデプロイする場合は、最初に HDInsight クラスターとサブネットを作成します。 次に、このガイドの手順に従います。

## <a name="background"></a>バックグラウンド

HDInsight クラスターは、通常は仮想ネットワークにデプロイされます。 クラスターは、その仮想ネットワークの外部にあるサービスに依存しています。

受信管理トラフィックはファイアウォールを介して送信できません。 [ここ](./hdinsight-service-tags.md)に記載されているように、受信トラフィックには NSG サービス タグを使用できます。 

HDInsight の送信トラフィックの依存関係は、ほぼすべて、FQDN を使用して定義されています。 その背後には静的 IP アドレスがありません。 静的アドレスがないということは、ネットワーク セキュリティ グループ (NSG) によってクラスターからの送信トラフィックをロックできないことを意味します。 IP アドレスは頻繁に変わるので、現在の名前解決に基づいてルールを設定し、使用することはできません。

FQDN に基づいて送信トラフィックを制御できるファイアウォールを使用して、送信アドレスをセキュリティで保護します。 Azure Firewall では、宛先の FQDN または [FQDN タグ](../firewall/fqdn-tags.md)に基づいて送信トラフィックが制限されます。

## <a name="configuring-azure-firewall-with-hdinsight"></a>HDInsight に合わせて Azure Firewall を構成する

Azure Firewall を使用して既存の HDInsight からのエグレスをロックダウンする手順の概要は、次のとおりです。

1. サブネットを作成します。
1. ファイアウォールを作成します。
1. ファイアウォールにアプリケーション ルールを追加します
1. ファイアウォールにネットワーク ルールを追加します。
1. ルーティング テーブルを作成します。

### <a name="create-new-subnet"></a>新しいサブネットを作成する

クラスターが存在する仮想ネットワークに **AzureFirewallSubnet** という名前のサブネットを作成します。

### <a name="create-a-new-firewall-for-your-cluster"></a>クラスター用の新しいファイアウォールを作成します

「**ファイアウォールをデプロイする**」の手順に従って、**Test-FW01** という名前のファイアウォールを作成します。「[Tutorial: Deploy and configure Azure Firewall using the Azure portal (チュートリアル: Azure portal を使用して Azure Firewall のデプロイと構成を行う)](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)」を参照してください。

### <a name="configure-the-firewall-with-application-rules"></a>アプリケーション ルールを使用してファイアウォールを構成する

クラスターで重要な通信を送受信できるようにするアプリケーション ルール コレクションを作成します。

1. Azure portal で新しいファイアウォール「**Test-FW01**」を選択します。

1. **[設定]**  >  **[ルール]**  >  **[アプリケーション ルール コレクション]**  >  **[+ アプリケーション ルール コレクションの追加]** の順に移動します。

    :::image type="content" source="./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png" alt-text="タイトル:アプリケーション ルール コレクションを追加する":::

1. **[アプリケーション ルール コレクションの追加]** 画面で、次の情報を入力します。

    **一番上のセクション**

    | プロパティ|  値|
    |---|---|
    |名前| FwAppRule|
    |Priority|200|
    |アクション|Allow|

    **[FQDN タグ] セクション**

    | 名前 | ソース アドレス | FQDN タグ | Notes |
    | --- | --- | --- | --- |
    | Rule_1 | * | WindowsUpdate と HDInsight | HDI サービスに必要 |

    **[ターゲットの FQDN] セクション**

    | 名前 | ソース アドレス | プロトコル:ポート | ターゲット FQDN | Notes |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | https:443 | login.windows.net | Windows ログイン アクティビティを許可する |
    | Rule_3 | * | https:443 | login.microsoftonline.com | Windows ログイン アクティビティを許可する |
    | Rule_4 | * | https:443、http:80 | storage_account_name.blob.core.windows.net | `storage_account_name` を実際のストレージ アカウント名に置き換えます。 https 接続のみを使用するには、[[安全な転送が必須]](../storage/common/storage-require-secure-transfer.md) がストレージ アカウントで有効になっていることを確認します。 プライベート エンドポイントを使用してストレージ アカウントにアクセスする場合、この手順は必要ありません。また、ストレージ トラフィックはファイアウォールに転送されません。|

   :::image type="content" source="./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png" alt-text="タイトル:アプリケーション ルール コレクションの詳細を入力する":::

1. **[追加]** を選択します。

### <a name="configure-the-firewall-with-network-rules"></a>ネットワーク ルールを使用してファイアウォールを構成する

HDInsight クラスターを正しく構成するネットワーク ルールを作成します。

1. 前の手順に続けて、 **[ネットワーク ルール コレクション]**  >  **[+ ネットワーク ルール コレクションの追加]** の順に移動します。

1. **[ネットワーク ルール コレクションの追加]** 画面で、次の情報を入力します。

    **一番上のセクション**

    | プロパティ|  値|
    |---|---|
    |名前| FwNetRule|
    |Priority|200|
    |アクション|Allow|

    **[サービス タグ] セクション**

    | 名前 | Protocol | ソース アドレス | サービス タグ | ターゲット ポート | Notes |
    | --- | --- | --- | --- | --- | --- |
    | Rule_5 | TCP | * | SQL | 1433 | HDInsight によって提供される既定の SQL Server を使用している場合は、SQL の [サービス タグ] セクションで、SQL トラフィックのログを記録して監査するためのネットワーク ルールを構成します。 HDInsight サブネットで SQL Server 用にサービス エンドポイントを構成していない限り、ファイアウォールはバイパスされます。 Ambari、Oozie、Ranger、および Hive のメタストアにカスタム SQL サーバーを使用している場合は、独自のカスタム SQL サーバーへのトラフィックの許可のみが必要になります。|
    | Rule_6 | TCP | * | Azure Monitor | * | (省略可能) 自動スケール機能を使用する予定のお客様は、このルールを追加する必要があります。 |
    
   :::image type="content" source="./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png" alt-text="タイトル:アプリケーション ルール コレクションを入力する":::

1. **[追加]** を選択します。

### <a name="create-and-configure-a-route-table"></a>ルート テーブルを作成して構成する

次のエントリを使用してルート テーブルを作成します。

* 「[正常性サービスと管理サービス](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions)」に記載された IP アドレスのうち、次ホップの種類が **[インターネット]** であるすべての IP アドレス。 これには、汎用リージョンの 4 つの IP と、特定のリージョンの 2 つの IP が含まれている必要があります。 このルールは、ResourceProviderConnection が "*受信*" に設定されている場合にのみ必要です。 ResourceProviderConnection が "*送信*" に設定されている場合、これらの IP は UDR では必要ありません。 

* 次ホップが Azure Firewall プライベート IP アドレスである、IP アドレス 0.0.0.0/0 の 1 つの仮想アプライアンス ルート。

たとえば、"米国東部" の米国リージョンに作成したクラスターのルート テーブルを構成するには、次の手順を使用します。

1. Azure ファイアウォール「**Test-FW01**」を選択します。 **[概要]** ページに表示されている **[プライベート IP アドレス]** をコピーします。 この例では、**サンプル アドレス 10.0.2.4** を使用します。

1. その後、 **[すべてのサービス]**  >  **[ネットワーク]**  >  **[ルート テーブル]** の順に移動し、 **[ルート テーブルの作成]** に移動します。

1. 新しいルートから、 **[設定]**  >  **[ルート]**  >  **[+ 追加]** に移動します。 次のルートを追加します。

| ルート名 | アドレス プレフィックス | ネクストホップの種類 | 次ホップ アドレス |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | インターネット | NA |
| 23.99.5.239 | 23.99.5.239/32 | インターネット | NA |
| 168.61.48.131 | 168.61.48.131/32 | インターネット | NA |
| 138.91.141.162 | 138.91.141.162/32 | インターネット | NA |
| 13.82.225.233 | 13.82.225.233/32 | インターネット | NA |
| 40.71.175.99 | 40.71.175.99/32 | インターネット | NA |
| 0.0.0.0 | 0.0.0.0/0 | 仮想アプライアンス | 10.0.2.4 |

ルート テーブルの構成を完了します。

1. **[設定]** の **[サブネット]** を選択して、作成したルート テーブルを HDInsight サブネットに割り当てます。

1. **[+ 関連付け]** を選択します。

1. **[サブネットの関連付け]** 画面で、クラスターが作成された仮想ネットワークを選択します。 また、HDInsight クラスター用に使用した **サブネット** を選択します。

1. **[OK]** を選択します。

## <a name="edge-node-or-custom-application-traffic"></a>エッジノードまたはカスタム アプリケーション トラフィック

上記の手順で、クラスターが問題なく動作するようになります。 ただし、該当する場合は、エッジノードで実行されているカスタムのアプリケーションに対応する依存関係を構成する必要があります。

アプリケーションの依存関係を特定し、Azure Firewall またはルート テーブルに追加する必要があります。

非対称ルーティングの問題を回避するために、アプリケーションのトラフィック用にルートを作成する必要があります。

アプリケーションに他の依存関係がある場合は、それらを Azure Firewall に追加する必要があります。 その他すべてに関する HTTP/HTTPS トラフィックとネットワークのルールを許可するようにアプリケーション ルールを作成します。

## <a name="logging-and-scale"></a>ログ記録とスケール

Azure Firewall は、いくつかの異なるストレージ システムにログを送信できます。 ファイアウォールのログ記録の構成手順については、次のチュートリアルの手順に従ってください。「[チュートリアル: 「Azure Firewall のログとメトリックを監視する](../firewall/firewall-diagnostics.md)」を参照してください。

ログ記録のセットアップを完了した後、Log Analytics を使用する場合は、次のようなクエリでブロックされたトラフィックを表示することができます。

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

アプリケーションを初めて使用する際には、Azure Firewall と Azure Monitor ログを統合すると便利です。 特に、アプリケーションの依存関係をすべては把握していない場合に便利です。 Azure Monitor ログについて詳しくは、「[Azure Monitor でログ データを分析する](../azure-monitor/logs/log-query-overview.md)」をご覧ください

Azure Firewall のスケールの制限と要求の増加については、[こちら](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)のドキュメント、または [FAQ](../firewall/firewall-faq.yml) を参照してください。

## <a name="access-to-the-cluster"></a>クラスターへのアクセス

ファイアウォールを正常にセットアップした後は、内部エンドポイント (`https://CLUSTERNAME-int.azurehdinsight.net`) を使用して仮想ネットワーク内から Ambari にアクセスできます。

パブリック エンドポイント (`https://CLUSTERNAME.azurehdinsight.net`) または SSH エンドポイント (`CLUSTERNAME-ssh.azurehdinsight.net`) を使用するには、[こちら](../firewall/integrate-lb.md)で説明されている非対称ルーティングの問題を回避するために、必ずルート テーブルに正しいルートとNSG ルールが指定されていることを確認します。 特にこのケースでは、インバウンド NSG 規則のクライアント IP アドレスを許可し、次ホップを `internet` に設定してユーザー定義ルート テーブルに追加する必要があります。 ルーティングが正しく設定されていない場合、タイムアウト エラーが表示されます。

## <a name="next-steps"></a>次のステップ

* [Azure HDInsight 仮想ネットワーク アーキテクチャ](hdinsight-virtual-network-architecture.md)
* [ネットワーク仮想アプライアンスを構成する](./network-virtual-appliance.md)
