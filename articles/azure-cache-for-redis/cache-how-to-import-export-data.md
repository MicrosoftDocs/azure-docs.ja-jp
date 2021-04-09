---
title: Azure Cache for Redis でデータをインポートまたはエクスポートする
description: Premium Azure Cache for Redis インスタンスを使って Blob Storage との間でデータのインポートとエクスポートを行う方法について説明します
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/31/2017
ms.author: yegu
ms.openlocfilehash: 9ee3b447b2b5f6dfa8972749c3c46ae01f79bfdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96327510"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>Azure Cache for Redis でデータをインポートまたはエクスポートする
Import/Export は Azure Cache for Redis のデータ管理操作です。Azure Cache for Redis データベース (RDB) のスナップショットを Premium キャッシュと Azure Storage アカウント内の BLOB の間でインポートとエクスポートを行うことで、データを Azure Cache for Redis にインポートしたり、Azure Cache for Redis からエクスポートしたりすることができます。

- **Export** - ページ Blob に、Azure Cache for Redis RDB のスナップショットをエクスポートすることができます。
- **Import** - ページ Blob またはブロック Blob から、Azure Cache for Redis RDB のスナップショットをインポートすることができます。

Import/Export により、異なる Azure Cache for Redis インスタンス間での移行や、使用前のキャッシュへのデータ入力が可能になります。

この記事では Azure Cache for Redis でデータをインポートまたはエクスポートする方法を説明し、よく寄せられる質問に回答します。

> [!IMPORTANT]
> Import/Export は、[Premium レベル](cache-overview.md#service-tiers)のキャッシュでのみ使用できます。
>
>

## <a name="import"></a>[インポート]
Import は、任意のクラウドまたは環境で稼働している任意の Redis サーバー (Linux や Windows のほか、アマゾン ウェブ サービスをはじめとする各種クラウド プロバイダーで稼働している Redis など) から Redis と互換性のある RDB ファイルを取り込むときに使用できます。 データをインポートすると、あらかじめデータが入力されたキャッシュを簡単に作成できます。 インポート処理中に、Azure Cache for Redis では RDB ファイルが Azure Storage からメモリに読み込まれて、キーがキャッシュに挿入されます。

> [!NOTE]
> インポート操作を開始する前に、Redis データベース (RDB) ファイルが、Azure Cache for Redis インスタンスと同じリージョンとサブスクリプションにある Azure Storage 内のページ BLOB またはブロック BLOB にアップロードされる設定になっていることを確認してください。 詳細については、 [Azure Blob Storage の使用](../storage/blobs/storage-quickstart-blobs-dotnet.md)に関するページをご覧ください。 [Azure Cache for Redis の Export](#export) 機能を使って RDB ファイルをエクスポートした場合、そのファイルは既にページ BLOB に格納されており、インポートの準備ができています。
>
>

1. エクスポートされた 1 つ以上のキャッシュ BLOB をインポートするには、Azure Portal で [キャッシュを参照](cache-configure.md#configure-azure-cache-for-redis-settings)し、 **[リソース]** メニューの **[データのインポート]** をクリックします。

    ![データのインポート](./media/cache-how-to-import-export-data/cache-import-data.png)
2. **[BLOB の選択]** をクリックして、インポートするデータを含むストレージ アカウントを選択します。

    ![ストレージ アカウントの選択](./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png)
3. インポートするデータが含まれているコンテナーをクリックします。

    ![Choose container](./media/cache-how-to-import-export-data/cache-import-choose-container.png)
4. BLOB 名の左側の領域をクリックして、インポートする 1 つ以上の BLOB を選択し、 **[選択]** をクリックします。

    ![Choose blobs](./media/cache-how-to-import-export-data/cache-import-choose-blobs.png)
5. **[インポート]** をクリックしてインポート処理を開始します。

   > [!IMPORTANT]
   > インポート処理中は、キャッシュ クライアントからキャッシュにアクセスすることはできません。また、キャッシュ内の既存データはすべて削除されます。
   >
   >

    ![[インポート]](./media/cache-how-to-import-export-data/cache-import-blobs.png)

    インポート操作の進行状況を監視するには、Azure Portal からの通知を確認するか、[監査ログ](../azure-resource-manager/management/view-activity-logs.md)のイベントを確認します。

    ![Import progress](./media/cache-how-to-import-export-data/cache-import-data-import-complete.png)

## <a name="export"></a>[エクスポート]
Export では、Azure Cache for Redis に格納されたデータを、Redis と互換性のある RDB ファイルにエクスポートできます。 この機能を使えば、ある Azure Cache for Redis インスタンスから、別のインスタンスまたは別の Redis サーバーにデータを移動できます。 エクスポート処理中に、Azure Cache for Redis サーバー インスタンスをホストしている VM に一時ファイルが作成され、そのファイルが、指定されているストレージ アカウントにアップロードされます。 エクスポート処理が完了したら、処理の成否にかかわらず、この一時ファイルは削除されます。

1. キャッシュの現在の内容をストレージにエクスポートするには、Azure Portal で [キャッシュを参照](cache-configure.md#configure-azure-cache-for-redis-settings)し、 **[リソース]** メニューの **[データのエクスポート]** をクリックします。

    ![Contoso5premium のナビゲーション ウィンドウで、[管理] 一覧の [データのエクスポート] オプションが強調表示されています。](./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png)
2. **[ストレージ コンテナーの選択]** をクリックし、目的のストレージ アカウントを選択します。 ストレージ アカウントは、キャッシュと同じサブスクリプションおよびリージョン内にある必要があります。

   > [!IMPORTANT]
   > エクスポートが機能するページ BLOB は、クラシック ストレージ アカウントと Resource Manager ストレージ アカウントの両方でサポートされていますが、現時点では BLOB ストレージ アカウントではサポートされていません。 詳細については、「[Azure ストレージ アカウントの概要](../storage/common/storage-account-overview.md)」を参照してください。
   >

    ![ストレージ アカウント](./media/cache-how-to-import-export-data/cache-export-data-choose-account.png)
3. 目的の BLOB コンテナーを選び、 **[選択]** をクリックします。 新しいコンテナーを使用するには、まず **[コンテナーの追加]** をクリックして新しいコンテナーを追加したうえで、一覧からそれを選択します。

    ![Contoso55 の [コンテナー] で、+ 記号の [コンテナー] オプションが強調表示されています。 一覧には cachesaves という 1 つのコンテナーがあり、選択されて強調表示されています。 [選択] オプションが選択されて強調表示されています。](./media/cache-how-to-import-export-data/cache-export-data-container.png)
4. **BLOB 名のプレフィックス** を入力し、 **[エクスポート]** をクリックしてエクスポート処理を開始します。 BLOB 名のプレフィックスは、このエクスポート操作によって生成されるファイル名のプレフィックスとして使用されます。

    ![[エクスポート]](./media/cache-how-to-import-export-data/cache-export-data.png)

    エクスポート操作の進行状況を監視するには、Azure Portal からの通知を確認するか、[監査ログ](../azure-resource-manager/management/view-activity-logs.md)のイベントを確認します。

    ![データのエクスポートの完了](./media/cache-how-to-import-export-data/cache-export-data-export-complete.png)

    エクスポート処理中でも、キャッシュは使用可能な状態のままです。

## <a name="importexport-faq"></a>Import/Export の FAQ
このセクションでは、Import/Export 機能についてよく寄せられる質問を掲載しています。

* [Import/Export はどの価格レベルで使用できますか?](#what-pricing-tiers-can-use-importexport)
* [どの Redis サーバーからでもデータをインポートできるのでしょうか?](#can-i-import-data-from-any-redis-server)
* [RDB のどのバージョンをインポートできますか?](#what-rdb-versions-can-i-import)
* [Import/Export 操作中にキャッシュを使うことはできますか?](#is-my-cache-available-during-an-importexport-operation)
* [Redis クラスターで Import/Export を使うことはできますか?](#can-i-use-importexport-with-redis-cluster)
* [Import/Export がカスタム データベース設定を操作する方法](#how-does-importexport-work-with-a-custom-databases-setting)
* [Import/Export と Redis の永続化にはどのような違いがありますか?](#how-is-importexport-different-from-redis-persistence)
* [PowerShell、CLI、またはその他の管理クライアントを使って Import/Export を自動化することはできますか?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Import/Export 操作中にタイムアウト エラーが発生しました。これはどういうことですか?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Azure Blob Storage にデータをエクスポートしているときにエラーが発生しました。なぜでしょうか?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Import/Export はどの価格レベルで使用できますか?
Import/Export は Premium 価格レベルでのみ使用できます。

### <a name="can-i-import-data-from-any-redis-server"></a>どの Redis サーバーからでもデータをインポートできるのでしょうか?
はい。インポートできるのは Azure Cache for Redis インスタンスからエクスポートされたデータだけではありません。任意のクラウドや環境 (Linux や Windows のほか、アマゾン ウェブ サービスなどの各種クラウド プロバイダー) で稼働している任意の Redis サーバーから RDB ファイルをインポートできます。 これを行うには、RDB ファイルを目的の Redis サーバーから Azure ストレージ アカウント内のページ BLOB またはブロック BLOB にアップロードし、さらに Premium Azure Cache for Redis インスタンスにインポートします。 具体的には、運用環境のキャッシュからデータをエクスポートし、テストまたは移行のためのステージング環境の一部として使用するキャッシュにインポートする場合が考えられます。

> [!IMPORTANT]
> ページ BLOB を使用する場合、Azure Cache for Redis 以外の Redis サーバーからエクスポートされたデータを正常にインポートするには、ページ BLOB のサイズを 512 バイト境界に合わせる必要があります。 必要なバイト パディングを実行するサンプル コードについては、[ページ BLOB をアップロードするためのサンプル コード](https://github.com/JimRoberts-MS/SamplePageBlobUpload)に関するページを参照してください。
>
>

### <a name="what-rdb-versions-can-i-import"></a>RDB のどのバージョンをインポートできますか?

Azure Cache for Redis は、RDB バージョン 7 までの RDB のインポートをサポートしています。

### <a name="is-my-cache-available-during-an-importexport-operation"></a>Import/Export 操作中にキャッシュを使うことはできますか?
* **エクスポート** - キャッシュは使用可能な状態のままです。エクスポート操作中もキャッシュの使用を続行できます。
* **インポート** - インポート処理が開始されると、キャッシュは使用できなくなります。インポート処理が終了すると、キャッシュは使用可能になります。

### <a name="can-i-use-importexport-with-redis-cluster"></a>Redis クラスターで Import/Export を使うことはできますか?
はい。さらに、クラスター化されたキャッシュとクラスター化されていないキャッシュの間でインポート/エクスポートを実行することもできます。 Redis クラスターは、[データベース 0 のみをサポート](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)するため、0 以外のデータベースのデータはインポートされません。 クラスター化されたキャッシュのデータがインポートされると、クラスターのシャード間でキーが再配分されます。

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Import/Export がカスタム データベース設定を操作する方法
価格レベルによってさまざまな[データベースの制限](cache-configure.md#databases)があるため、キャッシュの作成中に `databases` の設定にカスタム値を設定する場合、インポート時の考慮事項がいくつかあります。

* エクスポートしたレベルより低い `databases` の制限の価格レベルにインポートする場合:
  * すべての価格レベルが 16 の `databases` の既定の数を使っている場合、データは失われません。
  * インポートしているレベルの制限範囲に含まれるユーザー設定の数値の `databases` を使用している場合、データは失われません。
  * エクスポートしたデータが新しいレベルの制限を超えるデータベースのデータを含む場合、レベルの高いデータベースのデータはインポートされません。

### <a name="how-is-importexport-different-from-redis-persistence"></a>Import/Export と Redis の永続化にはどのような違いがありますか?
Azure Cache for Redis 永続化では、Redis に保管されたデータを Azure Storage で永続化することができます。 永続化が構成されている場合、Azure Cache for Redis では、構成可能なバックアップ頻度に基づき Redis バイナリ形式でそのスナップショットがディスクに保持されます。 プライマリ キャッシュとレプリカ キャッシュの両方が無効になるような致命的なイベントが発生した場合、最新のスナップショットを使用してキャッシュ データが自動的に復元されます。 詳しくは、「[Premium Azure Cache for Redis のデータ永続化の構成方法](cache-how-to-premium-persistence.md)」をご覧ください。

Import/Export では、Azure Cache for Redis へのデータの取り込みと Azure Redis Cache からのデータのエクスポートが可能です。 Redis 永続化を使用してバックアップと復元が構成されることはありません。

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>PowerShell、CLI、またはその他の管理クライアントを使って Import/Export を自動化することはできますか?
はい、PowerShell の方法については、「[Azure Cache for Redis をインポートするには](cache-how-to-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis)」および「[Azure Cache for Redis をエクスポートするには](cache-how-to-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis)」をご覧ください。

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Import/Export 操作中にタイムアウト エラーが発生しました。 これはどういうことですか?
**[データのインポート]** ブレードまたは **[データのエクスポート]** ブレードに操作を開始しないまま留まっている時間が 15 分を超えると、次の例のようなエラー メッセージのエラーが発生します。

```output
The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.
```

これを解決するには、インポート操作またはエクスポート操作を 15 分以内に開始してください。

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Azure Blob Storage にデータをエクスポートしているときにエラーが発生しました。 なぜでしょうか?
Export は、ページ BLOB として格納されている RDB ファイルでのみ機能します。 それ以外のタイプの BLOB は、ホット層とクール層の BLOB ストレージ アカウントも含め、現時点ではサポートされていません。 詳細については、「[Azure ストレージ アカウントの概要](../storage/common/storage-account-overview.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
Azure Cache for Redis の機能について

* [Azure Cache for Redis サービス レベル](cache-overview.md#service-tiers)