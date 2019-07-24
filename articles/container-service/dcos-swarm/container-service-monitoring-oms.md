---
title: (非推奨) Azure DC/OS クラスターの監視 - Operations Management
description: Log Analytics で Azure Container Service DC/OS クラスターを監視します。
services: container-service
author: keikhara
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 290141136672729060f5156d645c47ac303fa0c3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58110972"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>(非推奨) Log Analytics で Azure Container Service DC/OS クラスターを監視する

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Log Analytics は、オンプレミスのインフラストラクチャやクラウド インフラストラクチャの管理および保護に役立つ、Microsoft のクラウド ベースの IT 管理ソリューションです。 コンテナー ソリューションは Log Analytics の 1 つのソリューションであり、コンテナー インベントリ、パフォーマンス、およびログを 1 つの場所で表示するのに役立ちます。 一元的な場所でログを表示して監査やコンテナーのトラブルシューティングを行い、ホスト上のノイズと消費の多いコンテナーを検索することができます。

![](media/container-service-monitoring-oms/image1.png)

コンテナー ソリューションの詳細については、「[Log Analytics のコンテナー ソリューション](../../azure-monitor/insights/containers.md)」を参照してください。

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>DC/OS Universe からの Log Analytics の設定


この記事では、DC/OS をセットアップし、クラスターに単純な Web コンテナー アプリケーションをデプロイしていることを前提としています。

### <a name="pre-requisite"></a>前提条件
- [Microsoft Azure サブスクリプション](https://azure.microsoft.com/free/) - 無料でサブスクリプションを取得できます。  
- Log Analytics ワークスペースのセットアップ - 下の「手順 3.」を参照
- [DC/OS CLI](https://docs.mesosphere.com/1.12/cli) がインストールされていること。

1. DC/OS ダッシュボードで、次のように [Universe] をクリックし、"OMS" を検索します。

   >[!NOTE]
   >OMS は、Log Analytics と呼ばれるようになりました。

   ![](media/container-service-monitoring-oms/image2.png)

2. **[インストール]** をクリックします。 バージョン情報と **[パッケージのインストール]** または **[Advanced Installation] (高度なインストール)** ボタンを含むポップアップが表示されます。 **[Advanced Installation (高度なインストール)]** をクリックすると、**[OMS specific configuration properties (OMS に固有の構成のプロパティ)]** ページが表示されます。

   ![](media/container-service-monitoring-oms/image3.png)

   ![](media/container-service-monitoring-oms/image4.png)

3. ここでは、`wsid` (Log Analytics ワークスペース ID) と `wskey` (ワークスペース ID の主キー) を入力するよう求められます。 `wsid` と `wskey` の両方を取得するには、<https://mms.microsoft.com> でアカウントを作成する必要があります。
   手順に従ってアカウントを作成してください。 アカウントの作成が完了したら、次のように **[設定]**、**[接続されたソース]**、**[Linux サーバー]** の順にクリックして `wsid` と `wskey` を取得する必要があります。

   ![](media/container-service-monitoring-oms/image5.png)

4. 必要なインスタンスの数を選択し、[Review and Install] (確認およびインストール) ボタンをクリックします。 通常、インスタンスの数は、エージェント クラスター内の VM の数と同じにします。 Linux 用 Log Analytics エージェントは、監視のための情報やログ情報を収集する各 VM に個別のコンテナーとしてインストールされます。

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

## <a name="setting-up-a-simple-log-analytics-dashboard"></a>シンプルな Log Analytics ダッシュ ボードの設定

Linux 用 Log Analytics エージェントを VM にインストールしたら、次に Log Analytics ダッシュボードを設定します。 Azure portal でダッシュ ボードを設定することができます。

### <a name="azure-portal"></a>Azure ポータル 

<https://portal.microsoft.com/> で Azure portal にサインインします。 **[Marketplace]** で **[監視 + 管理]** を選択し、**[See All (すべて表示)]** をクリックします。 検索ボックスに「`containers`」と入力します。 検索結果に "コンテナー" が表示されます。 **[コンテナー]** を選択し、**[作成]** をクリックします。

![](media/container-service-monitoring-oms/image9.png)

**[作成]** をクリックすると、ワークスペースを指定するよう求められます。 ワークスペースを選択するか、ワークスペースがない場合は新しいワークスペースを作成します。

![](media/container-service-monitoring-oms/image10.PNG)

ワークスペースを選択したら、**[作成]** をクリックします。

![](media/container-service-monitoring-oms/image11.png)

Log Analytics コンテナー ソリューションの詳細については、[Log Analytics のコンテナー ソリューション](../../azure-monitor/insights/containers.md)に関するページを参照してください。

### <a name="how-to-scale-log-analytics-agent-with-acs-dcos"></a>ACS DC/OS を使用して Log Analytics エージェントをスケーリングする方法 

実際のノード数に満たない Log Analytics エージェントをインストールしなければならない場合や VM を追加して仮想マシン スケール セットをスケールアップする場合は、`msoms` サービスをスケーリングすることで対応できます。

Marathon または DC/OS UI サービスのタブに移動し、ノード数をスケールアップできます。

![](media/container-service-monitoring-oms/image12.PNG)

この操作を実行すると、Log Analytics エージェントがまだデプロイされていない他のノードにデプロイできます。

## <a name="uninstall-ms-oms"></a>MS OMS のアンインストール

MS OMS をアンインストールするには、次のコマンドを入力します。

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>ご意見をお聞かせください。
どの部分が実用的でしたか。 足りない情報はありましたか。 この記事を実用的なものにするうえで他に必要なものはありますか。 <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a> までご意見をお寄せください。

## <a name="next-steps"></a>次の手順

 これで、コンテナーを監視するように Log Analytics が設定されたので、[コンテナー ダッシュボードを参照してください](../../azure-monitor/insights/containers.md)。
