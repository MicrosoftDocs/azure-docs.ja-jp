---
title: Cloud Foundry 監視向けの Azure Log Analytics Nozzle のデプロイ
description: Cloud Foundry loggregator Nozzle for Azure Log Analytics をデプロイする手順について説明します。 Nozzle を使用して、Cloud Foundry システムの正常性とパフォーマンス メトリックを監視します。
services: virtual-machines-linux
author: ningk
manager: jeconnoc
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.openlocfilehash: d71f1d6af0944a676e35dfe6347fafb8706f21b8
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286641"
---
# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Cloud Foundry システム監視向けの Azure Log Analytics Nozzle のデプロイ

[Azure Monitor](https://azure.microsoft.com/services/log-analytics/) は Azure のサービスです。 このサービスは、クラウドおよびオンプレミス環境から生成されたデータを収集して分析するときに役立ちます。

Log Analytics Nozzle (Nozzle) は Cloud Foundry (CF) コンポーネントであり、[Cloud Foundry loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) firehose から Azure Monitor ログにメトリックを転送します。 Nozzle を使用すると、CF のシステム正常性とパフォーマンスのメトリックを複数のデプロイで収集、表示、分析できます。

このドキュメントでは、Nozzle を CF 環境内にデプロイし、Azure Monitor ログ コンソールからデータにアクセスする方法を学習します。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>前提条件

Nozzle をデプロイする前提条件として、次の手順を実行します。

### <a name="1-deploy-a-cf-or-pivotal-cloud-foundry-environment-in-azure"></a>1.Azure に CF または Pivotal Cloud Foundry 環境をデプロイする

オープン ソースの CF デプロイまたは Pivotal Cloud Foundry (PCF) デプロイで、Nozzle を使用できます。

* [Azure に Cloud Foundry をデプロイする](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Azure に Pivotal Cloud Foundry をデプロイする](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2.Nozzle をデプロイするための CF コマンドライン ツールをインストールする

Nozzle は、CF 環境でアプリケーションとして実行されます。 アプリケーションをデプロイするには、CF CLI が必要です。

また、Nozzle には、loggregator firehose と Cloud Controller に対するアクセス許可も必要です。 ユーザーを作成および構成するには、User Account and Authentication (UAA) サービスが必要です。

* [Cloud Foundry CLI のインストール](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Cloud Foundry UAA コマンドライン クライアントのインストール](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

UAA コマンドライン クライアントをセットアップする前に、RubyGems がインストールされていることを確認します。

### <a name="3-create-a-log-analytics-workspace-in-azure"></a>3.Azure で Log Analytics ワークスペースを作成する

Log Analytics ワークスペースは、手動で、またはテンプレートを使用して作成できます。 テンプレートにより、Azure Monitor ログ コンソール用に事前構成された KPI ビューとアラートのセットアップがデプロイされます。 

#### <a name="to-create-the-workspace-manually"></a>ワークスペースを手動で作成するには:

1. Azure portal で Azure Marketplace でサービスの一覧を検索し、[Log Analytics] ワークスペースを選択します。
2. **[作成]** を選択し、次の項目について選択します。

   * **Log Analytics ワークスペース**:ワークスペースの名前を入力します。
   * **サブスクリプション**:複数のサブスクリプションがある場合、CF デプロイと同じものを選択します。
   * **[リソース グループ]** :新しいリソース グループを作成するか、CF デプロイと同じリソース グループを使用します。
   * **[場所]** :場所を入力します。
   * **価格レベル**:完了するには **[OK]** を選択します。

詳細については、[Azure Monitor ログの使用](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)に関するページを参照してください

#### <a name="to-create-the-log-analytics-workspace-through-the-monitoring-template-from-azure-market-place"></a>Azure Marketplace の監視テンプレートを使用して Log Analytics ワークスペースを作成するには:

1. Azure portal を開きます。
1. [+] 記号をクリックするか、左上隅の [リソースの作成] をクリックします。
1. 検索ウィンドウに「Cloud Foundry」と入力し、[Cloud Foundry 監視ソリューション] を選択します。
1. Cloud Foundry 監視ソリューション テンプレートのフロントページが読み込まれたら、[作成] をクリックしてテンプレート ブレードを起動します。
1. 必要なパラメーターを入力します。
    * **サブスクリプション**:Log Analytics ワークスペースの Azure サブスクリプションを選択します。通常は Cloud Foundry デプロイと同じです。
    * **[リソース グループ]** :既存のリソース グループを選択するか、Log Analytics ワークスペース用の新しいグループを作成します。
    * **リソース グループの場所**:リソース グループの場所を選択します。
    * **OMS_Workspace_Name**:ワークスペース名を入力します。ワークスペースが存在しない場合は、テンプレートによって新しいワークスペースが作成されます。
    * **OMS_Workspace_Region**:ワークスペースの場所を選択します。
    * **OMS_Workspace_Pricing_Tier**:Log Analytics ワークスペース SKU を選択します。 [料金ガイダンス](https://azure.microsoft.com/pricing/details/log-analytics/)を参考にしてください。
    * **法律条項**:[法律条項] をクリックし、[作成] をクリックして法律条項に同意します。
1. すべてのパラメーターを指定したら、[作成] をクリックしてテンプレートをデプロイします。 デプロイが完了すると、通知タブに状態が表示されます。


## <a name="deploy-the-nozzle"></a>Nozzle のデプロイ

Nozzle は、PCF タイルとして、または CF アプリケーションとしてデプロイする方法があります。

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>PCF Operations Manager タイルとして Nozzle をデプロイする

[PCF 用 Azure Log Analytics Nozzle のインストールと構成](https://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html)の手順に従います。これは簡素化された方法であり、PCF Operations Manager タイルによって Nozzle が自動的に構成され、プッシュされます。 

### <a name="deploy-the-nozzle-manually-as-a-cf-application"></a>CF アプリケーションとして Nozzle を手動でデプロイする

PCF Operations Manager を使用していない場合は、アプリケーションとして Nozzle をデプロイします。 以下のセクションでは、このプロセスについて説明します。

#### <a name="sign-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>CF CLI を利用して管理者として CF デプロイにサインインする

次のコマンドを実行します。
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

"SYSTEM_DOMAIN" は CF ドメイン名です。 CF デプロイ マニフェスト ファイルで "SYSTEM_DOMAIN" を検索すると確認できます。 

"CF_User" は CF 管理者の名前です。 CF デプロイ マニフェスト ファイルで "scim" セクションを検索し、名前と "cf_admin_password" を検索すると、名前とパスワードを確認できる場合があります。

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>CF ユーザーを作成して必要な権限を付与する

次のコマンドを実行します。
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

"SYSTEM_DOMAIN" は CF ドメイン名です。 CF デプロイ マニフェスト ファイルで "SYSTEM_DOMAIN" を検索すると確認できます。

#### <a name="download-the-latest-log-analytics-nozzle-release"></a>最新の Log Analytics Nozzle リリースをダウンロードする

次のコマンドを実行します。
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables"></a>環境変数の設定

現在のディレクトリにある manifest.yml ファイルに環境変数を設定することができます。 Nozzle のアプリケーション マニフェストの例を次に示します。 値は、実際の Log Analytics ワークスペース情報に置き換えてください。

```
OMS_WORKSPACE             : Log Analytics workspace ID: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_KEY                   : OMS key: Open your Log Analytics workspace in the Azure portal, select **Advanced settings**, select **Connected Sources**, and select **Windows Servers**.
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to Azure Monitor logs. The default is 10 seconds.
OMS_BATCH_TIME            : Interval for posting a batch to Azure Monitor logs. The default is 10 seconds.
OMS_MAX_MSG_NUM_PER_BATCH : The maximum number of messages in a batch to Azure Monitor logs. The default is 1000.
API_ADDR                  : The API URL of the CF environment. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
DOPPLER_ADDR              : Loggregator's traffic controller URL. For more information, see the preceding section, "Sign in to your CF deployment as an admin through CF CLI."
FIREHOSE_USER             : CF user you created in the preceding section, "Create a CF user and grant required privileges." This user has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma-separated list. Valid event types are METRIC, LOG, and HTTP.
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the traffic controller.
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments.
IDLE_TIMEOUT              : The Keep Alive duration for the firehose consumer. The default is 60 seconds.
LOG_LEVEL                 : The logging level of the Nozzle. Valid levels are DEBUG, INFO, and ERROR.
LOG_EVENT_COUNT           : If true, the total count of events that the Nozzle has received and sent are logged to Azure Monitor logs as CounterEvents.
LOG_EVENT_COUNT_INTERVAL  : The time interval of the logging event count to Azure Monitor logs. The default is 60 seconds.
```

### <a name="push-the-application-from-your-development-computer"></a>開発コンピューターからアプリケーションをプッシュする

現在のフォルダーが oms-log-analytics-firehose-nozzle であることを確認します。 次のコマンドを実行します。
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Nozzle インストールの検証

### <a name="from-apps-manager-for-pcf"></a>Apps Manager から (PCF 向け)

1. Operations Manager にサインインし、インストールのダッシュボードにタイルが表示されていることを確認します。
2. Apps Manager にサインインします。Nozzle 用に作成した容量が使用状況レポートに表示されていて、状態が正常であることを確認します。

### <a name="from-your-development-computer"></a>開発コンピューターから

CF CLI ウィンドウで、次のように入力します。
```
cf apps
```
OMS Nozzle アプリケーションが実行中であることを確認します。

## <a name="view-the-data-in-the-azure-portal"></a>Azure portal でデータを表示する

Marketplace テンプレートを使用して監視ソリューションをデプロイした場合は、Azure portal に移動して、そのソリューションを見つけます。 ソリューションは、テンプレートで指定したリソース グループにあります。 ソリューションをクリックし、"ログ分析コンソール" を参照すると、事前に構成されたビューが一覧表示され、Cloud Foundry システムの上位 KPI、アプリケーション データ、アラート、VM の正常性メトリックが表示されます。 

Log Analytics ワークスペースを手動で作成した場合は、次の手順に従ってビューとアラートを作成します。

### <a name="1-import-the-oms-view"></a>1.OMS ビューをインポートする

OMS ポータルから **[ビュー デザイナー]**  >  **[インポート]**  >  **[参照]** を参照し、omsview ファイルのいずれかを選択します。 たとえば、*Cloud Foundry.omsview* を選択してビューを保存します。 **[概要]** ページにタイルが表示されます。 選択すると、視覚的なメトリックが表示されます。

**[ビュー デザイナー]** で、これらのビューをカスタマイズするか、新しいビューを作成できます。

*"Cloud Foundry.omsview"* は、Cloud Foundry OMS ビュー テンプレートのプレビュー バージョンです。 これは完全に構成された既定のテンプレートです。 テンプレートについて提案やフィードバックがある場合は、[問題のセクション](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues)に送信してください。

### <a name="2-create-alert-rules"></a>2.アラート ルールの作成

[アラートを作成](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts)して、必要に応じてクエリとしきい値をカスタマイズできます。 次のアラートが推奨されます。

| Search query (検索クエリ)                                                                  | 基づくアラートの生成 | 説明                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | 結果の数: 1 より小さい   | **bbs.Domain.cf-apps** は、cf-apps ドメインが最新の状態かどうかを示します。 つまり、Cloud Controller からの CF App 要求が、実行に備えて bbs.LRPsDesired (Diego-desired AIs) に同期されていることを意味します。 データが受信されない場合、指定された時間枠内で cf-apps ドメインが最新ではないことを示します。 |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | 結果の数: 0 より大きい   | Diego セルの場合、0 は正常、1 は問題があることを意味します。 指定された時間枠内で問題がある Diego セルが複数検出される場合にアラートを設定します。 |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | 結果の数: 0 より大きい | 1 はシステムが正常であることを意味し、0 はシステムが正常でないことを意味します。 |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | 結果の数: 0 より大きい   | Consul により、正常性の状態が定期的に出力されます。 0 はシステムが正常な状態を意味し、1 は Consul がダウンしていることをルート エミッターが検出したことを意味します。 |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" or Name_s="doppler.shedEnvelopes") Delta_d>0 | 結果の数: 0 より大きい | バック プレッシャーが原因で、メッセージの差分番号が Doppler により意図的に削除されました。 |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | 結果の数: 0 より大きい   | Loggregator は、ログのプロセスに関する問題を示す **LGR** を出力します。 このような問題の例として、ログ メッセージの出力が多すぎる場合などがあります。 |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | 結果の数: 0 より大きい   | Nozzle は、Loggregator から低速のコンシューマー アラートを受け取ると、**slowConsumerAlert** ValueMetric を Azure Monitor ログに送信します。 |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | 結果の数: 0 より大きい   | 失われたイベントの差分番号がしきい値に達した場合、Nozzle に問題が生じている可能性があることを意味します。 |

## <a name="scale"></a>スケール

Nozzle と loggregator は拡大縮小することができます。

### <a name="scale-the-nozzle"></a>Nozzle のスケーリング

2 つ以上の Nozzle インスタンスから始めることをお勧めします。 firehose は、Nozzle のすべてのインスタンス間でワークロードを分散します。
Nozzle が firehose のデータ トラフィックを最新の状態に保つことができるように、**slowConsumerAlert** アラート (前のセクションの「アラート ルールの作成」を参照してください) を設定します。 アラートが発生したら、[低速な Nozzle に関するガイダンス](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz)のセクションに従って、スケーリングが必要かどうかを判断します。
Nozzle をスケールアップするには、Apps Manager または CF CLI を使用してインスタンス数を増やすか、Nozzle のメモリまたはディスク リソースを増やします。

### <a name="scale-the-loggregator"></a>loggregator のスケーリング

loggregator は、ログのプロセスに関する問題を示す **LGR** ログ メッセージを送信します。 アラートを監視して、Loggregator のスケールアップが必要かどうかを決定できます。
loggregator をスケール アップするには、CF マニフェストで Doppler のバッファー サイズを増やすか、Doppler サーバー インスタンスを追加します。 詳細については、[loggregator のスケーリングに関するガイダンス](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling)を参照してください。

## <a name="update"></a>更新

Nozzle を新しいバージョンに更新するには、Nozzle の新しいリリースをダウンロードし、前述の「Nozzle のデプロイ」セクションの手順に従ってアプリケーションをもう一度プッシュします。

### <a name="remove-the-nozzle-from-ops-manager"></a>Operations Manager から Nozzle を削除する

1. Operations Manager にサインインします。
2. "**PCF 用の Microsoft Azure Log Analytics Nozzle**" タイルを見つけます。
3. ガベージ アイコンを選択して、削除を確定します。

### <a name="remove-the-nozzle-from-your-development-computer"></a>開発コンピューターから Nozzle を削除する

CF CLI ウィンドウで、次のように入力します。
```
cf delete <App Name> -r
```

Nozzle を削除しても、OMS ポータルのデータは自動的に削除されません。 データは、Azure Monitor ログのリテンション設定に基づいて有効期限が切れます。

## <a name="support-and-feedback"></a>サポートとフィードバック

Azure Log Analytics Nozzle はオープン ソース化されています。 質問とフィードバックがある場合は、[GitHub セクション](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues)に送信してください。 Azure のサポート リクエストを開くには、サービス カテゴリとして [Cloud Foundry を実行する仮想マシン] を選択します。 

## <a name="next-step"></a>次のステップ

PCF 2.0 から、VM のパフォーマンス メトリックは System Metrics Forwarder によって Azure Log Analytics Nozzle に転送され、Log Analytics ワークスペースに統合されます。 VM のパフォーマンス メトリックに Log Analytics エージェントは不要になりました。 ただし、Log Analytics エージェントを引き続き使用して、Syslog 情報を収集できます。 Log Analytics エージェントは、Bosh アドオンとして CF VM にインストールされています。 

詳細については、[Cloud Foundry デプロイへの Log Analytics エージェントのデプロイ](https://github.com/Azure/oms-agent-for-linux-boshrelease)に関するページを参照してください。
