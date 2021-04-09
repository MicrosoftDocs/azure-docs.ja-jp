---
title: チュートリアル:Gen2 の環境を設定する - Azure Time Series Insights Gen2 | Microsoft Docs
description: チュートリアル:Azure Time Series Insights Gen2 で環境を設定する方法について説明します。
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 02/25/2021
ms.custom: seodec18
ms.openlocfilehash: 76a33bdb773645c9e8f97a47b1378d813b165631
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/14/2021
ms.locfileid: "103464179"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-gen2-environment"></a>チュートリアル:Azure Time Series Insights Gen2 環境を設定する

このチュートリアルでは、Azure Time Series Insights Gen2 の "*従量課金制*" (PAYG) 環境を作成する手順について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * Azure Time Series Insights Gen2 環境を作成する。
> * Azure Time Series Insights Gen2 環境を IoT ハブに接続する。
> * ソリューション アクセラレータのサンプルを実行して、Azure Time Series Insights Gen2 環境にデータをストリーム配信する。
> * データの基本的な分析を実行する。
> * 時系列モデルの種類と階層を定義して、インスタンスに関連付ける。

>[!TIP]
> [IoT ソリューション アクセラレータ](https://www.azureiotsolutions.com/Accelerators)によって、カスタム IoT ソリューションの開発を高速化するために使用できる、エンタープライズ レベルのあらかじめ構成されたソリューションが提供されます。

お持ちでない場合は、[無料の Azure サブスクリプション](https://azure.microsoft.com/free/)にサインアップしてください。

## <a name="prerequisites"></a>前提条件

* 少なくとも、Azure サブスクリプションに対する **共同作成者** ロールが必要です。 詳細については、「[Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)」を参照してください。

* [Azure portal](#create-an-azure-time-series-insights-gen2-environment) または [CLI](how-to-create-environment-using-cli.md) を使用して環境を作成します。

## <a name="create-a-device-simulation"></a>デバイス シミュレーションを作成する

このセクションでは、Azure IoT Hub のインスタンスにデータを送信する、3 つのシミュレートされたデバイスを作成します。

1. [Azure IoT ソリューション アクセラレータのページ](https://www.azureiotsolutions.com/Accelerators)に移動します。 Azure アカウントを使用してサインインし、 **[デバイス シミュレーション]** を選択します。

   [![Azure IoT ソリューション アクセラレータのページ。](media/tutorial-set-up-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-set-up-environment/iot-solution-accelerators-landing-page.png#lightbox)

1. 下にスクロールして、[概要](https://github.com/Azure/azure-iot-pcs-device-simulation#overview)と[作業の開始](https://github.com/Azure/azure-iot-pcs-device-simulation#getting-started)に関するセクションを参照してください。

1. 作業の開始セクションにある[デプロイ手順](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/deployment/README.md)に従います。

   このプロセスが完了するまでに最大で 20 分かかる場合があります。

1. デプロイが完了すると、シミュレーションの URL が提供されます。 後で戻ってくるので、この Web ページは開いたままにしておきます。

   >[!IMPORTANT]
   > まだソリューション アクセラレータは入力しないでください。 後で戻ってくるので、この Web ページは開いたままにしておきます。

   [![デバイス シミュレーション ソリューションのプロビジョニング完了。](media/tutorial-set-up-environment/iot-solution-accelerator-ready.png)](media/tutorial-set-up-environment/iot-solution-accelerator-ready.png#lightbox)

1. ここで、Azure portal で新しく作成されたリソースを検査します。 **[リソース グループ]** ページで、ARM テンプレート パラメーター ファイルで指定した `solutionName` を使用して新しいリソース グループが作成されたことを確認します。 デバイス シミュレーション用に作成されたリソースをメモします。

   [![デバイス シミュレーションのリソース。](media/tutorial-set-up-environment/device-sim-solution-resources.png)](media/tutorial-set-up-environment/device-sim-solution-resources.png#lightbox)

## <a name="create-an-azure-time-series-insights-gen2-environment"></a>Azure Time Series Insights Gen2 環境を作成する

このセクションでは、Azure Time Series Insights Gen2 環境を作成し、[Azure portal](https://portal.azure.com/) を使用して、IoT ソリューション アクセラレータによって作成された IoT ハブにこれを接続する方法について説明します。

1. Azure サブスクリプション アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左上隅の **[+ リソースの作成]** を選択します。
1. **[モノのインターネット (IoT)]** カテゴリを選択し、 **[Time Series Insights]** を選択します。

   [![Time Series Insights 環境リソースを選択する。](media/tutorial-set-up-environment/create-new-environment.png)](media/tutorial-set-up-environment/create-new-environment.png#lightbox)

1. **[Time Series Insights 環境の作成]** ウィンドウの **[基本]** タブで、次のパラメーターを設定します。

    | パラメーター | アクション |
    | --- | ---|
    | **環境名** | Azure Time Series Insights Gen2 環境の一意名を入力します。 |
    | **サブスクリプション** | Azure Time Series Insights Gen2 環境を作成するサブスクリプションを入力します。 ベスト プラクティスとしては、デバイス シミュレーターによって作成される他の IoT リソースと同じサブスクリプションを使用します。 |
    | **リソース グループ** | Azure Time Series Insights Gen2 環境リソースに既存のリソース グループを選択するか、新しいリソース グループを作成します。 リソース グループとは、Azure リソース用のコンテナーです。 ベスト プラクティスとしては、デバイス シミュレーターによって作成される他の IoT リソースと同じリソース グループを使用します。 |
    | **場所** | Azure Time Series Insights Gen2 環境のデータ センター リージョンを選択します。 待ち時間の増加を防ぐために、デバイス シミュレーターによって作成された IoT ハブと同じリージョン内に Azure Time Series Insights Gen2 環境を作成することをお勧めします。 |
    | **レベル** |  **[Gen2 (L1)]** を選択します。 これは、Azure Time Series Insights Gen2 製品の SKU です。 |
    | **Time Series ID property name\(時系列 ID のプロパティ名\)** | 時系列インスタンスを一意に識別する値を含むプロパティの名前を入力します。 時系列 ID として **[プロパティ名]** ボックスに入力する値は後で変更できません。 このチュートリアルでは、「**_iothub-connection-device-id_**」と入力します。複合時系列 ID を含む、時系列 ID の詳細については、「[時系列 ID の選択のベスト プラクティス](./how-to-select-tsid.md)」を参照してください。 |
    | **Storage account name \(ストレージ アカウント名\)** | 新しいストレージ アカウント用のグローバルな一意名を入力します。|
    | **ストレージ アカウントの種類** | 新しいストレージ アカウントのストレージの種類を選択します。 StorageV2 をお勧めします|
    | **ストレージ アカウントのレプリケーション** | 新しいストレージ アカウントのストレージの種類を選択します。 場所の選択に基づいて、LRS、GRS、ZRS から選択できます。 このチュートリアルでは、LRS を選択できます|
    | **階層構造の名前空間** |このオプションは、ストレージの種類に [StorageV2] を選択すると選択できます。 既定では無効になっています。 このチュートリアルでは、既定の *[無効]* 状態のままにしておくことができます|
    |**Enable warm store (\ウォーム ストアを有効にする\)**|**[はい]** を選択して、ウォーム ストアを有効にします。 この設定は無効にして、環境が作成された後に再度有効にすることもできます。 |
    |**Data retention (in days) \(データ保有期間 (日数)\)**|既定のオプションである 7 日を選択します。 |

    [![新しい Azure Time Series Insights 環境の構成。](media/tutorial-set-up-environment/environment-configuration.png)](media/tutorial-set-up-environment/environment-configuration.png#lightbox)
    [![新しい Azure Time Series Insights 環境の構成 (続き)。](media/tutorial-set-up-environment/environment-configuration2.png)](media/tutorial-set-up-environment/environment-configuration2.png#lightbox)

1. **[Next:Event Source]\(次へ: イベント ソース\)** をクリックします。

   [![環境に合わせてタイム シリーズ ID を構成します。](media/tutorial-set-up-environment/time-series-id-selection.png)](media/tutorial-set-up-environment/time-series-id-selection.png#lightbox)

1. **[Event Source]\(イベント ソース\)** タブで、次のパラメーターを設定します。

   | パラメーター | アクション |
   | --- | --- |
   | **Create an Event Source? \(イベント ソースを作成しますか\)** | **[はい]** を選択します。|
   | **ソースの種類** | **[IoT Hub]** を選択します。 |
   | **名前** | イベント ソース名に一意の値を入力します。 |
   | **ハブを選択** | **[既存のものを選択]** を選択します。 |
   | **サブスクリプション** | デバイス シミュレーターに使用したサブスクリプションを選択します。 |
   | **IoT Hub name (IoT ハブの名前)** | デバイス シミュレーター用に作成した IoT ハブの名前を選択します。 |
   | **IoT Hub access policy (IoT ハブのアクセス ポリシー)** | **[iothubowner]** を選びます。 |
   | **IoT Hub コンシューマー グループ** | **[New]\(新規\)** を選択し、一意の名前を入力します。次に、 **[+ Add]\(追加\)** を選択します。 コンシューマー グループは、Azure Time Series Insights Gen2 で一意の値である必要があります。 |
   | **Timestamp property \(タイムスタンプのプロパティ\)** | この値は、受信したテレメトリ データで **タイムスタンプ** プロパティを識別するために使用されます。 このチュートリアルでは、このボックスを空のままにします。 このシミュレーターでは、IoT ハブからの受信タイムスタンプを使用します。これが Azure Time Series Insights Gen2 の既定の設定です。 |

1. **[確認および作成]** を選択します。

   [![作成した IoT ハブをイベント ソースとして構成します。](media/tutorial-set-up-environment/configure-event-source.png)](media/tutorial-set-up-environment/configure-event-source.png#lightbox)

1. **[確認および作成]** を選択します。

    [![[Review + create]\(確認と作成\) ページと [Create]\(作成\) ボタン。](media/tutorial-set-up-environment/environment-confirmation.png)](media/tutorial-set-up-environment/environment-confirmation.png#lightbox)

    デプロイの状態を確認できます。

    [![デプロイが完了したことを示す通知。](media/tutorial-set-up-environment/deployment-notification.png)](media/tutorial-set-up-environment/deployment-notification.png#lightbox)

1. デプロイの詳細を展開します。

## <a name="stream-data"></a>データをストリーム配信する

Azure Time Series Insights Gen2 環境をデプロイできたので、分析のためにデータのストリーム配信を開始します。

1. ソリューション アクセラレータのデプロイが完了すると、URL が付与されます。

1. URL をクリックして、デバイスのシミュレーションを開始します。

1. **[+ New simulation]\(+ 新しいシミュレーション\)** を選択します。

    1. **[Simulation setup]\(シミュレーションのセットアップ\)** ページの読み込み後、次の必須パラメーターを入力します。

        | パラメーター | アクション |
        | --- | --- |
        | **名前** | シミュレーターの一意名を入力します。 |
        | **説明** | 定義を入力します。 |
        | **[Simulation duration]\(シミュレーション期間\)** | **[Run indefinitely]\(無期限に実行する\)** に設定します。 |
        | **[デバイス モデル]** | **[+ Add a device type]\(+ デバイスの種類の追加\)** をクリックします <br />**Name**:「**Elevator**」と入力します。 <br />**[Amount]\(量\)** :「**3**」と入力します。 <br /> 他の既定値はそのまま使用します |
        | **[Target IoT Hub]\(IoT Hub をターゲットにする\)** | **[Use pre-provisioned IoT Hub]\(事前プロビジョニングされている IoT Hub を使用する\)** に設定します。 |

        [![パラメーターを構成して起動します。](media/tutorial-set-up-environment/launch-solution-accelerator.png)](media/tutorial-set-up-environment/launch-solution-accelerator.png#lightbox)

    1. **[シミュレーションの開始]** を選択します。 デバイス シミュレーション ダッシュボードに、 **[アクティブなデバイス]** と **[Total messages]\(メッセージの総数\)** が表示されます。

        [![Azure IoT シミュレーション ダッシュボード。](media/tutorial-set-up-environment/see-active-devices-and-messages.png)](media/tutorial-set-up-environment/see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>データを分析する

このセクションでは、[Azure Time Series Insights Gen2 エクスプローラー](./concepts-ux-panels.md)を使用して、時系列データの基本的な分析を実行します。

1. [Azure portal](https://portal.azure.com/) のリソース ページで URL を選択して、Azure Time Series Insights Gen2 エクスプローラーに移動します。

    [![Azure Time Series Insights Gen2 エクスプローラーの URL。](media/tutorial-set-up-environment/select-explorer-url.png)](media/tutorial-set-up-environment/select-explorer-url.png#lightbox)

1. Azure Time Series Insights Gen2 エクスプローラーで、画面の上部にバーが表示されます。 これは、可用性ピッカーです。 少なくとも 2 つの 2 m が選択されていることを確認し、必要に応じて、ピッカー ハンドルを選択して左右にドラッグすることで時間枠を広げます。

1. **[Time Series Instances]\(時系列インスタンス\)** が左側に表示されます。

    [![親のないインスタンスの一覧。](media/tutorial-set-up-environment/explorer-unparented-instances.png)](media/tutorial-set-up-environment/explorer-unparented-instances.png#lightbox)

1. 最初の時系列インスタンスを選択します。 次に、 **[Show temperature]\(気温の表示\)** を選択します。

    [![選択されている時系列インスタンスと、平均気温を表示するメニュー コマンド。](media/tutorial-set-up-environment/select-instance-and-temperature.png)](media/tutorial-set-up-environment/select-instance-and-temperature.png#lightbox)

    時系列グラフが表示されます。 **[間隔]** を **[30s]\(30 秒\)** に変更します。

1. 次のグラフに示すように、他の 2 つの時系列インスタンスについて前の手順を繰り返して、3 つすべてが表示されるようにします。

    [![すべての時系列のグラフ。](media/tutorial-set-up-environment/explorer-add-three-instances.png)](media/tutorial-set-up-environment/explorer-add-three-instances.png#lightbox)

1. 右上にある時間範囲ピッカーを選択します。 ここでは、特定の開始時刻と終了時刻をミリ秒単位で選択したり、**過去 30 分** などの事前構成済みオプションから選択したりできます。 既定のタイム ゾーンを変更することもできます。

    [![時間の範囲を過去 30 分に設定します。](media/tutorial-set-up-environment/explorer-thirty-minute-time-range.png)](media/tutorial-set-up-environment/explorer-thirty-minute-time-range.png#lightbox)

    **[Last 30 minutes]\(過去 30 分\)** のソリューション アクセラレータの進行状況が、Azure Time Series Insights Gen2 エクスプローラーに表示されるようになります。

## <a name="define-and-apply-a-model"></a>モデルを定義して適用する

このセクションでは、モデルを適用してデータを構造化します。 モデルを完成させるには、タイプ、階層、およびインスタンスを定義します。 データ モデリングの詳細については、[時系列モデル](./concepts-model-overview.md)に関するページを参照してください。

1. エクスプローラーで、 **[モデル]** タブを選択します。

   [![エクスプローラーで [モデル] タブを表示します。](media/tutorial-set-up-environment/select-model-view.png)](media/tutorial-set-up-environment/select-model-view.png#lightbox)

   **[Types]\(種類\)** タブで、 **[+ Add]\(追加\)** を選択します。

1. 次のパラメーターを入力します。

    | パラメーター | アクション |
    | --- | ---|
    | **名前** | 「**Elevator**」と入力します |
    | **説明** | 「**This is a type definition for Elevator (これはエレベーターのタイプ定義です)** 」と入力します |

1. 次に、 **[変数]** タブを選択します。

    1. **[+ 変数の追加]** を選択し、Elevator タイプの最初の変数に次の値を入力します。 合計 3 つの変数を作成します。

        | パラメーター | アクション |
        | --- | --- |
        | **名前** | 「**Avg Temperature**」と入力します。 |
        | **種類** | **[数値]** を選択します |
        | **Value** | プリセットから選択します。 **[temperature (Double)]\(温度 (Double)\)** を選択します。 <br /> 注:Azure Time Series Insights Gen2 でイベントの受信が開始されてから **[値]** が自動的に入力されるまで、数分かかる場合があります。|
        | **Aggregation Operation (集計操作)** | **[詳細オプション]** を展開します。 <br /> **[AVG]** を選択します。 |

    1. **[適用]** を選択します。 **[+ 変数の追加]** を選択し、次の値を設定します。

        | パラメーター | アクション |
        | --- | --- |
        | **名前** | 「**Avg Vibration**」と入力します。 |
        | **種類** | **[数値]** を選択します |
        | **Value** | プリセットから選択します。 **[vibration (Double)]\(振動 (Double)\)** を選択します。 <br /> 注:Azure Time Series Insights Gen2 でイベントの受信が開始されてから **[値]** が自動的に入力されるまで、数分かかる場合があります。|
        | **Aggregation Operation (集計操作)** | **[詳細オプション]** を展開します。 <br /> **[AVG]** を選択します。 |

    1. **[適用]** を選択します。 **[+ 変数の追加]** を選択し、3 つ目の最後の変数に次の値を設定します。

        | パラメーター | アクション |
        | --- | --- |
        | **名前** | 「**Floor**」と入力します。 |
        | **種類** | **[カテゴリ別]** を選択します |
        | **Value** | プリセットから選択します。 **[Floor (Double)]\(フロア (Double)\)** を選択します。 <br /> 注:Azure Time Series Insights Gen2 でイベントの受信が開始されてから **[値]** が自動的に入力されるまで、数分かかる場合があります。|
        | **Categories (カテゴリ)** | <span style="text-decoration: underline">ラベル</span>  - <span style="text-decoration: underline">値</span> <br /> Lower:1,2,3,4 <br /> Middle:5,6,7,8,9 <br /> Upper:10,11,12,13,14,15 |
        | **既定のカテゴリ** | 「**Unknown**」と入力します |

        [![型の変数を追加します。](media/tutorial-set-up-environment/add-type-variables.png)](media/tutorial-set-up-environment/add-type-variables.png#lightbox)

    1. **[適用]** を選択します。
    1. **[保存]** を選択します。 3 つの変数が作成されて表示されます。

        [![型を追加した後は、[Model]\(モデル\) ビューで確認します。](media/tutorial-set-up-environment/add-type-and-view.png)](media/tutorial-set-up-environment/add-type-and-view.png#lightbox)

1. **[階層]** タブを選択します。次に、 **[+ 追加]** を選択します。

   1. **[Edit Hierarchy]\(階層の編集\)** ウィンドウで、次のパラメーターを設定します。

        | パラメーター | アクション |
        | --- | ---|
        | **名前** | 「**Location Hierarchy**」と入力します。 |
        |**レベル**| 最初のレベルの名前として「**Country**」を入力します<br />**[+ レベルの追加]** を選択します<br />2 つ目のレベルとして「**City**」を入力し、 **[+ レベルの追加]** を選択します<br />3 つ目の最後のレベルの名前として「**Building**」を入力します |

   1. **[保存]** を選択します。

        [![[Model]\(モデル\) ビューで新しい階層を表示します。](media/tutorial-set-up-environment/add-hierarchy-and-view.png)](media/tutorial-set-up-environment/add-hierarchy-and-view.png#lightbox)

1. **[インスタンス]** に移動します。

    1. 右端の **[操作]** にある鉛筆アイコンを選択し、次の値を使用して最初のインスタンスを編集します。

        | パラメーター | アクション |
        | --- | --- |
        | **Type** | **[Elevator]** を選択します。 |
        | **名前** | 「**Elevator 1**」と入力します|
        | **説明** | 「**Instance for Elevator 1**」と入力します |

    1. **[インスタンス フィールド]** に移動し、次の値を入力します。

        | パラメーター | アクション |
        | --- | --- |
        | **Hierarchies (階層)** | **[Location Hierarchy]\(場所の階層\)** を選択します |
        | **Country (国)** | 「**USA**」と入力します |
        | **City (市)** | 「**Seattle**」と入力します |
        | **Building (建物)** | 「**Space Needle**」と入力します |

    1. **[保存]** を選択します。

1. 次の値を使用して、他の 2 つのインスタンスで前の手順を繰り返します。

    **Elevator 2 の場合:**

    | パラメーター | アクション |
    | --- | --- |
    | **Type** | **[Elevator]** を選択します。 |
    | **名前** | 「**Elevator 2**」と入力します|
    | **説明** | 「**Instance for Elevator 2**」と入力します |
    | **Hierarchies (階層)** | **[Location Hierarchy]\(場所の階層\)** を選択します |
    | **Country (国)** | 「**USA**」と入力します |
    | **City (市)** | 「**Seattle**」と入力します |
    | **Building (建物)** | 「**Pacific Science Center**」と入力します |

    **Elevator 3 の場合:**

    | パラメーター | アクション |
    | --- | --- |
    | **Type** | **[Elevator]** を選択します。 |
    | **名前** | 「**Elevator 3**」と入力します|
    | **説明** | 「**Instance for Elevator 3**」と入力します |
    | **Hierarchies (階層)** | **[Location Hierarchy]\(場所の階層\)** を選択します |
    | **Country (国)** | 「**USA**」と入力します |
    | **City (市)** | 「**New York**」と入力します |
    | **Building (建物)** | 「**Empire State Building**」と入力します |

    [![更新されたインスタンスを表示します。](media/tutorial-set-up-environment/iot-solution-accelerator-instances.png)](media/tutorial-set-up-environment/iot-solution-accelerator-instances.png#lightbox)

1. **[分析]** タブに戻り、グラフ ペインを表示します。 **[Location Hierarchy]\(場所の階層\)** で、すべての階層レベルを展開して時系列インスタンスを表示します。

    [![すべての階層をグラフ ビューで表示します。](media/tutorial-set-up-environment/iot-solution-accelerator-view-hierarchies.png)](media/tutorial-set-up-environment/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. **Pacific Science Center** で時系列インスタンス **[Elevator 2]** を選択してから、 **[Show Average Temperature]\(平均温度の表示\)** を選択します。

1. 同じインスタンス **[Elevator 2]** について、 **[Show Floor]\(Floor の表示\)** を選択します。

    カテゴリ変数を使用すると、エレベーターが上方、下方、および中間のフロアで費やした時間を調べることができます。

    [![階層とデータを使用して、Elevator 2 を視覚化します。](media/tutorial-set-up-environment/iot-solution-accelerator-elevator-two.png)](media/tutorial-set-up-environment/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

チュートリアルを完了したので、作成したリソースをクリーンアップします。

1. [Azure portal](https://portal.azure.com) の左側のメニューにある **[すべてのリソース]** を選択し、目的の Azure Time Series Insights Gen2 リソース グループを見つけます。
1. **[削除]** を選択してリソース グループ全体 (およびその中に含まれるすべてのリソース) を削除するか、各リソースを個別に削除します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

* デバイス シミュレーション アクセラレータを作成して使用する。
* Azure Time Series Insights Gen2 PAYG 環境を作成する。
* Azure Time Series Insights Gen2 環境を IoT ハブに接続する。
* ソリューション アクセラレータのサンプルを実行して、Azure Time Series Insights Gen2 環境にデータをストリーム配信する。
* データの基本的な分析を実行する。
* 時系列モデルの種類と階層を定義して、インスタンスに関連付ける。

Azure Time Series Insights Gen2 環境を自分で作成する方法がわかったので、Azure Time Series Insights Gen2 の主要な概念について詳しく学習してください。

Azure Time Series Insights Gen2 のインジェストについて読む。

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 のデータ インジェストの概要](./concepts-ingestion-overview.md)

Azure Time Series Insights Gen2 のストレージについて読む。

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 のデータ ストレージ](./concepts-storage.md)

時系列モデルの詳細を確認する。

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 のデータ モデリング](./concepts-model-overview.md)

Power BI への環境の接続について詳細を確認する。

> [!div class="nextstepaction"]
> [Power BI の Azure Time Series Insights Gen2 からのデータを視覚化する](./how-to-connect-power-bi.md)
