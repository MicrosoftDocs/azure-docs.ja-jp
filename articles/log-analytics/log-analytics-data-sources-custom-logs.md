---
title: "OMS Log Analytics のカスタム ログの収集 | Microsoft Docs"
description: "Log Analytics は、Windows コンピューターと Linux コンピューターの両方のテキスト ファイルからイベントを収集できます。  この記事では、OMS リポジトリで作成したレコードの新しいカスタム ログと詳細を定義する方法について説明します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: aca7f6bb-6f53-4fd4-a45c-93f12ead4ae1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 653696779e612726ed5b75829a5c6ed2615553d7
ms.openlocfilehash: a9c70810c4f731b2d8b395873fa6b94db78306aa


---
# <a name="custom-logs-in-log-analytics"></a>Log Analytics のカスタム ログ
Log Analytics のカスタム ログ データ ソースでは、Windows コンピューターと Linux コンピューターの両方のテキスト ファイルからイベントを収集できます。 多くのアプリケーションは、Windows イベント ログや Syslog などの標準のログ記録サービスの代わりに、テキスト ファイルに情報を記録します。  収集後、Log Analytics の [カスタム フィールド](log-analytics-custom-fields.md) 機能を利用し、ログの各レコードを個別のフィールドに解析できます。

![カスタム ログの収集](media/log-analytics-data-sources-custom-logs/overview.png)

収集するログ ファイルは、次の条件に一致する必要があります。

* ログでは&1; 行につき&1; エントリとするか、各エントリの先頭に次のいずれかの形式に一致するタイムスタンプを使用する必要があります。
  
    YYYY-MM-DD HH:MM:SS  <br>
    M/D/YYYY HH:MM:SS AM/PM <br>
    Mon DD,YYYY HH:MM:SS
* ログ ファイルには、新しいエントリでファイルが上書きされる巡回更新を許可しません。 

## <a name="defining-a-custom-log"></a>カスタム ログを定義する
次の手順でカスタム ログ ファイルを定義できます。  この記事の最後にカスタム ログ追加のサンプル チュートリアルがあります。

### <a name="step-1-open-the-custom-log-wizard"></a>手順 1. カスタム ログ ウィザードを開く
カスタム ログ ウィザードは OMS ポータルで実行され、収集する新しいカスタム ログを定義できます。

1. OMS ポータルで、 **[設定]**に進みます。
2. **[データ]** をクリックし、**[カスタム ログ]** をクリックします。
3. 既定では、すべての構成変更はすべてのエージェントに自動的にプッシュされます。  Linux エージェントの場合、構成ファイルが Fluentd データ コレクターに送信されます。  各 Linux エージェントでこのファイルを手動で変更する場合、 *[Apply below configuration to my Linux machines (Linux コンピューターに以下の構成を適用する)]*チェック ボックスのチェックを外します。
4. **[追加+]** をクリックし、カスタム ログ ウィザードを開きます。

### <a name="step-2-upload-and-parse-a-sample-log"></a>手順 2. サンプル ログをアップロードし、解析する
始めにカスタム ログのサンプルをアップロードします。  ユーザーが評価できるように、ウィザードはこのファイルのエントリを解析して表示します。  Log Analytics はユーザーが指定した区切り記号を利用して各レコードを識別します。

**改行**が既定の区切り記号であり、1 行につき&1; エントリのログ ファイルに利用されます。  利用可能な形式の&1; つで表現された日付と時刻で行が始まるとき、区切り記号として**タイムスタンプ**を指定できます。その場合、1 行以上のエントリに対応します。 

区切り記号としてタイムスタンプが使用されるとき、OMS に保存される各レコードの TimeGenerated プロパティに、ログ ファイルでそのエントリに指定された日付/時刻が入力されます。  区切り記号として改行が使用される場合、Log Analytics がエントリを収集した日付と時刻が TimeGenerated に入力されます。 

> [!NOTE]
> Log Analytics では現在のところ、UTC を区切り記号のタイムスタンプとして利用し、ログから収集した日付/時刻を処理します。  間もなく、エージェントの時間帯を使用するように変更される予定です。 
> 
> 

1. **[閲覧]** をクリックし、サンプル ファイルを表示します。  一部のブラウザーでは、このボタンのラベルは **[ファイルの選択]** になっていることがあります。
2. **[次へ]**をクリックします。 
3. カスタム ログ ウィザードはファイルをアップロードし、識別したレコードを一覧表示します。
4. 新しいレコードの識別に使用される区切り記号を変更し、ログ ファイルのレコードを最も効率的に識別する区切り記号を選択します。
5. ページの下部にある **[次へ]**」を参照してください。

### <a name="step-3-add-log-collection-paths"></a>手順 3. ログのコレクション パスを追加する
エージェントに&1; つまたは複数のパスを定義する必要があります。エージェントがカスタム ログを見つける場所です。  ログ ファイルの特定のパスか名前を入力するか、名前にワイルドカードを含むパスを指定できます。  毎日新しいファイルを作成するアプリケーションに対応し、1 つのファイルが一定のサイズに到達した場合にも対応します。  また、1 つのログ ファイルに複数のパスを指定できます。

たとえば、ログ ファイルを毎日作成するアプリケーションがあります。log20100316.txt のように、名前に日付を含めます。 このようなログのパターンは、たとえば、*log\*.txt* になります。このアプリケーションの命名規則に従うあらゆるログ ファイルに適用されます。

次の表は、異なるログ ファイルを指定する有効なパターンの例をまとめたものです。 

| 説明 | パス |
|:--- |:--- |
| Windows エージェントの *C:\Logs* にあり、拡張子が .txt のすべてのファイル |C:\Logs\\\*.txt |
| Windows エージェントの *C:\Logs* にあり、名前が log で始まり、拡張子が .txt のすべてのファイル |C:\Logs\log\*.txt |
| Linux エージェントの */var/log/audit* にあり、拡張子が .txt のすべてのファイル |/var/log/audit/*.txt |
| Linux エージェントの */var/log/audit* にあり、名前が log で始まり、拡張子が .txt のすべてのファイル |/var/log/audit/log\*.txt |

1. Windows または Linux を選択し、追加するパス書式を指定します。
2. パスを入力し、 **+** ボタンをクリックします。
3. 追加のパスがあれば、以上のプロセスを繰り返します。

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>手順 4. ログの名前と説明を指定する
指定した名前は上述の種類のログに使用されます。  カスタム ログとして区別されるように常に _CL で終わります。

1. ログの名前を入力します。  **\_CL** が接尾辞として自動的に追加されます。
2. 任意で **[説明]**を追加します。
3. [ **次へ** ] をクリックし、カスタム ログ定義を保存します。

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>手順 5. カスタム ログが収集されていることを確認する
新しいカスタム ログの最初のデータが Log Analytics に表示されるまで最大&1; 時間かかることがあります。  指定されたパスにあるログからエントリの収集が始まります。カスタム ログに定義されているポイントから開始されます。  カスタム ログ作成中にアップロードしたエントリは保持されませんが、ログ ファイルに既に存在するエントリが収集されます。

Log Analytics がカスタム ログから収集を始めると、そのレコードがログ検索で利用できるようになります。  クエリの **[種類]** としてカスタム ログに指定した名前を使用します。

> [!NOTE]
> RawData プロパティが検索に表示されない場合、ブラウザーを閉じて再び開いてみてください。
> 
> 

### <a name="step-6-parse-the-custom-log-entries"></a>手順 6. カスタム ログ エントリを解析する
ログ エントリ全体は、 **RawData**と呼ばれる&1; つのプロパティに格納されます。  多くの場合、各エントリのさまざまな情報を個別のプロパティに分けてレコードに保存するほうが効率的です。  それは Log Analytics の [カスタム フィールド](log-analytics-custom-fields.md) 機能で実行できます。

カスタム ログ エントリを解析するための詳しい手順はここでは紹介しません。  その情報については、 [カスタム フィールド](log-analytics-custom-fields.md) 文書をご覧ください。

## <a name="disabling-a-custom-log"></a>カスタム ログを無効にする
カスタム ログの定義は、一度作成したら削除できませんが、そのコレクション パスをすべて削除することで無効にすることはできます。

1. OMS ポータルで、 **[設定]**に進みます。
2. **[データ]** をクリックし、**[カスタム ログ]** をクリックします。
3. 無効にするカスタム ログ定義の横にある **[詳細]** をクリックします。
4. カスタム ログ定義の各コレクション パスを削除します。

## <a name="data-collection"></a>データ収集
Log Analytics は約 5 分おきに各カスタム ログから新しいエントリを収集します。  エージェントは、収集元の場所を各ログ ファイルに記録します。  エージェントが一定時間オフラインになった場合、Log Analytics は中止した箇所からエントリを回収し、オンラインの間に作成されたエントリも回収されます。

ログ エントリのコンテンツ全体は **RawData** という名前の&1; つのプロパティに書き込まれます。  これを複数のプロパティに解析し、個別に分析し、検索できます。その場合、カスタム ログの作成後に[カスタム フィールド](log-analytics-custom-fields.md)を定義します。

## <a name="custom-log-record-properties"></a>カスタム ログ レコードのプロパティ
カスタム ログ レコードには、種類、ユーザーが指定するログ名、次の表にあるプロパティが与えられます。

| プロパティ | 説明 |
|:--- |:--- |
| TimeGenerated |Log Analytics がレコードを収集した日付と時刻。  ログが時間基準の区切り記号を使用する場合、これはエントリから収集された時間になります。 |
| SourceSystem |レコードが収集されたエージェントの種類。 <br> OpsManager – Windows エージェント、直接接続または SCOM <br> Linux – すべての Linux エージェント |
| RawData |収集されたエントリの完全テキスト。 |
| ManagementGroupName |SCOM エージェントの管理グループの名前。  その他のエージェントの場合、これは AOI-\<workspace ID\> です。 |

## <a name="log-searches-with-custom-log-records"></a>カスタム ログ レコードとログ検索
カスタム ログのレコードは、他のデータ ソースのレコードと同様に、OMS リポジトリに格納されます。  ログを定義したときに指定した名前に一致する種類が与えられます。検索でこの [種類] プロパティを利用し、特定のログから収集したレコードを取得できます。

次の表は、カスタム ログからレコードを取得するログ検索のさまざまな例をまとめたものです。

| クエリ | Description |
|:--- |:--- |
| Type=MyApp_CL |MyApp_CL という名前のカスタム ログからのすべてのイベント |
| Type=MyApp_CL Severity_CF=error |MyApp_CL という名前のカスタム ログに含まれ、*Severity_CF* という名前のカスタム フィールドの値が *error* になっているすべてのイベント。 |

## <a name="sample-walkthrough-of-adding-a-custom-log"></a>カスタム ログ追加のサンプル チュートリアル
次のセクションでは、カスタム ログの作成例を段階的に説明します。  収集されるサンプル ログには&1; 行につき&1; エントリが与えられます。これは日付で始まり、コード、状態、メッセージがコンマで区切られて続きます。  以下はサンプルのエントリです。

    2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2016-03-10 01:38:22 302,Error,Application could not connect to database
    2016-03-10 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>サンプル ログをアップロードし、解析する
ログ ファイルの&1; つをお見せします。ログ ファイルで収集されているイベントを確認できます。  この例の場合、区切り記号には開業を選択して問題ありません。  ログの&1; エントリが複数行にまたがるようであれば、区切り記号としてタイムスタンプを使用する必要があります。

![サンプル ログをアップロードし、解析する](media/log-analytics-data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>ログのコレクション パスを追加する
ログ ファイルは *C:\MyApp\Logs* に置かれます。  新しいファイルが毎日作成されます。名前には日付が含まれ、*appYYYYMMDD.log* というパターンになります。  このログには *C:\MyApp\Logs\\\*.log* というパターンを使えばよいでしょう。

![ログのコレクション パス](media/log-analytics-data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>ログの名前と説明を指定する
*MyApp_CL* という名前を使用し、**[説明]** に入力します。

![ログ名](media/log-analytics-data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>カスタム ログが収集されていることを確認する
*Type=MyApp_CL* というクエリを使用すると、収集されたログからすべてのレコードが返されます。

![カスタム フィールドのないログ クエリ](media/log-analytics-data-sources-custom-logs/query-01.png)

### <a name="parse-the-custom-log-entries"></a>カスタム ログ エントリを解析する
カスタム フィールドを利用し、*EventTime*、*Code*、*Status*、*Message* フィールドを定義します。クエリにより返されるレコードの違いがわかります。

![カスタム フィールドのあるログ クエリ](media/log-analytics-data-sources-custom-logs/query-02.png)

## <a name="next-steps"></a>次のステップ
* [カスタム フィールド](log-analytics-custom-fields.md) を使用し、カスタム ログのエントリを個別のフィールドに解析します。
* [ログ検索](log-analytics-log-searches.md) について学習し、データ ソースとソリューションから収集されたデータを分析します。 




<!--HONumber=Jan17_HO4-->


