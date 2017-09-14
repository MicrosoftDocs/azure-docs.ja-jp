---
title: "Event Hubs のデータを Azure Data Lake Store にキャプチャする | Microsoft Docs"
description: "Azure Data Lake Store を使用して Event Hubs からデータをキャプチャします"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: a9e69576958ae96d22a4eb03d0df429f0b307298
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---
# <a name="use-azure-data-lake-store-to-capture-data-from-event-hubs"></a>Azure Data Lake Store を使用して Event Hubs からデータをキャプチャする

Azure Data Lake Store を使用して、Azure Event Hubs で受信したデータをキャプチャする方法を説明します。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure Data Lake Store アカウント**。 このアカウントを作成する手順については、[Azure Data Lake Store の使用開始](data-lake-store-get-started-portal.md)に関するページを参照してください。

*  **Event Hubs 名前空間**。 手順については、「[Event Hubs 名前空間を作成します](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)」を参照してください。 Data Lake Store アカウントと Event Hubs 名前空間が同じ Azure サブスクリプションにあるようにしてください。


## <a name="assign-permissions-to-event-hubs"></a>Event Hubs にアクセス許可を割り当てる

このセクションでは、Event Hubs からデータをキャプチャするアカウント内にフォルダーを作成します。 さらに、Event Hubs にアクセス許可を割り当てて、Data Lake Store アカウントにデータを書き込むことができるようにします。 

1. Event Hubs からデータをキャプチャする Data Lake Store アカウントを開き、**[データ エクスプローラー]** をクリックします。

    ![Data Lake Store データ エクスプローラー](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Data Lake Store データ エクスプローラー")

2.  **[新しいフォルダー]** をクリックし、データをキャプチャするフォルダーの名前を入力します。

    ![Data Lake Store に新しいフォルダーを作成する](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Data Lake Store に新しいフォルダーを作成する")

3. Data Lake Store のルートにアクセス許可を割り当てます。 

    a. **[データ エクスプローラー]** をクリックし、Data Lake Store アカウントのルートを選択して、**[アクセス]** をクリックします。

    ![Data Lake Store のルートにアクセス許可を割り当てる](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Data Lake Store のルートにアクセス許可を割り当てる")

    b. **[アクセス]** で **[追加]**、**[ユーザーまたはグループの選択]** の順にクリックし、`Microsoft.EventHubs` を検索します。 

    ![Data Lake Store のルートにアクセス許可を割り当てる](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Data Lake Store のルートにアクセス許可を割り当てる")
    
    **[選択]**をクリックします。

    c. **[アクセス許可の割り当て]** で **[アクセス許可の選択]** をクリックします。 **[アクセス許可]** を **[実行]** に設定します。 **[追加先]** を **[このフォルダーとすべての子]** に設定します。 **[追加ユーザー]** を **[アクセス許可エントリと既定のアクセス許可エントリ]** に設定します。

    ![Data Lake Store のルートにアクセス許可を割り当てる](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Data Lake Store のルートにアクセス許可を割り当てる")

    **[OK]**をクリックします。

4. データをキャプチャする Data Lake Store アカウントのフォルダーにアクセス許可を割り当てます。

    a. **[データ エクスプローラー]** をクリックし、Data Lake Store アカウントのフォルダーを選択して、**[アクセス]** をクリックします。

    ![Data Lake Store のフォルダーにアクセス許可を割り当てる](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Data Lake Store のフォルダーにアクセス許可を割り当てる")

    b. **[アクセス]** で **[追加]**、**[ユーザーまたはグループの選択]** の順にクリックし、`Microsoft.EventHubs` を検索します。 

    ![Data Lake Store のフォルダーにアクセス許可を割り当てる](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Data Lake Store のフォルダーにアクセス許可を割り当てる")
    
    **[選択]**をクリックします。

    c. **[アクセス許可の割り当て]** で **[アクセス許可の選択]** をクリックします。 **[アクセス許可]** を **[読み取り、書き込み]** と **[実行]** に設定します。 **[追加先]** を **[このフォルダーとすべての子]** に設定します。 最後に、**[追加ユーザー]** を **[アクセス許可エントリと既定のアクセス許可エントリ]** に設定します。

    ![Data Lake Store のフォルダーにアクセス許可を割り当てる](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Data Lake Store のフォルダーにアクセス許可を割り当てる")
    
    **[OK]**をクリックします。 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-store"></a>Data Lake Store にデータをキャプチャするよう Event Hubs を構成する

このセクションでは、Event Hubs 名前空間内にイベント ハブを作成します。 さらに、Azure Data Lake Store アカウントにデータをキャプチャするよう、そのイベント ハブを構成します。 このセクションでは、Event Hubs 名前空間を既に作成していることを前提としています。

2. Event Hubs 名前空間の **[概要]** ウィンドウで、**+ [イベント ハブ]** をクリックします。

    ![イベント ハブを作成する](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "イベント ハブを作成する")

3. 次の値を設定して、Data Lake Store にデータをキャプチャするよう Event Hubs を構成します。

    ![イベント ハブを作成する](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "イベント ハブを作成する")

    a. イベント ハブの名前を指定します。
    
    b. このチュートリアルでは、**[パーティション数]** と **[メッセージのリテンション期間]** を既定値に設定します。
    
    c. **[キャプチャ]** を **[オン]** に設定します。 **[時間枠]** (キャプチャの頻度) と **[ウィンドウのサイズ変更]** (キャプチャするデータのサイズ) を設定します。 
    
    d. **[Capture Provider]\(キャプチャ プロバイダー\)** には **[Azure Data Lake Store]** を選択し、前に作成した Data Lake Store を選択します。 **[Data Lake Path]\(Data Lake パス\)** には、Data Lake Store アカウントに作成したフォルダーの名前を入力します。 入力するのはフォルダーの相対パスだけでかまいません。

    e. **[サンプルのキャプチャ ファイル名の形式]** は既定値のままにします。 このオプションは、キャプチャ フォルダーの下に作成されるフォルダー構造を制御します。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[作成]**をクリックします。

## <a name="test-the-setup"></a>設定をテストする

Azure Event Hubs にデータを送信することで、ソリューションをテストできるようになりました。 [Azure Event Hubs へのイベントの送信](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md)に関するページで説明されている手順に従ってください。 データの送信を開始すると、指定したフォルダー構造を使用して Data Lake Store に反映されたデータが表示されます。 例として、Data Lake Store に表示されたフォルダー構造を次のスクリーンショットに示します。

![Data Lake Store のサンプル EventHub データ](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Data Lake Store のサンプル EventHub データ")

> [!NOTE]
> Event Hubs に送信されたメッセージがない場合でも、Event Hubs はヘッダーだけの空ファイルを Data Lake Store アカウントに書き込みます。 これらのファイルは、イベント ハブの作成時に指定した期間と同じ間隔で書き込まれます。
> 
>

## <a name="analyze-data-in-data-lake-store"></a>Analyze data in Data Lake Store

データが Data Lake Store に保存されると、分析ジョブを実行してデータを処理および高速処理できます。 Azure Data Lake Analytics を使用してこれを行う方法については、[USQL Avro の例](https://github.com/Azure/usql/tree/master/Examples/AvroExamples)のページを参照してください。
  

## <a name="see-also"></a>関連項目
* [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)
* [Azure Storage BLOB から Data Lake Store へのデータのコピー](data-lake-store-copy-data-azure-storage-blob.md)

