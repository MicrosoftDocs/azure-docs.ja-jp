---
title: Azure HDInsight クラスターを自動的にスケーリングする (プレビュー)
description: Azure HDInsight の自動スケーリング機能を使用して Apache Hadoop クラスターを自動的にスケーリングする
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: hrasheed
ms.openlocfilehash: 9071b41ab39c62f639b62a439e4d2530a7d7e11b
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70880069"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Azure HDInsight クラスターを自動的にスケーリングする (プレビュー)

> [!Important]
> 自動スケーリング機能は、2019年 5月 8日より後に作成された Spark、Hive、および MapReduce のクラスターでのみ機能します。 

Azure HDInsight のクラスター自動スケーリング機能では、クラスター内のワーカー ノードの数が自動的にスケールアップおよびスケールダウンされます。 現在、クラスター内の他の種類のノードはスケーリングできません。  新しい HDInsight クラスターの作成中に、ワーカー ノードの最小数と最大数を設定できます。 その後、自動スケーリングによって分析負荷のリソース要件が監視され、ワーカー ノードの数がスケールアップまたはスケールダウンされます。 この機能に対して追加料金は発生しません。

## <a name="cluster-compatibility"></a>クラスターの互換性

次の表では、自動スケーリング機能と互換性のあるクラスターの種類とバージョンについて説明します。

| Version | Spark | Hive | LLAP | hbase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 (ESP なし) | はい | はい | いいえ | いいえ | いいえ | いいえ | いいえ |
| HDInsight 4.0 (ESP なし) | はい | はい | いいえ | いいえ | いいえ | いいえ | いいえ |
| HDInsight 3.6 (ESP あり) | はい | はい | いいえ | いいえ | いいえ | いいえ | いいえ |
| HDInsight 4.0 (ESP あり) | はい | はい | いいえ | いいえ | いいえ | いいえ | いいえ |

## <a name="how-it-works"></a>動作のしくみ

HDInsight クラスターでは、負荷ベースのスケーリングまたはスケジュール ベースのスケーリングを選択できます。 負荷ベースのスケーリングでは、ユーザーが設定した範囲内でクラスター内のノードの数が変更され、最適な CPU 使用率と最小のランニング コストが保証されます。

スケジュール ベースのスケーリングでは、特定の時点で有効な条件に基づいて、クラスター内のノードの数が変更されます。 これらの条件により、目的のノード数にクラスターがスケーリングされます。

### <a name="metrics-monitoring"></a>メトリックの監視

自動スケールはクラスターを継続的に監視し、次のメトリックを収集します。

* **保留中の CPU の合計**: すべての保留中のコンテナーの実行を開始するために必要なコアの総数。
* **保留中のメモリの合計**: すべての保留中のコンテナーの実行を開始するために必要なメモリの合計 (MB 単位)。
* **空き CPU の合計**: アクティブなワーカー ノード上のすべての未使用のコアの合計。
* **空きメモリの合計**: アクティブなワーカー ノード上の未使用のメモリの合計 (MB 単位)。
* **ノードごとの使用済みメモリ**: ワーカー ノード上の負荷。 10 GB のメモリが使用されているワーカー ノードは、使用済みメモリが 2 GB のワーカー ノードより多くの負荷がかかっていると見なされます。
* **ノードごとのアプリケーション マスターの数**: ワーカー ノード上で実行されているアプリケーション マスター (AM) コンテナーの数。 2 つの AM コンテナーをホストしているワーカー ノードは、ホストしている AM コンテナーがないワーカー ノードより重要であると見なされます。

上のメトリックは 60 秒ごとにチェックされます。 自動スケーリングでは、これらのメトリックに基づいてスケールアップおよびスケールダウンの決定が行われます。

### <a name="load-based-cluster-scale-up"></a>負荷ベースのクラスターのスケールアップ

次の条件が検出されると、自動スケーリングによりスケールアップ要求が発行されます。

* 保留中の CPU の合計が空き CPU の合計を超えて 3 分以上が経過した。
* 保留中のメモリの合計が空きメモリの合計を超えて 3 分以上が経過した。

HDInsight サービスによって、現在の CPU とメモリの要件を満たすために必要な新しいワーカー ノードの数が計算された後、必要な数のノードを追加するスケールアップ要求が発行されます。

### <a name="load-based-cluster-scale-down"></a>負荷ベースのクラスターのスケールダウン

次の条件が検出されると、自動スケーリングによりスケールダウン要求が発行されます。

* 保留中の CPU の合計が空き CPU の合計を下回って 10 分以上が経過した。
* 保留中のメモリの合計が空きメモリの合計を下回って 10 分以上が経過した。

ノードごとの AM コンテナーの数および現在の CPU とメモリの要件に基づき、自動スケーリングにより、特定の数のノードを削除する要求が発行されます。 また、サービスでは、現在のジョブの実行に基づいて、削除候補のノードも検出されます。 スケールダウン操作では、最初にノードの使用が停止された後、クラスターから削除されます。

## <a name="get-started"></a>作業開始

### <a name="create-a-cluster-with-load-based-autoscaling"></a>負荷ベースの自動スケーリングでクラスターを作成する

負荷ベースのスケーリングで自動スケーリング機能を有効にするには、通常のクラスターの作成プロセスの一部として、次の手順を行います。

1. **[簡易作成]** ではなく、 **[カスタム (サイズ、設定、アプリ)]** を選択します。
1. **カスタム**の手順 5 (**クラスター サイズ**) で、 **[ワーカー ノードの自動スケーリング]** チェック ボックスをオンにします。
1. **[Autoscale type]\(自動スケーリングの種類\)** で、 **[Load-based]\(負荷ベース\)** オプションを選択ます。
1. 次のプロパティに希望する値を入力します。  

    * **ワーカー ノードの初期の数**  
    * ワーカー ノードの**最小**数  
    * ワーカー ノードの**最大**数  

    ![ワーカー ノードの負荷ベースの自動スケーリングを有効にする](./media/hdinsight-autoscale-clusters/hdinsight-using-autoscale.png)

ワーカー ノードの初期の数には、最小数から最大数までの数を指定する必要があります。 この値によって、クラスターが作成されるときのその初期サイズが定義されます。 ワーカー ノードの最小数は 1 以上である必要があります。

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>スケジュール ベースの自動スケーリングでクラスターを作成する

スケジュール ベースのスケーリングで自動スケーリング機能を有効にするには、通常のクラスターの作成プロセスの一部として、次の手順を行います。

1. **[簡易作成]** ではなく、 **[カスタム (サイズ、設定、アプリ)]** を選択します。
1. **カスタム**の手順 5 (**クラスター サイズ**) で、 **[ワーカー ノードの自動スケーリング]** チェック ボックスをオンにします。
1. クラスターのスケールアップの制限を制御する **[Number of Worker nodes]\(ワーカー ノードの数\)** を入力します。
1. **[Autoscale type]\(自動スケーリングの種類\)** で、 **[Schedule-based]\(スケジュール ベース\)** オプションを選択ます。
1. **[構成]** をクリックして **[Autoscale configuration]\(自動スケーリングの構成\)** ウィンドウを開きます。
1. タイムゾーンを選択し、 **[+ Add condition]\(+ 条件の追加\)** をクリックします
1. 新しい条件を適用する曜日を選択します。
1. 条件を有効にする時刻と、クラスターのスケーリング後のノード数を編集します。
1. 必要に応じて、さらに条件を追加します。

    ![ワーカー ノードのスケジュールベースの作成を有効にする](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

ノードの数は、1 から条件追加前に入力したワーカー ノードの数の間である必要があります。

### <a name="final-creation-steps"></a>最後の作成手順

負荷ベースとスケジュール ベースの両方のスケーリングで、 **[ワーカー ノードのサイズ]** と **[ヘッド ノードのサイズ]** をクリックして、ワーカー ノードの VM の種類を選択します。 ノードの種類ごとに VM の種類を選択すると、クラスター全体の概算のコスト範囲を表示できるようになります。 予算に合わせて VM の種類を調整します。

![ワーカー ノードのノード サイズに対するスケジュールベースの自動スケーリングを有効にする](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-node-size-selection.png)

サブスクリプションには、リージョンごとに容量のクォータがあります。 ヘッド ノードのコアの総数とワーカー ノードの最大数の合計が容量のクォータを超えることはできません。 ただし、このクォータはソフト制限です。それを簡単に増やすためのサポート チケットをいつでも作成できます。

> [!Note]  
> 合計のコア クォータ制限を超えた場合は、"最大ノードがこのリージョン内の使用可能なコア数を超えました。別のリージョンを選択するか、またはサポートに連絡してクォータを増やしてください" というエラー メッセージが表示されます。

Azure portal を使用した HDInsight クラスターの作成に関する詳細については、「[Azure Portal を使用した HDInsight の Linux ベースのクラスターの作成](hdinsight-hadoop-create-linux-clusters-portal.md)」を参照してください。  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Resource Manager テンプレートでクラスターを作成する

#### <a name="load-based-autoscaling"></a>負荷ベースの自動スケーリング

以下の JSON スニペットに示すように、プロパティ `minInstanceCount` と `maxInstanceCount` で `computeProfile` > `workernode` セクションに `autoscale` ノードを追加することで、Azure Resource Manager テンプレートで負荷ベースの自動スケーリングを使用する HDInsight クラスターを作成できます。

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

#### <a name="schedule-based-autoscaling"></a>スケジュール ベースの自動スケーリング

`computeProfile` > `workernode` セクションに `autoscale` ノードを追加することで、Azure Resource Manager テンプレートでスケジュール ベースの自動スケーリングを使用する HDInsight クラスターを作成できます。 `autoscale` ノードには、`timezone` および変更を実行するタイミングが記述されている `schedule` を含む `recurrence` が含まれます。

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        },
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4,
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>実行中のクラスターの自動スケーリングの有効化および無効化

#### <a name="using-the-azure-portal"></a>Azure ポータルの使用
実行中のクラスターで自動スケーリングを有効にするには、 **[設定]** の **[クラスター サイズ]** を選択します。 **[自動スケーリングの有効化]** をクリックします。 使用する自動スケーリングの種類を選択し、負荷ベースまたはスケジュール ベースのスケーリングのオプションを入力します。 最後に、 **[保存]** をクリックします。

![ワーカー ノードの実行中のクラスターに対するスケジュールベースの自動スケーリングを有効にする](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

#### <a name="using-the-rest-api"></a>REST API の使用
REST API を使用して、実行中のクラスターでの自動スケーリングを有効または無効にするには、下のコード スニペットに示すように、自動スケーリング エンドポイントへの POST 要求を作成します。

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

要求ペイロードでは適切なパラメーターを使用します。 自動スケーリングを有効にするには、下の json ペイロードを使用できます。 自動スケーリングを無効にするには、ペイロード `{autoscale: null}` を使用します。

```json
{ autoscale: { capacity: { minInstanceCount: 1, maxInstanceCount: 2 } } }
```

すべてのペイロード パラメーターの完全な説明については、前のセクションの[負荷ベースの自動スケーリングの有効化](#load-based-autoscaling)を参照してください。

## <a name="best-practices"></a>ベスト プラクティス

### <a name="choosing-load-based-or-schedule-based-scaling"></a>負荷ベースまたはスケジュール ベースのスケーリングを選択する

どちらのモードを選択するかを決定する前に、次の要因を検討してください。

* 負荷変動: クラスターの負荷は、特定の日の特定の時刻に一貫性のあるパターンに従っていますか。 そうでない場合、負荷ベースのスケジューリングが適切な選択肢になります。
* SLA 要件:自動スケーリングは、予測型ではなく反応型です。 負荷が増加し始めるタイミングと、クラスターがそのターゲット サイズに達している必要があるタイミングとの間に十分な遅延がありますか。 厳密な SLA 要件があり、負荷が、固定した既知のパターンである場合は、「スケジュール ベース」が適切な選択肢になります。

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>スケールアップ操作またはスケールダウン操作の待ち時間について検討する

スケーリング操作が完了するには 10 分から 20 分かかる可能性があります。 カスタマイズしたスケジュールを設定する場合は、この遅延を考慮してください。 たとえば、午前 9時にクラスター サイズを 20 にする必要がある場合は、スケーリング操作が午前 9時までに完了するように、スケジュールのトリガーを午前 8時 30分などの早い時刻に設定する必要があります。

### <a name="preparation-for-scaling-down"></a>スケール ダウンの準備

クラスターのスケール ダウン プロセス中、自動スケーリングはターゲット サイズを満たすためにノードを使用停止にします。 これらのノードに実行中のタスクがある場合、自動スケーリングはそれらのタスクが完了するまで待機します。 各ワーカー ノードは HDFS でのロールも果たすため、一時データは残りのノードに移動されます。 したがって、残りのノードに、すべての一時データをホストするのに十分なスペースがあることを確認する必要があります。 

実行中のジョブは引き続き実行されて終了します。 保留中のジョブは、少ない数の使用可能ワーカー ノードで通常どおりにスケジュールされるのを待ちます。

## <a name="monitoring"></a>監視

### <a name="cluster-status"></a>クラスターの状態

Azure portal に表示されるクラスターの状態は、自動スケーリング アクティビティの監視に役立ちます。

![ワーカー ノードのクラスター状態に対する負荷ベースの自動スケーリングを有効にする](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

表示される可能性があるすべてのクラスター状態メッセージを、以下の一覧で説明します。

| クラスターの状態 | 説明 |
|---|---|
| 実行中 | クラスターは正常に動作しています。 以前のすべての自動スケーリング アクティビティは、正常に完了しました。 |
| 更新中  | クラスターの自動スケーリング構成は更新されています。  |
| HDInsight 構成  | クラスターのスケールアップまたはスケールダウン操作が進行中です。  |
| 更新エラー  | 自動スケーリング構成の更新中に HDInsight で問題が発生しました。 お客様は、更新の再試行または自動スケーリングの無効化を選択できます。  |
| Error  | クラスターには何らかの問題があり、使用できません。 このクラスターを削除し、新しいクラスターを作成してください。  |

クラスターの現在のノード数を見るには、クラスターの **[概要]** ページの **[クラスター サイズ]** グラフに移動するか、または **[設定]** の **[クラスター サイズ]** をクリックします。

### <a name="operation-history"></a>操作の履歴

クラスター メトリックの一部として、クラスターのスケールアップおよびスケールダウンの履歴を表示できます。 また、過去の 1 日、1 週間、またはその他の期間中のすべてのスケーリング アクションを一覧表示することもできます。

**[監視]** で **[メトリック]** を選択します。 その後、 **[メトリック]** ドロップダウン ボックスから **[メトリックの追加]** と **[Number of Active Workers]\(アクティブなワーカーの数\)** をクリックします。 右上にあるボタンをクリックして、時間範囲を変更します。

![ワーカー ノードのスケジュールベースの自動スケーリング メトリックを有効にする](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)


## <a name="next-steps"></a>次の手順

* クラスターを手動でスケール調整するためのベスト プラクティスについては、[スケール調整のベスト プラクティス](hdinsight-scaling-best-practices.md)に関するページを参照してください。
