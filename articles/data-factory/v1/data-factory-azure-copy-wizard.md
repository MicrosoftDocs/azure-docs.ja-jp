---
title: Azure Data Factory コピー ウィザード
description: Azure Data Factory コピー ウィザードを使用して、サポートされるデータ ソースからシンクにデータをコピーする方法を説明します。
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0f95b0d62bc81a8dddc72239491a05ca78945490
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393379"
---
# <a name="azure-data-factory-copy-wizard"></a>Azure Data Factory コピー ウィザード

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 

Azure Data Factory コピー ウィザードを使用すると、通常はエンド ツー エンドのデータ統合シナリオの最初の手順である、データの取り込みプロセスを容易に実行できます。 リンクされたサービス、データセット、およびパイプラインの JSON 定義を理解していなくても、Azure Data Factory コピー ウィザードを使用することができます。 ウィザードによってパイプラインが自動的に作成され、選択したデータ ソースから選択した宛先にデータがコピーされます。 また、コピー ウィザードは作成時に取り込まれるデータの検証にも利用できます。 これにより、特にデータ ソースから初めてデータを取り込む場合に時間が節約できます。 コピー ウィザードを起動するには、Data Factory のホーム ページで **[データをコピー]** タイルをクリックします。

![コピー ウィザード](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>ビッグ データ向けの設計
このウィザードを使用すると、さまざまなソースから宛先にデータを数分以内で簡単に移動することができます。 ウィザードを最後まで進めると、依存する Data Factory のエンティティ (リンクされたサービスとデータセット) と共に、コピー アクティビティのあるパイプラインが自動的に作成されます。 パイプラインの作成に、追加の手順は必要ありません。   

![データ ソースの選択](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> サンプルのパイプラインを作成して Azure BLOB から Azure SQL Database テーブルにデータをコピーする詳細な手順については、[コピー ウィザードのチュートリアル](data-factory-copy-data-wizard-tutorial.md)に関するページを参照してください。

このウィザードは最初からビッグ データを考慮して設計されており、さまざまな種類のデータとオブジェクトをサポートしています。 数百個のフォルダー、ファイル、またはテーブルを移動する Data Factory パイプラインを作成できます。 このウィザードは、自動データ プレビュー、スキーマのキャプチャとマッピング、およびデータのフィルター処理に対応しています。

## <a name="automatic-data-preview"></a>自動データ プレビュー
選択したデータ ソースのデータの一部をプレビューし、それがコピー対象として正しいデータかどうかを検証できます。 さらに、ソース データがテキスト ファイル内にある場合は、コピー ウィザードによってテキスト ファイルが解析され、行および列の区切り記号とスキーマが自動的に学習されます。

![ファイル形式設定](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>スキーマのキャプチャとマッピング
入力データのスキーマは、場合によっては出力データのスキーマと一致しない可能性があります。 このシナリオでは、ソース スキーマの列を宛先スキーマの列にマップする必要があります。

> [!TIP]
> SQL Server または Azure SQL Database から Azure Synapse Analytics にデータをコピーするとき、コピー先ストアにテーブルが存在しない場合、Data Factory ではコピー元のスキーマを使ったテーブルの自動作成がサポートされています。 詳しくは、[Azure Data Factory を使用した Azure Synapse Analytics 間でのデータの移動](./data-factory-azure-sql-data-warehouse-connector.md)に関する記事をご覧ください。

ドロップダウン リストを使用して、宛先スキーマの列にマップするソース スキーマの列を選択します。 コピー ウィザードは、列のマッピングに関するユーザーのパターンを理解しようとします。 ユーザーがそれぞれの列を個別に選択しなくてもスキーマのマッピングが完了するように、同じパターンを残りの列にも適用します。 適用されたマッピングは、ドロップダウン リストを使用して 1 列ずつマップすることにより、必要に応じてオーバーライドできます。 マップする列が増えるにつれ、パターンはさらに正確になります。 このパターンはコピー ウィザードによって常に更新され、最終的にはユーザーが望む正しい列マッピングのパターンになります。     

![スキーマ マッピング](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>データのフィルター処理
ソース データをフィルター処理して、シンク データ ストアにコピーする必要があるデータのみを選択できます。 フィルター処理によって、シンク データ ストアにコピーするデータの量が削減されるため、コピー操作のスループットが向上します。 これにより、SQL クエリ言語を使用することでリレーショナル データベース内のデータを、また [Data Factory の関数および変数](data-factory-functions-variables.md)を使用することで Azure BLOB フォルダー内のファイルを柔軟にフィルター処理できます。   

### <a name="filtering-of-data-in-a-database"></a>データベース内のデータのフィルター処理
次のスクリーンショットは、`Text.Format` 関数と `WindowStart` 変数を使用した SQL クエリを示しています。

![式の検証](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Azure BLOB フォルダー内のデータのフィルター処理
フォルダー パスで変数を使用すると、[システム変数](data-factory-functions-variables.md#data-factory-system-variables)に基づいて実行時に決定されるフォルダーからデータをコピーできます。 サポートされている変数は、 **{year}** 、 **{month}** 、 **{day}** 、 **{hour}** 、 **{minute}** 、 **{custom}** です。 例: inputfolder/{year}/{month}/{day}。

次の形式の入力フォルダーがあるとします。

```text
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

**[ファイルまたはフォルダー]** の **[参照]** ボタンをクリックして、これらのフォルダーのいずれか (例: 2016->03->01->02) を参照し、 **[選択]** をクリックします。 テキスト ボックスに `2016/03/01/02` と表示されます。 ここで、**2016** を **{year}** 、**03** を **{month}** 、**01** を **{day}** 、**02** を **{hour}** にそれぞれ置き換え、**Tab** キーを押します。 この 4 つの変数の形式を選択するドロップダウン リストが表示されます。

![システム変数の使用](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

次のスクリーンショットに示すように、 **custom** 変数と、任意の [サポートされる書式文字列](/dotnet/standard/base-types/custom-date-and-time-format-strings)を使用することもできます。 その構造のフォルダーを選択するには、まず **[参照]** をクリックします。 次に、値を **{custom}** に置き換え、**Tab** キーを押して、書式文字列を入力できるテキスト ボックスを表示します。     

![カスタム変数の使用](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>スケジュール オプション
コピー操作は 1 回だけ実行することも、スケジュールに従って (毎時、毎日など) 実行することもできます。 このどちらのオプションも、オンプレミス、クラウド、ローカル デスクトップ コピーといった環境のさまざまなコネクタで使用できます。

1 回限りのコピー操作では、ソースからコピー先に 1 回だけデータを移動できます。 これは、サポートされている形式のあらゆるサイズのデータに適用されます。 スケジュールされたコピーでは、指定した繰り返しでデータをコピーできます。 豊富な設定 (再試行、タイムアウト、アラートなど) を使用して、スケジュールされたコピーを構成できます。

![スケジュール プロパティ](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="troubleshooting"></a>トラブルシューティング

このセクションでは、Azure Data Factory のコピー ウィザードの一般的なトラブルシューティング方法について説明します。

> [!NOTE] 
> これらのトラブルシューティングのヒントは、Data Factory のバージョン 1 のコピー ウィザードに適用されます。 Data Factory v2 については、トラブルシューティング ガイドの [Azure Data Factory のトラブルシューティング](../data-factory-ux-troubleshoot-guide.md)に関する記事を参照してください。

### <a name="error-code-unable-to-validate-in-copy-wizard"></a>エラー コード:コピー ウィザードの "Unable to validate" (検証できません)

- **現象**:コピー ウィザードの最初のステップで、"Unable to Validate" (検証できません) という警告メッセージが表示されます。
- **原因**:これは、すべてのサード パーティの cookie が無効にされている場合に発生する可能性があります。
- **解決方法**: 
    - Internet Explorer または Microsoft Edge ブラウザーを使用してください。
    - Chrome ブラウザーを使用している場合は、次の手順に従って *microsoftonline.com* と *windows.net* を Cookie の例外として追加します。
        1.  Chrome ブラウザーを開きます。
        2.  右側にあるレンチまたは 3 本の線をクリックします (Google Chrome をカスタマイズおよび制御します)。
        3.  **[設定]** をクリックします。
        4.  **Cookie** を検索するか、[Advanced Settings] の **[Privacy]** にアクセスします。
        5.  **[Content Settings]** を選択します。    
        6.  Cookie には、 **[allow local data to be set (recommended)]** を設定する必要があります。
        7.  **[Manage exceptions]** をクリックします。 **[hostname pattern]** に次の文字列を入力し、 **[Allow]** が動作セットであることを確認します。
            - login.microsoftonline.com
            - login.windows.net
        8.  ブラウザーを閉じて再起動します。
    - Firefox ブラウザーを使用している場合は、次の手順に従って Cookie の例外を追加してください。
        1. Firefox メニューから、 **[Tools]**  >  **[Options]** にアクセスします。
        2. **[Privacy]**  >  **[History]** で、現在の設定が **[Use Custom settings for history]** になっている可能性があります。
        3. **[Accept third-party cookie]** で、現在の設定が **[Never]** である場合があります。その場合は、右側にある **[Exceptions]** をクリックして、次のサイトを追加します。
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  ブラウザーを閉じて再起動します。 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>エラー コード:Unable to open login page and enter password (ログイン ページを開けず、パスワードを入力できません)

- **現象**:コピー ウィザードによってログイン ページにリダイレクトされますが、ログイン ページが正常に表示されません。
- **原因**:この問題は、ネットワーク環境をオフィス ネットワークからホーム ネットワークに変更した場合に発生する可能性があります。 ブラウザーにはキャッシュがあります。 
- **解決方法**: 
    1.  ブラウザーを閉じて、もう一度やり直してください。 問題がまだ発生する場合は、次の手順に進みます。   
    2.  Internet Explorer ブラウザーを使用している場合は、プライベート モードで開いてみてください (Ctrl + Shift + P キーを押します)。 Chrome ブラウザーを使用している場合は、シークレット モードで開いてみてください (Ctrl + Shift + N キーを押します)。 問題がまだ発生する場合は、次の手順に進みます。 
    3.  別のブラウザーを使用してみてください。 


## <a name="next-steps"></a>次のステップ
Data Factory コピー ウィザードを使用して、コピー アクティビティを含むパイプラインを作成する簡単なチュートリアルについては、 [コピー ウィザードを使用してパイプラインを作成する方法に関するチュートリアル](data-factory-copy-data-wizard-tutorial.md)をご覧ください。