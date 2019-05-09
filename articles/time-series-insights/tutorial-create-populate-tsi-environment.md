---
title: チュートリアル:Azure Time Series Insights 環境の作成 | Microsoft Docs
description: シミュレートされたデバイスからのデータが入力された Time Series Insights 環境を作成する方法について説明します。
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 12/05/2018
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 58b19a039bb6aebcd2687ab80cdd29c5f378645e
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54200209"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>チュートリアル: Azure Time Series Insights 環境を作成する

このチュートリアルでは、シミュレートされたデバイスからのデータが入力された Time Series Insights (TSI) 環境を作成するプロセスについて説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * TSI 環境の作成 
> * IoT Hub が含まれるデバイス シミュレーション ソリューションを作成する
> * IoT Hub を TSI 環境に接続する
> * デバイス シミュレーションを実行して TSI 環境にデータをストリーミングする
> * シミュレートされたテレメトリ データを確認する

## <a name="video"></a>ビデオ

このビデオでは、Azure IoT Solution Accelerator を使用して、Time Series Insights を使用するために使うデータを生成する方法を紹介します。

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。 

また、Azure のサインイン アカウントは、サブスクリプションの所有者ロールのメンバーである必要があります。 詳細については、「[RBAC と Azure portal を使用してアクセスを管理する](/azure/role-based-access-control/role-assignments-portal)」を参照してください。

## <a name="overview"></a>概要

TSI 環境とは、デバイス データが収集、格納される場所のことです。 TSI 環境に格納されたデータは、[TSI エクスプローラー](time-series-quickstart.md)や [TSI Query API](/rest/api/time-series-insights/ga-query-api) を使用してデータのクエリや分析を実行できます。

シミュレートされたデバイスや物理デバイスと同じように、IoT Hub は Azure クラウドに安全に接続してデータを転送するためにデバイスによって使用される、コネクション ポイントです。 [TSI の概要](time-series-insights-overview.md)で説明したように、IoT Hub はデータを TSI 環境にストリーミングするイベント ソースとしても機能します。 

また、このチュートリアルでは [IoT ソリューション アクセラレータ](/azure/iot-accelerators/)を使用し、サンプルのテレメトリ データを生成して IoT Hub にストリーミングします。 IoT ソリューション アクセラレータは、カスタム IoT ソリューションの開発を高速化するための、エンタープライズ レベルのあらかじめ構成されたソリューションを提供します。 

## <a name="create-a-tsi-environment"></a>TSI 環境の作成

まず、次のように Azure サブスクリプションに TSI 環境を作成します。

1. Azure サブスクリプション アカウントを使用して、[Azure Portal](https://portal.azure.com) にサインインします。  
2. 左上隅の **[+ リソースの作成]** を選択します。  
3. **[モノのインターネット (IoT)]** カテゴリを選択し、**[Time Series Insights]** を選択します。  
   
   [![Time Series Insights 環境リソースを選択する](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

4. **[Time Series Insights 環境]** ページで、次の必須パラメーターを入力します。
   
   パラメーター|説明
   ---|---
   **環境名** | TSI 環境の一意の名前を選択します。 名前は TSI エクスプローラーと Query API で使用されます。
   **サブスクリプション** | サブスクリプションとは、Azure リソース用のコンテナーです。 TSI 環境を作成するサブスクリプションを選択します。
   **[リソース グループ]** | リソース グループとは、Azure リソース用のコンテナーです。 TSI 環境リソースに既存のリソース グループを選択するか、新しいリソース グループを作成します。
   **場所** | TSI 環境のデータ センターのリージョンを選択します。 帯域幅のコストや待機時間の増加を防ぐために、TSI 環境を IoT リソースと同じリージョンにすることをお勧めします。
   **[Pricing SKU]\(価格 SKU\)** | 必要なスループットを選択します。 コストと初期容量を最小限に抑えるために、S1 を選択します。
   **[容量]** | 容量は、イングレス レート、ストレージ容量、選択した SKU に関連するコストに適用される乗数です。  環境の容量は、作成後に変更できます。 コストを最小限に抑えるために、容量には 1 を選択します。 

   完了したら、**[作成]** をクリックしてプロビジョニング プロセスを開始します。

   ![Time Series Insights 環境リソースを作成する](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)

5. **[通知]** パネルをチェックしてデプロイの進行状況を監視します。これには 1 分もかかりません。  

   ![Time Series Insights 環境のデプロイ成功](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)

## <a name="create-a-device-simulation"></a>デバイス シミュレーションを作成する

次に、TSI 環境に入力されるテスト データを生成する、デバイス シミュレーション ソリューションを作成します。

1. 別のウィンドウまたはタブで https://www.azureiotsolutions.com に移動し、同じ Azure サブスクリプション アカウントを使用してサインインして、「デバイス シミュレーション」アクセラレータを選択します。

   ![デバイス シミュレーション アクセラレータを実行する](media/tutorial-create-populate-tsi-environment/sa-main.png)

2. **[デバイス シミュレーション ソリューションの作成]** ページで、次の必須パラメーターに入力します。

   パラメーター|説明
   ---|---
   **[ソリューション名]** | 新しいリソース グループの作成に使用される一意の値。 一覧の Azure リソースが作成され、リソース グループに割り当てられます。
   **サブスクリプション** | 前のセクションでの TSI 環境の作成に使用した同じサブスクリプションを指定します。
   **[リージョン]** | 前のセクションでの TSI 環境の作成に使用した同じリージョンを指定します。 
   **[オプションの Azure リソースをデプロイする]** | **[IoT Hub]** はオンのままにします。シミュレートされたデバイスはこれを使用してデータに接続してストリーミングします。

   完了したら、**[ソリューションの作成]** をクリックしてソリューションの Azure リソースをプロビジョニングします。これには約 6 ～ 7 分かかります。

   ![デバイス シミュレーション ソリューションをプロビジョニングする](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)

3. プロビジョニングが完了すると、新しいソリューションの上にあるテキストが「プロビジョニング中...」から「準備完了」に変更されます。

   >[!IMPORTANT]
   > まだ **[起動]** ボタンはクリックしないでください。 ただし、後で戻ってくるためこの Web ページは開いたままにしてください。

   ![デバイス シミュレーション ソリューションのプロビジョニング完了](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)

4. これで Azure Portal に戻り、サブスクリプション内に新しく作成されたリソースを検査します。 ポータルの **[リソース グループ]** ページに、前の手順で指定した **[ソリューション名]** を使用して作成した新しいリソース グループを確認できます。 また、デバイス シミュレーション ソリューションをサポートするように作成されたすべてのリソースを確認できます。

   [![デバイス シミュレーション ソリューションのリソース](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-tsi-environment-to-the-iot-hub"></a>IoT Hub を TSI 環境に接続する 

ここまでで、2 セットのリソースを次のリソース グループ内に作成する方法について学習しました。 
- 空の TSI 環境 
- ソリューション アクセラレータによって生成されたデバイス シミュレーション ソリューションのリソース (IoT Hub を含む)

シミュレートされたデバイスは、IoT Hub に接続してデバイス データをストリーミングする必要があります。 TSI 環境にデータが流れるようにするには、IoT Hub と TSI 環境の両方の構成を変更する必要があります。 

### <a name="iot-hub-configuration-define-a-consumer-group"></a>IoT Hub の構成: コンシューマー グループを定義する

IoT Hub には、機能を他のアクターと共有する各種エンドポイントが用意されています。 「イベント」エンドポイントは、データが IoT Hub のインスタンスにストリーミングされるときに、他のアプリケーションがそのデータをどのように使用するかを指定します。 具体的には、「コンシューマー グループ」はアプリケーションが IoT Hub からデータをリッスンしてプルするメカニズムを提供します。 

次に、デバイス シミュレーション ソリューションの IoT Hub の「イベント」エンドポイント上に、新しい「コンシューマー グループ」プロパティを定義します。 

1. Azure Portal で、デバイス シミュレーション ソリューションに作成したリソース グループの **[概要]** ページに移動し、IoT Hub のリソースを選択します。

   [![デバイス シミュレーション ソリューションのリソース グループ](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   また、後で参照できるように、ソリューションで生成された IoT Hub のリソースの**名前**を書き留めておいてください。

2. 下にスクロールして **[エンドポイント]** ページを選択し、**[イベント]** エンドポイントを選択します。 エンドポイントの **[プロパティ]** ページで、[$Default] コンシューマー グループの下のエンドポイントに一意の名前を入力し、**[保存]** をクリックします。

   [![デバイス シミュレーション ソリューションの IoT Hub のエンドポイント](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="tsi-environment-configuration-define-an-event-source"></a>TSI 環境の構成: イベント ソースを定義する

ここでは新しい IoT Hub 「コンシューマー グループ」のイベント エンドポイントを TSI 環境に「イベント ソース」として接続します。

1. TSI 環境に作成したリソース グループの **[概要]** ページに移動し、その TSI 環境を選択します。

   [![TSI 環境のリソース グループと環境](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

2. TSI 環境のページで、**[イベント ソース]** を選択し、**[+ 追加]** をクリックします。

   ![TSI 環境の概要](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)

3. **[新しいイベント ソース]** ページで、次の必須パラメーターを入力します。 

   パラメーター|説明
   ---|---
   **[イベント ソース名]** | イベント ソースの名前に使用される一意の値が必要です。
   **ソース** | [IoT Hub] を選択します。
   **[インポート オプション]** | 既定の [使用可能なサブスクリプションから Iot Hub を使用してください] を使用します。 このオプションにより、次のドロップダウン リストに使用可能なサブスクリプションが表示されます。
   **サブスクリプション** | TSI 環境とデバイス シミュレーションのリソースを作成した同じサブスクリプションを選択します。
   **[IoT Hub 名]** | 既定で前にメモした IoT Hub の名前が指定されます。 されていない場合は、正しい IoT Hub を選択します。
   **[IoT Hub ポリシー名]** | 既定値の [iothubowner] をそのまま使用します。
   **[Iot Hub コンシューマー グループ]** | 既定で前にメモした IoT Hub コンシューマー グループの名前が指定されます。 されていない場合は、正しいコンシューマー グループ名を選択します。 
   **イベントのシリアル化の形式** | 既定値の [JSON] をそのまま使用します。
   **[タイムスタンプのプロパティ名]** | [timestamp] を指定します。

   完了したら、**[作成]** をクリックしてイベント ソースを追加します。 リソース グループの **[概要]** ページに戻ると、TSI 環境のリソースとともに、新しい「Time Series Insights イベント ソース」リソースが表示されます。

   ![TSI 環境の新しいイベント ソース](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)

## <a name="run-a-device-simulation-to-stream-data-into-the-tsi-environment"></a>デバイス シミュレーションを実行して TSI 環境にデータをストリーミングする

これですべての構成作業が完了しました。次は TSI 環境にシミュレートされたデバイスからのサンプル データを入力します。

「[デバイス シミュレーションを作成する](#create-a-device-simulation)」セクションで、ソリューションをサポートする複数の Azure リソースがアクセラレータによって作成されました。 前に説明した IoT Hub とともに、シミュレートされたデバイス テレメトリを作成して転送する Azure App Service Web アプリケーションが生成されました。

1. [ソリューション アクセラレータのダッシュ ボード](https://www.azureiotsolutions.com/Accelerators#dashboard)に戻ります。 必要に応じて、このチュートリアルで使用している同じ Azure アカウントを使用して再度サインインします。 これで「デバイス シミュレーション」ソリューションの **[起動]** ボタンをクリックできるようになります。

     ![ソリューション アクセラレータのダッシュボード](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)

2. この時点でデバイス シミュレーション Web アプリが起動します。初期読み込みに数秒かかる場合があります。 また、Web アプリケーションに「サインインとプロファイルの読み取り」アクセス許可を付与することに同意するよう求められます。 このアクセス許可により、アプリケーションがアプリケーションの機能をサポートするのに必要なユーザー プロファイル情報を取得することを許可します。

     ![デバイス シミュレーション Web アプリケーションの同意](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)

3. **[Simulation setup]\(シミュレーションのセットアップ\)** ページが読み込まれたら、次の必須パラメーターを入力します。 

   パラメーター|説明
   ---|---
   **[Target IoT Hub]\(IoT Hub をターゲットにする\)** | [Use pre-provisioned IoT Hub]\(事前プロビジョニングされている IoT Hub を使用する\) を選択します。
   **[デバイス モデル]** | [Chiller] を選択します。
   **[Number of devices]\(デバイスの数\)**  | **[数量]** に 1000 と入力します。
   **[Telemetry frequency]\(テレメトリ頻度\)** | 10 秒と入力します。
   **[Simulation duration]\(シミュレーション期間\)** | **[End in:]\(実行時間:\)** を選択し、5 分と入力します。

   完了したら､**[シミュレーションの開始]** をクリックします｡ シミュレーションは合計で 5 分間実行され、10 秒ごとにシミュレートされた 1000 台のデバイスからデータを生成します。  

   ![デバイス シミュレーションのセットアップ](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)

4. シミュレーションの実行中、10 秒ごとに **[Total messages]\(メッセージの総数\)** フィールドと **[Messages per second]\(1 秒ごとのメッセージ数\)** フィールドが更新されます。 シミュレーションは約 5 分後に終了し、**[Simulation setup]\(シミュレーションのセットアップ\)** に戻ります。

   ![実行中のデバイス シミュレーション](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)

## <a name="verify-the-telemetry-data"></a>テレメトリ データを確認する

この最後のセクションでは、生成されたテレメトリ データが TSI 環境に格納されるのを確認します。 データを確認するには、Time Series Insights エクスプローラーを使用します。これは、テレメトリ データのクエリと分析に使用します。

1. TSI 環境のリソース グループの **[概要]** ページに戻り、もう一度その TSI 環境を選択します。

   [![TSI 環境のリソース グループと環境](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

2. TSI 環境の **[概要]** ページで、**[Time Series Insights エクスプローラーの URL]** をクリックして TSI エクスプローラーを開きます。

   [![TSI エクスプローラー](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

3. TSI エクスプローラーはお使いの Azure Portal アカウントを使用して読み込みと認証を行います。 最初のビューのグラフ領域で、シミュレートされたテレメトリ データが TSI 環境に実際に入力されたことを確認できます。 時間の範囲を限定するには、左上のドロップダウンを選択します。 次に、デバイス シミュレーションの期間が含まれる時間の範囲を入力します。 その後、検索の虫眼鏡をクリックします。

   [![TSI エクスプローラーの時間の範囲設定フィルター](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

4. 時間の範囲を限定すると、IoT Hub や TSI 環境に明らかなデータ転送のバーストが確認できる部分にグラフをズームインできます。 また、右上の「**Streaming complete**」(ストリーミングが完了しました) のテキストには、見つかったイベントの合計数が表示されます。 **[Interval size]\(間隔のサイズ\)** スライダーをドラッグして、グラフのプロット細分性を制御することもできます。

   [![TSI エクスプローラーの時間の範囲設定フィルターが適用されたビュー](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

5. 最後に、リージョンを左クリックして範囲をフィルター処理し、その後右クリックして **[イベントの探索]** を使用してタブ形式の **[イベント]** ビューでイベントの詳細を表示します。

   [![TSI エクスプローラーの時間の範囲設定フィルターが適用されたビューとイベント](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルは実行中の Azure サービスをいくつか作成し、TSI 環境とデバイス シミュレーション ソリューションをサポートします。 このチュートリアル シリーズを破棄する、または完了を遅らせる場合は、不要なコストが発生しないようにすべてのリソースを削除することをお勧めします。 

Azure Portal の左側のメニューで、次のように操作します。

1. **[リソース グループ]** アイコンをクリックし、TSI 環境に作成したリソース グループを選択します。 ページの上部で **[リソース グループの削除]** をクリックし、リソース グループの名前を入力してから **[削除]** をクリックします。 
2. **[リソース グループ]** アイコンをクリックし、デバイス シミュレーション ソリューション アクセラレータによって作成されたリソース グループを選択します。 ページの上部で **[リソース グループの削除]** をクリックし、リソース グループの名前を入力してから **[削除]** をクリックします。 

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * TSI 環境の作成 
> * IoT Hub が含まれるデバイス シミュレーション ソリューションを作成する
> * IoT Hub を TSI 環境に接続する
> * デバイス シミュレーションを実行して TSI 環境にデータをストリーミングする
> * シミュレートされたテレメトリ データを確認する

独自の TSI 環境を作成する方法はわかったので、次は、TSI 環境のデータを使用する Web アプリケーションを構築する方法について確認します。

> [!div class="nextstepaction"]
> [Azure Time Series Insights のシングルページ Web アプリを作成する](tutorial-create-tsi-sample-spa.md)


