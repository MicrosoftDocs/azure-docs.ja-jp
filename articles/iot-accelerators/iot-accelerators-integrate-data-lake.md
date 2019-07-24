---
title: リモート監視ソリューションを Data Lake Store と統合する - Azure | Microsoft Docs
description: Azure Stream Analytics ジョブを使用して Azure Data Lake Store をリモート監視ソリューションと統合する方法を説明します。
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 021f18f588613817110539d408f9260fb9247895
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53603931"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>リモート監視ソリューションを Azure Data Lake Store と統合する

リモート監視ソリューションで提供される内容を上回る分析が必要になる場合があります。 Azure Data Lake Store は、大量の多様なデータセットのデータを格納するだけでなく、Azure Data Lake Analytics との統合によってオンデマンド分析も提供できるため、そのようなアプリケーションとして最適です。

このハウツー記事では、Azure Stream Analytics ジョブを使用して、リモート監視ソリューションの IoT Hub から Azure Data Lake Store にデータをストリーミングします。

## <a name="prerequisites"></a>前提条件

このハウツー記事を完了するには、以下が必要です。

* [リモート監視ソリューション アクセラレータをデプロイする](quickstart-remote-monitoring-deploy.md)
  * リモート監視ソリューションは、この記事で使用される IoT Hub と Azure Stream Analytics ジョブを Azure サブスクリプション内にデプロイします。
* [Azure Data Lake Store をデプロイする](../data-lake-store/data-lake-store-get-started-portal.md)
  * Data Lake Store は、リモート監視ソリューションと同じリージョンにデプロイする必要があります。
  * アカウント内に "streaming" という名前の[フォルダーを作成します](../data-lake-store/data-lake-store-get-started-portal.md#createfolder)。

## <a name="create-a-consumer-group"></a>コンシューマー グループの作成

リモート監視ソリューションの IoT hub 内に専用のコンシューマー グループを作成します。 これは、Data Lake Store にデータをストリーミングするために Stream Analytics ジョブによって使用されます。

> [!NOTE]
> コンシューマー グループは、Azure IoT Hub からデータをプルするアプリケーションによって使用されます。 5 つの出力コンシューマーのすべてに対して、新しいコンシューマー グループを作成する必要があります。 最大 32 のコンシューマー グループを作成できます。

1. Azure ポータルにサインインします。

1. Azure Portal で **[クラウド シェル]** ボタンをクリックします。

    ![ポータル起動アイコン](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. 次のコマンドを実行して、新しいコンシューマー グループを作成します。

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> リモート監視ソリューションのリソース グループと IoT Hub の名前を使用してください。

## <a name="create-stream-analytics-job"></a>Stream Analytics ジョブを作成する

IoT Hub から Azure Data Lake Store にデータをストリーミングする Azure Stream Analytics ジョブを作成します。

1. **[リソースの作成]** をクリックします。Marketplace から [モノのインターネット] を選択し、**[Stream Analytics ジョブ]** をクリックします。

    ![新しい Stream Analytics ジョブ](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. ジョブの名前を入力し、適切なサブスクリプションとリソース グループを選択します。

1. Data Lake Store に近いリージョンまたは Data Lake Store と同じリージョン内の場所を選択します。 ここでは、[米国東部] を使用します。

1. ホスティング環境は、必ず、既定値である **[クラウド]** のままにしておきます。

1. **Create** をクリックしてください。

    ![Stream Analytics ジョブを作成する](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Stream Analytics ジョブを構成する

1. リモート監視ソリューションのリソース グループ内の **Stream Analytics ジョブ**に移動します。

1. [概要] ページで、**[入力]** をクリックします。

    ![[概要] ページ](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. **[ストリーム入力の追加]** をクリックし、ドロップダウン リストから **[IoT Hub]** を選択します。

    ![入力を追加する](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. [新しい入力] タブで、**IoT Hub** の入力のエイリアスを入力します。

1. [コンシューマー グループ]: ドロップダウンから、先ほど作成したコンシューマー グループを選択します。 ここでは、**streamanalyticsjob** を使用しています。

    ![入力の選択](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. **[Save]** をクリックします。

1. [概要] ページで、**[出力]** をクリックします。

    ![Data Lake Store の追加](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. **[追加]** をクリックし、ドロップダウン リストから **Data Lake Store** を選択します。

    ![出力の追加](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. [新しい出力] タブで、 **DataLakeStore** の出力のエイリアスを入力します。

1. 前の手順で作成した Data Lake Store アカウントを選択し、ストアにデータをストリーミングするフォルダー構造を用意します。

1. [日付の形式] フィールドに、「**/streaming/{date}/{time}**」と入力します。 [日付の形式] と [時刻の形式] は既定のままにします (それぞれ [YYYY/MM/DD] と [HH])。

    ![フォルダー構造の用意](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. **[承認]** をクリックします。

    Stream analytics ジョブにファイル システムへの書き込みアクセスを付与するために、Data Lake Store で承認する必要があります。

    ![Data Lake Store に対する Stream Analytics の承認](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    ポップアップが表示されます。ポップアップを閉じ、承認が完了すると、[承認] ボタンが淡色表示されます。

    > [!NOTE]
    > ポップアップ ウィンドウにエラーが表示された場合は、新しいブラウザー ウィンドウを Incognito モードで開いてやり直してください。

1. **[Save]** をクリックします。

## <a name="edit-the-stream-analytics-query"></a>Stream Analytics クエリを編集する

Azure Stream Analytics では、SQL に似たクエリ言語を使用して、データのストリーミングと目的に応じたデータの変換を実行する入力ソースと、多様なストレージや処理変換先への出力を指定します。

1. [概要] タブで、**[クエリの編集]** をクリックします。

    ![クエリの編集](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. クエリ エディターで、プレースホルダー の [YourOutputAlias] と [YourInputAlias] を、先ほど定義した値に置き換えます。

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Stream Analytics クエリ](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. **[Save]** をクリックします。
1. **[はい]** をクリックして変更を適用します。

## <a name="start-the-stream-analytics-job"></a>Stream Analytics ジョブの開始

1. [概要] タブで、**[開始]** をクリックします。

    ![Stream Analytics ジョブを開始します。](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. [ジョブの開始] タブで、**[カスタム]** をクリックします。

1. データを取得するために、デバイスがストリーミングを開始した時点から数時間遡ったカスタム時間を設定します。

1. **[開始]** をクリックします。

    ![カスタム日付の選択](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    ジョブが実行状態になるまで待ちます。クエリから発生した可能性があるエラーが表示された場合は、構文が正しいことを確認してください。

    ![ジョブを実行中](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    ストリーミング ジョブが、IoT Hub からのデータを読み取りと Data Lake Store へのデータの格納を開始します。 データが Data Lake Store に表示され始めるまで、しばらく時間がかかる場合があります。

## <a name="explore-the-streaming-data"></a>ストリーミング データを探索する

1. Data Lake Store に移動します。

1. [概要] タブで、**[データ エクスプローラー]** をクリックします。

1. データ エクスプローラーで、**/streaming** フォルダーに移動します。 YYYY/MM/DD/HH 形式で作成されたフォルダーが表示されます。

    ![ストリーミング データの探索](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    1 時間あたり 1 つの json ファイルが表示されます。

    ![ストリーミング データの探索](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>次の手順

Azure Data Lake Analytics を使用して、Data Lake Store データセットに関するビッグ データ分析を実行できます。 詳細については、「[Azure Data Lake Analytics のドキュメント](https://docs.microsoft.com/azure/data-lake-analytics)」を参照してください。
