---
title: チュートリアル - Azure IoT Central でデータをエクスポートし、分析情報を視覚化する
description: このチュートリアルでは、IoT Central からデータをエクスポートし、Power BI ダッシュボードで分析情報を視覚化する方法について説明します。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: dobett
author: dominicbetts
ms.date: 11/12/2019
ms.openlocfilehash: 2695b34745ff02d55f18cebbe87a468f807ca77a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99831690"
---
# <a name="tutorial-export-data-from-azure-iot-central-and-visualize-insights-in-power-bi"></a>チュートリアル:Azure IoT Central からデータをエクスポートし、Power BI で分析情報を視覚化する


前の 2 つのチュートリアルでは、**ストア内の分析 - チェックアウト** アプリケーション テンプレートを使用して、IoT Central アプリケーションを作成し、カスタマイズしました。 このチュートリアルでは、デバイスから収集されたテレメトリをエクスポートするように、IoT Central アプリケーションを構成します。 その後、Power BI を使用して、店のマネージャーがテレメトリから得られた分析情報を視覚化するためのカスタム ダッシュボードを作成します。

このチュートリアルでは、次の内容を学習します。
> [!div class="checklist"]
> * テレメトリをイベント ハブにエクスポートするように IoT Central アプリケーションを構成します。
> * Logic Apps を使用して、イベント ハブから Power BI ストリーミング データセットにデータを送信します。
> * ストリーミング データセットのデータを視覚化するための Power BI ダッシュボードを作成します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

* 前の 2 つのチュートリアル「[Azure IoT Central でストア内分析アプリケーションを作成する](./tutorial-in-store-analytics-create-app.md)」と「[Azure IoT Central でのオペレーター ダッシュボードのカスタマイズと、デバイスの管理](./tutorial-in-store-analytics-customize-dashboard.md)」を完了していること。
* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
* Power BI アカウント Power BI アカウントを持っていない場合は、始める前に[無料の Power BI Pro 試用版](https://app.powerbi.com/signupredirect?pbi_source=web)にサインアップしてください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

イベント ハブとロジック アプリを作成する前に、リソース グループを作成して管理する必要があります。 そのリソース グループは、**ストア内の分析 - チェックアウト** IoT Central アプリケーションと同じ場所に存在する必要があります。 リソース グループを作成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側のナビゲーションで、 **[リソース グループ]** を選択します。 その後、 **[追加]** を選択します。
1. **[サブスクリプション]** で、IoT Central アプリケーションの作成に使用した Azure サブスクリプションの名前を選択します。
1. **[リソース グループ]** の名前として、「_retail-store-analysis_」と入力します。
1. **[リージョン]** で、IoT Central アプリケーションに対して選択したのと同じリージョンを選択します。
1. **[確認および作成]** を選択します。
1. **[確認および作成]** ページで、 **[作成]** を選択します。

これで、サブスクリプションに **retail-store-analysis** という名前のリソース グループが作成されました。

## <a name="create-an-event-hub"></a>イベント ハブの作成

テレメトリをエクスポートするようにリテール監視アプリケーションを構成する前に、エクスポートされたデータを受け取るイベント ハブを作成する必要があります。 次の手順では、イベント ハブを作成する方法を示します。

1. Azure portal で、画面の左上にある **[リソースの作成]** を選択します。
1. **[Marketplace を検索]** に「_Event Hubs_」と入力し、**Enter** キーを押します。
1. **[Event Hubs]** ページで **[作成]** を選択します。
1. **[名前空間の作成]** ページで、次の手順のようにします。
    * 名前空間の一意の名前を入力します (_yourname-retail-store-analysis_ など)。 この名前を使用できるかどうかが確認されます。
    * **[Basic]** 価格レベルを選択します。
    * IoT Central アプリケーションの作成に使用したものと同じ **[サブスクリプション]** を選択します。
    * **retail-store-analysis** リソース グループを選択します。
    * IoT Central アプリケーションに使用したものと同じ場所を選択します。
    * **［作成］** を選択します システムによってリソースが完全にプロビジョニングされるまで、数分間待たなければならない場合があります。
1. ポータルで、**retail-store-analysis** リソース グループに移動します。 デプロイが完了するまで待ちます。 場合によっては、 **[最新の情報に更新]** を選択してデプロイの状態を更新する必要があります。 また、 **[通知]** で、イベント ハブ名前空間の作成の状態を確認することもできます。
1. **retail-store-analysis** リソース グループで、 **[Event Hubs 名前空間]** を選択します。 ポータルで **Event Hubs 名前空間** のホーム ページが表示されます。

**Event Hubs 名前空間** が作成されたので、IoT Central アプリケーションで使用する **イベント ハブ** を作成できます。

1. ポータルの **Event Hubs 名前空間** のホーム ページで、 **[+ イベント ハブ]** を選択します。
1. **[イベント ハブの作成]** ページで、名前として「_store-telemetry_」と入力し、 **[作成]** を選択します。

これで、IoT Central アプリケーションからのデータ エクスポートを構成するときに使用できるイベント ハブが作成されました。

![イベント ハブ](./media/tutorial-in-store-analytics-visualize-insights/event-hub.png)

## <a name="configure-data-export"></a>データのエクスポートを構成する

イベント ハブを作成したので、接続されているデバイスからテレメトリをエクスポートするように、**ストア内の分析 - チェックアウト** アプリケーションを構成できます。 次の手順では、エクスポートを構成する方法を示します。

1. **ストア内の分析 - チェックアウト** IoT Central アプリケーションにサインインします。
1. 左側のウィンドウで、 **[データのエクスポート]** を選択します。
1. **[新規] > [Azure Event Hubs]** を選択します。
1. **[表示名]** として「_テレメトリのエクスポート_」と入力します。
1. お使いの **[Event Hubs 名前空間]** を選択します。
1. **store-telemetry** イベント ハブを選択します。
1. **[エクスポートするデータ]** セクションで、 **[デバイス]** と **[デバイス テンプレート]** をオフにします。
1. **[保存]** を選択します。

データのエクスポートでイベント ハブへのテレメトリの送信が開始されるまで、数分かかる場合があります。 **[データのエクスポート]** ページで、エクスポートの状態を確認できます。

![継続的データ エクスポート構成](./media/tutorial-in-store-analytics-visualize-insights/export-configuration.png)

## <a name="create-the-power-bi-datasets"></a>Power BI のデータセットを作成する

Power BI ダッシュボードには、リテール監視アプリケーションからのデータが表示されます。 このソリューションでは、Power BI ダッシュボードのデータ ソースとして Power BI ストリーミング データセットを使用します。 このセクションでは、ロジック アプリでイベント ハブからのデータを転送できるように、ストリーミング データセットのスキーマを定義します。 次の手順では、環境センサー用の 2 つのストリーミング データセットと、混雑状況センサー用の 1 つのストリーミング データセットを作成する方法について説明します。

1. **Power BI** アカウントにサインインします。
1. **[ワークスペース]** を選択し、 **[ワークスペースの作成]** を選択します。
1. **[ワークスペースの作成]** ページで、 **[ワークスペース名]** として「_In-store analytics - checkout_」と入力します。
1. **[In-store analytics - checkout ワークスペースへようこそ]** ページの一番下までスクロールし、 **[スキップ]** を選択します。
1. ワークスペースのページで、 **[作成] > [ストリーミング データセット]** を選択します。
1. **[新しいストリーミング データセット]** ページで **[API]** を選択し、 **[次へ]** を選択します。
1. **[データセット名]** として「_Zone 1 sensor_」と入力します。
1. **[ストリームからの値]** に、次の表の 3 つの値を入力します。

    | 値の名前  | 値の型 |
    | ----------- | ---------- |
    | Timestamp   | DateTime   |
    | 湿度    | Number     |
    | 気温 | Number     |

1. **[履歴データの解析]** をオンにします。
1. **[作成]** を選択し、 **[完了]** を選択します。
1. **Zone 1 sensor** ストリーミング データセットと同じスキーマおよび設定を使用して、**Zone 2 sensor** という名前の別のストリーミング データセットを作成します。

これで、2 つのストリーミング データセットが作成されます。 ロジック アプリでは、**ストア内の分析 - チェックアウト** アプリケーションに接続された 2 つの環境センサーからのテレメトリが、これら 2 つのデータセットにルーティングされます。

![ゾーン データセット](./media/tutorial-in-store-analytics-visualize-insights/dataset-1.png)

Power BI ではストリーミング データにフィルターを適用できないため、このソリューションでは、センサーごとに 1 つのストリーミング データセットを使用します。

混雑状況テレメトリ用のストリーミング データセットも必要です。

1. ワークスペースのページで、 **[作成] > [ストリーミング データセット]** を選択します。
1. **[新しいストリーミング データセット]** ページで **[API]** を選択し、 **[次へ]** を選択します。
1. **[データセット名]** として「_Occupancy sensor_」と入力します。
1. **[ストリームからの値]** に、次の表の 5 つの値を入力します。

    | 値の名前     | 値の型 |
    | -------------- | ---------- |
    | Timestamp      | DateTime   |
    | Queue Length 1 | Number     |
    | Queue Length 2 | Number     |
    | Dwell Time 1   | Number     |
    | Dwell Time 2   | Number     |

1. **[履歴データの解析]** をオンにします。
1. **[作成]** を選択し、 **[完了]** を選択します。

これで、シミュレートされた混雑状況センサーの値を格納する 3 番目のストリーミング データセットが作成されました。 このセンサーでは、店内の 2 つのレジの待ち行列の長さと、これらの列で客が待っている時間の長さが報告されます。

![混雑状況データセット](./media/tutorial-in-store-analytics-visualize-insights/dataset-2.png)

## <a name="create-a-logic-app"></a>ロジック アプリを作成します

このソリューションでは、ロジック アプリによってイベント ハブからテレメトリを読み取り、データを解析し、作成した Power BI ストリーミング データセットにそれを送信します。

ロジック アプリを作成する前に、「[Azure IoT Central でストア内分析アプリケーションを作成する](./tutorial-in-store-analytics-create-app.md)」チュートリアルで IoT Central アプリケーションに接続した 2 つの RuuviTag センサーのデバイス ID が必要です。

1. **ストア内の分析 - チェックアウト** IoT Central アプリケーションにサインインします。
1. 左側のウィンドウで **[デバイス]** を選択します。 次に、**RuuviTag** を選択します。
1. **[デバイス ID]** を記録しておきます。 次のスクリーンショットでは、ID は **f5dcf4ac32e8** と **e29ffc8d5326** です。

    ![デバイス ID](./media/tutorial-in-store-analytics-visualize-insights/device-ids.png)

次の手順では、Azure portal でロジック アプリを作成する方法を示します。

1. [Azure portal](https://portal.azure.com) にサインインし、画面の左上にある **[リソースの作成]** を選択します。
1. **[Marketplace を検索]** に「_Logic App_」と入力し、**Enter** キーを押します。
1. **[Logic App]** ページで、 **[作成]** を選択します。
1. **ロジック アプリ** の作成ページで次のようにします。
    * ロジック アプリの一意の名前を入力します (_yourname-retail-store-analysis_ など)。
    * IoT Central アプリケーションの作成に使用したものと同じ **[サブスクリプション]** を選択します。
    * **retail-store-analysis** リソース グループを選択します。
    * IoT Central アプリケーションに使用したものと同じ場所を選択します。
    * **［作成］** を選択します システムによってリソースが完全にプロビジョニングされるまで、数分間待たなければならない場合があります。
1. Azure portal で新しいロジック アプリに移動します。
1. **[Logic Apps デザイナー]** ページで、下にスクロールして **[空のロジック アプリ]** を選択します。
1. **[コネクタとトリガーを検索する]** に、「_Event Hubs_」と入力します。
1. **[トリガー]** で、 **[When events are available in Event Hub]\(イベント ハブでイベントを使用できるとき\)** を選択します。
1. **[接続名]** として「_Store telemetry_」と入力し、使用する **[Event Hubs 名前空間]** を選択します。
1. **RootManageSharedAccess** ポリシーを選択し、 **[作成]** を選択します。
1. **[When events are available in Event Hub]\(イベント ハブでイベントを使用できるとき\)** アクションで、次のようにします。
    * **[イベント ハブ名]** で、**store-telemetry** を選択します。
    * **[コンテンツ タイプ]** で、 **[application/json]** を選択します。
    * **[間隔]** を 3 に設定し、 **[頻度]** を秒に設定します
1. **[保存]** を選択して、ロジック アプリを保存します。

ロジックをロジック アプリの設計に追加するには、 **[コード ビュー]** を選択します。

1. `"actions": {},` を次の JSON に置き換えます。 2 つのプレースホルダー `[YOUR RUUVITAG DEVICE ID 1]` と `[YOUR RUUVITAG DEVICE ID 2]` を、記録した 2 つの RuuviTag デバイスの ID に置き換えます。

    ```json
    "actions": {
        "Initialize_Device_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "DeviceID",
                        "type": "String"
                    }
                ]
            },
            "runAfter": {},
            "type": "InitializeVariable"
        },
        "Initialize_Interface_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "InterfaceID",
                        "type": "String",
                        "value": "Other"
                    }
                ]
            },
            "runAfter": {
                "Initialize_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "InitializeVariable"
        },
        "Parse_Properties": {
            "inputs": {
                "content": "@triggerBody()?['Properties']",
                "schema": {
                    "properties": {
                        "iothub-connection-auth-generation-id": {
                            "type": "string"
                        },
                        "iothub-connection-auth-method": {
                            "type": "string"
                        },
                        "iothub-connection-device-id": {
                            "type": "string"
                        },
                        "iothub-enqueuedtime": {
                            "type": "string"
                        },
                        "iothub-interface-name": {
                            "type": "string"
                        },
                        "iothub-message-source": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "integer"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Parse_Telemetry": {
            "inputs": {
                "content": "@triggerBody()?['ContentData']",
                "schema": {
                    "properties": {
                        "DwellTime1": {
                            "type": "number"
                        },
                        "DwellTime2": {
                            "type": "number"
                        },
                        "count1": {
                            "type": "number"
                        },
                        "count2": {
                            "type": "number"
                        },
                        "humidity": {
                            "type": "number"
                        },
                        "temperature": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Set_Device_ID_variable": {
            "inputs": {
                "name": "DeviceID",
                "value": "@body('Parse_Properties')?['iothub-connection-device-id']"
            },
            "runAfter": {
                "Parse_Properties": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Set_Interface_ID_variable": {
            "inputs": {
                "name": "InterfaceID",
                "value": "@body('Parse_Properties')?['iothub-interface-name']"
            },
            "runAfter": {
                "Set_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Switch_by_DeviceID": {
            "cases": {
                "Occupancy": {
                    "actions": {
                        "Switch_by_InterfaceID": {
                            "cases": {
                                "Dwell_Time_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_v2_1l0"
                                },
                                "People_Count_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_iv"
                                }
                            },
                            "default": {
                                "actions": {}
                            },
                            "expression": "@variables('InterfaceID')",
                            "runAfter": {},
                            "type": "Switch"
                        }
                    },
                    "case": "Occupancy"
                },
                "Zone 2 environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 2]"
                },
                "Zone_1_environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 1]"
                }
            },
            "default": {
                "actions": {}
            },
            "expression": "@variables('DeviceID')",
            "runAfter": {
                "Parse_Telemetry": [
                    "Succeeded"
                ],
                "Set_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "Switch"
        }
    },
    ```

1. **[保存]** を選択した後 **[デザイナー]** を選択して、追加したロジックのビジュアル バージョンを表示します。

    ![ロジック アプリの設計](./media/tutorial-in-store-analytics-visualize-insights/logic-app.png)

1. **[Switch by DeviceID]\(DeviceID で切り替え\)** を選択して、アクションを展開します。 次に、 **[Zone 1 environment]\(ゾーン 1 の環境\)** を選択し、 **[アクションの追加]** を選択します。
1. **[コネクタとアクションを検索する]** に「**Power BI**」と入力して、**Enter** キーを押します。
1. **[Add rows to a dataset (preview)]\(データセットに行を追加する (プレビュー)\)** アクションを選択します。
1. **[サインイン]** を選択し、画面の指示に従って Power BI アカウントにサインインします。
1. サインイン プロセスが完了した後、 **[Add rows to a dataset]\(データセットに行を追加する\)** アクションで次のようにします。
    * ワークスペースとして **In-store analytics - checkout** を選択します。
    * データセットとして **Zone 1 sensor** を選択します。
    * テーブルとして **RealTimeData** を選択します。
    * **[新しいパラメーターの追加]** を選択し、**Timestamp**、**Humidity**、**Temperature** の各フィールドを選択します。
    * **Timestamp** フィールドを選択し、 **[動的なコンテンツ]** の一覧から **x-opt-enqueuedtime** を選択します。
    * **Humidity** フィールドを選択し、 **[Parse Telemetry]\(テレメトリの解析\)** の横にある **[See more]\(詳細\)** を選択します。 次に、**humidity** を選択します。
    * **Temperature** フィールドを選択し、 **[Parse Telemetry]\(テレメトリの解析\)** の横にある **[See more]\(詳細\)** を選択します。 次に、**temperature** を選択します。
    * **[保存]** を選択して変更を保存します。 **[Zone 1 environment]\(ゾーン 1 の環境\)** アクションは、次のスクリーンショットのようになります。![ゾーン 1 の環境](./media/tutorial-in-store-analytics-visualize-insights/zone-1-action.png)
1. **[Zone 2 environment]\(ゾーン 2 の環境\)** アクションを選択し、 **[アクションの追加]** を選択します。
1. **[コネクタとアクションを検索する]** に「**Power BI**」と入力して、**Enter** キーを押します。
1. **[Add rows to a dataset (preview)]\(データセットに行を追加する (プレビュー)\)** アクションを選択します。
1. **[Add rows to a dataset 2]\(データセット 2 に行を追加する\)** アクションで次のようにします。
    * ワークスペースとして **In-store analytics - checkout** を選択します。
    * データセットとして **Zone 2 sensor** を選択します。
    * テーブルとして **RealTimeData** を選択します。
    * **[新しいパラメーターの追加]** を選択し、**Timestamp**、**Humidity**、**Temperature** の各フィールドを選択します。
    * **Timestamp** フィールドを選択し、 **[動的なコンテンツ]** の一覧から **x-opt-enqueuedtime** を選択します。
    * **Humidity** フィールドを選択し、 **[Parse Telemetry]\(テレメトリの解析\)** の横にある **[See more]\(詳細\)** を選択します。 次に、**humidity** を選択します。
    * **Temperature** フィールドを選択し、 **[Parse Telemetry]\(テレメトリの解析\)** の横にある **[See more]\(詳細\)** を選択します。 次に、**temperature** を選択します。
    **[保存]** を選択して変更を保存します。  **[Zone 2 environment]\(ゾーン 2 の環境\)** アクションは、次のスクリーンショットのようになります。![ゾーン 2 の環境](./media/tutorial-in-store-analytics-visualize-insights/zone-2-action.png)
1. **[Occupancy]\(混雑状況\)** アクションを選択し、 **[Switch by Interface ID]\(インターフェイス ID で切り替え\)** アクションを選択します。
1. **[Dwell Time interface]\(待ち時間インターフェイス\)** アクションを選択し、 **[アクションの追加]** を選択します。
1. **[コネクタとアクションを検索する]** に「**Power BI**」と入力して、**Enter** キーを押します。
1. **[Add rows to a dataset (preview)]\(データセットに行を追加する (プレビュー)\)** アクションを選択します。
1. **[Add rows to a dataset]\(データセットに行を追加する\)** アクションで次のようにします。
    * ワークスペースとして **In-store analytics - checkout** を選択します。
    * データセットとして **Occupancy Sensor** を選択します。
    * テーブルとして **RealTimeData** を選択します。
    * **[新しいパラメーターの追加]** を選択し、**Timestamp**、**Dwell Time 1**、**Dwell Time 2** の各フィールドを選択します。
    * **Timestamp** フィールドを選択し、 **[動的なコンテンツ]** の一覧から **x-opt-enqueuedtime** を選択します。
    * **Dwell Time 1** フィールドを選択し、 **[Parse Telemetry]\(テレメトリの解析\)** の横にある **[See more]\(詳細\)** を選択します。 次に、**DwellTime1** を選択します。
    * **Dwell Time 2** フィールドを選択し、 **[Parse Telemetry]\(テレメトリの解析\)** の横にある **[See more]\(詳細\)** を選択します。 次に、**DwellTime2** を選択します。
    * **[保存]** を選択して変更を保存します。 **[Dwell Time interface]\(待ち時間インターフェイス\)** アクションは、次のスクリーンショットのようになります。![[Dwell Time interface]\(待ち時間インターフェイス\) アクションを表示する画面のスクリーンショット。](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-1.png)
1. **[People Count interface]\(客数インターフェイス\)** アクションを選択し、 **[アクションの追加]** を選択します。
1. **[コネクタとアクションを検索する]** に「**Power BI**」と入力して、**Enter** キーを押します。
1. **[Add rows to a dataset (preview)]\(データセットに行を追加する (プレビュー)\)** アクションを選択します。
1. **[Add rows to a dataset]\(データセットに行を追加する\)** アクションで次のようにします。
    * ワークスペースとして **In-store analytics - checkout** を選択します。
    * データセットとして **Occupancy Sensor** を選択します。
    * テーブルとして **RealTimeData** を選択します。
    * **[新しいパラメーターの追加]** を選択し、**Timestamp**、**Queue Length 1**、**Queue Length 2** の各フィールドを選択します。
    * **Timestamp** フィールドを選択し、 **[動的なコンテンツ]** の一覧から **x-opt-enqueuedtime** を選択します。
    * **Queue Length 1** フィールドを選択し、 **[Parse Telemetry]\(テレメトリの解析\)** の横にある **[See more]\(詳細\)** を選択します。 **count1** を選択します。
    * **Queue Length 2** フィールドを選択し、 **[Parse Telemetry]\(テレメトリの解析\)** の横にある **[See more]\(詳細\)** を選択します。 **count2** を選択します。
    * **[保存]** を選択して変更を保存します。 **[People Count interface]\(客数インターフェイス\)** アクションは、次のスクリーンショットのようになります。![待ち時間アクション](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-2.png)

ロジック アプリは自動的に実行されます。 各実行の状態を確認するには、Azure portal でロジック アプリの **[概要]** ページに移動します。

## <a name="create-a-power-bi-dashboard"></a>Power BI ダッシュボードを作成する

これで、IoT Central アプリケーションからイベント ハブを通してテレメトリを送信できるようになりました。 その後、ロジック アプリによってイベント ハブ メッセージが解析され、Power BI のストリーミング データセットに追加されます。 ここでは、Power BI ダッシュボードを作成してテレメトリを視覚化します。

1. **Power BI** アカウントにサインインします。
1. **[ワークスペース] で In-store analytics - checkout** を選択します。
1. **[作成] > [ダッシュボード]** を選択します。
1. ダッシュボード名として「**店舗分析**」と入力し、 **[作成]** を選択します。

### <a name="add-line-charts"></a>折れ線グラフを追加する

2 つの環境センサーの気温と湿度を示す 4 つの折れ線グラフ タイルを追加します。 次の表の情報を使用して、タイルを作成します。 各タイルを追加するには、まず **[...] (その他のオプション) > [タイル]** を選択します。 **[カスタム ストリーミング データ]** を選択し、 **[次へ]** を選択します。

| 設定 | グラフ 1 | グラフ 2 | グラフ 3 | グラフ 4 |
| ------- | -------- | -------- | -------- | -------- |
| データセット | Zone 1 sensor | Zone 1 sensor | Zone 2 sensor | Zone 2 sensor |
| 視覚化の種類 | 折れ線グラフ | 折れ線グラフ | 折れ線グラフ | 折れ線グラフ |
| 軸 | Timestamp | Timestamp | Timestamp | Timestamp |
| 値 | 気温 | 湿度 | 気温 | 湿度 |
| Time window (時間枠) | 約 60 分 | 約 60 分 | 約 60 分 | 約 60 分 |
| タイトル | 気温 (1 時間) | 湿度 (1 時間) | 気温 (1 時間) | 湿度 (1 時間) |
| サブタイトル | ゾーン 1 | ゾーン 1 | ゾーン 2 | ゾーン 2 |

次のスクリーンショットで示すのは、1 つ目のグラフの設定です。

![折れ線グラフの設定](./media/tutorial-in-store-analytics-visualize-insights/line-chart.png)

### <a name="add-cards-to-show-environmental-data"></a>環境データを表示するカードを追加する

2 つの環境センサーから最新の気温と湿度の値を表示する 4 つのカード タイルを追加します。 次の表の情報を使用して、タイルを作成します。 各タイルを追加するには、まず **[...] (その他のオプション) > [タイル]** を選択します。 **[カスタム ストリーミング データ]** を選択し、 **[次へ]** を選択します。

| 設定 | カード 1 | カード 2 | カード 3 | カード 4 |
| ------- | ------- | ------- | ------- | ------- |
| データセット | Zone 1 sensor | Zone 1 sensor | Zone 2 sensor | Zone 2 sensor |
| 視覚化の種類 | Card | Card | Card | Card |
| フィールド | 気温 | 湿度 | 気温 | 湿度 |
| タイトル | 気温 (F) | 湿度 (%) | 気温 (F) | 湿度 (%) |
| サブタイトル | ゾーン 1 | ゾーン 1 | ゾーン 2 | ゾーン 2 |

次のスクリーンショットで示すのは、1 つ目のカードの設定です。

![カードの設定](./media/tutorial-in-store-analytics-visualize-insights/card-settings.png)

### <a name="add-tiles-to-show-checkout-occupancy-data"></a>レジの混雑状況データを表示するタイルを追加する

店の 2 つのレジでの待ち行列の長さと待ち時間を表示する 4 つのカード タイルを追加します。 次の表の情報を使用して、タイルを作成します。 各タイルを追加するには、まず **[...] (その他のオプション) > [タイル]** を選択します。 **[カスタム ストリーミング データ]** を選択し、 **[次へ]** を選択します。

| 設定 | カード 1 | カード 2 | カード 3 | カード 4 |
| ------- | ------- | ------- | ------- | ------- |
| データセット | Occupancy sensor | Occupancy sensor | Occupancy sensor | Occupancy sensor |
| 視覚化の種類 | 集合縦棒グラフ | 集合縦棒グラフ | ゲージ | ゲージ |
| 軸    | Timestamp | Timestamp | 該当なし | 該当なし |
| 値 | Dwell Time 1 | Dwell Time 2 | Queue Length 1 | Queue Length 2 |
| Time window (時間枠) | 約 60 分 | 約 60 分 |  該当なし | 該当なし |
| タイトル | 待ち時間 | 待ち時間 | キューの長さ | キューの長さ |
| サブタイトル | レジ 1 | レジ 2 | レジ 1 | レジ 2 |

次のスクリーンショットのように、ダッシュボード上のタイルのサイズと配置を変更します。

![タイルのサイズと配置が変更された Power BI ダッシュボードのスクリーンショット。](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard.png)

他のグラフィックス リソースを追加して、ダッシュボードをさらにカスタマイズしてかまいません。

![Power BI ダッシュボード](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard-graphics.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

IoT Central アプリケーションが完成したら、アプリケーションにサインインし、 **[管理]** セクションの **[アプリケーションの設定]** ページに移動して、アプリケーションを削除できます。

アプリケーションを残すけれども、関連するコストを減らしたい場合は、テレメトリをイベント ハブに送信しているデータのエクスポートを無効にします。

Azure portal で **retail-store-analysis** という名前のリソース グループを削除することにより、イベント ハブとロジック アプリを削除できます。

Power BI のワークスペースの設定ページでワークスペースを削除することにより、Power BI のデータセットとダッシュボードを削除できます。

## <a name="next-steps"></a>次の手順

以上の 3 つのチュートリアルでは、**ストア内の分析 - チェックアウト** IoT Central アプリケーション テンプレートを使用するエンドツーエンドのソリューションについて説明しました。 デバイスをアプリケーションに接続し、IoT Central を使用してデバイスを監視し、Power BI を使用してデバイスのテレメトリから分析情報を表示するダッシュボードを作成しました。 次のステップとして、他の IoT Central アプリケーション テンプレートのいずれかを調べることをお勧めします。

> [!div class="nextstepaction"]
> [IoT Central を使用してエネルギー ソリューションを構築する](../energy/overview-iot-central-energy.md)
