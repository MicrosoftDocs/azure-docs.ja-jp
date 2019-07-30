---
title: Azure Stream Analytics on IoT Edge
description: Azure Stream Analytics でエッジ ジョブを作成し、Azure IoT Edge で実行中のデバイスに展開します。
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 07/01/2019
ms.custom: seodec18
ms.openlocfilehash: 8e3b6d0fbefb8e3d3437fd5e24f929e453c573df
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621007"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics on IoT Edge
 
Azure Stream Analytics (ASA) on IoT Edge を使用することで、開発者は、ほぼリアルタイムの分析インテリジェンスをより IoT デバイスに近い場所に展開し、デバイス生成データの価値を最大限に活用できるようになります。 Azure Stream Analytics は、低遅延、回復性、帯域幅の効率的な使用、およびコンプライアンスを考慮して設計されています。 企業は制御ロジックを生産工程に近い場所に展開し、クラウドで実行されるビッグ データ分析を補完できるようになりました。  

Azure Stream Analytics on IoT Edge は [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) フレームワークの内部で実行されます。 ASA でジョブが作成されたら、IoT Hub を使用して展開し、管理できます。

## <a name="scenarios"></a>シナリオ
![IoT Edge の概要図](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **コマンドと制御の待機時間が短い**:たとえば、製造安全システムは、非常に短い待機時間で運用データに応答する必要があります。 ASA on IoT Edge を使用することで、センサー データをほぼリアルタイムで分析し、異常を検出した際はコマンドを発行してマシンを停止したり、アラートをトリガーしたりできます。
*   **クラウドへの接続が制限されている**:リモート採掘装置、接続された船舶、海洋掘削など、ミッション クリティカルなシステムでは、クラウド接続が断続的なときでも、データを分析し、そのデータに対応する必要があります。 ASA では、ネットワーク接続とは関係なくストリーミング ロジックが実行され、さらなる処理や保存のためにクラウドに送信するデータを選択できます。
* **帯域幅が制限されている**:ジェット エンジンやコネクテッド カーによって生成されるデータ量は膨大になる可能性があるため、クラウドに送信する前に、データをフィルター処理または前処理する必要があります。 ASA を使用すると、クラウドに送信する必要があるデータをフィルター処理したり集計したりできます。
* **コンプライアンス**:法令順守では、一部のデータについてはクラウドに送信する前に、ローカルでの匿名化または集計が必要になる可能性があります。

## <a name="edge-jobs-in-azure-stream-analytics"></a>Azure Stream Analytics のエッジ ジョブ
### <a name="what-is-an-edge-job"></a>"エッジ" ジョブとは

ASA エッジ ジョブは、[Azure IoT Edge デバイス](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)に展開されたコンテナー内で実行されます。 これは 2 つの部分で構成されます。
1.  ジョブ定義の役割を担うクラウド部分: ユーザーが、クラウド内の入力、出力、クエリ、およびその他の設定 (順不同のイベントなど) を定義します。
2.  IoT デバイス上で実行されているモジュール。 ASA エンジンが含まれ、クラウドからジョブ定義を受け取ります。 

ASA では、IoT ハブを使用してエッジ ジョブをデバイスに展開します。 IoT Edge の展開の詳細については、[こちら](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)をご覧ください。

![Azure Stream Analytics エッジ ジョブ](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>インストール手順
手順の概要を次の表に示します。 詳細については、以降のセクションを参照してください。

|      |手順   | メモ   |
| ---   | ---   |  ---      |
| 1   | **ストレージ コンテナーを作成する**   | ストレージ コンテナーを使用してジョブ定義を保存します。コンテナーには、IoT デバイスからアクセスできます。 <br>  既存のストレージ コンテナーを再利用できます。     |
| 2   | **ASA エッジ ジョブを作成する**   |  新しいジョブを作成し、**ホスティング環境**として **Edge** を選択します。 <br> このジョブはクラウドから作成および管理され、お使いの IoT Edge デバイスで実行されます。     |
| 3   | **デバイス上に IoT Edge 環境を設定する**   | [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) または [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) 用の手順。          |
| 4   | **ASA を IoT Edge デバイスに展開する**   |  ASA ジョブ定義は、先ほど作成したストレージ コンテナーにエクスポートされます。       |

最初の ASA ジョブを IoT Edge に展開するには、[順を追って解説したこちらのチュートリアル](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)に従ってください。 次のビデオは、IoT Edge デバイスで Stream Analytics ジョブを実行するプロセスを理解するのに役立ちます。  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>ストレージ コンテナーを作成する
ASA のコンパイルされたクエリとジョブ構成をエクスポートするには、ストレージ コンテナーが必要です。 これは、特定のクエリで ASA Docker イメージを構成するときに使用されます。 
1. Azure Portal からストレージ アカウントを作成するには、[こちらの手順](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)に従ってください。 ASA でアカウントを使用するために、オプションを変更する必要はありません。
2. 新しく作成したストレージ アカウントで、Blob Storage コンテナーを作成します。
    1. **[BLOB]** 、 **[+ コンテナー]** の順にクリックします。 
    2. 名前を入力し、コンテナーを **[プライベート]** のままにします。

#### <a name="create-an-asa-edge-job"></a>ASA Edge ジョブを作成する
> [!Note]
> このチュートリアルでは、Azure Portal を使用した ASA ジョブの作成について説明します。 [Visual Studio プラグインを使用して ASA エッジ ジョブを作成する](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)こともできます

1. Azure Portal で、新しい "Stream Analytics ジョブ" を作成します。 新しい ASA ジョブを作成するには、[こちらの直接リンク](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob)を参照してください。

2. 作成画面で、**ホスティング環境**として **Edge** を選択します (次の図を参照)

   ![Edge 上に Stream Analytics ジョブを作成する](media/stream-analytics-edge/create-asa-edge-job.png)
3. ジョブ定義
    1. **入力ストリームの定義**。 ジョブに対して 1 つ以上の入力ストリームを定義します。
    2. 参照データの定義 (省略可能)。
    3. **出力ストリームの定義**。 ジョブに対して 1 つ以上の出力ストリームを定義します。 
    4. **クエリの定義**。 インライン エディターを使用して、クラウドで ASA クエリを定義します。 ASA エッジに対して有効になっている構文が、コンパイラによって自動的にチェックされます。 サンプル データをアップロードして、クエリをテストすることもできます。 

4. **[IoT Edge の設定]** メニュー で、ストレージ コンテナー情報を設定します。

5. オプション設定を設定します
    1. **イベントの順序付け**。 ポータルで順不同ポリシーを構成できます。 ドキュメントは[こちら](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)で入手できます。
    2. **ロケール**。 内部化形式を設定します。



> [!Note]
> 展開が作成されると、ASA によってジョブ定義がストレージ コンテナーにエクスポートされます。 このジョブ定義は、展開中に変更されることはありません。 このため、エッジで実行されているジョブを更新するには、ASA でジョブを編集し、IoT ハブで新しい展開を作成する必要があります。


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>デバイスで IoT Edge 環境を設定する
エッジ ジョブは、Azure IoT Edge が実行されているデバイスに展開できます。
これを行うには、次の手順に従う必要があります。
- IoT ハブを作成します。
- Docker と IoT Edge ランタイムをエッジ デバイスにインストールします。
- IoT ハブで **IoT Edge デバイス**としてデバイスを設定します。

この手順の説明については、[Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) または [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) の IoT Edge ドキュメントを参照してください。  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>ASA を IoT Edge デバイスに展開する
##### <a name="add-asa-to-your-deployment"></a>ASA を展開に追加する
- Azure portal で IoT Hub を開き、 **[IoT Edge]** に移動して、この展開の対象となるデバイスをクリックします。
- **[モジュールの設定]** 、 **[+ 追加]** 、 **[Azure Stream Analytics モジュール]** の順に選択します。
- サブスクリプションと、作成した ASA Edge ジョブを選択します。 [保存] をクリックします。
![ASA モジュールを展開に追加する](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> この手順中に、ASA によって、ストレージ コンテナー内に "EdgeJobs" という名前のフォルダーが作成されます (まだ存在しない場合)。 "EdgeJobs" フォルダーには、展開ごとに新しいサブフォルダーが作成されます。
> ジョブを IoT Edge デバイスに展開すると、ASA は、ジョブ定義ファイルに対して共有アクセス署名 (SAS) を作成します。 SAS キーは、デバイス ツインを使用して、IoT Edge デバイスに安全に送信されます。 このキーの有効期限は、作成日から 3 年間です。 IoT Edge ジョブを更新すると SAS が変更されますが、イメージのバージョンは変更されません。 **更新**したら、以下の展開ワークフローに従ってください。更新通知がデバイスに記録されます。


IoT Edge の展開の詳細については、[こちらのページ](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)をご覧ください。


##### <a name="configure-routes"></a>ルートを構成する
IoT Edge は、モジュール間およびモジュールと IoT ハブの間でメッセージを宣言的にルーティングするための方法を提供します。 完全な構文については、[こちら](https://docs.microsoft.com/azure/iot-edge/module-composition)をご覧ください。
ASA ジョブに作成された入力および出力の名前を、ルーティングのエンドポイントとして使用できます。  

###### <a name="example"></a>例

```json
{
    "routes": {
        "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
        "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream",
        "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")"
    }
}

```
この例は、次の図で説明するシナリオのルートを示しています。 これには "**ASA**" というエッジ ジョブが含まれており、入力の名前は "**temperature**" で、出力の名前は "**alert**" です。
![メッセージ ルーティングの例の図](media/stream-analytics-edge/edge-message-routing-example.png)

この例は、次のルートを定義しています。
- **temperature** という名前の入力に対して、**tempSensor** からのすべてのメッセージが、**ASA** という名前のモジュールに送信されます。
- **ASA** モジュールのすべての出力が、このデバイス ($upstream) にリンクされた IoT ハブに送信されます。
- **ASA** モジュールのすべての出力が、**tempSensor** の **control** エンドポイントに送信されます。


## <a name="technical-information"></a>技術情報
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>クラウド ジョブと比較した IoT Edge ジョブの現在の制限事項
目的は、IoT Edge ジョブとクラウド ジョブの間の類似性を確認することです。 SQL クエリ言語のほとんどの機能がサポートされており、クラウドと IoT Edge の両方で同じロジックを実行できます。
ただし、次の機能はエッジ ジョブではまだサポートされていません。
* JavaScript でのユーザー定義関数 (UDF)。 UDF は、[IoT Edge ジョブ向けの C#](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (プレビュー) で使用できます。
* ユーザー定義集計 (UDA)。
* Azure ML 関数
* 1 つの手順での 14 を超える集計の使用。
* 入力/出力の AVRO 形式。 現時点では、CSV と JSON のみがサポートされています。
* 次の SQL 演算子:
    * PARTITION BY
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>ランタイムとハードウェア要件
ASA on IoT Edge を実行するには、[Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) を実行できるデバイスが必要です。 

ASA と Azure IoT Edge では、**Docker** コンテナーを使用して、複数のホスト オペレーティング システム (Windows、Linux) で実行されるポータブル ソリューションを提供します。

ASA on IoT Edge は、x86-64 または ARM (Advanced RISC Machines) アーキテクチャの両方で実行される Windows および Linux イメージとして使用可能になります。 


### <a name="input-and-output"></a>入力と出力
#### <a name="input-and-output-streams"></a>入力ストリームと出力ストリーム
ASA Edge ジョブは、IoT Edge デバイスで実行されている他のモジュールから入力と出力を取得できます。 特定のモジュールとの接続を確立するには、展開時にルーティング構成を設定します。 詳細については、[IoT Edge モジュールの構成に関するドキュメント](https://docs.microsoft.com/azure/iot-edge/module-composition)をご覧ください。

入力と出力の両方で、CSV 形式および JSON 形式がサポートされます。

ASA ジョブで作成した入力ストリームおよび出力ストリームごとに、対応するエンドポイントが、展開済みのモジュールに作成されます。 こうしたエンドポイントは、展開のルートで使用できます。

現在、サポートされているストリーム入力およびストリーム出力の種類は Edge Hub だけです。 参照入力では参照ファイルがサポートされます。 他の出力には、ダウンストリームのクラウド ジョブを使用して到達できます。 たとえば、Edge 内でホストされている Stream Analytics ジョブで出力が Edge Hub に送信された後、Edge Hub から IoT Hub に出力を送信できます。 クラウドでホストされている 2 つ目の Azure Stream Analytics ジョブを、IoT Hub からの入力と Power BI または別の出力の種類への出力で使用できます。



##### <a name="reference-data"></a>参照データ
参照データ (ルックアップ テーブルとも呼ばれます) は、有限のデータ セットで、本来は静的であるか、あまり変更されません。 これは、参照の実行やデータ ストリームとの関連付けに使用されます。 Azure Stream Analytics ジョブで参照データを使用するには、一般にクエリで[参照データの結合](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics)を使用します。 詳しくは、「[Stream Analytics での参照に参照データを使用する](stream-analytics-use-reference-data.md)」をご覧ください。

ローカルの参照データのみがサポートされます。 ジョブが IoT Edge デバイスに展開されると、ユーザー定義のファイル パスから参照データが読み込まれます。

Edge 上で参照データを使用してジョブを作成するには:

1. ジョブに対して新しい入力を作成します。

2. **ソースの種類**として**参照データ**を選択します。

3. デバイス上で参照データ ファイルを準備します。 Windows コンテナーの場合は、ローカル ドライブ上に参照データ ファイルを配置し、ローカル ドライブを Docker コンテナーと共有します。 Linux コンテナーの場合は、Docker ボリュームを作成し、ボリュームにデータ ファイルを設定します。

4. ファイル パスを設定します。 Windows ホスト OS と Windows コンテナーの場合は、絶対パス `E:\<PathToFile>\v1.csv` を使用します。 Windows ホスト OS と Linux コンテナーまたは Linux OS と Linux コンテナーの場合は、ボリューム内のパス `<VolumeName>/file1.txt` を使用します。

![IoT Edge 上の Azure Stream Analytics ジョブ用の新しい参照データ入力](./media/stream-analytics-edge/Reference-Data-New-Input.png)

IoT Edge の更新プログラム上の参照データは、デプロイによってトリガーされます。 トリガーされると、実行中のジョブを停止することなく、更新されたデータが ASA モジュールによって取得されます。

参照データを更新する方法は 2 つあります。
* Azure portal から ASA ジョブ内の参照データ パスを更新します。
* IoT Edge デプロイを更新します。

## <a name="license-and-third-party-notices"></a>ライセンスとサード パーティに関する通知
* [Azure Stream Analytics on IoT Edge ライセンス](https://go.microsoft.com/fwlink/?linkid=862827)。 
* [Azure Stream Analytics on IoT Edge に関するサード パーティへの通知](https://go.microsoft.com/fwlink/?linkid=862828)。

## <a name="azure-stream-analytics-module-image-information"></a>Azure Stream Analytics モジュールのイメージ情報 

このバージョン情報は、2019 年 6 月 27 日に最終更新が行われました。

- イメージ: `asaedge.azurecr.io/public/azure-stream-analytics/azureiotedge:1.0.3-linux-amd64`
   - 基本イメージ: microsoft/dotnet:2.1.6-runtime-alpine3.7
   - プラットフォーム:
      - アーキテクチャ: amd64
      - OS: linux
  
- イメージ: `asaedge.azurecr.io/public/azure-stream-analytics/azureiotedge:1.0.3-linux-arm32v7`
   - 基本イメージ: microsoft/dotnet:2.1.6-runtime-bionic-arm32v7
   - プラットフォーム:
      - アーキテクチャ: arm
      - OS: linux
  
- イメージ: `asaedge.azurecr.io/public/azure-stream-analytics/azureiotedge:1.0.3-windows-amd64`
   - 基本イメージ: microsoft/dotnet:2.1.6-runtime-nanoserver-1809
   - プラットフォーム:
      - アーキテクチャ: amd64
      - OS: windows
      
      
## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次の手順

* [Azure IoT Edge の詳細](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [ASA on IoT Edge チュートリアル](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Visual Studio Tools を使用して Stream Analytics Edge ジョブを作成する](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)
* [API を使用して Stream Analytics 向けの CI/CD を実装する](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
