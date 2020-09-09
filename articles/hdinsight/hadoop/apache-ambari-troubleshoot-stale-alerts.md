---
title: Azure HDInsight での Apache Ambari の古いアラート
description: HDInsight での Apache Ambari の古いアラートについて、考えられる原因と解決策を説明し、分析します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f9dfcb930e3fe4f862f9f51ff00270d0eb0c66ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539112"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>シナリオ:Azure HDInsight での Apache Ambari の古いアラート

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Ambari UI では、次のようなアラートが表示される場合があります。

![Apache Ambari の古いアラートの例](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>原因

Ambari エージェントでは、多くのリソースの正常性が継続的に監視されます。 *アラート*は、特定のクラスターのプロパティが事前に定義されたしきい値内にあるかどうかを通知するように構成できます。 各リソース チェックが実行された後、アラートの条件が満たされている場合、Ambari エージェントから Ambari サーバーへ状態が報告され、アラートがトリガーされます。 アラート プロファイルの間隔に従ってアラートが確認されていない場合、サーバーでは *Ambari Server Stale Alerts* アラートがトリガーされます。

定義された間隔で正常性チェックが実行されない原因はいくつかあります。

* ホストの使用率が高い (CPU 使用率が高くなっている) ため、Ambari エージェントがアラートを時間通りに実行するのに十分なシステム リソースを取得できません。

* クラスターでは負荷の高い期間中に多くのジョブまたはサービスを実行しているため、ビジー状態です。

* クラスター内の少数のホストが多数のコンポーネントをホストしているため、多くのアラートの実行を要求されています。 コンポーネントの数が多い場合、アラート ジョブがスケジュールされた間隔で実行されない可能性があります。

## <a name="resolution"></a>解決策

Ambari の古いアラートに関する問題を解決するには、次の方法を試してください。

### <a name="increase-the-alert-interval-time"></a>アラート間隔の時間を長くする

個々のアラート間隔の値を、クラスターの応答時間と負荷に基づいて大きくすることができます。

1. Apache Ambari UI で、 **[アラート]** タブを選択します。
1. 目的のアラート定義名を選択します。
1. 定義から、 **[Edit]\(編集\)** を選択します。
1. **[Check Interval]\(チェック間隔\)** の値を大きくし、 **[保存]** を選択します。

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>Ambari Server Alerts のアラート間隔の時間を長くする

1. Apache Ambari UI で、 **[アラート]** タブを選択します。
1. **[Groups]\(グループ\)** ドロップダウン リストで、 **[AMBARI Default]\(AMBARI の既定値\)** を選択します。
1. **[Ambari Server Alerts]** アラートを選択します。
1. 定義から、 **[Edit]\(編集\)** を選択します。
1. **[Check Interval]\(チェック間隔\)** の値を大きくします。
1. **[Interval Multiplier]\(間隔の乗数\)** の値を大きくし、 **[保存]** を選択します。

### <a name="disable-and-reenable-the-alert"></a>アラートを無効にしてから再度有効にする

古いアラートを破棄するには、それを無効にしてから再度有効にします。

1. Apache Ambari UI で、 **[アラート]** タブを選択します。
1. 目的のアラート定義名を選択します。
1. 定義から、UI の右端にある **[有効]** を選択します。
1. **[確認]** ポップアップ ウィンドウで、 **[無効化の確認]** を選択します。
1. ページに表示されているすべてのアラート "インスタンス" が消去されるまで数秒待ちます。
1. 定義から、UI の右端にある **[無効]** を選択します。
1. **[確認]** ポップアップ ウィンドウで、 **[有効化の確認]** を選択します。

### <a name="increase-the-alert-grace-period"></a>アラートの猶予期間を長くする

Ambari エージェントには、構成されたアラートがスケジュールどおりに実行されなかったことが報告されるまでに、猶予期間があります。 アラートがスケジュールどおりの時刻に実行されなかったが、猶予期間内に実行された場合、古いアラートは生成されません。

既定の `alert_grace_period` 値は 5 秒です。 /etc/ambari-agent/conf/ambari-agent.ini でこの設定を構成できます。 一定の間隔で古いアラートが発生しているホストについては、値を 10 に増やしてみてください。 その後、Ambari エージェントを再起動します。

## <a name="next-steps"></a>次のステップ

ここで問題が説明されていない場合、または解決できない場合は、次のいずれかのチャネルを参照してください。

* [Azure コミュニティ サポート](https://azure.microsoft.com/support/community/)で、Azure の専門家からの回答をご確認ください。

* Twitter で [@AzureSupport](https://twitter.com/azuresupport) と繋がります。 これは、カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウントです。 Azure コミュニティを適切なリソース (回答、サポート、エキスパート) と結び付けます。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信します。 表示するには、ポータル メニューから [ヘルプ] ( **?** ) を選択するか、 **[Help + support]\(ヘルプとサポート\)** ウィンドウを開きます。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」を参照してください。 

  サブスクリプションの管理と課金のサポートは、Microsoft Azure サブスクリプションに含まれています。 テクニカル サポートは、[Azure サポート プラン](https://azure.microsoft.com/support/plans/)を通じて提供されます。
