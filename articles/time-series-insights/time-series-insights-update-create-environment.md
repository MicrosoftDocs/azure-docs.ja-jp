---
title: チュートリアル:Azure Time Series Insights プレビューの環境を設定する | Microsoft Docs
description: Azure Time Series Insights プレビューで環境を設定する方法について説明します。
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 06/18/2019
ms.custom: seodec18
ms.openlocfilehash: 824d24b97f192583a42192b3bb90eb1818e1aa18
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273002"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>チュートリアル:Azure Time Series Insights プレビューの環境を設定する

このチュートリアルでは、Azure Time Series Insights プレビューの従量課金制 (PAYG) 環境を作成する手順について説明します。 

このチュートリアルでは、以下の内容を学習します。

* Azure Time Series Insights プレビューの環境を作成する。
* Azure Time Series Insights プレビュー環境を Azure Event Hubs のイベント ハブに接続する。
* ソリューション アクセラレータのサンプルを実行して、Azure Time Series Insights プレビュー環境にデータをストリーム配信する。
* データの基本的な分析を実行する。
* 時系列モデルの種類と階層を定義して、インスタンスに関連付ける。

>[!TIP]
> [IoT ソリューション アクセラレータ](https://www.azureiotsolutions.com/Accelerators)によって、カスタム IoT ソリューションの開発を高速化するために使用できる、エンタープライズ レベルのあらかじめ構成されたソリューションが提供されます。

## <a name="create-a-device-simulation"></a>デバイス シミュレーションを作成する

このセクションでは、Azure IoT Hub のインスタンスにデータを送信する、3 つのシミュレートされたデバイスを作成します。

1. [Azure IoT ソリューション アクセラレータのページ](https://www.azureiotsolutions.com/Accelerators)に移動します。 このページには、あらかじめ構築された例がいくつか表示されます。 お使いの Azure アカウントを使用して、サインインします。 次に、 **[Device Simulation]** を選択します。

   [![Azure IoT ソリューション アクセラレータのページ](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   **[今すぐ試してみる]** をクリックします。

1. **[デバイス シミュレーション ソリューションの作成]** ページで、次のパラメーターを設定します。

    | パラメーター | Action |
    | --- | --- |
    | **デプロイ名** | 新しいリソース グループに一意の値を入力します。 一覧の Azure リソースが作成され、リソース グループに割り当てられます。 |
    | **Azure サブスクリプション** | Time Series Insights 環境の作成に使用したサブスクリプションを選択します。 |
    | **Azure の場所** | Time Series Insights 環境の作成に使用したリージョンを選択します。 |
    | **デプロイ オプション** | **[Provision new IoT Hub]\(新しい IoT ハブをプロビジョニングする\)** を選択します。 |
 
    **[ソリューションの作成]** を選択します。 ソリューションのデプロイが完了するのに最大で 20 分かかる場合があります。

    [![[ソリューションの作成 Device Simulation] ページ](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Time Series Insights プレビューの PAYG 環境を作成する

このセクションでは、Azure Time Series Insights プレビュー環境を作成し、[Azure portal](https://portal.azure.com/) を使用して、IoT ソリューション アクセラレータによって作成された IoT ハブにこれを接続する方法について説明します。

1. 自分のサブスクリプション アカウントを使用して、Azure portal にサインインします。

1. **[リソースの作成]**  >  **[モノのインターネット (IoT)]**  >  **[Time Series Insights]** を選択します。

   [![[モノのインターネット (IoT)] を選択し、[Time Series Insights] を選択する](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. **[Time Series Insights 環境の作成]** ウィンドウの **[基本]** タブで、次のパラメーターを設定します。

    | パラメーター | Action |
    | --- | ---|
    | **環境名** | Azure Time Series Insights プレビュー環境の一意名を入力します。 |
    | **サブスクリプション** | Azure Time Series Insights プレビュー環境を作成するサブスクリプションを入力します。 ベスト プラクティスとしては、デバイス シミュレーターによって作成される他の IoT リソースと同じサブスクリプションを使用します。 |
    | **リソース グループ** | Azure Time Series Insights プレビュー環境リソースに既存のリソース グループを選択するか、新しいリソース グループを作成します。 リソース グループとは、Azure リソース用のコンテナーです。 ベスト プラクティスとしては、デバイス シミュレーターによって作成される他の IoT リソースと同じリソース グループを使用します。 |
    | **Location** | Azure Time Series Insights プレビュー環境のデータセンター リージョンを選択します。 待ち時間の増加を防ぐために、お使いの他の IoT リソースと同じリージョン内に Azure Time Series Insights プレビュー環境を作成することをお勧めします。 |
    | **レベル** |  **[PAYG]** (*従量課金制*) を選択します。 これは、Azure Time Series Insights プレビュー製品の SKU です。 |
    | **プロパティ ID** | ご自分の時系列インスタンスを一意に識別できる値を入力します。 **[プロパティ ID]** ボックスに入力する値は変更不可です。 これは後で変更することはできません。 このチュートリアルでは、「**iothub-connection-device-id**」と入力します。Time Series ID の詳細については、[時系列 ID の選択のベスト プラクティス](./time-series-insights-update-how-to-id.md)に関するページを参照してください。 |
    | **Storage account name \(ストレージ アカウント名\)** | 作成する新しいストレージ アカウントのグローバルな一意名を入力します。 |
   
   **[次へ:Event Source]\(次へ: イベント ソース\)** をクリックします。

   [![Time Series Insights 環境の作成ウィンドウ](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. **[Event Source]\(イベント ソース\)** タブで、次のパラメーターを設定します。

   | パラメーター | Action |
   | --- | --- |
   | **Create an Event Source? \(イベント ソースを作成しますか\)** | **[はい]** を選択します。|
   | **Name** | イベント ソース名に一意の値を入力します。 |
   | **ソースの種類** | **[IoT Hub]** を選択します。 |
   | **ハブを選択** | **[既存のものを選択]** を選択します。 |
   | **サブスクリプション** | デバイス シミュレーターに使用したサブスクリプションを選択します。 |
   | **IoT Hub name (IoT ハブの名前)** | デバイス シミュレーター用に作成した IoT ハブの名前を選択します。 |
   | **IoT Hub access policy (IoT ハブのアクセス ポリシー)** | **[iothubowner]** を選びます。 |
   | **IoT Hub コンシューマー グループ** | **[New]\(新規\)** を選択し、一意の名前を入力します。次に、 **[Add]\(追加\)** を選択します。 コンシューマー グループは、Azure Time Series Insights プレビューで一意の値である必要があります。 |
   | **Timestamp property \(タイムスタンプのプロパティ\)** | この値は、受信したテレメトリ データで**タイムスタンプ** プロパティを識別するために使用されます。 このチュートリアルでは、このボックスを空のままにします。 このシミュレーターでは、IoT ハブからの受信タイムスタンプを使用します。これが Time Series Insights の既定の設定です。 |

   **[Review + create]\(レビュー + 作成\)** を選択します。

   [![イベント ソースを構成する](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. **[Review + create]\(確認と作成\)** タブで選択項目を確認し、 **[Create]\(作成\)** を選択します。

    [![[Review + create]\(確認と作成\) ページと [Create]\(作成\) ボタン](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    デプロイの状態を確認できます。

    [![デプロイが完了したことを示す通知](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. テナントを所有している場合は、Azure Time Series Insights プレビュー環境にアクセスできます。 アクセスがあることを確認するには:

   1. 自分のリソース グループを検索し、Azure Time Series Insights プレビュー環境を選択します。

      [![選択されている環境](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. Azure Time Series Insights プレビュー ページで、 **[データ アクセス ポリシー]** を選択します。

      [![データ アクセス ポリシー](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. ご自分の資格情報が表示されることを確認します。

      [![表示された資格情報](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   ご自分の資格情報が表示されない場合は、環境にアクセスするためのアクセス許可を自分自身に付与する必要があります。 アクセス許可の設定について詳しくは、「[データ アクセスの許可](./time-series-insights-data-access.md)」を参照してください。

## <a name="stream-data-into-your-environment"></a>環境にデータをストリーム配信する

1. [Azure IoT ソリューション アクセラレータのページ](https://www.azureiotsolutions.com/Accelerators)に戻ります。 ソリューション アクセラレータ ダッシュボードで、対象のソリューションを見つけます。 次に **[起動]** を選択します。

    [![デバイス シミュレーション ソリューションを起動する](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. **Microsoft Azure IoT デバイス シミュレーション**のページにリダイレクトされます。 ページの右上隅にある **[New simulation]\(新しいシミュレーション\)** を選択します。

    [![Azure IoT のシミュレーション ページ](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. **[Simulation setup]\(シミュレーションの設定\)** ウィンドウで、次のパラメーターを設定します。

    | パラメーター | Action |
    | --- | --- |
    | **Name** | シミュレーターの一意名を入力します。 |
    | **説明** | 定義を入力します。 |
    | **[Simulation duration]\(シミュレーション期間\)** | **[Run indefinitely]\(無期限に実行する\)** に設定します。 |
    | **[デバイス モデル]** | **[名前]** :「**Chiller**」と入力します。 <br />**[Amount]\(量\)** :「**3**」と入力します。 |
    | **[Target IoT Hub]\(IoT Hub をターゲットにする\)** | **[Use pre-provisioned IoT Hub]\(事前プロビジョニングされている IoT Hub を使用する\)** に設定します。 |

    [![設定するパラメーター](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    **[シミュレーションの開始]** を選択します。

    デバイス シミュレーション ダッシュボードで、 **[Active devices]\(アクティブなデバイス\)** と **[Messages per second]\(1 秒あたりのメッセージ数\)** に表示される情報を確認します。

    [![Azure IoT シミュレーション ダッシュボード](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data-in-your-environment"></a>環境内でデータを分析する

このセクションでは、[Azure Time Series Insights プレビュー エクスプローラー](./time-series-insights-update-explorer.md)を使用して、時系列データの基本的な分析を実行します。

1. [Azure portal](https://portal.azure.com/) のリソース ページで URL を選択して、Azure Time Series Insights プレビュー エクスプローラーに移動します。

    [![Time Series Insights プレビュー エクスプローラーの URL](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. エクスプローラーで、 **[Time Series Instances]\(Time Series インスタンス\)** ノードを選択して、環境内のすべての Azure Time Series Insights プレビュー インスタンスを表示します。

    [![親のないインスタンスの一覧](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. 最初の時系列インスタンスを選択します。 次に、 **[Show pressure]\(気圧の表示\)** を選択します。

    [![選択されている時系列インスタンスと、平均気圧を表示するメニュー コマンド](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    時系列グラフが表示されます。 **[Interval]\(間隔\)** を **[15 秒間]** に変更します。

    [![時系列グラフ](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. 他の 2 つの時系列インスタンスで、手順 3 を繰り返します。 次のグラフに示すように、すべての時系列インスタンスを表示できます。

    [![すべての時系列のグラフ](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. **[Timeframe]\(期間\)** オプション ボックスで時間の範囲を変更して、過去 1 時間の時系列の傾向を確認します。

    [![時間範囲を 1 時間に設定する](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>モデルを定義して適用する

このセクションでは、モデルを適用してデータを構造化します。 モデルを完成させるには、タイプ、階層、およびインスタンスを定義します。 データ モデリングの詳細については、[時系列モデル](./time-series-insights-update-tsm.md)に関するページを参照してください。

1. エクスプローラーで、 **[Model]\(モデル\)** タブを選択します。

   [![エクスプローラーの [Model]\(モデル\) タブ](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. **[Add]\(追加\)** を選択してタイプを追加します。

   [![タイプの [Add]\(追加\) ボタン](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. 次に、そのタイプの 3 つの変数 (*気圧*、*気温*、*湿度*) を定義します。 **[Add a Type]\(タイプの追加\)** ウィンドウで、次のパラメーターを設定します。

    | パラメーター | Action |
    | --- | ---|
    | **Name** | 「**Chiller**」と入力します。 |
    | **説明** | 「**This is a type definition of Chiller**」と入力します。 |

   * *圧力*を定義するには、 **[Variables]\(変数\)** で次のパラメーターを設定します。

     | パラメーター | Action |
     | --- | ---|
     | **Name** | 「**Avg Pressure**」と入力します。 |
     | **値** | **[pressure (Double)]\(圧力 (Double)\)** を選択します。 Azure Time Series Insights プレビューでイベントの受信が開始されてから **[値]** が自動的に入力されるまで、数分かかる場合があります。 |
     | **Aggregation Operation (集計操作)** | **[AVG]** を選択します。 |

      [![圧力を定義するための選択](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      次の変数を追加するには、 **[Add Variable]\(変数の追加\)** を選択します。

   * *温度*を定義します。

     | パラメーター | Action |
     | --- | ---|
     | **Name** | 「**Avg Temperature**」と入力します。 |
     | **値** | **[temperature (Double)]\(温度 (Double)\)** を選択します。 Azure Time Series Insights プレビューでイベントの受信が開始されてから **[値]** が自動的に入力されるまで、数分かかる場合があります。 |
     | **Aggregation Operation (集計操作)** | **[AVG]** を選択します。|

      [![気温を定義するための選択](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

      次の変数を追加するには、 **[Add Variable]\(変数の追加\)** を選択します。

   * *湿度*を定義します。

      | | |
      | --- | ---|
      | **Name** | 「**Max Humidity**」と入力する |
      | **値** | **[humidity (Double)]\(湿度 (Double)\)** を選択します。 Azure Time Series Insights プレビューでイベントの受信が開始されてから **[値]** が自動的に入力されるまで、数分かかる場合があります。 |
      | **Aggregation Operation (集計操作)** | **[MAX]** を選択します。|

      [![気温を定義するための選択](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

    **作成** を選択します。

    追加したタイプを確認できます。

    [![追加したタイプに関する情報](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. 次の手順では、階層を追加します。 **[Hierarchies]\(階層\)** で、 **[Add]\(追加\)** を選択します。

    [![[Hierarchies]\(階層\) タブと [Add]\(追加\) ボタン](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. **[Edit Hierarchy]\(階層の編集\)** ウィンドウで、次のパラメーターを設定します。

   | パラメーター | Action |
   | --- | ---|
   | **Name** | 「**Location Hierarchy**」と入力します。 |
   | **Level 1 (レベル 1)** | 「**Country**」と入力します。 |
   | **Level 2 (レベル 2)** | 「**City**」と入力します。 |
   | **Level 3 (レベル 3)** | 「**Building**」と入力します。 |

   **[保存]** を選択します。

    [![[Hierarchies]\(階層\) のフィールドと [Create]\(作成\) ボタン](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   作成した階層を確認できます。

    [![階層に関する情報](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. **[Instances]\(インスタンス\)** を選択します。 最初のインスタンスを選択し、 **[Edit]\(編集\)** を選択します。

    [![インスタンスの [Edit]\(編集\) ボタンの選択](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. **[Edit instances]\(インスタンスの編集\)** ウィンドウで、次のパラメーターを設定します。

    | パラメーター | Action |
    | --- | --- |
    | **Type** | **[Chiller]** を選択します。 |
    | **説明** | 「**Instance for Chiller-01.1**」と入力します。 |
    | **Hierarchies (階層)** | **[Location Hierarchy]\(場所の階層\)** を選択します。 |
    | **Country (国)** | 「**USA**」と入力します。 |
    | **City (市)** | 「**Seattle**」と入力します。 |
    | **Building (建物)** | 「**Space Needle**」と入力します。 |

    [![[保存] ボタンがある [インスタンス] フィールド](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

   **[保存]** を選択します。

1. 他のセンサーについて前の手順を繰り返します。 次の値を更新します。

   * Chiller 01.2 の場合:

     | パラメーター | Action |
     | --- | --- |
     | **Type** | **[Chiller]** を選択します。 |
     | **説明** | 「**Instance for Chiller-01.2**」と入力します。 |
     | **Hierarchies (階層)** | **[Location Hierarchy]\(場所の階層\)** を選択します。 |
     | **Country (国)** | 「**USA**」と入力します。 |
     | **City (市)** | 「**Seattle**」と入力します。 |
     | **Building (建物)** | 「**Pacific Science Center**」と入力します。 |

   * Chiller 01.3 の場合:

     | パラメーター | Action |
     | --- | --- |
     | **Type** | **[Chiller]** を選択します。 |
     | **説明** | 「**Instance for Chiller-01.3**」と入力します。 |
     | **Hierarchies (階層)** | **[Location Hierarchy]\(場所の階層\)** を選択します。 |
     | **Country (国)** | 「**USA**」と入力します。 |
     | **City (市)** | 「**New York**」と入力します。 |
     | **Building (建物)** | 「**Empire State Building**」と入力します。 |

1. **[Analyze]\(分析\)** タブを選択し、ページを更新します。 **[Location Hierarchy]\(場所の階層\)** で、すべての階層レベルを展開して時系列インスタンスを表示します。

   [![[Analyze]\(分析\) タブ](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. 過去 1 時間の時系列インスタンスを調べるには、 **[Quick Times]\(クイック タイム\)** を **[Last Hour]\(過去 1 時間\)** に設定します。

    [![[Quick Times]\(クイック タイム\) ボックスと、[Last Hour]\(過去 1 時間\) の選択](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. **Pacific Science Center** の時系列インスタンスを選択し、 **[Show Max Humidity]\(最大湿度の表示\)** を選択します。

    [![選択された時系列インスタンスと、[Show Max Humidity]\(最大湿度の表示\) メニューの選択](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. **最大湿度**の時系列が **1 分**の間隔サイズで開きます。 リージョンをフィルター処理するには、リージョンを選択します。 時間枠内のイベントを分析するには、グラフを右クリックし、 **[Zoom]\(拡大\)** を選択します。

   [![選択された範囲と、ショートカット メニューの [Zoom]\(拡大\) コマンド](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. イベントの詳細を表示するには、リージョンを選択してからグラフを右クリックします。

   [![イベントの詳細なリスト](media/v2-update-provision/define-eighteen.png)](media/v2-update-provision/define-eighteen.png#lightbox)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。  

> [!div class="checklist"]
> * デバイス シミュレーション アクセラレータを作成して使用する。
> * Azure Time Series Insights プレビューの PAYG 環境を作成する。
> * Azure Time Series Insights プレビュー環境をイベント ハブに接続する。
> * ソリューション アクセラレータのサンプルを実行して、Azure Time Series Insights プレビュー環境にデータをストリーム配信する。
> * データの基本的な分析を実行する。
> * 時系列モデルの種類と階層を定義して、インスタンスに関連付ける。

Azure Time Series Insights プレビュー環境を自分で作成する方法がわかったので、Azure Time Series Insights の主要な概念について詳しく学習してください。

Azure Time Series Insights のストレージ構成について読む。

> [!div class="nextstepaction"]
> [Azure Time Series Insights プレビューのストレージとイングレス](./time-series-insights-update-storage-ingress.md)

時系列モデルの詳細を確認する。

> [!div class="nextstepaction"]
> [Azure Time Series Insights プレビューのデータ モデリング](./time-series-insights-update-tsm.md)
