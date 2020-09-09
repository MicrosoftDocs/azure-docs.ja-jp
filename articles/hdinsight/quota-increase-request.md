---
title: CPU コア クォータの増加の要求 - Azure HDInsight
description: サブスクリプションに割り当てられている CPU コアの増加を要求するプロセスについて説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/07/2020
ms.openlocfilehash: 4de32bad763c0bbf0e143a7ce639efec8c9c41ae
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86079617"
---
# <a name="requesting-quota-increases-for-azure-hdinsight"></a>Azure HDInsight のクォータの増加を要求する

CPU コア クォータは、確実に、特定の Azure リージョン内のすべての顧客にリソース使用量が公平に配分されるようにするのに役立ちます。 ただし、状況によっては、現在のクォータで許可されているよりも多くのクラスター リソースが業務で必要になる場合があります。 このような場合は、データ処理の要件に合わせてクラスターをデプロイできるように、CPU コア クォータの増加を要求できます。

クォータの上限に達すると、新しいクラスターをデプロイしたり、ワーカー ノードを追加して既存のクラスターをスケールアウトしたりすることができません。 唯一のクォータ制限は CPU コア クォータに関するものであり、サブスクリプションごとにリージョン レベルで存在します。 たとえば、ご利用のサブスクリプションは、米国東部リージョンにおいては 30 CPU コアの制限が存在し、米国東部 2 においてはさらに 30 CPU コアを使用できる場合があります。

## <a name="gather-required-information"></a>必要な情報を収集する

クォータ制限に達したことを示すエラーを受け取ったら、このセクションで説明されているプロセスを使用して、重要な情報を収集し、クォータの増加要求を送信します。

1. 目的のクラスター VM のサイズ、スケール、種類を決定します。
1. サブスクリプションの現在のクォータの容量制限を確認します。 使用可能なコアを確認するには、次の手順を実行します。

    1. [Azure portal](https://portal.azure.com/) にサインインします。
    1. HDInsight クラスターの **[概要]** ページに移動します。
    1. 左側のメニューで、 **[クォータ制限]** を選択します。 このページには、使用中のコア数、使用可能なコア数、およびコアの合計が表示されます。

クォータの増加を要求するには、次の手順を行います。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ページの左下にある **[ヘルプとサポート]** を選択します。

    ![[ヘルプとサポート] ボタン](./media/quota-increase-request/help-support-button.png)

1. **[新しいサポート リクエスト]** を選択します。
1. **[新しいサポート要求]** ページの **[基本]** タブで、次のオプションを選択します。

   - **問題の種類**: **サービスとサブスクリプションの制限 (クォータ)**
   - **サブスクリプション**: 使用するサブスクリプション
   - **クォータの種類**: **HDInsight**

     ![HDInsight コア クォータを増やすためのサポート要求を作成します。](./media/quota-increase-request/hdinsight-quota-support-request.png)

1. **次へ:ソリューション >>** を選択します。
1. **[詳細]** ページで、問題に関する説明を入力し、問題の重大度、希望する連絡方法、およびその他の必須フィールドを選択します。 次に示すテンプレートを使用して、必要な情報を確実に提供てください。 クォータの増加要求は、HDInsight 製品チームではなく、Azure の容量チームによって評価されます。 詳細な情報を提供すれば、要求が承認される可能性が高くなります。

    ```
    I would like to request [SPECIFY DESIRED AMOUNT] on [DESIRED SKU] for [SUBSCRIPTION ID].
    
    My current quota on this subscription is [CURRENT QUOTA AMOUNT].
    
    I would like to use the extra cores for [DETAIL REASON].
    ```

    ![問題の詳細](./media/quota-increase-request/problem-details.png)

1. **次へ:確認と作成 >>** を選択します。
1. **[確認および作成]** タブで、 **[作成]** を選択します。

> [!NOTE]  
> プライベート リージョンで HDInsight コア クォータを増やす必要がある場合は、[ホワイト リストの要求を送信](https://aka.ms/canaryintwhitelist)してください。

[サポートに連絡してクォータの引き上げを要求](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)できます。

固定のクォータ制限がいくつか存在します。 たとえば、1 つの Azure サブスクリプションに許可されるコアの数は最大 10,000 です。 これらの制限の詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)」をご覧ください。

## <a name="next-steps"></a>次のステップ

* [Apache Hadoop、Spark、Kafka などを使用して HDInsight でクラスターをセットアップする](hdinsight-hadoop-provision-linux-clusters.md): HDInsight でクラスターをセットアップして構成する方法について説明します。
* [クラスター パフォーマンスを監視する](hdinsight-key-scenarios-to-monitor.md): クラスターの容量に影響を及ぼす可能性のある HDInsight クラスターを監視するための主なシナリオについて説明します。
