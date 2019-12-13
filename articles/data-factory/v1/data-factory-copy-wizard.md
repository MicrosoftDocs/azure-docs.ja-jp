---
title: コピー ウィザードを使用して簡単にデータをコピーする - Azure
description: Data Factory コピー ウィザードを使用して、サポートされるデータ ソースからシンクにデータをコピーする方法を説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: f904972f-cd33-48db-9755-2b3196ae4168
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8b872cc30ae66e83274f189138dad6d609e3f536
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927047"
---
# <a name="copy-or-move-data-easily-with-azure-data-factory-copy-wizard"></a>Azure Data Factory コピー ウィザードでデータを簡単にコピーまたは移動する
> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[コピー アクティビティのチュートリアル](../quickstart-create-data-factory-dot-net.md)に関するページを参照してください。 


Azure Data Factory コピー ウィザードを使用すると、通常エンド ツー エンドのデータ統合シナリオの最初の手順である、データの取り込みプロセスを容易に実行できます。 リンクされたサービス、データセット、およびパイプラインの JSON 定義を理解していなくても、Azure Data Factory コピー ウィザードを使用することができます。 このウィザードのすべての手順を完了すると、ウィザードによってパイプラインが自動的に作成され、選択したデータ ソースから選択した宛先にデータがコピーされます。 また、コピー ウィザードを使用すると、作成時に取り込まれるデータを検証できます。そのため、データ ソースから初めてデータを取り込むときに特に、時間を大幅に節約できます。 コピー ウィザードを起動するには、Data Factory のホーム ページで **[データをコピー]** タイルをクリックします。

![コピー ウィザード](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="an-intuitive-wizard-for-copying-data"></a>データをコピーするための直観的なウィザード
このウィザードを使用すると、さまざまなソースから宛先にデータを数分以内で簡単に移動することができます。 ウィザードを進めると、依存する Data Factory のエンティティ (リンクされたサービスとデータセット) と共に、コピー アクティビティのあるパイプラインが自動的に作成されます。 パイプラインの作成に、追加の手順は必要ありません。   

![データ ソースの選択](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> サンプルのパイプラインを作成して Azure BLOB から Azure SQL Database テーブルにデータをコピーする詳細な手順については、[コピー ウィザードのチュートリアル](data-factory-copy-data-wizard-tutorial.md)に関するページを参照してください。 
> 
> 

このウィザードは、最初からビッグ データを考慮して設計されています。 数百個のフォルダー、ファイル、またはテーブルを移動する Data Factory パイプラインを、データのコピー ウィザードを使用して簡単かつ効率的に作成できます。 このウィザードは、自動データ プレビュー、スキーマのキャプチャとマッピング、データのフィルター処理の 3 つの機能をサポートしています。 

## <a name="automatic-data-preview"></a>自動データ プレビュー
このコピー ウィザードでは、選択したデータ ソースのデータの一部を確認できるため、データがコピー対象の適切なデータであるかどうかを検証できます。 さらに、ソース データがテキスト ファイル内にある場合は、コピー ウィザードによってテキスト ファイルが解析され、行および列の区切り記号とスキーマが自動的に学習されます。 

![ファイル形式設定](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>スキーマのキャプチャとマッピング
入力データのスキーマは、場合によっては出力データのスキーマと一致しない可能性があります。 このシナリオでは、ソース スキーマの列を宛先スキーマの列にマップする必要があります。 

コピー ウィザードによって、ソース スキーマの列が宛先スキーマの列に自動的にマップされます。 ドロップダウン リストを使用してマッピングをオーバーライドすることや、データのコピー中にスキップする必要がある列かどうかを指定することができます。   

![スキーマ マッピング](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>データのフィルター処理
このウィザードで、ソース データをフィルター処理して、宛先/シンク データ ストアにコピーする必要があるデータのみを選択できます。 フィルター処理によって、シンク データ ストアにコピーするデータの量が削減されるため、コピー操作のスループットが向上します。 これにより、SQL クエリ言語を使用することでリレーショナル データベース内のデータを、また [Data Factory の関数および変数](data-factory-functions-variables.md)を使用することで Azure BLOB フォルダー内のファイルを柔軟にフィルター処理できます。   

### <a name="filtering-of-data-in-a-database"></a>データベース内のデータのフィルター処理
この例では、SQL クエリで、`Text.Format` 関数と `WindowStart` 変数を使用しています。 

![式の検証](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Azure BLOB フォルダー内のデータのフィルター処理
フォルダー パスで変数を使用すると、[システム変数](data-factory-functions-variables.md#data-factory-system-variables)に基づいて実行時に決定されるフォルダーからデータをコピーできます。 サポートされている変数は、 **{year}** 、 **{month}** 、 **{day}** 、 **{hour}** 、 **{minute}** 、 **{custom}** です。 例: inputfolder/{year}/{month}/{day}。

次の形式の入力フォルダーがあるとします。

    2016/03/01/01
    2016/03/01/02
    2016/03/01/03
    ...

**[ファイルまたはフォルダー]** の **[参照]** ボタンをクリックして、これらのフォルダーのいずれか (例: 2016->03->01->02) を参照し、 **[選択]** をクリックします。 テキスト ボックスに `2016/03/01/02` と表示されます。 ここで、**2016** を **{year}** 、**03** を **{month}** 、**01** を **{day}** 、**02** を **{hour}** にそれぞれ置き換え、Tab キーを押します。この 4 つの変数の形式を選択するドロップダウン リストが表示されます。

![システム変数の使用](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

次のスクリーンショットに示すように、 **custom** 変数と、任意の [サポートされる書式文字列](https://msdn.microsoft.com/library/8kb3ddd4.aspx)を使用することもできます。 その構造のフォルダーを選択するには、まず **[参照]** をクリックします。 次に、値を **{custom}** に置き換え、Tab キーを押して、書式文字列を入力できるテキスト ボックスを表示します。     

![カスタム変数の使用](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="support-for-diverse-data-and-object-types"></a>多様なデータとオブジェクトの種類のサポート
コピー ウィザードを使用すると、数百個のフォルダー、ファイル、またはテーブルを効率的に移動できます。

![データのコピー元のテーブルの選択](./media/data-factory-copy-wizard/select-tables-to-copy-data.png)

## <a name="scheduling-options"></a>スケジュール オプション
コピー操作は 1 回だけ実行することも、スケジュールに従って (毎時、毎日など) 実行することもできます。 このどちらのオプションも、オンプレミス、クラウド、ローカル デスクトップ コピーのさまざまなコネクタで使用できます。

1 回限りのコピー操作では、ソースからコピー先に 1 回だけデータを移動できます。 これは、サポートされている形式のあらゆるサイズのデータに適用されます。 スケジュールされたコピーでは、指定した繰り返しでデータをコピーできます。 豊富な設定 (再試行、タイムアウト、アラートなど) を使用して、スケジュールされたコピーを構成できます。

![スケジュール プロパティ](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="next-steps"></a>次の手順
Data Factory コピー ウィザードを使用して、コピー アクティビティを含むパイプラインを作成する簡単なチュートリアルについては、「[チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md)」を参照してください。

