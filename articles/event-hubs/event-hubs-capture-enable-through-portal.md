---
title: Event Hubs - Azure portal を使用してストリーミング イベントをキャプチャする
description: この記事では、Azure portal を使用し、Azure Event Hubs からストリーム配信されるイベントのキャプチャを有効にする方法について説明します。
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 8c89a6e56c2d0bc7b7ff09d5fd63737009239eb9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "100653953"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Azure Event Hubs からストリーム配信されるイベントのキャプチャを有効にする

Azure [Event Hubs Capture][capture-overview] を使用すると、Event Hubs のストリーミング データを任意の [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) アカウントまたは [Azure Data Lake Storage Gen 1 または Gen 2](https://azure.microsoft.com/services/data-lake-store/) アカウントに自動的に配信することができます。

[Azure Portal](https://portal.azure.com) を使用して、イベント ハブの作成時に Capture を構成することができます。 データは、Azure [Blob Storage](https://azure.microsoft.com/services/storage/blobs/) コンテナーまたは [Azure Data Lake Storage Gen 1 または Gen 2](https://azure.microsoft.com/services/data-lake-store/) アカウントにキャプチャすることができます。

詳細については、[Event Hubs Capture の概要][capture-overview]に関するページを参照してください。

> [!IMPORTANT]
> 宛先となるストレージ (Azure Storage または Azure Data Lake Storage) は、イベント ハブと同じサブスクリプションに存在する必要があります。

## <a name="capture-data-to-azure-storage"></a>Azure Storage へのデータのキャプチャ

Capture は、イベント ハブを作成するときに、**[イベント ハブの作成]** ポータル画面の **[オン]** ボタンをクリックすることによって有効にできます。 その後、**[Capture Provider]\(キャプチャ プロバイダー\)** ボックスの **[Azure Storage]** をクリックして、ストレージ アカウントとコンテナーを指定します。 Event Hubs Capture ではストレージによるサービス間サービス認証が使用されるため、ストレージ接続文字列を指定する必要はありません。 リソース ピッカーにより、ストレージ アカウントのリソース URI が自動的に選択されます。 Azure Resource Manager を使用している場合は、この URI を文字列として明示的に指定する必要があります。

既定の時間ウィンドウは 5 分です。 最小値は 1、最大値は 15 です。 **サイズ** ウィンドウの範囲は 10 ～ 500 MB です。

![キャプチャの時間枠][1]

> [!NOTE]
> キャプチャ期間中にイベントが発生しないときの空のファイルの生成を、有効または無効にすることができます。 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2 へのデータのキャプチャ 

1. Azure Storage アカウントを作成するには、「[ストレージ アカウントの作成](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account)」の記事を参照してください。 **[Advanced]\(詳細\)** タブで **[階層型名前空間]** を **[Enabled]\(有効\)** に設定して、これを Azure Data Lake Storage Gen 2 アカウントにします。
2. イベント ハブを作成するときは、次の手順に従います。 

    1. **[キャプチャ]** で、**[オン]** を選択します。 
    2. キャプチャ プロバイダーとして **[Azure Storage]** を選択します。 **[キャプチャ プロバイダー]** に表示される **[Azure Data Lake Store]** オプションは、Azure Data Lake Storage Gen 1 用です。 Azure Data Lake Storage Gen 2 を使用するには、**[Azure Storage]** を選択します。
    2. **[コンテナーの選択]** ボタンを選択します。 

        ![Data Lake Storage Gen 2 へのキャプチャの有効化](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. 一覧から、**[Azure Data Lake Storage Gen 2]** アカウントを選択します。 

    ![Data Lake Storage Gen 2 の選択](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. **コンテナー** (Data Lake Storage Gen 2 のファイル システム) を選択します。

    ![ストレージのファイル システムの選択](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. **[イベント ハブの作成]** ページで、**[作成]** を選択します。 

    ![[作成] ボタンの選択](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > このユーザー インターフェイス (UI) を使用して Azure Data Lake Storage Gen 2 で作成したコンテナーは、**Storage Explorer** の **[ファイル システム]** に表示されます。 同様に、Data Lake Storage Gen 2 アカウントで作成したファイル システムは、この UI でコンテナーとして表示されます。 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1 へのデータのキャプチャ 

Azure Data Lake Storage Gen 1 にデータをキャプチャするには、次の手順で Data Lake Storage Gen 1 アカウントを作成し、イベント ハブを作成します。

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>Azure Data Lake Storage Gen 1 アカウントおよびフォルダーの作成

1. [Azure portal で Storage Gen 1 の使用を開始する](../data-lake-store/data-lake-store-get-started-portal.md)手順に従って、Data Lake Storage アカウントを作成します。
2. 「[Event Hubs にアクセス許可を割り当てる](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs)」セクションの手順に従って、Event Hubs からのデータをキャプチャするフォルダーを Data Lake Storage Gen 1 アカウントに作成し、Event Hubs にアクセス許可を割り当てて、Data Lake Storage Gen 1 アカウントにデータを書き込むことができるようにします。  


### <a name="create-an-event-hub"></a>イベント ハブの作成

1. イベント ハブは、作成した Azure Data Lake Storage Gen 1 アカウントと同じ Azure サブスクリプションに存在する必要があります。 **[イベント ハブの作成]** ポータル ページの **[Capture]\(キャプチャ\)** の **[オン]** ボタンをクリックしてイベント ハブを作成します。 
2. **[イベント ハブの作成]** ポータル ページで、**[Capture Provider]\(キャプチャ プロバイダー\)** ボックスの **[Azure Data Lake Store]** を選択します。
3. **[Data Lake Store]** ドロップダウン リストの横にある **[ストアの選択]** で、先ほど作成した Data Lake Storage Gen 1 アカウントを指定し、作成したデータ フォルダーのパスを **[Data Lake パス]** フィールドに入力します。

    ![Data Lake Storage アカウントの選択][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>既存のイベント ハブに対する Capture の追加または構成

Capture は、Event Hubs 名前空間内の既存のイベント ハブに対して構成することができます。 既存のイベント ハブで Capture を有効にするか、Capture の設定を変更するには、名前空間をクリックして概要画面を読み込み、Capture の有効化または Capture 設定の変更を行う対象のイベント ハブをクリックします。 最後に、次の図に示すように、開いているページの左側にある **[キャプチャ]** オプションをクリックして設定を編集します。

### <a name="azure-blob-storage"></a>Azure Blob Storage

![Azure Blob Storage の構成][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

![Azure Data Lake Storage Gen 2 の構成](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1 

![Azure Data Lake Storage の構成][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>次のステップ

- Event Hubs Capture の詳細については、[Event Hubs Capture の概要][capture-overview]に関するページを参照してください。
- Event Hubs Capture は Azure Resource Manager テンプレートを使用して構成することもできます。 詳細については、[Azure Resource Manager テンプレートを使用した Capture の有効化](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)に関するページを参照してください。
- [Event Hubs 名前空間をソースとして Azure Event Grid サブスクリプションを作成する方法を確認します](store-captured-data-data-warehouse.md)。
- [Azure Portal で Azure Data Lake Store の使用を開始する](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
