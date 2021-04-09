---
title: Azure Storage BLOB から Azure Data Lake Storage Gen1 にデータをコピーする
description: AdlCopy ツールを使用して Azure Storage BLOB から Azure Data Lake Storage Gen1 にデータをコピーする
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 84ee65b05af4393f49696875bda41df39e283d5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85980091"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Azure Storage BLOB から Azure Data Lake Storage Gen1 にデータをコピーする

> [!div class="op_single_selector"]
> * [DistCp を使用](data-lake-store-copy-data-wasb-distcp.md)
> * [AdlCopy を使用](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Azure Data Lake Storage Gen1 には、次のソースからデータをコピーするコマンド ライン ツール [AdlCopy](https://www.microsoft.com/download/details.aspx?id=50358) が用意されています。

* Azure Storage BLOB から Data Lake Storage Gen1 に。 AdlCopy を使用して Data Lake Storage Gen1 から Azure Storage BLOB にデータをコピーすることはできません。
* 2 つの Azure Data Lake Storage Gen1 アカウント間で。

AdlCopy ツールは 2 つの異なるモードで使用できます。

* **スタンドアロン**。この場合、このツールは Data Lake Storage Gen1 リソースを使用してタスクを実行します。
* **Data Lake Analytics アカウントの使用**。Data Lake Analytics アカウントに割り当てられているユニットを使用して、コピー操作を実行する方法です。 予測可能な方法でコピー操作を実行したい場合は、このオプションを利用できます。

## <a name="prerequisites"></a>前提条件

この記事を読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure Storage Blobs** コンテナー (いくらかのデータを含む)。
* **Data Lake Storage Gen1 アカウント**。 これを作成する手順については、[Azure Data Lake Storage Gen1 の使用開始](data-lake-store-get-started-portal.md)に関するページを参照してください。
* **Data Lake Analytics アカウント (省略可能)** - Data Lake Analytics アカウントを作成する方法については、「[Azure Data Lake Analytics の使用を開始する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)」を参照してください。
* **AdlCopy ツール**。 [AdlCopy ツール](https://www.microsoft.com/download/details.aspx?id=50358)をインストールします。

## <a name="syntax-of-the-adlcopy-tool"></a>AdlCopy ツールの構文

AdlCopy ツールを操作するには、次の構文を使用します。

```console
AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern
```

構文のパラメーターを次に示します。

| オプション | 説明 |
| --- | --- |
| source |Azure Storage Blob のソース データの場所を指定します。 ソースには、BLOB コンテナー、BLOB、または別の Data Lake Storage Gen1 アカウントを指定できます。 |
| Dest |コピー先の Data Lake Storage Gen1 を指定します。 |
| SourceKey |Azure Storage Blob ソースのストレージ アクセス キーを指定します。 これが必要なのは、ソースが BLOB コンテナーかBLOB の場合のみです。 |
| Account |**オプション**。 Azure Data Lake Analytics アカウントを使用してコピー ジョブを実行する場合に、このオプションを使用します。 構文に /Account オプションを使用して、Data Lake Analytics アカウントを指定しない場合、AdlCopy は既定のアカウントを使用してジョブを実行します。 また、このオプションを使用する場合は、Data Lake Analytics アカウントのデータ ソースとしてソース (Azure Storage BLOB) とコピー先 (Azure Data Lake Storage Gen1) も追加する必要があります。 |
| Units |コピー ジョブに使用する Data Lake Analytics ユニット数を指定します。 **/Account** オプションを使用して Data Lake Analytics アカウントを指定する場合、このオプションは必須です。 |
| Pattern |コピーするBLOB やファイルを示す regex パターンを指定します。 AdlCopy は、大文字と小文字を区別する照合を使用します。 すべての項目のコピーにパターンが指定されていない場合は、既定のパターンが使用されます。 複数のファイル パターンを指定することはサポートされていません。 |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>AdlCopy を (スタンドアロンとして) 使用して Azure Storage Blob からデータをコピーする

1. コマンド プロンプトを開き、AdlCopy がインストールされているディレクトリ (通常は `%HOMEPATH%\Documents\adlcopy`) に移動します。
1. 次のコマンドを実行して、ソース コンテナーの特定の BLOB を Data Lake Storage Gen1 フォルダーにコピーします。

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>
    ```

    次に例を示します。

    ```console
    AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==
    ```

    >[!NOTE]
    >上の構文は、Data Lake Storage Gen1 アカウント内のフォルダーにコピーされるファイルを指定しています。 指定されたフォルダー名が存在しない場合は、AdlCopy ツールがフォルダーを作成します。

    Data Lake Storage Gen1 アカウントが存在する Azure サブスクリプションの資格情報を入力するよう求められます。 次のような出力が表示されます。

    ```output
    Initializing Copy.
    Copy Started.
    100% data copied.
    Finishing Copy.
    Copy Completed. 1 file copied.
    ```

1. 次のコマンドを使用して、あるコンテナーのすべての BLOB を Data Lake Storage Gen1 アカウントにコピーすることもできます。

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>  
    ```      

    次に例を示します。

    ```console
    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==
    ```

### <a name="performance-considerations"></a>パフォーマンスに関する考慮事項

Azure Blob Storage アカウントからコピーする場合は、Blob Storage 側でコピー中に調整されることがあります。 これにより、コピー ジョブのパフォーマンスが低下します。 Azure Blob Storage の制限の詳細については、[Azure サブスクリプションとサービスの制限事項](../azure-resource-manager/management/azure-subscription-service-limits.md)に関するページをご覧ください。

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>AdlCopy を (スタンドアロンとして) 使用して別の Data Lake Storage Gen1 アカウントからデータをコピーする

AdlCopy を使用して 2 つの Data Lake Storage Gen1 アカウント間でデータをコピーすることもできます。

1. コマンド プロンプトを開き、AdlCopy がインストールされているディレクトリ (通常は `%HOMEPATH%\Documents\adlcopy`) に移動します。
1. 次のコマンドを実行して、ある Data Lake Storage Gen1 アカウントの特定のファイルを別のアカウントにコピーします。

    ```console
    AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/
    ```

    次に例を示します。

    ```console
    AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/
    ```

   > [!NOTE]
   > 上の構文は、コピー先の Data Lake Storage Gen1 アカウント内のフォルダーにコピーされるファイルを指定しています。 指定されたフォルダー名が存在しない場合は、AdlCopy ツールがフォルダーを作成します。
   >
   >

    Data Lake Storage Gen1 アカウントが存在する Azure サブスクリプションの資格情報を入力するよう求められます。 次のような出力が表示されます。

    ```output
    Initializing Copy.
    Copy Started.|
    100% data copied.
    Finishing Copy.
    Copy Completed. 1 file copied.
    ```
1. 次のコマンドは、ソースの Data Lake Storage Gen1 アカウント内の特定のフォルダーのすべてのファイルをコピー先の Data Lake Storage Gen1 アカウント内のフォルダーにコピーします。

    ```console
    AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/
    ```

### <a name="performance-considerations"></a>パフォーマンスに関する考慮事項

AdlCopy をスタンドアロン ツールとして使用する場合、コピーは、共有 Azure が管理する共有リソースで実行されます。 この環境のパフォーマンスは、システムの負荷と使用可能なリソースによって異なります。 このモードは、アド ホック ベースでの少量の転送に適しています。 AdlCopy をスタンドアロン ツールとして使用する場合は、パラメーターを調整する必要はありません。

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>AdlCopy を (Data Lake Analytics アカウントで) 使用してデータをコピーする

Data Lake Analytics アカウントを使用して、Azure Storage BLOB から Data Lake Storage Gen1 にデータをコピーする AdlCopy ジョブを実行することもできます。 通常、このオプションを使用するのは、移動対象のデータが GB および TB の範囲で、パフォーマンスのスループットを向上し、予測可能にする場合です。

Data Lake Analytics アカウントと AdlCopy を使用して、Azure Storage Blob からコピーするには、ソース (Azure Storage Blob) を Data Lake Analytics アカウントのデータ ソースとして追加する必要があります。 データ ソースを Data Lake Analytics アカウントに追加する手順については、[Data Lake Analytics アカウントのデータ ソースの管理](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources)に関するセクションを参照してください。

> [!NOTE]
> Data Lake Analytics アカウントを使用して、ソースとして Azure Data Lake Storage Gen1 アカウントからコピーしている場合、その Data Lake Storage Gen1 アカウントを Data Lake Analytics アカウントに関連付ける必要はありません。 Data Lake Analytics アカウントにソース ストアを関連付ける必要があるのは、ソースが Azure Storage アカウントの場合のみです。
>
>

次のコマンドを実行して、Data Lake Analytics アカウントを使用して Azure Storage BLOB から Data Lake Storage Gen1 アカウントにコピーします。

```console
AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>
```

次に例を示します。

```console
AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2
```

同様に、次のコマンドを実行して、Data Lake Analytics アカウントを使用してソースの Data Lake Storage Gen1 アカウント内の特定のフォルダーのすべてのファイルをコピー先の Data Lake Storage Gen1 アカウント内のフォルダーにコピーします。

```console
AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2
```

### <a name="performance-considerations"></a>パフォーマンスに関する考慮事項

数テラバイトのデータをコピーするときは、AdlCopy を独自の Azure Data Lake Analytics アカウントで使用することで、パフォーマンスが向上し、さらに予測しやすくなります。 調整するパラメーターは、コピー ジョブで使用する Azure Data Lake Analytics ユニットの数です。 ユニットの数を増やすと、コピー ジョブのパフォーマンスが向上します。 コピーする各ファイルで使用できるのは 1 ユニットだけです。 コピーするファイル数よりも多くのユニットを指定しても、パフォーマンスは向上しません。

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>AdlCopy を使用したパターン一致によるデータのコピー

このセクションでは、AdlCopy を使用して、パターン マッチングを使用してソース (次の例では、Azure Storage BLOB を使用します) からコピー先の Data Lake Storage Gen1 アカウントにデータをコピーする方法について説明します。 たとえば、次の手順を使用して、 .csv 拡張子が付いたすべてのファイルをソース BLOB からコピー先にコピーすることができます。 

1. コマンド プロンプトを開き、AdlCopy がインストールされているディレクトリ (通常は `%HOMEPATH%\Documents\adlcopy`) に移動します。
1. 次のコマンドを実行して、ソース コンテナーの特定の BLOB 内の *.csv 拡張子を持つすべてのファイルを Data Lake Storage Gen1 フォルダーにコピーします。

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv
    ```

    次に例を示します。

    ```console
    AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv
    ```

## <a name="billing"></a>課金

* AdlCopy ツールをスタンドアロンとして使用する場合は、ソースの Azure Storage アカウントが Data Lake Storage Gen1 アカウントと同じリージョン内になければ、データを移動するための送信コストが課金されます。
* Data Lake Analytics アカウントで AdlCopy ツールを使用する場合、標準の [Data Lake Analytics 料金](https://azure.microsoft.com/pricing/details/data-lake-analytics/) が適用されます。

## <a name="considerations-for-using-adlcopy"></a>AdlCopy の使用に関する注意点

* AdlCopy (バージョン 1.0.5) では、合計で数千個を超えるファイルとフォルダーがあるソースからのデータのコピーをサポートしています。 ただし、大規模なデータセットをコピーする際に問題が発生する場合は、ファイルやフォルダーを異なるサブフォルダーに分散し、代わりにこれらのサブフォルダーへのパスをソースとして使用します。

## <a name="performance-considerations-for-using-adlcopy"></a>AdlCopy を使用するときのパフォーマンスに関する考慮事項

AdlCopy では、数千ものファイルとフォルダーを含むデータのコピーに対応していますが、 大規模なデータセットのコピーで問題が発生した場合は、ファイルやフォルダーを、小さなサブフォルダーに分散させてください。 AdlCopy はアドホック コピー用に構築されています。 定期的にデータをコピーする場合は、コピー操作を完全に管理できる [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) の使用を検討してください。

## <a name="release-notes"></a>リリース ノート

* 1.0.13 - 複数の adlcopy コマンドにまたがって同じ Azure Data Lake Storage Gen1 アカウントにデータをコピーしている場合、実行のたびに資格情報を再入力する必要はなくなりました。 Adlcopy は複数の実行にまたがる情報をキャッシュするようになりました。

## <a name="next-steps"></a>次のステップ

* [Data Lake Storage Gen1 でのデータのセキュリティ保護](data-lake-store-secure-data.md)
* [Data Lake Storage Gen1 で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Storage Gen1 で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)
