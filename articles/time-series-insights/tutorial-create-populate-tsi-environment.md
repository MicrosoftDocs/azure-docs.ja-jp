---
title: チュートリアル:Azure Time Series Insights 環境の作成 | Microsoft Docs
description: シミュレートされたデバイスからのデータが入力された Time Series Insights 環境を作成する方法について説明します。
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 06a450c47c7264bdecb663c9f71e3a9753df5e1e
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273622"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>チュートリアル:Azure Time Series Insights 環境を作成する

このチュートリアルでは、シミュレートされたデバイスのデータが入力される Azure Time Series Insights 環境を作成するプロセスについて説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Time Series Insights 環境を作成します。
> * IoT ハブを含むデバイス シミュレーション ソリューションを作成する。
> * Time Series Insights 環境を IoT ハブに接続する。
> * デバイス シミュレーションを実行して Time Series Insights 環境にデータをストリーミングする。
> * シミュレートされたテレメトリ データを確認する。

## <a name="video"></a>ビデオ

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Azure IoT ソリューション アクセラレータを使用して、データを生成し、Time Series Insights の使用を開始する方法について説明します。 </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
* また、Azure のサインイン アカウントは、サブスクリプションの**所有者**ロールのメンバーである必要があります。 詳細については、[ロールベースのアクセス制御と Azure portal を使用したアクセスの管理](/azure/role-based-access-control/role-assignments-portal)に関するページをご覧ください。

## <a name="overview"></a>概要

Time Series Insights 環境とは、デバイス データが収集、格納される場所のことです。 格納された後は、[Azure Time Series Insights エクスプローラー](time-series-quickstart.md)と [Time Series Insights クエリ API](/rest/api/time-series-insights/ga-query-api) を使用して、データのクエリおよび分析を行うことができます。

Azure IoT Hub は、お使いの Azure クラウドに安全に接続してデータを転送するために、チュートリアルのすべての (シミュレートされた、または物理的な) デバイスによって使用される、イベント ソースです。

また、このチュートリアルでは [IoT ソリューション アクセラレータ](https://www.azureiotsolutions.com)を使用し、サンプルのテレメトリ データを生成して IoT Hub にストリーム配信します。

>[!TIP]
> [IoT ソリューション アクセラレータ](https://www.azureiotsolutions.com)によって、カスタム IoT ソリューションの開発を高速化するために使用できる、エンタープライズ レベルのあらかじめ構成されたソリューションが提供されます。

## <a name="create-a-device-simulation"></a>デバイス シミュレーションを作成する

最初に、Time Series Insights 環境に入力されるテスト データを生成する、デバイス シミュレーション ソリューションを作成します。

1. 別のウィンドウまたはタブで [azureiotsolutions.com](https://www.azureiotsolutions.com) に移動します。 同じ Azure サブスクリプション アカウントを使用してサインインし、 **[デバイス シミュレーション]** アクセラレータを選択します。

   [![デバイス シミュレーション アクセラレータを実行する](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. **[デバイス シミュレーション ソリューションの作成]** ページで、次の必須パラメーターを入力します。

   パラメーター|説明
   ---|---
   **デプロイ名** | この一意の値は、新しいリソース グループを作成するために使用されます。 一覧の Azure リソースが作成され、リソース グループに割り当てられます。
   **Azure サブスクリプション** | 前のセクションで Time Series Insights 環境の作成に使用したものと同じサブスクリプションを指定します。
   **デプロイ オプション** | **[Provision new IoT Hub]\(新しい IoT ハブをプロビジョニングする\)** を選択して、このチュートリアル専用の新しい IoT ハブを作成します。
   **Azure の場所** | 前のセクションで Time Series Insights 環境の作成に使用したものと同じリージョンを指定します。

   完了したら、 **[ソリューションの作成]** を選択してソリューションの Azure リソースをプロビジョニングします。 このプロセスが完了するまでに最大で 20 分かかる場合があります。

   [![デバイス シミュレーション ソリューションをプロビジョニングする](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. プロビジョニング完了後、新しいソリューションの上にあるテキストが "**プロビジョニング中...** " から "**準備完了**" に変わります。

   >[!IMPORTANT]
   > **[起動]** はまだ選択しないでください。 後で戻ってくるので、この Web ページは開いたままにしておきます。

   [![デバイス シミュレーション ソリューションのプロビジョニング完了](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. ここで、Azure portal で新しく作成されたリソースを検査します。 **[リソース グループ]** ページで、最後の手順で指定した**ソリューション名**を使用して新しいリソース グループが作成されたことを確認します。 デバイス シミュレーション用に作成されたリソースをメモします。

   [![デバイス シミュレーションのリソース](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>環境の作成

次に、お使いの Azure サブスクリプションに Time Series Insights 環境を作成します。

1. Azure サブスクリプション アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。 
1. 左上隅の **[+ リソースの作成]** を選択します。 
1. **[モノのインターネット (IoT)]** カテゴリを選択し、 **[Time Series Insights]** を選択します。 

   [![Time Series Insights 環境リソースを選択する](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. **[Time Series Insights 環境]** ページで、必須パラメーターを入力します。

   パラメーター|説明
   ---|---
   **環境名** | Time Series Insights 環境の一意の名前を選択します。 その名前は Time Series Insights エクスプローラーと [Query API シリーズ](https://docs.microsoft.com/rest/api/time-series-insights/ga-query)で使用されます。
   **サブスクリプション** | サブスクリプションとは、Azure リソース用のコンテナーです。 Time Series Insights 環境を作成するサブスクリプションを選択します。
   **リソース グループ** | リソース グループとは、Azure リソース用のコンテナーです。 Time Series Insights 環境リソースに既存のリソース グループを選択するか、新しいリソース グループを作成します。
   **Location** | Time Series Insights 環境のデータ センター リージョンを選択します。 待ち時間の増加を防ぐために、Time Series Insights 環境を他の IoT リソースと同じリージョンに作成します。
   **レベル** | 必要なスループットを選択します。 **[S1]** を選択します。
   **Capacity** | 容量は、選択した SKU に関連するイングレス レートとストレージ容量に適用される乗数です。 この容量は、作成後に変更できます。 容量には **1** を選択します。

   終了したら、 **[Review + create]\(確認と作成\)** を選択して次のステップに進みます。

   [![Time Series Insights 環境リソースを作成する](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. 次に、Time Series Insights 環境を、ソリューション アクセラレータによって作成された IoT ハブに接続します。 **[ハブを選択]** を `Select existing` に設定します。 次に、**IoT ハブ名**を設定するときにソリューション アクセラレータによって作成された IoT ハブを選択します。

   [![作成した IoT ハブに Time Series Insights 環境を接続する](media/tutorial-create-populate-tsi-environment/ap-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-iot-hub.png#lightbox)

1. **[通知]** パネルを確認して、デプロイの完了を監視します。 

   [![Time Series Insights 環境のデプロイ成功](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation-to-stream-data"></a>デバイス シミュレーションを実行してデータをストリーミングする

これでデプロイおよび初期構成が完了しました。次は、[アクセラレータによって作成されたシミュレートされたデバイス](#create-a-device-simulation)からのサンプル データを Time Series Insights 環境に入力します。

IoT ハブに加えて、シミュレートされたデバイス テレメトリを作成して転送する Azure App Service Web アプリケーションが生成されました。

1. [ソリューション アクセラレータのダッシュ ボード](https://www.azureiotsolutions.com/Accelerators#dashboard)に戻ります。 必要に応じて、このチュートリアルで使用している同じ Azure アカウントを使用して再度サインインします。 これで "デバイス シミュレーション" ソリューションの **[起動]** を選択できるようになります。

     [![ソリューション アクセラレータのダッシュボード](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png#lightbox)

1. デバイス シミュレーション Web アプリは、最初に Web アプリケーションに "サインインとプロファイルの読み取り" アクセス許可を付与することをユーザーに促します。 このアクセス許可により、アプリケーションがアプリケーションの機能をサポートするのに必要なユーザー プロファイル情報を取得することを許可します。

     [![デバイス シミュレーション Web アプリケーションの同意](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. **[Simulation setup]\(シミュレーションのセットアップ\)** ページの読み込み後、次の必須パラメーターを入力します。

   パラメーター|説明
   ---|---
   **[Target IoT Hub]\(IoT Hub をターゲットにする\)** | **[Use pre-provisioned IoT Hub]\(事前プロビジョニングされている IoT Hub を使用する\)** を選択します。
   **[デバイス モデル]** | **[Chiller]** を選択します。
   **[Number of devices]\(デバイスの数\)**  | **[数量]** に `1000` と入力します。
   **[Telemetry frequency]\(テレメトリ頻度\)** | `10` 秒を入力します。
   **[Simulation duration]\(シミュレーション期間\)** | **[End in:]\(実行時間:\)** を選択し、`5` 分と入力します。

   完了したら、 **[シミュレーションの開始]** を選択します。 シミュレーションは、合計で 5 分間実行されます。 1,000 台のシミュレートされたデバイスから 10 秒ごとにデータを生成します。 

   [![デバイス シミュレーションのセットアップ](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. シミュレーションの実行中、約 10 秒ごとに **[Total messages]\(メッセージの総数\)** フィールドと **[Messages per second]\(1 秒ごとのメッセージ数\)** フィールドが更新されることに注目してください。 シミュレーションは約 5 分後に終了し、 **[Simulation setup]\(シミュレーションのセットアップ\)** に戻ります。

   [![実行中のデバイス シミュレーション](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>テレメトリ データを確認する

この最後のセクションでは、テレメトリ データが作成され、 Time Series Insights 環境に格納されたことを確認します。 データを確認するには、Time Series Insights エクスプローラーを使用します。これは、テレメトリ データのクエリと分析に使用します。

1. Time Series Insights 環境のリソース グループの **[概要]** ページに戻ります。 Time Series Insights 環境を選択します。

   [![Time Series Insights 環境のリソース グループと環境](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. Time Series Insights 環境の **[概要]** ページで、 **[Time Series Insights エクスプローラーの URL]** を選択して Time Series Insights エクスプローラーを開きます。

   [![Time Series Insights エクスプローラー](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. Time Series Insights エクスプローラーでは、Azure portal アカウントを使用して読み込みと認証が行われます。 最初のビューのグラフ領域で、シミュレートされたテレメトリ データが Time Series Insights 環境に入力されたことを確認できます。 時間の範囲を限定するには、左上隅のドロップダウンを選択します。 デバイス シミュレーションの期間が含まれる時間の範囲を入力します。 次に、検索の虫眼鏡をクリックします。

   [![Time Series Insights エクスプローラーの時間の範囲フィルター](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. 時間の範囲を限定すると、IoT Hub や Time Series Insights 環境に対する明らかなデータ転送バーストの部分にグラフをズームインできます。 また、右上隅の "**Streaming complete**" (ストリーミングが完了しました) のテキストには、見つかったイベントの合計数が表示されます。 **[Interval size]\(間隔のサイズ\)** スライダーをドラッグして、グラフのプロット細分性を制御することもできます。

   [![Time Series Insights エクスプローラーの時間の範囲フィルターが適用されたビュー](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. 最後に、リージョンを左クリックして、範囲をフィルター処理することもできます。 その後右クリックして **[イベントの探索]** を使用してタブ形式の **[イベント]** ビューでイベントの詳細を表示します。

   [![Time Series Insights エクスプローラーの時間の範囲フィルターが適用されたビューとイベント](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルでは、稼働する Azure サービスをいくつか作成し、Time Series Insights 環境とデバイス シミュレーション ソリューションをサポートします。 これらを削除するには、Azure portal に戻ります。

Azure portal の左側のメニューで、次のようにします。

1. **[リソース グループ]** アイコンを選択します。 次に、Time Series Insights 環境に作成したリソース グループを選択します。 ページの上部で **[リソース グループの削除]** を選択し、リソース グループの名前を入力して、 **[削除]** を選択します。

1. **[リソース グループ]** アイコンを選択します。 次に、デバイス シミュレーション ソリューション アクセラレータによって作成されたリソース グループを選択します。 ページの上部で **[リソース グループの削除]** を選択し、リソース グループの名前を入力して、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Time Series Insights 環境を作成します。
> * IoT ハブを含むデバイス シミュレーション ソリューションを作成する。
> * Time Series Insights 環境を IoT ハブに接続する。
> * デバイス シミュレーションを実行して Time Series Insights 環境にデータをストリーミングする。
> * シミュレートされたテレメトリ データを確認する。

独自の Time Series Insights 環境を作成する方法を確認したので、Time Series Insights 環境のデータを使用する Web アプリケーションを構築する方法について確認します。

> [!div class="nextstepaction"]
> [Azure Time Series Insights のシングルページ Web アプリを作成する](tutorial-create-tsi-sample-spa.md)
