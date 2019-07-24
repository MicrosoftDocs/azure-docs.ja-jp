---
title: チュートリアル:Azure Time Series Insights プレビューの環境を設定する | Microsoft Docs
description: Azure Time Series Insights プレビューで環境を設定する方法について説明します。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: 1b09c0e31b217d7d67f936aefe9045d190241389
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53633115"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>チュートリアル:Azure Time Series Insights プレビューの環境を設定する

このチュートリアルでは、Azure Time Series Insights プレビューの従量課金制 (PAYG) 環境を作成する手順について説明します。 このチュートリアルでは、以下の内容を学習します。

* Azure Time Series Insights プレビューの環境を作成する。
* Azure Time Series Insights プレビュー環境を Azure Event Hubs のイベント ハブに接続する。
* ソリューション アクセラレータのサンプルを実行して、Azure Time Series Insights プレビュー環境にデータをストリーム配信する。
* データの基本的な分析を実行する。
* 時系列モデルの種類と階層を定義して、インスタンスに関連付ける。

# <a name="create-a-device-simulation"></a>デバイス シミュレーションを作成する

このセクションでは、IoT ハブにデータを送信する 3 つのシミュレートされたデバイスを作成します。

1. [Azure IoT ソリューション アクセラレータのページ](https://www.azureiotsolutions.com/Accelerators)に移動します。 このページには、あらかじめ構築された例がいくつか表示されます。 お使いの Azure アカウントを使用して、サインインします。 次に、**[Device Simulation]** を選択します。

   ![Azure IoT ソリューション アクセラレータのページ][1]

   **[今すぐ試してみる]** をクリックします。

1. **[デバイス シミュレーション ソリューションの作成]** ページで、次の必須パラメーターに入力します。

   | パラメーター | 説明 |
   | --- | --- |
   | **[ソリューション名]** |    新しいリソース グループを作成するために一意の値を入力します。 一覧の Azure リソースが作成され、リソース グループに割り当てられます。 |
   | **サブスクリプション** | Time Series Insights 環境の作成に使用したものと同じサブスクリプションを指定します。 |
   | **[リージョン]** |   Time Series Insights 環境の作成に使用したものと同じリージョンを指定します。 |
   | **[オプションの Azure リソースをデプロイする]**    | [IoT Hub] はオンのままにします。シミュレートされたデバイスはこれを使用してデータに接続してストリーム配信します。 |

   次に **[ソリューションの作成]** を選択します。 ソリューションがデプロイされるまで 10 分から 15 分待ちます。

   ![[ソリューションの作成 Device Simulation] ページ][2]

1. ソリューション アクセラレータ ダッシュボードで、**[起動]** ボタンを選択します。

   ![デバイス シミュレーション ソリューションを起動する][3]

1. **Microsoft Azure IoT デバイス シミュレーション**のページにリダイレクトされます。 ページの右上にある **[+ New simulation]\(+ 新しいシミュレーション\)** を選択します。

   ![Azure IoT のシミュレーション ページ][4]

1.  次のように、必要なパラメーターを入力します。

    ![入力するパラメーター][5]

    |||
    | --- | --- |
    | **名前** | シミュレーターの一意名を入力します。 |
    | **説明** | 定義を入力します。 |
    | **[Simulation duration]\(シミュレーション期間\)** | **[Run indefinitely]\(無期限に実行する\)** に設定します。 |
    | **[デバイス モデル]** | **[名前]**:「**Chiller**」と入力します。 </br>**[Amount]\(量\)**:「**3**」と入力します。 |
    | **[Target IoT Hub]\(IoT Hub をターゲットにする\)** | **[Use pre-provisioned IoT Hub]\(事前プロビジョニングされている IoT Hub を使用する\)** に設定します。 |

    次に、**[シミュレーションの開始]** を選択します。

1. デバイス シミュレーション ダッシュボードで、**[Active devices]\(アクティブなデバイス\)** と **[Messages per second]\(1 秒あたりのメッセージ数\)** を確認します。

    ![Azure IoT シミュレーション ダッシュボード][6]

## <a name="list-device-simulation-properties"></a>デバイス シミュレーションのプロパティを一覧表示する

Azure Time Series Insights 環境を作成する前に、お使いの IoT ハブ、サブスクリプション、リソース グループの名前が必要です。

1. ソリューション アクセラレータ ダッシュボードに移動し、同じ Azure サブスクリプション アカウントを使用してサインインします。 前の手順で作成したデバイス シミュレーションを探します。

1. そのデバイス シミュレーターを選択し、**[起動]** を選択します。 右側にある **[Azure の管理ポータル]** リンクをクリックします。

    ![シミュレーターの一覧][7]

1. IoT ハブ、サブスクリプション、リソース グループの名前を書き留めます。

    ![Azure ポータル][8]

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Time Series Insights プレビューの PAYG 環境を作成する

このセクションでは、[Azure portal](https://portal.azure.com/) を使用して Azure Time Series Insights プレビューの環境を作成する方法について説明します。

1. 自分のサブスクリプション アカウントを使用して、Azure portal にサインインします。

1. **[リソースの作成]** を選択します。

1. **[モノのインターネット (IoT)]** カテゴリを選択し、**[Time Series Insights]** を選択します。

   ![[モノのインターネット (IoT)] を選択し、[Time Series Insights] を選択する][9]

1. ページのフィールドに次のように入力します。

   | | |
   | --- | ---|
   | **環境名** | Azure Time Series Insights プレビュー環境の一意名を選択します。 |
   | **サブスクリプション** | Azure Time Series Insights プレビュー環境を作成するサブスクリプションを入力します。 デバイス シミュレーターによって作成される他の IoT リソースと同じサブスクリプションを使用することをお勧めします。 |
   | **[リソース グループ]** | リソース グループとは、Azure リソース用のコンテナーです。 Azure Time Series Insights プレビュー環境リソースに既存のリソース グループを選択するか、新しいリソース グループを作成します。 デバイス シミュレーターによって作成される他の IoT リソースと同じリソース グループを使用することをお勧めします。 |
   | **場所** | Azure Time Series Insights プレビュー環境のデータセンター リージョンを選択します。 帯域幅のコストや待機時間の増加を防ぐために、Azure Time Series Insights プレビュー環境を IoT リソースと同じリージョンにすることをお勧めします。 |
   | **レベル** |  従量課金制を意味する **[PAYG]** を選択します。 これは、Azure Time Series Insights プレビュー製品の SKU です。 |
   | **プロパティ ID** | 自分の時系列を一意に識別できるものを入力します。 このフィールドは変更不可であり、後で変更できないことに注意してください。 このチュートリアルでは、**iothub-connection-device-id** を使用します。時系列 ID について詳しくは、[時系列 ID の選択方法](./time-series-insights-update-how-to-id.md)に関するページを参照してください。 |
   | **Storage account name \(ストレージ アカウント名\)** | 作成する新しいストレージ アカウントのグローバルな一意名を入力します。 |

   次に、**[Next:Event Source]\(次へ: イベント ソース\)** をクリックします。

   ![Time Series Insights 環境の作成ページ][10]

1. イベント ソースのページでフィールドに次のように入力します。

   | | |
   | --- | --- |
   | **Create an Event Source? \(イベント ソースを作成しますか\)** | 「**Yes**」と入力します。|
   | **名前** | イベント ソースの名前に使用される一意の値を入力します。|
   | **ソースの種類** | 「**IoT Hub**」と入力します。 |
   | **Select a Hub? \(ハブを選択しますか\)** | 「**Select existing**」と入力します。 |
   | **サブスクリプション** | デバイス シミュレーターに使用したサブスクリプションを入力します。 |
   | **IoT Hub name (IoT ハブの名前)** | デバイス シミュレーター用に作成した IoT ハブの名前を入力します。 |
   | **IoT Hub access policy (IoT ハブのアクセス ポリシー)** | 「**iothubowner**」と入力します。 |
   | **Iot Hub consumer group (IoT Hub コンシューマー グループ)** | Azure Time Series Insights プレビュー用の一意のコンシューマー グループが必要です。 **[New]\(新規\)** を選択し、一意の名前を入力します。次に、**[Add]\(追加\)** を選択します。 |
   | **Timestamp property \(タイムスタンプのプロパティ\)** | このフィールドは、受信したテレメトリ データでタイムスタンプ プロパティを識別するために使用されます。 このチュートリアルでは、このフィールドに入力しないでください。 このシミュレーターでは、IoT ハブからの受信タイムスタンプを使用します。これが Time Series Insights の既定の設定です。|

   次に、**[Review + create]\(確認と作成\)** を選択します。

   ![イベント ソースの設定ページ][13]

1. 確認ページですべてのフィールドを確認し、**[Create]\(作成\)** を選択します。

   ![[Review + create]\(確認と作成\) ページと [Create]\(作成\) ボタン][14]

1. デプロイの状態を確認できます。

   ![デプロイが完了したことを示す通知][15]

1. テナントを所有している場合、Azure Time Series Insights プレビュー環境へのアクセスを受け取る必要があります。 アクセスがあることを確認するには:

   a. 自分のリソース グループを検索し、Azure Time Series Insights プレビュー環境を選択します。

      ![選択されている環境][16]

   b. Azure Time Series Insights プレビュー ページで、**[データ アクセス ポリシー]** に移動します。

     ![データ アクセス ポリシー][17]

   c. 自分の資格情報が表示されることを確認します。

     ![表示された資格情報][18]

   自分の資格情報が表示されない場合は、環境にアクセスするためのアクセス許可を自分自身に付与する必要があります。 アクセス許可の設定について詳しくは、「[データ アクセスの許可](./time-series-insights-data-access.md)」を参照してください。

## <a name="analyze-data-in-your-environment"></a>環境内でデータを分析する

このセクションでは、[Azure Time Series Insights プレビュー エクスプローラー](./time-series-insights-update-explorer.md)を使用して、時系列データの基本的な分析を実行します。

1. [Azure portal](https://portal.azure.com/) のリソース ページで URL を選択して、Azure Time Series Insights プレビュー エクスプローラーに移動します。

   ![Time Series Insights プレビュー エクスプローラーの URL][19]

1. エクスプローラーで、**[Unparented Instances]\(親のないインスタンス\)** ノードを選択して、環境内のすべての Azure Time Series Insights プレビュー インスタンスを表示します。

   ![親のないインスタンスの一覧][20]

1. 表示された時系列で、最初のインスタンスを選択します。 次に、**[Show Avg pressure]\(平均気圧の表示\)** を選択します。

   ![選択されているインスタンスと、平均気圧を表示するメニュー コマンド][21]

   時系列グラフが右側に表示されます。

   ![時系列グラフ][22]

1. 他の 2 つの時系列で手順 3 を繰り返します。 そうすると、次のグラフに示すように、すべての時系列を表示できます。

   ![すべての時系列のグラフ][23]

1. 時間の範囲を変更して、過去 1 時間の時系列の傾向を確認します。 

   a. **[From]\(開始\)** オプション ボックスを選択します。

      ![[From]\(開始\) オプション ボックス][24]

   b. このボックスで、過去 1 時間のイベントが表示されるように時刻を変更します。

      ![時刻の調整][25]

1. 3 つのデバイスすべての過去 1 時間の圧力を比較できます。

   ![3 つのデバイスの比較][26]

## <a name="define-and-apply-a-model"></a>モデルを定義して適用する

このセクションでは、モデルを適用してデータを構造化します。 モデルを完成させるには、タイプ、階層、およびインスタンスを定義します。 データ モデリングについて詳しくは、「[タイム シリーズ モデル](./time-series-insights-update-tsm.md)」を参照してください。

1. エクスプローラーで、**[Model]\(モデル\)** タブを選択します。

   ![エクスプローラーの [Model]\(モデル\) タブ][27]

1. 次に、**[+ Add]\(+ 追加\)** を選択してタイプを追加します。 右側にタイプ エディターが開きます。

   ![タイプの [Add]\(追加\) ボタン][28]

1. タイプの 3 つの変数 (気圧、気温、湿度) を定義します。 次の情報を入力します。

   | | |
   | --- | ---|
   | **名前** | 「**Chiller**」と入力します。 |
   | **説明** | 「**This is a type definition of Chiller**」と入力します。 |

   * 3 つの変数を使って圧力を定義します。

      | | |
      | --- | ---|
      | **名前** | 「**Avg Pressure**」と入力します。 |
      | **値** | **[pressure (Double)]\(圧力 (Double)\)** を選択します。 Azure Time Series Insights プレビューでイベントの受信が開始されてからこのフィールドが設定されるまで、数分かかる場合があることに注意してください。 |
      | **Aggregation Operation (集計操作)** | **[AVG]** を選択します。 |

      ![圧力を定義するための選択][29]

      **[+ Add Variable]\(+ 変数の追加\)** を選択して、次の変数を追加します。

   * 温度を定義します。

      | | |
      | --- | ---|
      | **名前** | 「**Avg Temperature**」と入力します。 |
      | **値** | **[temperature (Double)]\(温度 (Double)\)** を選択します。 Azure Time Series Insights プレビューでイベントの受信が開始されてからこのフィールドが設定されるまで、数分かかる場合があることに注意してください。 |
      | **Aggregation Operation (集計操作)** | **[AVG]** を選択します。|

      ![気温を定義するための選択][30]

   * 湿度を定義します。

      | | |
      | --- | ---|
      | **名前** | 「**Max Humidity**」と入力します。 |
      | **値** | **[humidity (Double)]\(湿度 (Double)\)** を選択します。 Azure Time Series Insights プレビューでイベントの受信が開始されてからこのフィールドが設定されるまで、数分かかる場合があることに注意してください。 |
      | **Aggregation Operation (集計操作)** | **[MAX]** を選択します。|

      ![気温を定義するための選択][31]

   そのうえで **[Create]\(作成\)** を選択します。

1. 追加したタイプを確認できます。

   ![追加したタイプに関する情報][32]

1. 次の手順では、階層を追加します。 **[Hierarchies]\(階層\)** セクションで **[+ Add]\(+ 追加\)** を選択します。

   ![[Hierarchies]\(階層\) タブと [Add]\(追加\) ボタン][33]

1. 階層を定義します。 次のようにフィールドに入力します。

   | | |
   | --- | ---|
   | **名前** | 「**Location Hierarchy**」と入力します。 |
   | **Level 1 (レベル 1)** | 「**Country**」と入力します。 |
   | **Level 2 (レベル 2)** | 「**City**」と入力します。 |
   | **Level 3 (レベル 3)** | 「**Building**」と入力します。 |

   そのうえで **[Create]\(作成\)** を選択します。

   ![[Hierarchies]\(階層\) のフィールドと [Create]\(作成\) ボタン][34]

1. 作成した階層を確認できます。

   ![階層に関する情報][35]

1. 左側の **[Instances]\(インスタンス\)** を選択します。 インスタンスが表示されたら、最初のインスタンスを選択し、**[Edit]\(編集\)** を選択します。

   ![インスタンスの [Edit]\(編集\) ボタンの選択][36]

1. 右側にテキスト エディターが表示されます。 以下の情報を追加します。

   | | |
   | --- | --- |
   | **種類** | **[Chiller]** を選択します。 |
   | **説明** | 「**Instance for Chiller-01.1**」と入力します。 |
   | **Hierarchies (階層)** | **[Location Hierarchy]\(場所の階層\)** を有効にします。 |
   | **Country (国)** | 「**USA**」と入力します。 |
   | **City (市)** | 「**Seattle**」と入力します。 |
   | **Building (建物)** | 「**Space Needle**」と入力します。 |

    次に、**[保存]** を選択します。

   ![[Instances]\(インスタンス\) のフィールドと [Save]\(保存\) ボタン][37]

1. 他のセンサーについて前の手順を繰り返します。 以下のフィールドを使用します。

   * Chiller 01.2 の場合:

     | | |
     | --- | --- |
     | **種類** | **[Chiller]** を選択します。 |
     | **説明** | 「**Instance for Chiller-01.2**」と入力します。 |
     | **Hierarchies (階層)** | **[Location Hierarchy]\(場所の階層\)** を有効にします。 |
     | **Country (国)** | 「**USA**」と入力します。 |
     | **City (市)** | 「**Seattle**」と入力します。 |
     | **Building (建物)** | 「**Pacific Science Center**」と入力します。 |

   * Chiller 01.3 の場合:

     | | |
     | --- | --- |
     | **種類** | **[Chiller]** を選択します。 |
     | **説明** | 「**Instance for Chiller-01.1**」と入力します。 |
     | **Hierarchies (階層)** | **[Location Hierarchy]\(場所の階層\)** を有効にします。 |
     | **Country (国)** | 「**USA**」と入力します。 |
     | **City (市)** | 「**New York**」と入力します。 |
     | **Building (建物)** | 「**Empire State Building**」と入力します。 |

1. **[Analyze]\(分析\)** タブに移動し、ページを更新します。 すべての階層レベルを展開して、時系列を見つけます。

   ![[Analyze]\(分析\) タブ][38]

1. 過去 1 時間の時系列を調べるには、**[Quick Times]\(クイック タイム\)** を **[Last Hour]\(過去 1 時間\)** に設定します。

   ![[Quick Times]\(クイック タイム\) ボックスと、[Last Hour]\(過去 1 時間\) の選択][39]

1. **Pacific Science Center** の時系列を選択し、**[Show Max Humidity]\(最大湿度の表示\)** を選択します。

   ![選択された時系列と、[Show Max Humidity]\(最大湿度の表示\) メニューの選択][40]

1. **最大湿度**の時系列が 1 分の間隔サイズで開きます。 領域を選択して、範囲をフィルター処理します。 次に、右クリックし、**[Zoom]\(拡大\)** を選択し、時間枠内のイベントを分析します。

   ![選択された範囲と、ショートカット メニューの [Zoom]\(拡大\) コマンド][41]

1. 領域を選択してから右クリックして、イベントの詳細を表示することもできます。

   ![イベントの詳細なリスト][44]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。  

* デバイス シミュレーション アクセラレータを作成して使用する。
* Azure Time Series Insights プレビューの PAYG 環境を作成する。
* Azure Time Series Insights プレビュー環境をイベント ハブに接続する。
* ソリューション アクセラレータのサンプルを実行して、Azure Time Series Insights プレビュー環境にデータをストリーム配信する。
* データの基本的な分析を実行する。
* 時系列モデルの種類と階層を定義して、インスタンスに関連付ける。

Azure Time Series Insights プレビュー環境を自分で作成する方法がわかったので、Azure Time Series Insights の主要な概念について詳しく学習してください。

Azure Time Series Insights のストレージ構成について読む。

> [!div class="nextstepaction"]
> [Azure Time Series Insights プレビューのストレージとイングレス](./time-series-insights-update-storage-ingress.md)

時系列モデルの詳細を確認する。

> [!div class="nextstepaction"]
> [Azure Time Series Insights プレビューのデータ モデリング](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/device-one-accelerator.png
[2]: media/v2-update-provision/device-two-create.png
[3]: media/v2-update-provision/device-three-launch.png
[4]: media/v2-update-provision/device-four-iot-sim-page.png
[5]: media/v2-update-provision/device-five-params.png
[6]: media/v2-update-provision/device-seven-dashboard.png
[7]: media/v2-update-provision/device-six-listings.png
[8]: media/v2-update-provision/device-eight-portal.png

[9]: media/v2-update-provision/payg-one-azure.png
[10]: media/v2-update-provision/payg-two-create.png
[11]: media/v2-update-provision/payg-three-new.png
[12]: media/v2-update-provision/payg-four-add.png
[13]: media/v2-update-provision/payg-five-event-source.png
[14]: media/v2-update-provision/payg-six-review.png
[15]: media/v2-update-provision/payg-seven-deploy.png
[16]: media/v2-update-provision/payg-eight-environment.png
[17]: media/v2-update-provision/payg-nine-data-access.png
[18]: media/v2-update-provision/payg-ten-verify.png

[19]: media/v2-update-provision/analyze-one-portal.png
[20]: media/v2-update-provision/analyze-two-unparented.png
[21]: media/v2-update-provision/analyze-three-show-pressure.png
[22]: media/v2-update-provision/analyze-four-chart.png
[23]: media/v2-update-provision/analyze-five-chart.png
[24]: media/v2-update-provision/analyze-six-from.png
[25]: media/v2-update-provision/analyze-seven-change-from.png
[26]: media/v2-update-provision/analyze-eight-all.png

[27]: media/v2-update-provision/define-one-model.png
[28]: media/v2-update-provision/define-two-add.png
[29]: media/v2-update-provision/define-three-variable.png
[30]: media/v2-update-provision/define-four-avg.png
[31]: media/v2-update-provision/define-five-humidity.png
[32]: media/v2-update-provision/define-six-type.png
[33]: media/v2-update-provision/define-seven-hierarchy.png
[34]: media/v2-update-provision/define-eight-add-hierarchy.png
[35]: media/v2-update-provision/define-nine-created.png
[36]: media/v2-update-provision/define-ten-edit.png
[37]: media/v2-update-provision/define-eleven-chiller.png
[38]: media/v2-update-provision/define-twelve.png
[39]: media/v2-update-provision/define-thirteen-explore.png
[40]: media/v2-update-provision/define-fourteen-show-max.png
[41]: media/v2-update-provision/define-fifteen-filter.png
[42]: media/v2-update-provision/define-sixteen.png
[43]: media/v2-update-provision/define-seventeen.png
[44]: media/v2-update-provision/define-eighteen.png