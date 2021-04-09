---
title: Event Hubs のデータを Azure Data Lake Storage Gen1 にキャプチャする
description: Azure Data Lake Storage Gen1 を使用して、Azure Event Hubs で受信したデータをキャプチャする方法を説明します。 まず、前提条件を検証します。
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8be242369ecae2c809a38428284c9ddcad440e3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91578242"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Azure Data Lake Storage Gen1 を使用して Event Hubs からデータをキャプチャする

Azure Data Lake Storage Gen1 を使用して、Azure Event Hubs で受信したデータをキャプチャする方法を説明します。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* **Azure Data Lake Storage Gen1 アカウント**。 これを作成する手順については、[Azure Data Lake Storage Gen1 の使用開始](data-lake-store-get-started-portal.md)に関する記事をご覧ください。

*  **Event Hubs 名前空間**。 手順については、「[Event Hubs 名前空間を作成します](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)」を参照してください。 Data Lake Storage Gen1 アカウントと Event Hubs 名前空間が同じ Azure サブスクリプションにあるようにしてください。


## <a name="assign-permissions-to-event-hubs"></a>Event Hubs にアクセス許可を割り当てる

このセクションでは、Event Hubs からデータをキャプチャするアカウント内にフォルダーを作成します。 さらに、Event Hubs にアクセス許可を割り当てて、Data Lake Storage Gen1 アカウントにデータを書き込むことができるようにします。 

1. Event Hubs からデータをキャプチャする Data Lake Storage Gen1 アカウントを開き、 **[データ エクスプローラー]** をクリックします。

    ![Data Lake Storage Gen1 のデータ エクスプローラー](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Data Lake Storage Gen1 のデータ エクスプローラー")

1.  **[新しいフォルダー]** をクリックし、データをキャプチャするフォルダーの名前を入力します。

    ![Data Lake Storage Gen1 に新しいフォルダーを作成する](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Data Lake Storage Gen1 に新しいフォルダーを作成する")

1. Data Lake Storage Gen1 のルートにアクセス許可を割り当てます。 

    a. **[データ エクスプローラー]** をクリックし、Data Lake Storage Gen1 アカウントのルートを選択して、 **[アクセス]** をクリックします。

    ![アカウントのルートと [アクセス] オプションが強調表示されている Data Explorer のスクリーンショット。](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Data Lake Storage Gen1 ルートにアクセス許可を割り当てる")

    b. **[アクセス]** で **[追加]** 、 **[ユーザーまたはグループの選択]** の順にクリックし、`Microsoft.EventHubs` を検索します。 

    ![[追加] オプション、[ユーザーまたはグループの選択] オプション、Microsoft Eventhubs オプションが強調表示されている [アクセス] ページのスクリーンショット。](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Data Lake Storage Gen1 ルートにアクセス許可を割り当てる")
    
    **[選択]** をクリックします。

    c. **[アクセス許可の割り当て]** で **[アクセス許可の選択]** をクリックします。 **[アクセス許可]** を **[実行]** に設定します。 **[追加先]** を **[このフォルダーとすべての子]** に設定します。 **[追加ユーザー]** を **[アクセス許可エントリと既定のアクセス許可エントリ]** に設定します。

    > [!IMPORTANT]
    > Azure Event Hubs で受信したデータをキャプチャするための新しいフォルダー階層を作成する際、これは宛先フォルダーに確実にアクセスする簡単な方法です。  ただし、子ファイルとフォルダーが多数含まれる最上位フォルダーのすべての子にアクセス許可を追加すると、長い時間がかかる可能性があります。  ルート フォルダーに多数のファイルとフォルダーが含まれている場合、最終的な宛先フォルダーへのパスに `Microsoft.EventHubs` の **実行** 権限を各フォルダーに個別に追加した方が速いことがあります。 

    ![[アクセス許可の選択] オプションが強調表示されている [アクセス許可の割り当て] セクションのスクリーンショット。[アクセス許可の選択] セクションの横では、[実行] オプション、[追加先] オプション、[追加ユーザー] オプションが強調表示されています。](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Data Lake Storage Gen1 ルートにアクセス許可を割り当てる")

    **[OK]** をクリックします。

1. データをキャプチャする Data Lake Storage Gen1 アカウントのフォルダーにアクセス許可を割り当てます。

    a. **[データ エクスプローラー]** をクリックし、Data Lake Storage Gen1 アカウントのフォルダーを選択して、 **[アクセス]** をクリックします。

    ![アカウントのフォルダーと [アクセス] オプションが強調表示されている Data Explorer のスクリーンショット。](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Data Lake Storage Gen1 フォルダーにアクセス許可を割り当てる")

    b. **[アクセス]** で **[追加]** 、 **[ユーザーまたはグループの選択]** の順にクリックし、`Microsoft.EventHubs` を検索します。 

    ![[追加] オプション、[ユーザーまたはグループの選択] オプション、Microsoft Eventhubs オプションが強調表示されている Data Explorer の [アクセス] ページのスクリーンショット。](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Data Lake Storage Gen1 フォルダーにアクセス許可を割り当てる")
    
    **[選択]** をクリックします。

    c. **[アクセス許可の割り当て]** で **[アクセス許可の選択]** をクリックします。 **[アクセス許可]** を **[読み取り、書き込み]** と **[実行]** に設定します。 **[追加先]** を **[このフォルダーとすべての子]** に設定します。 最後に、 **[追加ユーザー]** を **[アクセス許可エントリと既定のアクセス許可エントリ]** に設定します。

    ![[アクセス許可の選択] オプションが強調表示されている [アクセス許可の割り当て] セクションのスクリーンショット。[アクセス許可の選択] セクションの横では、[読み取り]、[書き込み]、[実行] の各オプション、[追加先] オプション、[追加ユーザー] オプションが強調表示されています。](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Data Lake Storage Gen1 フォルダーにアクセス許可を割り当てる")
    
    **[OK]** をクリックします。 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Data Lake Storage Gen1 にデータをキャプチャするよう Event Hubs を構成する

このセクションでは、Event Hubs 名前空間内にイベント ハブを作成します。 さらに、Azure Data Lake Storage Gen1 アカウントにデータをキャプチャするよう、そのイベント ハブを構成します。 このセクションでは、Event Hubs 名前空間を既に作成していることを前提としています。

1. Event Hubs 名前空間の **[概要]** ウィンドウで、 **+ [イベント ハブ]** をクリックします。

    ![[イベント ハブ] オプションが強調表示されている [概要] ペインのスクリーンショット。](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "イベント ハブの作成")

1. 次の値を設定して、Data Lake Storage Gen1 にデータをキャプチャするよう Event Hubs を構成します。

    ![[名前] テキスト ボックス、[キャプチャ] オプション、[キャプチャ プロバイダー] オプション、[Data Lake Store の選択] オプション、[Data Lake パス] オプションが強調表示されている [イベント ハブの作成] ダイアログ ボックスのスクリーンショット。](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "イベント ハブの作成")

    a. イベント ハブの名前を指定します。
    
    b. このチュートリアルでは、 **[パーティション数]** と **[メッセージのリテンション期間]** を既定値に設定します。
    
    c. **[キャプチャ]** を **[オン]** に設定します。 **[時間枠]** (キャプチャの頻度) と **[ウィンドウのサイズ変更]** (キャプチャするデータのサイズ) を設定します。 
    
    d. **[Capture Provider]\(キャプチャ プロバイダー\)** には **[Azure Data Lake Store]** を選択し、前に作成した Data Lake Storage Gen1 アカウントを選択します。 **[Data Lake Path]\(Data Lake パス\)** には、Data Lake Storage Gen1 アカウントに作成したフォルダーの名前を入力します。 入力するのはフォルダーの相対パスだけでかまいません。

    e. **[サンプルのキャプチャ ファイル名の形式]** は既定値のままにします。 このオプションは、キャプチャ フォルダーの下に作成されるフォルダー構造を制御します。

    f. **Create** をクリックしてください。

## <a name="test-the-setup"></a>設定をテストする

Azure Event Hubs にデータを送信することで、ソリューションをテストできるようになりました。 [Azure Event Hubs へのイベントの送信](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md)に関するページで説明されている手順に従ってください。 データの送信を開始すると、指定したフォルダー構造を使用して Data Lake Storage Gen1 に反映されたデータが表示されます。 例として、Data Lake Storage Gen1 アカウントに表示されたフォルダー構造を次のスクリーンショットに示します。

![Data Lake Storage Gen1 でのサンプル EventHub データ](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Data Lake Storage Gen1 でのサンプル EventHub データ")

> [!NOTE]
> Event Hubs に送信されたメッセージがない場合でも、Event Hubs はヘッダーだけの空ファイルを Data Lake Storage Gen1 アカウントに書き込みます。 これらのファイルは、イベント ハブの作成時に指定した期間と同じ間隔で書き込まれます。
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>Data Lake Storage Gen1 でのデータの分析

データが Data Lake Storage Gen1 に保存されると、分析ジョブを実行してデータを処理および高速処理できます。 Azure Data Lake Analytics を使用してこれを行う方法については、[USQL Avro の例](https://github.com/Azure/usql/tree/master/Examples/AvroExamples)のページを参照してください。
  

## <a name="see-also"></a>関連項目
* [Data Lake Storage Gen1 でのデータのセキュリティ保護](data-lake-store-secure-data.md)
* [Azure Storage Blob から Data Lake Storage Gen1 へのデータのコピー](data-lake-store-copy-data-azure-storage-blob.md)
