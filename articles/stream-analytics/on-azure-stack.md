---
title: Azure Stack での Azure Stream Analytics の実行 (プレビュー)
description: Azure Stream Analytics Edge ジョブを作成し、IoT Edge ランタイムを使用して Azure Stack ハブに展開します。
ms.service: stream-analytics
author: raan
ms.author: raan
ms.reviewer: mamccrea
ms.topic: how-to
ms.date: 08/21/2020
ms.custom: seodec18
ms.openlocfilehash: 1fe035d99f8a5962406d5aae3f093d71d432b310
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88860406"
---
# <a name="run-azure-stream-analytics-on-azure-stack-preview"></a>Azure Stack での Azure Stream Analytics の実行 (プレビュー)

> [!IMPORTANT]
> この機能はプレビュー段階であるため、運用環境では使用しないことをお勧めします。

Azure Stream Analytics は Azure Stack Hub で IoT Edge モジュールとして実行できます。 IoT Edge モジュールに構成が追加されたことにより、すべての Azure Stack Hub の利用で見つかったカスタム URI を使用できるようになることで、Azure Stack Hub サブスクリプションで実行されている BLOB ストレージ、Event Hubs、IoT Hub と対話できるようになります。

Azure Stack で Stream Analytics を使用すると、独自のプライベートな自律クラウドでストリーム処理を行うための真のハイブリッド アーキテクチャを構築できます。これは、一貫性のある Azure サービスをオンプレミスで使用して、クラウドネイティブ アプリと接続または切断することができます。 

この記事では、Azure Stack Hub サブスクリプションで IoT Hub またはイベント ハブから別のイベント ハブまたは BLOB ストレージにデータをストリーム配信し、これを Stream Analytics で処理する方法について説明します。

## <a name="set-up-environments"></a>環境を設定する

Azure Stream Analytics は Azure Stack Hub 上のハイブリッド サービスです。 これは Azure で構成されていますが Azure Stack Hub で実行できる IoT Edge モジュールです。  

Azure Stack Hub または IoT Edge を初めて使用する場合は、以下の手順に従って環境を設定してください。

### <a name="prepare-the-azure-stack-hub-environment"></a>Azure Stack Hub 環境を準備する

Azure Stack Hub サブスクリプションを作成します。 詳細については、[Azure Stack Hub サブスクリプションを作成するためのチュートリアル](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services/)を参照してください。

独自のサーバーで Azure Stack Hub を評価する場合は、Azure Stack Development Kit (ASDK) を使用できます。  ASDK の詳細については、[ASDK の概要](https://docs.microsoft.com/azure-stack/asdk/)に関する記事を参照してください。

### <a name="install-the-iot-edge-runtime"></a>IoT Edge ランタイムをインストールする

Azure Stack Hub で Azure Stream Analytics を実行するには、デバイスに IoT Edge ランタイムが必要です。また、Azure Stack Hub へのネットワーク接続があるか、Azure Stack Hub で実行されている VM である必要があります。 IoT Edge ランタイムを使用すると、Stream Analytics Edge ジョブを、Azure Stack Hub で実行されている Azure Storage および Azure Event Hubs と統合できます。 詳細については、「[Azure Stream Analytics on IoT Edge](stream-analytics-edge.md)」をご覧ください。 

Azure Stack Hub リソースへのネットワーク アクセスだけでなく、IoT Edge デバイス (または VM) は、Stream Analytics モジュールを構成するために、Azure パブリック クラウド内の Azure IoT Hub にアクセスする必要があります。 

次のガイドでは、デバイスまたは VM で IoT Edge ランタイムを設定する方法について説明します。

* [Windows に Azure IoT Edge ランタイムをインストールする](../iot-edge/how-to-install-iot-edge-windows.md)
* [Debian ベースの Linux システムに Azure IoT Edge ランタイムをインストールする](../iot-edge/how-to-install-iot-edge-linux.md)


## <a name="create-an-azure-stream-analytics-edge-job"></a>Azure Stream Analytics Edge ジョブを作成する

ASA エッジ ジョブは、Azure IoT Edge デバイスに展開されたコンテナー内で実行されます。 これは 2 つの部分で構成されます。
* ジョブ定義の役割を担うクラウド部分: ユーザーが、クラウド内の入力、出力、クエリ、およびその他の設定 (順不同のイベントなど) を定義します。
* IoT デバイス上で実行されているモジュール。 ASA エンジンが含まれ、クラウドからジョブ定義を受け取ります。

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

Azure Stream Analytics ジョブを作成して IoT Edge デバイスで実行する場合は、デバイスから呼び出すことができるようにジョブを格納する必要があります。 既存の Azure ストレージ アカウントを使用するか、新しいストレージ アカウントを作成することができます。
1. Azure portal で、 **[リソースの作成]、[Storage]、[ストレージ アカウント - Blob、File、Table、Queue]** の順にクリックします。
2. ストレージ アカウントを作成するには、以下の値を指定します。

   | フィールド | 値 |
   | --- | --- |
   | 名前 | ストレージ アカウント用に一意の名前を指定します。 |
   | 場所 | 近くの場所を選択します。|
   | サブスクリプション | IoT ハブと同じサブスクリプションを選択します。|
   | リソース グループ | [IoT Edge のクイック スタート](https://docs.microsoft.com/azure/iot-edge/quickstart)およびチュートリアルで作成するすべてのテスト リソースに、同じリソース グループを使用することをお勧めします。 たとえば、**IoTEdgeResources** を使用します。 |

3. 他のフィールドは既定値のままにして、**[作成]** を選択します。


### <a name="create-a-new-job"></a>新しいジョブを作成する

1. Azure portal で、 **[リソースの作成] > [モノのインターネット (IoT)] > [Stream Analytics ジョブ]** の順に移動します。
2. ストレージ アカウントを作成するには、以下の値を指定します。

   | フィールド | 値 |
   | --- | --- |
   | ジョブ名 | ジョブの名前を指定します。 たとえば、「**IoTEdgeJob**」と指定します。 |
   | サブスクリプション | IoT ハブと同じサブスクリプションを選択します。|
   | リソース グループ | [IoT Edge のクイック スタート](https://docs.microsoft.com/azure/iot-edge/quickstart)およびチュートリアルで作成するすべてのテスト リソースに、同じリソース グループを使用することをお勧めします。 たとえば、**IoTEdgeResources** を使用します。 |
   | 場所 | 近くの場所を選択します。 |
   | ホスト環境 | **[Edge]** を選択します。 |

3. **［作成］** を選択します

### <a name="configure-your-job"></a>ジョブを構成する

Azure portal で Stream Analytics ジョブが作成されたら、ジョブの入力、出力、および通過するデータに対して実行するクエリを構成することができます。 Azure Stack Hub サブスクリプションで、IoT Hub またはイベント ハブからの入力を手動で指定できます。

1. Azure portal で Stream Analytics ジョブに移動します。
2. **[構成]** で、 **[ストレージ アカウントの設定]** を選択し、前の手順で作成したストレージ アカウントを選択します。
   > [!div class="mx-imgBorder"]
   > [ ![ジョブ ストレージ アカウントの設定](media/on-azure-stack/storage-account-settings.png) ](media/on-azure-stack/storage-account-settings.png#lightbox)
3. **[ジョブ トポロジ]** で、 **[入力]** 、 **[ストリーム入力の追加]** の順に選択します。
4. ドロップダウン リストから **[IoT ハブ]** 、 **[イベントハブ]** 、または **[Edge ハブ]** を選択します。 
5. 入力が Azure Stack Hub サブスクリプションのイベント ハブまたは IoT ハブである場合は、次に示すように情報を手動で入力してください。

   #### <a name="event-hub"></a>イベント ハブ

   | フィールド | 値 |
   | --- | --- |
   | 入力のエイリアス | この入力を参照するジョブのクエリで使用するわかりやすい名前。 |
   | Service Bus 名前空間 | 名前空間は、一連のメッセージング エンティティのコンテナーです。 新しいイベント ハブを作成するときは、名前空間も作成します。 (例: *sb://<Event Hub Name>.eventhub.shanghai.azurestack.corp.microsoft.com*) |
   | イベント ハブ名 | 入力として使用するイベント ハブの名前。 |
   | イベント ハブ ポリシー名 | イベント ハブへのアクセスを提供する共有アクセス ポリシー。 各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 Event Hub の設定を手動で入力するオプションを選択しない限り、このオプションは自動的に設定されます。 |
   | イベント ハブ ポリシー キー | イベント ハブへのアクセスを承認するために使用する共有アクセス キー。 イベント ハブの設定を手動で入力するオプションを選択しない限り、このオプションは自動的に事前設定されます。 これはイベント ハブの設定で見つけることができます。 |
   | イベント ハブ コンシューマー グループ (省略可能) | Stream Analytics ジョブごとに個別のコンシューマー グループを使用することを強くお勧めします。 イベント ハブからデータを取り込むために使用するコンシューマー グループが、この文字列によって識別されます。 コンシューマー グループが指定されていない場合、Stream Analytics ジョブは $Default コンシューマー グループを使用します。 |
   | [パーティション数] | パーティション数はイベント ハブ内のパーティションの数です。 |

   > [!div class="mx-imgBorder"]
   > [ ![イベント ハブの入力](media/on-azure-stack/event-hub-input.png) ](media/on-azure-stack/event-hub-input.png#lightbox)

   #### <a name="iot-hub"></a>IoT Hub

   | フィールド | 値 |
   | --- | --- |
   | 入力のエイリアス | この入力を参照するジョブのクエリで使用するわかりやすい名前。 |
   | IoT Hub | 入力として使用する IoT Hub の名前。 (例: *<IoT Hub Name>.shanghai.azurestack.corp.microsoft.com*) |
   | 共有アクセス ポリシー名 | IoT Hub へのアクセスを提供する共有アクセス ポリシー。 各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 |
   | 共有アクセス ポリシー キー | IoT Hub へのアクセスを承認するために使用する共有アクセス キー。 IoT Hub の設定を手動で入力するオプションを選択しない限り、このオプションは自動的に事前設定されます。 |
   | コンシューマー グループ (省略可能) | Stream Analytics ジョブごとに異なるコンシューマー グループを使用することを強くお勧めします。 IoT Hub からのデータを取り込むために使用するコンシューマー グループです。 明示的に指定されない限り、Stream Analytics は $Default コンシューマー グループを使用します。 |
   | [パーティション数] | パーティション数はイベント ハブ内のパーティションの数です。 |

   > [!div class="mx-imgBorder"]
   > [ ![IoT Hub の入力](media/on-azure-stack/iot-hub-input.png) ](media/on-azure-stack/iot-hub-input.png#lightbox)

6. 他のフィールドは既定値のままにして、 [保存] を選択します。
7. [ジョブ トポロジ] で [出力] を開き、 [追加] を選択します。
8. ドロップダウン リストから [Blob ストレージ]、[イベントハブ]、または [Edge ハブ] を選択します。
9. 入力が Azure Stack Hub サブスクリプションのイベント ハブまたは BLOB ストレージある場合は、次に示すように情報を手動で入力してください。

   #### <a name="event-hub"></a>イベント ハブ

   | フィールド | 値 |
   | --- | --- |
   | 出力エイリアス | クエリの出力をこのイベント ハブに出力するためにクエリ内で使用されるフレンドリ名です。 |
   | Service Bus 名前空間 | 一連のメッセージング エンティティのコンテナーです。 新しいイベント ハブを作成したときに、Service Bus 名前空間も作成されました。(例: *sb://<Event Hub Name>.eventhub.shanghai.azurestack.corp.microsoft.com*) |
   | イベント ハブ名 | ご自分のイベント ハブ出力の名前です。 |
   | イベント ハブ ポリシー名 | 共有アクセス ポリシーです。イベント ハブの [構成] タブで作成できます。各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 |
   | イベント ハブ ポリシー キー | イベント ハブの名前空間へのアクセスを認証するために使用される共有アクセス キーです。 |

   > [!div class="mx-imgBorder"]
   > [ ![イベント ハブの出力](media/on-azure-stack/event-hub-output.png) ](media/on-azure-stack/event-hub-output.png#lightbox)

   #### <a name="blob-storage"></a>Blob Storage 

   | フィールド | 値 |
   | --- | --- |
   | 出力エイリアス | クエリの出力をこの BLOB ストレージに出力するためにクエリで使用されるわかりやすい名前です。 |
   | ストレージ アカウント | 出力を送信するストレージ アカウントの名前です。(例: *<Storage Account Name>.blob.shanghai.azurestack.corp.microsoft.com*) |
   | ストレージ アカウント キー | ストレージ アカウントに関連付けられている秘密キー。 Blob Storage の設定を手動で入力するオプションを選択しない限り、このオプションは自動的に事前設定されます。 |

> [!NOTE]
> Parquet 形式は Azure Stack Hub の Edge ジョブではサポートされていません。 最小行数と最大時間については、0 を使用するか、空白のままにしてください。


## <a name="deploy-stream-analytics-on-a-vm-or-device-connected-to-azure-stack"></a>Azure Stack に接続されている VM またはデバイスに Stream Analytics を展開する

1. Azure portal で IoT Hub を開きます。 **[IoT Edge]** に移動して、この展開の対象となるデバイス (VM) をクリックします。
2. **[Set modules]\(モジュールの設定\)** を選びます。 **[+ 追加]** 、 **[Azure Stream Analytics モジュール]** の順に選択します。 
3. サブスクリプションと、作成した Stream Analytics Edge ジョブを選択します。 **[保存]** をクリックし、 **[次: ルート]** を選択します。

   > [!div class="mx-imgBorder"]
   > [ ![モジュールを追加する](media/on-azure-stack/edge-modules.png) ](media/on-azure-stack/edge-modules.png#lightbox)

4. **[確認および作成 >]** をクリックします
5. **[確認および作成]** ステップで、 **[作成]** を選択します。 
   > [!div class="mx-imgBorder"]
   > [ ![マニフェスト](media/on-azure-stack/module-content.png) ](media/on-azure-stack/module-content.png#lightbox)
6. モジュールが一覧に追加されていることを確認します。
   > [!div class="mx-imgBorder"]
   > [ ![展開ページ](media/on-azure-stack/edge-deployment.png) ](media/on-azure-stack/edge-deployment.png#lightbox)

## <a name="next-steps"></a>次のステップ
- [Azure Stream Analytics on IoT Edge](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge)
- [Stream Analytics Edge ジョブを開発する](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
