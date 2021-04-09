---
title: VM への送受信ネットワーク トラフィック フローのログ記録 - チュートリアル - Azure Portal | Microsoft Docs
description: Network Watcher の NSG フロー ログ機能を使用して、VM への送受信ネットワーク トラフィック フローをログに記録する方法を説明します。
services: network-watcher
documentationcenter: na
author: damendo
tags: azure-resource-manager
Customer intent: I need to log the network traffic to and from a VM so I can analyze it for anomalies.
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: 385d43e46cd3f9465c0fbf9a02eeae356f48fac4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94966532"
---
# <a name="tutorial-log-network-traffic-to-and-from-a-virtual-machine-using-the-azure-portal"></a>チュートリアル:Azure portal を使用して仮想マシンへの送受信ネットワーク トラフィックをログに記録する

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)

ネットワーク セキュリティ グループ (NSG) により、仮想マシン (VM) への着信トラフィックと 送信トラフィックをフィルターできます。 Network Watcher の NSG フロー ログ機能により、NSG を通過するネットワーク トラフィックをログに記録できます。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ネットワーク セキュリティ グループで VM 作成する
> * Network Watcher を有効にして、Microsoft.Insights プロバイダーを登録する
> * Network Watcher の NSG フロー ログ機能を使用して、NSG のトラフィック フローのログを有効にする
> * ログに記録されたデータをダウンロードする
> * ログに記録されたデータを表示する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-a-vm"></a>VM の作成

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. **[Compute]** を選択し、 **[Windows Server 2016 Datacenter]** またはいずれかのバージョンの **Ubuntu Server** を選択します。
3. 次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、 **[OK]** を選択します。

    |設定|値|
    |---|---|
    |名前|myVm|
    |ユーザー名| 任意のユーザー名を入力します。|
    |Password| 任意のパスワードを入力します。 パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。|
    |サブスクリプション| サブスクリプションを選択します。|
    |Resource group| **[新規作成]** を選択し、「**myResourceGroup** と入力します。|
    |場所| **[米国東部]** を選択します。|

4. VM のサイズを選択して、 **[選択]** を選択します。
5. **[設定]** で、すべての既定値をそのままにして、 **[OK]** を選択します。
6. **[概要]** の **[作成]** で **[作成]** を選択して、VM のデプロイを開始します。 VM のデプロイには数分かかります。 残りの手順を続行する前に、VM がデプロイを完了するまで待ちます。

VM の作成には数分かかります。 VM の作成が完了するまで、残りの手順を続行しないでください。 ポータルで、VM が作成される際に、**myVm nsg** という名前のネットワーク セキュリティ グループも作成され、それが VM のネットワーク インターフェイスに関連付けられます。

## <a name="enable-network-watcher"></a>Network Watcher を有効にする

米国東部リージョンで既に Network Watcher を有効にしている場合は、「[Insights プロバイダーの登録](#register-insights-provider)」に進んでください。

1. ポータルで **[すべてのサービス]** を選択します。 **[フィルター]** ボックスに、「*Network Watcher*」と入力します。 結果に **[Network Watcher]** が表示されたら、それを選択します。
2. 次の図に示すように、 **[リージョン]** を選択して展開し、 **[米国東部]** の右側の **[...]** を選択します。

    ![Network Watcher を有効にする](./media/network-watcher-nsg-flow-logging-portal/enable-network-watcher.png)

3. **[Network Watcher の有効化]** を選択します。

## <a name="register-insights-provider"></a>Insights プロバイダーの登録

NSG フローのログ記録には、**Microsoft.Insights** プロバイダーが必要です。 このプロバイダーを登録するには、次の手順を実行します。

1. ポータルの左上隅の **[すべてのサービス]** を選択します。 [フィルター] ボックスに「*Subscriptions*」と入力します。 検索結果に **[Subscriptions]** が表示されたら、それを選択します。
2. サブスクリプションの一覧から、プロバイダーを有効にするサブスクリプションを選択します。
3. **[設定]** で、 **[リソース プロバイダー]** を選択します。
4. 次の図に示すように、**microsoft.insights** プロバイダーの **[状態]** が **[登録済み]** であることを確認します。 状態が **[未登録]** である場合、プロバイダーの右側の **[登録]** を選択します。

    ![プロバイダーの登録](./media/network-watcher-nsg-flow-logging-portal/register-provider.png)

## <a name="enable-nsg-flow-log"></a>NSG フロー ログの有効化

1. NSG フロー ログ データは Azure Storage アカウントに書き込まれます。 Azure Storage アカウントを作成するには、ポータルの左上隅の **[+ リソースの作成]** を選択します。
2. **[ストレージ]** 、 **[ストレージ アカウント - Blob、File、Table、Queue]** の順に選択します。
3. 次の情報を入力するか選択し、それ以外の情報は既定値をそのまま使用して、 **[作成]** を選択します。

    | 設定        | 値                                                        |
    | ---            | ---   |
    | 名前           | 3 ～ 24 文字の長さで、小文字の英数字のみを含めることができ、すべての Azure Storage アカウントで一意である必要があります。                                                               |
    | 場所       | **[米国東部]** を選択します。                                           |
    | Resource group | **[既存のものを使用]** 、 **[myResourceGroup]** の順に選択します |

    ストレージ アカウントの作成には、しばらくかかる場合があります。 ストレージ アカウントが作成されるまで、残りの手順を続行しないでください。 どのような場合でも、ストレージ アカウントは、NSG と同じリージョンに存在する必要があります。
4. ポータルの左上隅の **[すべてのサービス]** を選択します。 *[フィルター]* ボックスに「**Network Watcher**」と入力します。 検索結果に **[Network Watcher]** が表示されたら、それを選択します。
5. 次の図に示すように、 **[ログ]** の　 **[NSG フロー ログ]** を選択します。

    ![Network Watcher の NSG フロー ログを示すスクリーンショット。](./media/network-watcher-nsg-flow-logging-portal/nsgs.png)

6. NSG の一覧から **myVm-nsg** という名前の NSG を選択します。
7. **[フローのログ設定]** の下で **[オン]** を選択します。
8. フロー ログのバージョンを選択します。 バージョン 2 には、フローセッションの統計 (バイトおよびパケット) が含まれます。

   ![フロー ログのバージョンの選択](./media/network-watcher-nsg-flow-logging-portal/select-flow-log-version.png)

9. 手順 3 で作成したストレージ アカウントを選択します。
   > [!NOTE]
   > NSG フロー ログは、[階層型名前空間](../storage/blobs/data-lake-storage-namespace.md)が有効になっているストレージ アカウントでは正しく動作しません。
1. ポータルの左上隅の **[すべてのサービス]** を選択します。 *[フィルター]* ボックスに「**Network Watcher**」と入力します。 検索結果に **[Network Watcher]** が表示されたら、それを選択します。
10. **[リテンション期間 (日数)]** を 5 に設定し、 **[保存]** を選択します。

## <a name="download-flow-log"></a>フロー ログのダウンロード

1. ポータルの Network Watcher から、 **[ログ]** の下の **[NSG フロー ログ]** を選択します。
2. 次の図に示すように、 **[構成済みのストレージ アカウントからフローのログをダウンロードできました。]** を選択します。

   ![フロー ログをダウンロードする](./media/network-watcher-nsg-flow-logging-portal/download-flow-logs.png)

3. 「[NSG フロー ログの有効化](#enable-nsg-flow-log)」の手順 2 で構成したストレージ アカウントを選択します。
4. **[Blob service]** で **[コンテナー]** を選択し、 **[insights-logs-networksecuritygroupflowevent]** コンテナーを選択します。
5. 次の図に示すように、コンテナー内のフォルダー階層を PT1H.json ファイルに到達するまで移動します。 ログ ファイルは、次の名前規則に従ってフォルダー階層に書き込まれます。 https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

   ![フローのログ](./media/network-watcher-nsg-flow-logging-portal/log-file.png)

6. PT1H.json ファイルの右側の **[...]** を選択し、 **[ダウンロード]** を選択します。

## <a name="view-flow-log"></a>フロー ログの表示

次の json は、データがログに記録される各フローについて、PT1H.json ファイルに表示される内容の例です。

### <a name="version-1-flow-log-event"></a>バージョン 1 のフロー ログ イベント
```json
{
    "time": "2018-05-01T15:00:02.1713710Z",
    "systemId": "<Id>",
    "category": "NetworkSecurityGroupFlowEvent",
    "resourceId": "/SUBSCRIPTIONS/<Id>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MYVM-NSG",
    "operationName": "NetworkSecurityGroupFlowEvents",
    "properties": {
        "Version": 1,
        "flows": [
            {
                "rule": "UserRule_default-allow-rdp",
                "flows": [
                    {
                        "mac": "000D3A170C69",
                        "flowTuples": [
                            "1525186745,192.168.1.4,10.0.0.4,55960,3389,T,I,A"
                        ]
                    }
                ]
            }
        ]
    }
}
```
### <a name="version-2-flow-log-event"></a>バージョン 2 のフロー ログ イベント
```json
{
    "time": "2018-11-13T12:00:35.3899262Z",
    "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
    "category": "NetworkSecurityGroupFlowEvent",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
    "operationName": "NetworkSecurityGroupFlowEvents",
    "properties": {
        "Version": 2,
        "flows": [
            {
                "rule": "DefaultRule_DenyAllInBound",
                "flows": [
                    {
                        "mac": "000D3AF87856",
                        "flowTuples": [
                            "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                            "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                            "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                        ]
                    }
                ]
            },
            {
                "rule": "DefaultRule_AllowInternetOutBound",
                "flows": [
                    {
                        "mac": "000D3AF87856",
                        "flowTuples": [
                            "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                            "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                            "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                            "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                        ]
                    }
                ]
            }
        ]
    }
}
```

前の出力の **mac** の値は、VM の作成時に作成されたネットワーク インターフェイスの MAC アドレスです。 **flowTuples** のコンマで区切られた情報を次に示します。

| サンプル データ | データが表す内容   | 説明                                                                              |
| ---          | ---                    | ---                                                                                      |
| 1542110377   | タイム スタンプ             | UNIX EPOCH 形式でフローが発生した際のタイム スタンプ。 前の例では、日付は 2018 年 5 月 1 日午後 2:59:05 GMT に変換されます。                                                                                    |
| 10.0.0.4  | 送信元 IP アドレス      | フローが発生したソース IP アドレス。 10.0.0.4 は、「[VM の作成](#create-a-vm)」で作成した VM のプライベート IP アドレスです。
| 13.67.143.118     | 宛先 IP アドレス | フローが送信された宛先 IP アドレス。                                                                                  |
| 44931        | 発信元ポート            | フローが発生したソース ポート。                                           |
| 443         | 宛先ポート       | フローが送信された宛先ポート。 トラフィックの送信先はポート 443 であったため、ログ ファイルの **UserRule_default-allow-rdp** という規則によって、フローが処理されました。                                                |
| T            | Protocol               | フローのプロトコルが TCP (T) かまたは UDP (U) か。                                  |
| O            | Direction              | トラフィックが受信 (I) かまたは送信 (O) か。                                     |
| A            | アクション                 | トラフィックが許可された (A) かまたは拒否された (D) か。  
| C            | フロー状態 (**バージョン 2 のみ**) | フローの状態をキャプチャします。 次の状態があります。**B**:開始。フローが作成された時点です。 統計は提供されません。 **C**: 継続中。フローが進行中です。 5 分間隔で統計が提供されます。 **E**:終了。フローが終了した時点です。 統計が提供されます。 |
| 30 | 送信済みパケット数 - 送信元から宛先 (**バージョン 2 のみ**) | 最後の更新以降に送信元から宛先に送信された TCP または UDP パケットの総数。 |
| 16978 | 送信済みバイト数 - 送信元から宛先 (**バージョン 2 のみ**) | 最後の更新以降に送信元から宛先に送信された TCP または UDP パケットのバイト数の合計。 パケットのバイト数には、パケット ヘッダーとペイロードが含まれます。 |
| 24 | 送信済みパケット数 - 宛先から送信元 (**バージョン 2 のみ**) | 最後の更新以降に宛先から送信元に送信された TCP または UDP パケットの総数。 |
| 14008| 送信済みバイト数 - 宛先から送信元 (**バージョン 2 のみ**) | 最後の更新以降に宛先から送信元に送信された TCP および UDP パケットのバイト数の合計。 パケットのバイト数には、パケット ヘッダーとペイロードが含まれます。|

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、NSG の NSG フローのログ記録を有効にする方法について説明しました。 また、ファイルに記録されたデータをダウンロードし、表示する方法も説明しました。 Json ファイル内の生データは解釈が難しい場合があります。 フロー ログのデータを視覚化する際は、[Azure Traffic Analytics](traffic-analytics.md) や [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md) などのツールを使用できます。 [PowerShell](network-watcher-nsg-flow-logging-powershell.md)、[Azure CLI](network-watcher-nsg-flow-logging-cli.md)、[REST API](network-watcher-nsg-flow-logging-rest.md)、[ARM テンプレート](network-watcher-nsg-flow-logging-azure-resource-manager.md)など、他の方法でも、NSG フロー ログを有効にしてみましょう。