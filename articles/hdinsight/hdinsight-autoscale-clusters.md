---
title: Azure HDInsight クラスターを自動的にスケーリングする (プレビュー)
description: HDInsight の自動スケール機能を使用してクラスターを自動的にスケール調整する
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: hrasheed
ms.openlocfilehash: 11828b3b056519d0ebe3233f078c6b3f6fc2ea1c
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997497"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Azure HDInsight クラスターを自動的にスケーリングする (プレビュー)

>[!Important]
>HDInsight の自動スケール機能は現在プレビュー段階です。 サブスクリプションで自動スケーリングを有効にするには、hdiautoscalepm@microsoft.com までメールをお送りください。

Azure HDInsight のクラスター自動スケール機能は、事前に定義された範囲内の負荷に基づいて、クラスター内のワーカー ノードの数を自動的にスケールアップおよびスケールダウンします。 新しい HDInsight クラスターの作成中に、ワーカー ノードの最小数と最大数を設定できます。 自動スケールはその後、分析負荷のリソース要件を監視し、それに応じてワーカー ノードの数をスケールアップまたはスケールダウンします。 この機能に対して追加料金は発生しません。

## <a name="getting-started"></a>使用の開始

### <a name="create-a-cluster-with-the-azure-portal"></a>Azure portal でのクラスターの作成

> [!Note]
> 自動スケールは現在、Azure HDInsight Hive、MapReduce、および Spark クラスター バージョン 3.6 でのみサポートされています。

自動スケーリング機能を有効にするには、通常のクラスターの作成プロセスの一部として、次の操作を行ってください。

1. **[簡易作成]** ではなく、**[カスタム (サイズ、設定、アプリ)]** を選択します。
2. **カスタム**の手順 5 (**クラスター サイズ**) で、**[ワーカー ノードの自動スケーリング]** チェックボックスをオンにします。
3. 次のプロパティに希望する値を入力します。  

    * **ワーカー ノードの初期の数**  
    * ワーカー ノードの**最小**数  
    * ワーカー ノードの**最大**数  

![[Worker node Autoscale (preview)] (ワーカー ノードの自動スケール (プレビュー)) オプションを有効にする](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

ワーカー ノードの初期の数には、最小数から最大数までの数を指定する必要があります。 この値によって、クラスターが作成されるときのその初期サイズが定義されます。 ワーカー ノードの最小数は 1 以上である必要があります。

ノードの種類ごとに VM の種類を選択すると、クラスター全体の概算のコスト範囲を表示できるようになります。 その後、これらの設定を予算に合わせて調整できます。

サブスクリプションには、リージョンごとに容量のクォータがあります。 ヘッド ノードのコアの総数とワーカー ノードの最大数の合計が容量のクォータを超えることはできません。 ただし、このクォータはソフト制限です。それを簡単に増やすためのサポート チケットをいつでも作成できます。

> [!Note]  
> 合計のコア クォータ制限を超えた場合は、'最大ノードがこのリージョン内の使用可能なコア数を超えました。別のリージョンを選択するか、またはサポートに連絡してクォータを増やしてください。' というエラー メッセージが表示されます。

Azure portal を使用した HDInsight クラスターの作成に関する詳細については、「[Azure Portal を使用した HDInsight の Linux ベースのクラスターの作成](hdinsight-hadoop-create-linux-clusters-portal.md)」を参照してください。  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Resource Manager テンプレートでクラスターを作成する

Azure Resource Manager テンプレートで HDInsight クラスターを作成するには、以下の JSON スニペットに示すように、プロパティ `minInstanceCount` と `maxInstanceCount` で `computeProfile` > `workernode` セクションに `autoscale` ノードを追加します。

```json
{                            
    "name": "workernode",
    "targetInstanceCount": 4,
    "autoscale": {
        "capacity": {
            "minInstanceCount": 2,
            "maxInstanceCount": 10
        }        
    },
    "hardwareProfile": {
        "vmSize": "Standard_D13_V2"
    },
    "osProfile": {
        "linuxOperatingSystemProfile": {
            "username": "[parameters('sshUserName')]",
            "password": "[parameters('sshPassword')]"
        }
    },
    "virtualNetworkProfile": null,
    "scriptActions": []
}
```

Resource Manager テンプレートを使用してクラスターを作成する方法の詳細については、「[Resource Manager テンプレートを使用して HDInsight で Apache Hadoop クラスターを作成する](hdinsight-hadoop-create-linux-clusters-arm-templates.md)」を参照してください。  

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>実行中のクラスターの自動スケーリングの有効化および無効化

新しい HDInsight クラスターの自動スケーリングのみを有効または無効にすることができます。

## <a name="monitoring"></a>監視

クラスター メトリックの一部として、クラスターのスケールアップおよびスケールダウンの履歴を表示できます。 また、過去の 1 日、1 週間、またはそれより長い期間中のすべてのスケール アクションを一覧表示することもできます。

## <a name="how-it-works"></a>動作のしくみ

### <a name="metrics-monitoring"></a>メトリックの監視

自動スケールはクラスターを継続的に監視し、次のメトリックを収集します。

1. **保留中の CPU の合計**: すべての保留中のコンテナーの実行を開始するために必要なコアの総数。
2. **保留中のメモリの合計**: すべての保留中のコンテナーの実行を開始するために必要なメモリの合計 (MB 単位)。
3. **空き CPU の合計**: アクティブなワーカー ノード上のすべての未使用のコアの合計。
4. **空きメモリの合計**: アクティブなワーカー ノード上の未使用のメモリの合計 (MB 単位)。
5. **ノードごとの使用済みメモリ**: ワーカー ノード上の負荷。 10 GB のメモリが使用されているワーカー ノードは、使用済みメモリが 2 GB のワーカー ノードより多くの負荷がかかっていると見なされます。
6. **ノードごとのアプリケーション マスターの数**: ワーカー ノード上で実行されているアプリケーション マスター (AM) コンテナーの数。 2 つの AM コンテナーをホストしているワーカー ノードは、ホストしている AM コンテナーがないワーカー ノードより重要であると見なされます。

上のメトリックは 60 秒ごとにチェックされます。 自動スケーリングでは、これらのメトリックに基づいてスケールアップおよびスケールダウンの決定が行われます。

### <a name="cluster-scale-up"></a>クラスターのスケール アップ

次の条件が検出されると、自動スケーリングによりスケールアップ要求が発行されます。

* 保留中の CPU の合計が空き CPU の合計を超えて 3 分以上が経過した。
* 保留中のメモリの合計が空きメモリの合計を超えて 3 分以上が経過した。

現在の CPU とメモリの要件を満たすために必要な新しいワーカー ノードの特定の数が計算された後、その数の新しいワーカー ノードを追加するスケールアップ要求が発行されます。

### <a name="cluster-scale-down"></a>クラスターのスケール ダウン

次の条件が検出されると、自動スケーリングによりスケールダウン要求が発行されます。

* 保留中の CPU の合計が空き CPU の合計を下回って 10 分以上が経過した。
* 保留中のメモリの合計が空きメモリの合計を下回って 10 分以上が経過した。

ノードごとの AM コンテナーの数および現在の CPU とメモリの要件に基づき、自動スケーリングにより、潜在的な削除候補のノードが指定されて、特定の数のノードを削除する要求が発行されます。スケール ダウンによってノードの使用停止がトリガーされ、ノードは完全に使用停止になってから削除されます。

## <a name="next-steps"></a>次の手順

* クラスターを手動でスケール調整するためのベスト プラクティスについては、[スケール調整のベスト プラクティス](hdinsight-scaling-best-practices.md)に関するページを参照してください。
