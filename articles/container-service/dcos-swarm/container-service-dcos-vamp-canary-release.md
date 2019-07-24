---
title: (非推奨) Azure DC/OS クラスターでの Vamp によるカナリア リリース
description: Vamp を使用してサービスをカナリア リリースし、Azure Container Service DC/OS クラスターでスマート トラフィック フィルタリングを適用する方法を説明します。
services: container-service
author: gggina
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: f1b3c08cce2cb33feab899ea082fc6fb40225182
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311219"
---
# <a name="deprecated-canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>(非推奨) Azure Container Service DC/OS クラスターで Vamp を使用してマイクロサービスをカナリア リリースする

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

このチュートリアルでは、DC/OS クラスターにより Azure Container Service に Vamp を設定します。 Vamp デモ サービス "sava" をカナリア リリースし、スマート トラフィック フィルタリングを適用して、Firefox とのサービスの非互換性を解決します。 

> [!TIP] 
> このチュートリアルでは、Vamp を DC/OS クラスターで実行しますが、オーケストレーターとして Vamp と Kubernetes を併用することもできます。
>

## <a name="about-canary-releases-and-vamp"></a>カナリア リリースと Vamp について


[カナリア リリース](https://martinfowler.com/bliki/CanaryRelease.html)は、Netflix、Facebook、Spotify などの革新的な組織で採用されている、スマート デプロイ戦略の 1 つです。 このアプローチでは、問題を減らし、セーフティ ネットを導入し、イノベーションを拡大できます。 こんなに便利な方法を、一部の企業しか使用していないのはなぜでしょうか。 カナリア戦略を含めるように CI/CD パイプラインを拡張することは複雑な作業で、開発運用に関する豊富な知識や経験を必要とします。 小規模な会社や企業が、このアプローチへの着手に尻込みするのも無理はありません。 

[Vamp](https://vamp.io/) は、この切り替えを簡単にし、お好みのコンテナー スケジューラでカナリア リリース機能を実行することを目的としたオープンソース システムです。 Vamp のカナリア機能は、パーセンテージを基にした展開をしのぐ機能です。 たとえば、特定のユーザー、IP 範囲、デバイスを対象とする、広範な条件に基づいてトラフィックをフィルター処理したり、分割したりすることができます。 Vamp はパフォーマンス メトリックを追跡して分析し、現実世界のデータに基づいた自動化を実現します。 エラーに対して自動ロールバックを設定したり、負荷または待機時間に基づいて個々のサービス バリアントをスケールしたりできます。

## <a name="set-up-azure-container-service-with-dcos"></a>Azure Container Service と DC/OS の設定



1. 既定サイズの 1 つのマスターと 2 つのエージェントを含む [DC/OS クラスターをデプロイ](container-service-deployment.md)します。 

2. [SSH トンネルを作成](../container-service-connect.md)し、DC/OS クラスターに接続します。 この記事では、ローカル ポート 80 でクラスターにトンネルすることを前提としています。


## <a name="set-up-vamp"></a>Vamp の設定

DC/OS クラスターが実行されたので、DC/OS UI (http:\//localhost:80) から Vamp をインストールできます。 

![DC/OS UI](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

インストールは、次の 2 段階で行います。

1. **Elasticsearch をデプロイ**します。

2. 次に、**Vamp をデプロイ**します。このためには、Vamp DC/OS ユニバース パッケージをインストールします。

### <a name="deploy-elasticsearch"></a>Elasticsearch のデプロイ

Vamp では、メトリックの収集と集計に Elasticsearch が必要です。 [magneticio Docker イメージ](https://hub.docker.com/r/magneticio/elastic/)を使用して、互換性のある Vamp Elasticsearch スタックをデプロイできます。

1. DC/OS UI で、**[サービス]** に移動し、**[サービスのデプロイ]** をクリックします。

2. **[Deploy New Service]\(新しいサービスのデプロイ\)** ポップアップから **[JSON モード]** を選択します。

   ![JSON モードの選択](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. 次の JSON を貼り付けます。 この構成は、RAM が 1 GB のコンテナーを実行し、Elasticsearch ポートで基本的な正常性チェックを実行します。
  
   ```JSON
   {
    "id": "elasticsearch",
    "instances": 1,
    "cpus": 0.2,
    "mem": 1024.0,
    "container": {
      "docker": {
        "image": "magneticio/elastic:2.2",
        "network": "HOST",
        "forcePullImage": true
      }
    },
    "healthChecks": [
      {
        "protocol": "TCP",
        "gracePeriodSeconds": 30,
        "intervalSeconds": 10,
        "timeoutSeconds": 5,
        "port": 9200,
        "maxConsecutiveFailures": 0
      }
    ]
   }
   ```
  

3. **[デプロイ]** をクリックします。

   DC/OS により、Elasticsearch コンテナーがデプロイされます。 進捗状況は **[サービス]** ページで追跡できます。  

   ![e?Elasticsearch のデプロイ](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Vamp のデプロイ

Elasticsearch が**実行中**として報告されたら、Vamp DC/OS ユニバース パッケージを追加できます。 

1. **[ユニバース]** に移動し、**[vamp]** を検索します。 
   ![DC/OS ユニバースでの Vamp](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. vamp パッケージの横の **[インストール]** をクリックし、**[Advanced Installation]\(高度なインストール\)** を選択します。

3. 下へスクロールし、次の elasticsearch-url を入力します: `http://elasticsearch.marathon.mesos:9200`。 

   ![Elasticsearch URL の入力](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. **[確認後にインストールする]** をクリックし、**[インストール]** をクリックしてデプロイを開始します。  

   DC/OS により、必要なすべての Vamp コンポーネントがデプロイされます。 進捗状況は **[サービス]** ページで追跡できます。
  
   ![Vamp のユニバース パッケージとしてのデプロイ](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. デプロイが完了したら、Vamp UI にアクセスできるようになります。

   ![DC/OS での Vamp サービス](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
   ![Vamp UI](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>最初のサービスのデプロイ

Vamp が稼働状態になったので、ブループリントからサービスをデプロイします。 

最も単純な形式では、[Vamp ブループリント](https://vamp.io/documentation/using-vamp/blueprints/)は、デプロイするエンドポイント (ゲートウェイ)、クラスター、およびサービスを説明します。 Vamp はクラスターを使用して、カナリア リリースまたは A/B テスト用に同じサービスのさまざまなバリアントを論理グループにグループ化します。  

このシナリオでは、[**sava**](https://github.com/magneticio/sava) と呼ばれるサンプルのモノリシック アプリケーション (バージョン 1.0) を使用します。 モノリシックは、Docker コンテナーにパッケージ化されます。このコンテナーは magneticio/sava:1.0.0 の下のDocker Hub にあります。 通常、アプリケーションはポート 8080 で実行されますが、ここではポート 9050 に公開します。 シンプルなブループリントを使用して、Vamp を使ってアプリをデプロイします。

1. **[デプロイ]** に移動します。

2. **[追加]** をクリックします。

3. 次のブループリント YAML を貼り付けます。 このブループリントには、サービス バリアントが 1 つのみの 1 つのクラスターが含まれます。これは、後の手順で変更します。

   ```YAML
   name: sava                        # deployment name
   gateways:
    9050: sava_cluster/webport      # stable endpoint
   clusters:
    sava_cluster:               # cluster to create
     services:
        -
          breed:
            name: sava:1.0.0        # service variant name
            deployable: magneticio/sava:1.0.0
            ports:
              webport: 8080/http # cluster endpoint, used for canary releasing
   ```

4. **[Save]** をクリックします。 Vamp により、デプロイが初期化されます。

デプロイが **[デプロイ]** ページに一覧表示されます。 その状態を監視するデプロイをクリックします。

![Vamp UI - sava のデプロイ](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![Vamp UI での sava サービス](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

2 つのゲートウェイが作成されます。これらは、**[ゲートウェイ]** ページで確認できます。

* 実行中のサービスにアクセスするための安定したエンドポイント (ポート 9050) 
* Vamp によって管理された内部ゲートウェイ (このゲートウェイについて詳しくは後の手順を参照)。 

![Vamp UI - sava ゲートウェイ](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

sava サービスをデプロイしましたが、これには外部からアクセスできません。これは、Azure Load Balancer がこれにトラフィックを転送する方法をまだ理解していないためです。 サービスにアクセスするには、Azure ネットワーク構成を更新します。


## <a name="update-the-azure-network-configuration"></a>Azure ネットワーク構成の更新

ポート 9050 で安定したエンドポイントを公開し、Vamp により、DC/OS エージェント ノードに sava サービスをデプロイしました。 DC/OS クラスターの外部からサービスにアクセスするには、クラスターのデプロイで Azure ネットワーク構成に次の変更を行います。 

1. エージェント (**dcos-agent-lb-xxxx** という名前のリソース) に対して、正常性プローブと、ポート 9050 でトラフィックを sava インスタンスに転送するルールと共に **Azure Load Balancer を構成**します。 

2. パブリック エージェント (**XXXX-agent-public-nsg-XXXX** という名前のリソース) の**ネットワーク セキュリティ グループを更新**して、ポート 9050 でのトラフィックを許可します。

Azure ポータルを使用してこれらのタスクを完了する詳細な手順については、「[Azure Container Service アプリケーションへのパブリック アクセスを有効にする](container-service-enable-public-access.md)」をご覧ください。 すべてのポート設定について、ポート 9050 を指定します。


すべてが作成されたら、DC/OS エージェント ロード バランサーの **[概要]** ブレードに移動します (**dcos-agent-lb-xxxx** という名前のリソース)。 **[パブリック IP アドレス]** で、ポート 9050 で sava にアクセスするためのアドレスを選びます。

![Azure ポータル - パブリック IP アドレスの取得](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>カナリア リリースの実行

運用環境にカナリア リリースするこのアプリケーションの新しいバージョンがあるとします。 これを magneticio/sava:1.1.0 としてコンテナー化し、準備ができているものとします。 Vamp を使用すると、新しいサービスを実行中のデプロイに簡単に追加できます。 これらの "マージされた" サービスは、クラスター内の既存のサービスと共にデプロイされ、0% の重みが割り当てられます。 トラフィック分散を調節するまで、新たにマージされたサービスにトラフィックはルーティングされません。 Vamp UI 内の重みスライダーでは、増分調整 (カナリア リリース) または瞬時のロールバックを許可して、分散を完全に制御することができます。

### <a name="merge-a-new-service-variant"></a>新しいサービス バリアントのマージ

新しい sava 1.1 サービスを実行中のデプロイとマージするには、次の手順を実行します。

1. Vamp UI で、**[Blueprints]\(ブループリント\)** をクリックします。

2. **[追加]** をクリックし、次のブループリント YAML を貼り付けます。このブループリントは、既存のクラスター (sava_cluster) 内にデプロイする新しいサービス バリアント (sava:1.1.0) を説明します。

   ```YAML
   name: sava:1.1.0      # blueprint name
   clusters:
    sava_cluster:       # cluster to update
      services:
        -
          breed:
            name: sava:1.1.0    # service variant name
            deployable: magneticio/sava:1.1.0    
            ports:
              webport: 8080/http # cluster endpoint to update
   ```
  
3. **[Save]** をクリックします。 ブループリントが格納され、**[ブループリント]** ページに一覧表示されます。

4. sava:1.1 ブループリントでアクション メニューを開き、**[Merge to]\(マージ先\)** をクリックします。

   ![Vamp UI - ブループリント](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. **[sava]** デプロイを選択し、**[Merge]\(マージ)\** をクリックします。

   ![Vamp UI - ブループリントをデプロイにマージ](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Vamp により、ブループリントに説明された新しい sava:1.1.0 サービス バリアントが、実行中のデプロイの **sava_cluster** 内のsava:1.0.0 と共にデプロイされます。 

![Vamp UI - 更新された sava デプロイ](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

**sava/sava_cluster/webport** ゲートウェイ (クラスター エンドポイント) も更新され、新しくデプロイされた sava:1.1.0 にルートが追加されます。 この時点では、ここにルーティングされるトラフィックはありません (**重み**は 0% に設定されます)。

![Vamp UI - クラスター ゲートウェイ](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>カナリア リリース

両バージョンの sava を同じクラスターにデプロイし、**重み**スライダー動かして、これらの sava 間のトラフィックの配分を調整します。

1. **[重み]** の横の ![[Vamp UI - edit]\(Vamp UI - 編集\)](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) をクリックします。

2. 重みの配分を 50%/50% に設定し、**[保存]** をクリックします。

   ![Vamp UI - ゲートウェイの重みスライダー](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. ブラウザーに戻り、数回 sava ページを更新します。 sava アプリケーションが、sava:1.0 ページと sava:1.1 ページで切り替わるようになります。

   ![sava1.0 サービスと sava1.1 サービスの切り替え](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > このページ切り替えの最適な方法として、ブラウザーの "Incognito" または "Anonymous" モードを使用することです。これは、静的なアセットのキャッシュによるものです。
  >

### <a name="filter-traffic"></a>トラフィックのフィルター処理

デプロイ後に、Firefox ブラウザーに表示の問題を発生させる非互換性が sava:1.1.0 で見つかったとします。 着信トラフィックをフィルターするように Vamp を設定し、すべての Firefox ユーザーを既知の安定した sava:1.0.0 に送信できます。 このフィルターは、Firefox ユーザーの中断の問題を直ちに解決するため、他のすべてのユーザーは改善された sava:1.1.0 の恩恵を引き続き受けることができます。

Vamp は**条件**を使用して、ゲートウェイのルート間のトラフィックをフィルター処理します。 まず、トラフィックはフィルターされ、各ルートに適用されている条件に従って送信されます。 残りのすべてのトラックは、ゲートウェイの重み設定によって配信されます。

すべての Firefox ユーザーをフィルター処理する条件を作成して、古い sava:1.0.0 にユーザーを送信することができます。

1. sava/sava_cluster/webport **[Gateways]\(ゲートウェイ\)** ページで、![[Vamp UI - edit]\(Vamp UI - 編集\)](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) をクリックして**条件**をルート sava/sava_cluster/sava:1.0.0/webport に追加します。 

2. 条件 **user-agent == Firefox** を入力し、![[Vamp UI - save]\(Vamp UI - 保存\)](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) をクリックします。

   Vamp により、既定の強度 0% で条件が追加されます。 トラフィックのフィルター処理を開始するには、条件の強度を調整する必要があります。

3. ![[Vamp UI - edit]\(Vamp UI - 編集\)](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) をクリックし、条件に適用されている**強度**を変更します。
 
4. **強度**を 100% に設定し、![[Vamp UI - save]\(Vamp UI - save\)](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) をクリックして保存します。

   ここで、Vamp は条件に一致するすべてのトラフィックを (すべての Firefox ユーザー) sava:1.0.0 に送信します。

   ![Vamp UI - ゲートウェイへの条件の適用](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. 最後に、ゲートウェイの重みを調整して、残りのすべてのトラフィック (すべての非 Firefox ユーザー) を新しい sava:1.1.0 に送信します。 **[重み]** の横の ![[Vamp UI - edit]\(Vamp UI - 編集\)](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) をクリックして、100% がルート sava/sava_cluster/sava:1.1.0/webport に送信されるように重みの配分を設定します。

   これで、条件によってフィルターされないすべてのトラフィックが、新しい sava:1.1.0 に送信されます。

6. 動作中のフィルターを表示するには、2 つの個々のブラウザー (Firefox とその他のブラウザー) を開き、両方から sava サービスにアクセスします。 すべての Firefox 要求が sava:1.0.0 に送信され、他のすべてのブラウザーが sava:1.1.0 に送信されます。

   ![Vamp UI - トラフィックのフィルター処理](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>要約

この記事では、DC/OS クラスターでの Vamp について簡単に紹介しました。 最初に、Azure Container Service DC/OS クラスターで Vamp を稼働させ、Vamp ブループリントでサービスをデプロイし、公開されたエンドポイント (ゲートウェイ) でこれにアクセスしました。

また、次のような Vamp の強力な機能の一部を紹介しました。新しいサービス バリアントを実行中のデプロイにマージし、それを段階的に導入した後、トラフィックをフィルター処理して既知の非互換性を解決しました。


## <a name="next-steps"></a>次の手順

* Vamp アクションの管理について詳しくは、「[Vamp REST API](https://vamp.io/documentation/api/api-reference/)」を参照してください。

* Node.js で Vamp 自動スクリプトを構築し、[Vamp ワークフロー](https://vamp.io/documentation/using-vamp/v1.0.0/workflows/#create-a-workflow)として実行します。

* その他の [VAMP チュートリアル](https://vamp.io/documentation/tutorials/)もご覧ください。

