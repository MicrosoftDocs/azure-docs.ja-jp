---
title: Azure Data Factory でイベントベースのトリガーを作成する | Microsoft Docs
description: イベントに応答してパイプラインを実行するトリガーを Azure Data Factory で作成する方法について説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 10/18/2018
ms.openlocfilehash: a2c046d4375b891d17d545edd804d0fa1da6ee75
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142350"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>イベントに応答してパイプラインを実行するトリガーを作成する

この記事では、Data Factory のパイプラインで作成できるイベントベースのトリガーについて説明します。

イベントドリブン アーキテクチャ (EDA) は、イベントの運用、検出、使用、および応答を含む一般的なデータ統合パターンです。 多くの場合、データ統合シナリオでは、Data Factory ユーザーが、Azure Storage アカウントのファイルの到着や削除などのイベントに基づいてパイプラインをトリガーする必要があります。 Data Factory は [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) と統合され、イベントに対してパイプラインをトリガーすることができるようになりました。

この機能の概要とデモンストレーションについては、以下の 10 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> この記事で説明されている統合は、[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) に依存しています。 サブスクリプションが Event Grid リソース プロバイダーに登録されていることを確認してください。 詳細については、「[リソース プロバイダーと種類](../azure-resource-manager/resource-manager-supported-services.md#azure-portal)」を参照してください。

## <a name="data-factory-ui"></a>Data Factory UI

このセクションでは、Azure Data Factory ユーザー インターフェイス内でイベント トリガーを作成する方法について説明します。

1. **作成キャンバス**に移動します

2. 左下隅にある **[トリガー]** ボタンをクリックします

3. **[+ 新規]** をクリックして、トリガーの作成サイド ナビゲーションを開きます

4. **[イベント]** のトリガーの種類を選択します

![新しいイベント トリガーを作成する](media/how-to-create-event-trigger/event-based-trigger-image1.png)

5. [Azure サブスクリプション] ドロップダウンからストレージ アカウントを選択するか、ストレージ アカウントのリソース ID を使用して手動で選択します。 イベントを発生させるコンテナーを選択します。 コンテナーの選択は省略可能ですが、すべてのコンテナーを選択すると、多数のイベントが発生する可能性があることに注意してください。

   > [!NOTE]
   > 現在、イベント トリガーでサポートされているのは、Azure Data Lake Storage Gen2 と汎用バージョン 2 のストレージ アカウントのみです。 Azure Event Grid の制限により、Azure Data Factory は、ストレージ アカウントあたり最大 500 のイベント トリガーのみをサポートします。

6. **[Blob path begins with]** (次で始まる BLOB パス) と **[Blob path ends with]** (次で終わる BLOB パス) のプロパティでは、イベントを受け取るコンテナー、フォルダー、および BLOB の名前を指定できます。 イベント トリガーでは、これらのプロパティの少なくとも 1 つを定義する必要があります。 この記事で後述する例に示すように、 **[Blob path begins with]\(Blob パスの先頭\)** と **[Blob path ends with]\(Blob パスの末尾\)** のプロパティにはさまざまなパターンを使用できます。

    * **次で始まる BLOB パス:** BLOB パスはフォルダーパスで始める必要があります。 有効な値として、`2018/` および `2018/april/shoes.csv` があります。 コンテナーが選択されていない場合、このフィールドを選択することはできません。
    * **次で終わる BLOB パス:** BLOB パスは、ファイル名または拡張子で終わる必要があります。 有効な値として、`shoes.csv` および `.csv` があります。 コンテナーとフォルダー名は省略可能ですが、指定する場合は、`/blobs/` セグメントで区切る必要があります。 たとえば、'orders' という名前のコンテナーには、`/orders/blobs/2018/april/shoes.csv` の値を指定できます。 任意のコンテナー内のフォルダーを指定するには、先頭の '/' 文字を省略します。 たとえば、`april/shoes.csv` は、任意のコンテナーの 'april' というフォルダー内の `shoes.csv` という名前の任意のファイルでイベントをトリガーします。 

7. トリガーが **[Blob created]** (作成された BLOB) イベント、 **[Blob deleted]** (削除された BLOB) イベント、またはその両方に応答するかどうかを選択します。 指定されたストレージの場所で、各イベントは、トリガーに関連付けられている Data Factory パイプラインをトリガーします。

    ![イベント トリガーを構成する](media/how-to-create-event-trigger/event-based-trigger-image2.png)

8. トリガーを構成したら、 **[次へ:データのプレビュー]** をクリックします。 この画面には、イベント トリガーの構成に一致する既存の BLOB が表示されます。 特定のフィルターがあることを確認します。 対象範囲が広くなりすぎるフィルターを構成すると、多数のファイルに一致する場合があり、コストに相当の影響が及ぶ可能性があります。 フィルター構成が確認されたら、 **[完了]** をクリックします。

    ![イベント トリガー データのプレビュー](media/how-to-create-event-trigger/event-based-trigger-image3.png)

9. このトリガーにパイプラインをアタッチするには、パイプライン キャンバスにアクセスし、 **[トリガーの追加]** をクリックして **[新規作成/編集]** を選択します。 サイド ナビゲーションが表示されたら、 **[Choose trigger...]** (トリガーの選択...) ドロップダウンをクリックし、作成したトリガーを選択します。 **[次へ: データのプレビュー]** をクリックして構成が正しいことを確認し、 **[次へ]** をクリックして [データのプレビュー] が正しいことを確認します。

10. パイプラインにパラメーターがある場合は、トリガー実行パラメーター サイド ナビゲーションで指定できます。 イベント トリガーは、BLOB のフォルダー パスとファイル名を、プロパティ `@triggerBody().folderPath` および `@triggerBody().fileName` にキャプチャします。 パイプラインでこれらのプロパティの値を使用するには、プロパティをパイプライン パラメーターにマップする必要があります。 プロパティをパラメーターにマップしたら、パイプライン全体で `@pipeline().parameters.parameterName` 式を使用して、トリガーでキャプチャされた値にアクセスできます。 終了したら **[完了]** をクリックします。

    ![パイプライン パラメーターへのプロパティのマッピング](media/how-to-create-event-trigger/event-based-trigger-image4.png)

前の例では、トリガーは .csv で終わる BLOB パスがコンテナー sample-data のフォルダー event-testing で作成されたときに起動するように構成されています。 **folderPath** と **fileName** のプロパティは、新しい BLOB の場所をキャプチャします。 たとえば、MoviesDB.csv が sample-data/event-testing のパスに追加された場合、`@triggerBody().folderPath` には `sample-data/event-testing` の値が、`@triggerBody().fileName` には `moviesDB.csv` の値が含まれます。 これらの値は、例では、パイプライン パラメーター `sourceFolder` および `sourceFile` にマップされ、それぞれ `@pipeline().parameters.sourceFolder` および `@pipeline().parameters.sourceFile` としてパイプライン全体で使用できます。

## <a name="json-schema"></a>JSON スキーマ

次の表に、イベントベースのトリガーに関連するスキーマ要素の概要を示します。

| **JSON 要素** | **説明** | **Type** | **使用できる値** | **必須** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | ストレージ アカウントの Azure Resource Manager リソース ID。 | string | Azure Resource Manager ID | はい |
| **events** | このトリガーを起動するイベントの種類。 | Array    | Microsoft.Storage.BlobCreated、Microsoft.Storage.BlobDeleted | はい、これらの値の任意の組み合わせが必須です。 |
| **blobPathBeginsWith** | BLOB パスは、トリガーを起動するために指定されているパターンで始まる必要があります。 たとえば、`/records/blobs/december/` のみが、`records` コンテナー下の `december` フォルダーにあるブロブのトリガーを起動します。 | string   | | これらのプロパティ (`blobPathBeginsWith` または `blobPathEndsWith`) の少なくとも 1 つの値を指定する必要があります。 |
| **blobPathEndsWith** | BLOB パスは、トリガーを起動するために指定されているパターンで終わる必要があります。 たとえば、`december/boxes.csv` のみが、`december` フォルダー内の `boxes` というブロブのトリガーを起動します。 | string   | | これらのプロパティ (`blobPathBeginsWith` または `blobPathEndsWith`) の少なくとも 1 つの値を指定する必要があります。 |

## <a name="examples-of-event-based-triggers"></a>イベントベース トリガーの例

このセクションでは、イベントベースのトリガー設定の例を示します。

> [!IMPORTANT]
> 以下の例に示すように、コンテナーとフォルダー、コンテナーとファイル、またはコンテナー、フォルダー、およびファイルを指定するたびに、パスの `/blobs/` セグメントを含める必要があります。 **blobPathBeginsWith** の場合、Azure Data Factory UI は、トリガー JSON のフォルダーとコンテナー名の間に自動的に `/blobs/` を追加します。

| プロパティ | 例 | 説明 |
|---|---|---|
| **次で始まる BLOB パス** | `/containername/` | コンテナー内の任意の BLOB のイベントを受信します。 |
| **次で始まる BLOB パス** | `/containername/blobs/foldername/` | `containername`コンテナーおよび `foldername` フォルダー内の任意の BLOB のイベントを受信します。 |
| **次で始まる BLOB パス** | `/containername/blobs/foldername/subfoldername/` | サブフォルダーを参照することもできます。 |
| **次で始まる BLOB パス** | `/containername/blobs/foldername/file.txt` | `containername` コンテナー下の `foldername` フォルダー内にある `file.txt` という名前の BLOB のイベントを受信します。 |
| **次で終わる BLOB パス** | `file.txt` | 任意のパスで `file.txt` という名前の BLOB のイベントを受信します。 |
| **次で終わる BLOB パス** | `/containername/blobs/file.txt` | コンテナー `containername` 下の `file.txt` という名前の BLOB のイベントを受信します。 |
| **次で終わる BLOB パス** | `foldername/file.txt` | 任意のコンテナー下の `foldername` フォルダーにある `file.txt` という名前の BLOB のイベントを受信します。 |

## <a name="next-steps"></a>次の手順
トリガーについて詳しくは、「[Azure Data Factory でのパイプラインの実行とトリガー](concepts-pipeline-execution-triggers.md#triggers)」をご覧ください。
