---
title: (非推奨) Azure DC/OS クラスターを監視する - ELK スタック
description: ELK (Elasticsearch、Logstash、Kibana) を使って Azure Container Service クラスター内の DC/OS クラスターを監視します。
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 342cf23db2df7d7c79a2b56df96d1a78d6ba215e
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998132"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>(非推奨) ELK を使って Azure Container Service クラスターを監視します

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

この記事では、Azure Container Service の DC/OS クラスター上に ELK (Elasticsearch、Logstash、Kibana) スタックをデプロイする方法について説明します。 

## <a name="prerequisites"></a>前提条件
Azure Container Service によって構成された DC/OS クラスターを[デプロイ](container-service-deployment.md)して[接続](../container-service-connect.md)してください。 DC/OS ダッシュボードと Marathon サービスについては[こちら](container-service-mesos-marathon-ui.md)を確認してください。 また、[Marathon ロード バランサー](container-service-load-balancing.md)をインストールしてください。


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch、Logstash、Kibana)
ELK スタックは、クラスター内のログの監視および分析に使用できるエンド ツー エンドのスタックを提供する、Elasticsearch、Logstash、Kibana の組み合わせです。

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>DC/OS クラスターで ELK スタックを構成する
[http://localhost:80/](http://localhost:80/) を介して DC/OS UI にアクセスしたら、**Universe** に移動します。 DC/OS Universe から Elasticsearch、Logstash、Kibana を検索し、この順序でインストールします。 **[Advanced Installation (高度なインストール)]** のリンクに移動すると、構成の詳細を確認できます。

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

ELK コンテナーが起動して実行されると、Marathon LB を介して Kibana にアクセスできるようにする必要があります。 次のように、**[サービス]** > **[kibana]** の順に移動し、**[編集]** をクリックします。

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


**JSON モード**に切り替えて、ラベル セクションが表示されるまで下にスクロールします。
以下に示す場所にに、`"HAPROXY_GROUP": "external"` エントリを追加する必要があります。
**[Deploy changes (デプロイの変更)]** をクリックすると、コンテナーが再起動されます。

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Kibana がサービスとして HAPROXY ダッシュボードに登録されていることを確認する場合は、HAPROXY はポート 9090 で実行されるので、エージェント クラスター上のポート 9090 を開く必要があります。
既定では、DC/OS エージェント クラスターでポート 80、8080、443 を開きます。
ポートを開き、パブリック アクセスを可能にする手順については、[こちら](container-service-enable-public-access.md)で説明しています。

HAPROXY ダッシュボードにアクセスするには、`http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats` で Marathon LB 管理インターフェイスを開きます。
この URL に移動すると、次のように HAPROXY ダッシュボードが表示され、Kibana のサービス エントリを確認できます。

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Kibana ダッシュボードはポート 5601 でデプロイされるため、アクセスするには ポート 5601 を開く必要があります。 [こちら](container-service-enable-public-access.md)で説明されている手順に従います。 次に、`http://localhost:5601` で Kibana ダッシュボードを開きます。

## <a name="next-steps"></a>次の手順

* システムおよびアプリケーションのログ転送とセットアップについては、「[Log Management in DC/OS with ELK (ELK での DC/OS のログ管理)](https://docs.mesosphere.com/1.8/administration/logging/elk/)」を参照してください。

* ログをフィルター処理するには、「[Filtering Logs with ELK (ELK でのログのフィルター処理)](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/)」を参照してください。 

 

