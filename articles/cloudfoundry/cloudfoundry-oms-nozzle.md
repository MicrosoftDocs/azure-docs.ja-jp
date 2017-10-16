---
title: "Cloud Foundry 監視向けの Azure Log Analytics Nozzle のデプロイ | Microsoft Docs"
description: "Azure Log Analytics 向けの Cloud Foundry Loggregator Nozzle のデプロイ、Azure Log Analytics および OMS コンソールの構成、これらのツールによる Cloud Foundry システム正常性とパフォーマンス メトリックの監視について手順を示します。"
services: virtual-machines-linux
documentationcenter: 
author: ningk
manager: timlt
editor: 
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: f704a2638a4b6b57c014d502dd87303a55334672
ms.contentlocale: ja-jp
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Cloud Foundry システム監視向けの Azure Log Analytics Nozzle のデプロイ

## <a name="background"></a>バックグラウンド

[Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/) は、Microsoft [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/) (OMS) のサービスです。 このサービスは、クラウドおよびオンプレミス環境から生成されたデータを収集して分析するときに役立ちます。

Microsoft Azure Log Analytics Nozzle (Nozzle) は Cloud Foundry コンポーネントであり、[Cloud Foundry Loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) Firehose から Azure Log Analytics にメトリックを転送します。 Nozzle を使用すると、Cloud Foundry のシステム正常性とパフォーマンスのメトリックを複数のデプロイで収集、表示、分析できます。

このドキュメントでは、Nozzle を Cloud Foundry 環境内にデプロイし、Azure Log Analytics OMS コンソールからデータにアクセスする方法を学習します。

## <a name="prerequisites"></a>前提条件

### <a name="1-deploy-a-cf-or-pcf-environment-in-azure"></a>1.Azure で CF または PCF 環境をデプロイする

オープン ソースの Cloud Foundry (CF) デプロイまたは Pivotal Cloud Foundry (PCF) デプロイで、Nozzle を使用できます。

* [Azure に Cloud Foundry をデプロイする](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Azure に Pivotal Cloud Foundry をデプロイする](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2.Nozzle をデプロイするための CF コマンドライン ツールをインストールする

CF 環境でアプリケーションとして Nozzle を実行するには、アプリケーションを展開するための CF CLI が必要です。 また、Loggregator Firehose と Cloud Controller へのアクセス許可も必要で、ユーザーを作成および構成するための UAA コマンドライン クライアントが必要です。

* [Cloud Foundry CLI のインストール](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Cloud Foundry UAA コマンドライン クライアントのインストール](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

UAA コマンドライン クライアントをセットアップする前に、Rubygems がインストールされていることを確認してください。

### <a name="3-create-an-oms-workspace-in-azure"></a>3.Azure で OMS ワークスペースを作成する

#### <a name="create-the-oms-workspace-manually"></a>OMS ワークスペースを手動で作成する

OMS ワークスペースを手動で作成し、Nozzle のデプロイが完了した後に、構成済みの OMS ビューとアラートを読み込むことができます。

1. Azure Portal の Marketplace のサービスの一覧で「Log Analytics」を検索し、[Log Analytics]を選択します。
2. [作成] をクリックし、次の項目について選択します。

* OMS ワークスペース: ワークスペースの名前を入力します。
* サブスクリプション: 複数のサブスクリプションがある場合、CF デプロイと同じものを選択します。
* リソース グループ: 新しいリソース グループを作成するか、CF デプロイと同じリソース グループを使用します。
* 場所
* 価格レベル: 完了するには [OK] をクリックします。
> 詳細については、[Log Analytics の概要](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)に関するページをご覧ください。

#### <a name="create-the-oms-workspace-through-the-oms-template"></a>OMS のテンプレートを使って OMS ワークスペースを作成する

[Cloud Foundry 用の Azure OMS Log Analytics ソリューション](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-cloudfoundry-solution)を利用すると、OMS ワークスペースを作成したり、構成済みの OMS ビューとアラートを読み込んだりできます。

## <a name="deploy-the-nozzle"></a>Nozzle のデプロイ

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>PCF Operations Manager タイルとして Nozzle をデプロイする

Operations Manager を使用して PCF をデプロイしている場合、次のステップに従って [PCF 用の Nozzle をインストールして構成します](http://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html)。Nozzle は Operations Manager でタイルとしてインストールされます。

### <a name="deploy-the-nozzle-as-an-application-to-cloud-foundry"></a>Cloud Foundry に Nozzle をアプリケーションとしてデプロイする

PCF Operations Manager を使用していない場合は、アプリケーションとして Nozzle をプッシュする必要があります。

#### <a name="log-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>CF CLI を利用して管理者として CF デプロイにログインする

開発ボックスで次のコマンドを実行します。
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

> "SYSTEM_DOMAIN" は CF ドメイン名です。 CF デプロイ マニフェスト ファイルで "SYSTEM_DOMAIN" を検索すると確認できます。 
> "CF_User" は CF 管理者の名前です。 CF デプロイ マニフェスト ファイルで "scim" セクションを検索し、名前と "cf_admin_password" を検索すると、名前とパスワードを確認できる場合があります。

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>CF ユーザーを作成して必要な権限を付与する

開発ボックスで次のコマンドを実行します。
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

> "SYSTEM_DOMAIN" は CF ドメイン名です。 CF デプロイ マニフェスト ファイルで "SYSTEM_DOMAIN" を検索すると確認できます。

#### <a name="download-the-latest-azure-log-analytics-nozzle-release"></a>最新の Azure Log Analytics Nozzle リリースをダウンロードする

開発ボックスで次のコマンドを実行します。
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables-in-manifestyml-in-your-current-directory"></a>現在のディレクトリにある "manifest.yml" に環境変数を設定する

これは Nozzle 用のアプリケーション マニフェストであり、値を独自の OMS ワークスペース情報に置き換える必要があります。

```
OMS_WORKSPACE             : OMS workspace ID: open OMS portal from your OMS workspace, click "Settings", click "connected sources"
OMS_KEY                   : OMS key: open OMS portal from your OMS workspace, click "Settings", click "connected sources"
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to OMS Log Analytics, default is 10s.
OMS_BATCH_TIME            : Interval for posting a batch to OMS Log Analytics, default is 10s.
OMS_MAX_MSG_NUM_PER_BATCH : The max number of messages in a batch to OMS Log Analytics, default is 1000.
API_ADDR                  : The api URL of the CF environment, refer to "Push the Nozzle as an App to Cloud Foundry" section step 1 on how to retrive your <CF_SYSTEM_DOMAIN>
DOPPLER_ADDR              : Loggregator's traffic controller URL, refer to "Deploy the Nozzle as an App to Cloud Foundry" section step 1 on how to retrive your <CF_SYSTEM_DOMAIN>
FIREHOSE_USER             : CF user you created in "Push the Nozzle as an App to Cloud Foundry" section, who has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma separated list, valid event types are METRIC,LOG,HTTP
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the Trafficcontroller
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments
IDLE_TIMEOUT              : Keep Alive duration for the firehose consumer, default is 60s.
LOG_LEVEL                 : Logging level of the nozzle, valid levels: DEBUG, INFO, ERROR
LOG_EVENT_COUNT           : If true, the total count of events that the nozzle has received and sent will be logged to OMS Log Analytics as CounterEvents
LOG_EVENT_COUNT_INTERVAL  : The time interval of logging event count to OMS Log Analytics, default is 60s.
```

### <a name="push-the-application-from-your-dev-box"></a>開発ボックスからアプリケーションをプッシュする

"oms-log-analytics-firehose-nozzle" フォルダーの下であることを確認し、次を実行します。
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Nozzle インストールの検証

### <a name="from-apps-manager-for-pcf"></a>Apps Manager から (PCF 向け)

1. Operations Manager にログインし、インストールのダッシュボードにタイルが表示されていることを確認します。
2. Apps Manager にログインします。Nozzle 用に作成した容量が使用状況レポートに表示されていて、状態が正常であることを確認します。

### <a name="from-dev-box"></a>開発ボックスから

開発ボックスの CF CLI ウィンドウで、次のように入力します。
```
cf apps
```
OMS Nozzle アプリケーションが実行中であることを確認します。

## <a name="view-the-data-in-oms-portal"></a>OMS ポータルでのデータの表示

### <a name="1-import-oms-view"></a>1.OMS ビューのインポート

OMS ポータルで、**[ビュー デザイナー]** -> **[インポート]** -> **[参照]** の順に移動して、omsview ファイルのいずれかを選択し (*Cloud Foundry.omsview*など)、ビューを保存します。 **タイル**が OMS 概要のメインページに表示されます。 **タイル**をクリックすると、視覚化されたメトリックが示されます。

これらのビューをカスタマイズするか、または **[ビュー デザイナー]** で新しいビューを作成できます。

*"Cloud Foundry.omsview"* は Cloud Foundry OMS ビュー テンプレートのプレビュー バージョンであり、完全な構成の既定のテンプレートは現在作成中です。提案やフィードバックについては[問題セクション](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues)にお寄せください。

### <a name="2-create-alert-rules"></a>2.アラート ルールの作成

[アラートを作成](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts)して、必要に応じてクエリとしきい値をカスタマイズできます。 推奨されるアラートのセットを次に示します。

| Search query (検索クエリ)                                                                  | 基づくアラートの生成 | Description                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | 結果の数: 1 より小さい   | **bbs.Domain.cf-apps** は、cf-apps ドメインが最新の状態かどうかを示します。つまり、Cloud Controller からの CF App 要求が、実行に備えて bbs.LRPsDesired (Diego-desired AIs) に同期されていることを意味します。 データが受信されない場合、指定された時間枠内で cf-apps ドメインが最新ではないことを示します。 |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | 結果の数: 0 より大きい   | Diego セルの場合、0 は正常、1 は問題があることを意味します。 指定された時間枠内で**問題がある Diego セル**が複数検出される場合にアラートを設定します。 |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | 結果の数: 0 より大きい | 1 はシステムが正常であることを意味し、0 はシステムが正常でないことを意味します。 |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | 結果の数: 0 より大きい   | Consul により、正常性の状態が定期的に出力されます。 0 はシステムが正常な状態を意味し、1 は **Consul がダウンしている**ことをルート エミッターが検出したことを意味します。 |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" or Name_s="doppler.shedEnvelopes") Delta_d>0 | 結果の数: 0 より大きい | バック プレッシャーが原因で、メッセージの差分番号が Doppler により意図的に**削除**されました。 |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | 結果の数: 0 より大きい   | ログのプロセスに関する問題 (ログ メッセージの出力が多すぎる場合など) を示す **LGR** が Loggregator により出力されます。 |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | 結果の数: 0 より大きい   | Nozzle は、Loggregator から低速のコンシューマー アラートを受け取ると、**slowConsumerAlert** ValueMetric を OMS に送信します。 |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | 結果の数: 0 より大きい   | **失われたイベント**の差分番号がしきい値に達した場合、Nozzle に問題が生じている可能性があることを意味します。 |

## <a name="scale"></a>スケール

### <a name="scale-the-nozzle"></a>Nozzle のスケーリング

Nozzle のインスタンスを最低でも 2 つ使用して開始することをお勧めします。 Firehoseは、Nozzle のすべてのインスタンス間でワークロードを分散します。
Firehose からのデータ トラフィックを Nozzle が維持できるようにするには、「アラート ルールの作成」セクションで示した **slowConsumerAlert** アラートを設定する必要があります。アラートが表示されたら、[Nozzle の速度低下に関するガイダンス](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz)に従い、スケーリングが必要かどうかを判断します。
ノズルをスケールアップするには、Apps Manager または CF CLI を使用してインスタンス数を増やすか、ノズルのメモリまたはディスク リソースを増やします。

### <a name="scale-the-loggregator"></a>Loggregator のスケーリング

Loggregator は、ログのプロセスに関する問題を示す **LGR** ログ メッセージを送信します。 アラートを監視して、Loggregator のスケールアップが必要かどうかを決定できます。
Loggregator をスケールアップするには、Doppler のバッファー サイズを増やすか、CF マニフェストに Doppler サーバー インスタンスを追加することができます。詳細については、[Loggregator のスケーリングに関するガイダンス](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling)をご覧ください。

## <a name="update"></a>更新

Nozzle を新しいバージョンに更新するには、Nozzle の新しいリリースをダウンロードし、"デプロイ" セクションの手順に従ってアプリケーションをもう一度プッシュします。

Nozzle を削除するには、次の手順を実行します。

### <a name="from-the-ops-manager"></a>Operations Manager から

1. Operations Manager にログイン
2. "PCF 用の Microsoft Azure Log Analytics Nozzle" タイルを見つけます
3. ガベージ アイコンをクリックして、削除操作を確定します。

### <a name="from-the-dev-box"></a>開発ボックスから

CF CLI ウィンドウで、次のように入力します。
```
cf delete <App Name> -r
```

Nozzle を削除する場合、OMS ポータル内のデータは自動的には削除されません。OMS Log Analytics の保持期間の設定に基づいて無効になります。

## <a name="support-and-feedback"></a>サポートとフィードバック

Azure Log Analytics Nozzle はオープン ソースです。ご質問やフィードバックは [GitHub セクション](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues)までお寄せください。 Azure のサポート要求を開くには、サービス カテゴリとして [Cloud Foundry を実行する仮想マシン] を使用します。 

## <a name="next-step"></a>次のステップ

OMS エージェントを使用すると、Nozzle の対象となる Cloud Foundry メトリックだけでなく、VM レベルの運用データ (Syslog、パフォーマンス、アラート、在庫) に関する情報も入手できます。OMS エージェントは、Bosh アドオンとして CF VM にインストールされています。
> 詳しくは、[Cloud Foundry デプロイへの OMS エージェントのデプロイ](https://github.com/Azure/oms-agent-for-linux-boshrelease)に関するページをご覧ください。
