---
title: "BI 分析ツールを使用して Azure DocumentDB に接続する | Microsoft Docs"
description: "Azure DocumentDB の ODBC ドライバーを使用して表とビューを作成し、正規化されたデータを BI とデータ分析ソフトウェアで表示できるようにする方法について説明します。"
keywords: "ODBC、ODBC ドライバー"
services: documentdb
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 9967f4e5-4b71-4cd7-8324-221a8c789e6b
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 03/27/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 5f712c7fa9b6ee06f7c89de40ba4227a925a35ce
ms.lasthandoff: 03/28/2017


---

# <a name="connect-to-documentdb-using-bi-analytics-tools-with-the-odbc-driver"></a>BI 分析ツールと ODBC ドライバーを使用して DocumentDB に接続する

DocumentDB の ODBC ドライバーを使用すると、SQL Server Integration Services、Power BI Desktop、Tableau などの BI 分析ツールを使って DocumentDB に接続できるため、これらのソリューションで DocumentDB データを分析したり視覚化したりできます。

DocumentDB の ODBC ドライバーは ODBC 3.8 に準拠し、ANSI SQL-92 構文をサポートしています。 ODBC ドライバーは、DocumentDB でデータを再正規化するために役立つ豊富な機能を提供します。 ドライバーを使うと、DocumentDB のデータを表やビューで表示できます。 またドライバーによって、表やビューに対して、挿入、更新、削除などのクエリを含む SQL 操作を実行できます。

## <a name="why-do-i-need-to-normalize-my-data"></a>データを正規化すべき理由
DocumentDB は NoSQL データベースであるため、アプリケーションがデータ モデルを迅速に反復し、スキーマの厳しい制約を受けないようにすることで、アプリの迅速な開発を実現します。 1 つの DocumentDB データベースに、さまざまな構造の JSON ドキュメントを格納できます。 これは迅速なアプリケーション開発には最適ですが、データ分析と BI ツールを使ってデータを分析し、レポートを作成する場合は、通常データをフラット化して特定のスキーマに準拠させる必要があります。

ここで役立つのが ODBC ドライバーです。 ODBC ドライバーを使うと、DocumentDB のデータをデータ分析やレポート作成のニーズに合った表やビューに再正規化することができます。 再正規化されたスキーマが元になるデータに影響を与えることはなく、開発者はスキーマに準拠する制約を受けずに、ODBC 準拠のツールを活用してデータに簡単にアクセスできます。 DocumentDB データベースは開発者チームにとって便利なだけでなく、データ アナリストも満足することでしょう。

それでは、ODBC ドライバーを使ってみましょう。

## <a id="install"></a>手順 1: DocumentDB の ODBC ドライバーをインストールする

1. お使いの環境用のドライバーをダウンロードします。

    * [Microsoft Azure DocumentDB ODBC 64-bit.msi](https://aka.ms/documentdb-odbc-64x64) (64 ビット Windows 用)
    * [Microsoft Azure DocumentDB ODBC 32x64-bit.msi](https://aka.ms/documentdb-odbc-32x64) (64 ビット上の 32 ビット Windows 用)
    * [Microsoft Azure DocumentDB ODBC 32-bit.msi](https://aka.ms/documentdb-odbc-32x32) (32 ビット Windows 用)

    msi ファイルをローカルで実行すると、**Microsoft Azure DocumentDB ODBC ドライバーのインストール ウィザード**が開始されます。 
2. 既定の入力を使用して ODBC ドライバーをインストールし、インストール ウィザードを完了します。
3. **ODBC データ ソース 管理者** アプリをコンピューターで開きます。これを行うには、Windows の検索ボックスに「**ODBC データ ソース**」と入力します。 
    ドライバーがインストールされたことを確認するには、**[ドライバー]** タブをクリックして **Microsoft DocumentDB ODBC ドライバー** が一覧に記載されていることを確認します。

    ![DocumentDB ODBC データ ソース管理者](./media/documentdb-nosql-odbc-driver/documentdb-nosql-odbc-driver.png)

## <a id="connect"></a>手順 2: DocumentDB データベースに接続する

1. [DocumentDB ODBC ドライバーをインストール](#install)したら、**[ODBC データ ソース管理者]** ウィンドウで **[追加]** をクリックします。 ユーザー DSN またはシステム DSN を作成できます。 この例では、ユーザー DSN を作成します。
2. **[新規データ ソースの作成]** ウィンドウで、**[Microsoft DocumentDB ODBC ドライバー]** を選択し、**[完了]** をクリックします。
3. **[DocumentDB ODBC Driver SDN Setup]** (DocumentDB ODBC ドライバーの SDN セットアップ) ウィンドウに、次の内容を入力します。 

    ![DocumentDB ODBC ドライバーの DSN セットアップ ウィンドウ](./media/documentdb-nosql-odbc-driver/documentdb-nosql-odbc-driver-dsn-setup.png)
    - **データ ソース名**: ODBC DSN の独自のフレンドリ名。 この名前は DocumentDB アカウントに対して一意なので、複数のアカウントがある場合は適切に名前を付けてください。
    - **説明**: データ ソースの簡単な説明。
    - **ホスト**: DocumentDB アカウントの URI。 Azure Portal の [DocumentDB キー] ブレードから取得できます。次のスクリーン ショットを参照してください。 
    - **アクセス キー**: Azure Portal の [DocumentDB キー] ブレードから、プライマリまたはセカンダリ、読み取り/書き込みまたは読み取り専用のキーを選択します。次のスクリーン ショットを参照してください。 DSN を読み取り専用のデータ処理とレポート作成に使用する場合は、読み取り専用キーを使用することをお勧めします。
    ![[DocumentDB キー] ブレード](./media/documentdb-nosql-odbc-driver/documentdb-nosql-odbc-driver-keys.png)
    - **Encrypt Access Key for** (アクセスキーの暗号化): このコンピューターのユーザーに基づいた最適な選択肢を選びます。 
4. **[テスト]** ボタンをクリックして、DocumentDB アカウントに接続できることを確認します。 
5. **[詳細オプション]** をクリックして、次の値を設定します。
    - **Query Consistency** (クエリの一貫性): 操作の[一貫性レベル](documentdb-consistency-levels.md)を選択します。 既定ではセッションです。
    - **再試行回数**: サービスの調整のために最初の要求が完了しない場合、操作を再試行する回数を入力します。
    - **スキーマ ファイル**: これにはオプションがいくつかあります。
        - 既定では、このエントリをそのまま (空白) にしておくと、ドライバーがすべてのコレクションの最初のページをスキャンして、各コレクションのスキーマを判定します。 これは、コレクション マッピングとして知られています。 スキーマ ファイルが定義されていないと、ドライバーはドライバー セッションごとにスキャンを実行する必要があるため、DSN を使ったアプリケーションの起動に時間がかかることがあります。 スキーマ ファイルを DSN と常に関連付けることをお勧めします。
        - ([スキーマ エディター](#schema-editor)を使用して作成した) スキーマ ファイルが既にある場合は、**[参照]** をクリックしてファイルに移動し、**[保存]** をクリックしてから **[OK]** をクリックします。
        - 新しいスキーマを作成する場合は、**[OK]** をクリックしてからメイン ウィンドウの **[スキーマ エディター]** をクリックします。 次に[スキーマ エディター](#schema-editor)の情報に進みます。 新しいスキーマ ファイルの作成時には、**[詳細オプション]** ウィンドウに戻って新しく作成したスキーマ ファイルを含めることに注意してください。

6. 操作を完了して **[DocumentDB ODBC Driver DSN Setup]**(DocumentDB ODBC ドライバーの DSN セットアップ) ウィンドウを閉じると、新しいユーザー DSN が [ユーザー DSN] タブに追加されます。

    ![[ユーザー DSN] タブの新しい DocumentDB ODBC DSN](./media/documentdb-nosql-odbc-driver/documentdb-nosql-odbc-driver-user-dsn.png)

## <a id="#collection-mapping"></a>手順 3: コレクション マッピングの方法を使ってスキーマ定義を作成する

使用できるサンプリング方法は、**コレクション マッピング**と**テーブル区切り記号**の 2 種類です。 サンプリング セッションではどちらのサンプリング方法も利用できますが、各コレクションが使用できるのは特定のサンプリング方法のみになります。 次の手順では、コレクション マッピングの方法を使って 1 つまた複数のコレクション内のデータのスキーマを作成します。 このサンプリング方法では、コレクションのページのデータを取得して、データの構造を判定します。 ODBC 側のテーブルにコレクションを入れ替えます。 このサンプリング方法は、コレクションのデータの種類が同じ場合は効率的で迅速です。 コレクションに異なる型のデータが含まれる場合は、[テーブル区切り記号のマッピング方法](#table-mapping)を使用することをお勧めします。この方法は、コレクション内のデータ構造を判定するより堅牢なサンプリング方法を提供します。 

1. 「[DocumentDB データベースに接続する](#connect)」の手順 1-4 が完了したら、**[DocumentDB ODBC Driver DSN Setup]**(DocumentDB ODBC ドライバーの DSN セットアップ) ウィンドウの **[スキーマ エディター]** をクリックします。

    ![[DocumentDB ODBC Driver DSN Setup](DocumentDB ODBC ドライバーの DSN セットアップ) ウィンドウの [スキーマ エディター] ボタン](./media/documentdb-nosql-odbc-driver/documentdb-nosql-odbc-driver-schema-editor.png)
2. **[スキーマ エディター]** ウィンドウで、**[新規作成]** をクリックします。
    **[スキーマを作成する]** ウィンドウに、DocumentDB アカウントのすべてのコレクションが表示されます。 
3. サンプリングするコレクションを 1 つまたは複数選択し、**[サンプル]** をクリックします。 
4. **[デザイン ビュー]** タブに、データベース、スキーマ、テーブルが表示されます。 [テーブル ビュー] には、列名 (SQL 名、ソース名など) と関連付けられている一連のプロパティがスキャンによって表示されます。
    列ごとに、列の SQL 名、SQL 型、SQL の長さ (該当する場合)、スケール (該当する場合)、有効桁数 (該当する場合)、Null 許容を変更できます。
    - クエリ結果から列を除外したい場合は、**[列の非表示]** を **true** に設定できます。 [列の非表示] が true でマークされた列は、まだスキーマの一部ではあるものの、選択やプロジェクションでは返されません。 たとえば、DocumentDB のシステムで必要な "_" で始まるすべてのプロパティを非表示にすることができます。
    - **ID** 列は非表示にできない唯一のフィールドですが、これは正規化されたスキーマでプライマリ キーとして使用されるためです。 
5. スキーマの定義を完了したら、**[ファイル]** | **[保存]** をクリックし、スキーマを保存するディレクトリに移動して、**[保存]** をクリックします。

    今後このスキーマを DSN と共に使用する場合は、[DocumentDB ODBC Driver DSN Setup](DocumentDB ODBC ドライバーの DSN セットアップ) ウィンドウを (ODBC データ ソース管理者経由で) 開き、[詳細オプション] をクリックして、[スキーマ ファイル] ボックスの保存されたスキーマに移動します。 スキーマ ファイルを既存の DSN に保存すると、DNS 接続のスコープが、スキーマによって定義されたデータと構造に変更されます。

## <a id="table-mapping"></a>手順 4: テーブル区切り記号のマッピング方法を使ってスキーマ定義を作成する

使用できるサンプリング方法は、**コレクション マッピング**と**テーブル区切り記号**の 2 種類です。 サンプリング セッションではどちらのサンプリング方法も利用できますが、各コレクションが使用できるのは特定のサンプリング方法のみになります。 

次の手順では、**テーブル区切り記号**のマッピング方法を使って 1 つまたは複数のコレクション内のデータのスキーマを作成します。 コレクションに異なる型のデータが含まれる場合は、このサンプリング方法を使用することをお勧めします。 この方法を使って、サンプリングのスコープを一連の属性と対応する値に設定できます。 たとえば、ドキュメントに "型" のプロパティが含まれる場合は、サンプリングのスコープをこのプロパティの値に設定できます。 サンプリングの最終的な結果は、指定した型の各値が記載されたテーブルのセットになります。 たとえば、型が自動車の場合は自動車のテーブルが生成され、型が飛行機の場合は飛行機のテーブルが生成されます。

1. 「[DocumentDB データベースに接続する](#connect)」の手順 1-4 が完了したら、[DocumentDB ODBC Driver DSN Setup](DocumentDB ODBC ドライバーの DSN セットアップ) ウィンドウの **[スキーマ エディター]** をクリックします。
2. **[スキーマ エディター]** ウィンドウで、**[新規作成]** をクリックします。
    **[スキーマを作成する]** ウィンドウに、DocumentDB アカウントのすべてのコレクションが表示されます。 
3. **[サンプル ビュー]** タブでコレクションを選択し、コレクションの**[マッピング定義]** 列で **[編集]** をクリックします。 次に **[マッピング定義]** ウィンドウで **[Table Delimiters]**(テーブル区切り記号) の方法を選択します。 次に、次を実行します。

    a. **[属性]** ボックスに、区切り記号のプロパティ名を入力します。 これは、サンプリングのスコープを設定するドキュメントのプロパティ (例：市区町村) です。次に Enter キーを押します。 

    b. サンプリングのスコープを入力した属性の特定の値に設定する場合は、[選択] ボックスで属性を選択し、**[値]** ボックスに値 (例：シアトル) を入力して Enter キーを押します。 引き続き属性に複数の値を追加できます。 値を入力するときは、適切な属性が選択されていることを確認してください。

    たとえば、市区町村の**属性**値を含めて、テーブルにニューヨークとドバイの都市の値の行のみを含めるよう制限する場合は、[属性] ボックスに「市区町村」と入力し、**[値]** ボックスには「ニューヨーク」と「ドバイ」を入力します。
4. **[OK]**をクリックします。 
5. サンプリングするコレクションのマッピング定義が完了したら、**[スキーマ エディター]** ウィンドウで **[サンプル]** をクリックします。
     列ごとに、列の SQL 名、SQL 型、SQL の長さ (該当する場合)、スケール (該当する場合)、有効桁数 (該当する場合)、Null 許容を変更できます。
    - クエリ結果から列を除外したい場合は、**[列の非表示]** を **true** に設定できます。 [列の非表示] が true でマークされた列は、まだスキーマの一部ではあるものの、選択やプロジェクションでは返されません。 たとえば、DocumentDB のシステムで必要な "_" で始まるすべてのプロパティを非表示にすることができます。
    - **ID** 列は非表示にできない唯一のフィールドですが、これは正規化されたスキーマでプライマリ キーとして使用されるためです。 
6. スキーマの定義を完了したら、**[ファイル]** | **[保存]** をクリックし、スキーマを保存するディレクトリに移動して、**[保存]** をクリックします。
7. **[DocumentDB ODBC Driver DSN Setup]**(DocumentDB ODBC ドライバーの DSN セットアップ) ウィンドウに戻って、[詳細オプション] をクリックします。 次に、**[スキーマ ファイル]** ボックスで保存したスキーマ ファイルに移動し、**[OK]** をクリックします。 もう 1 度 **[OK]** をクリックして DSN を保存します。 これにより、DSN に作成したスキーマが保存されます。 

## <a name="optional-creating-views"></a>(省略可能) ビューを作成する
サンプリング プロセスの一環として、ビューの定義と作成ができます。 これらのビューは、SQL ビューと同じです。 これらは読み取り専用で、DocumentDB SQL の選択とプロジェクションが定義されているスコープです。 

データのビューを作成するには、**[スキーマ エディター]** ウィンドウの **[ビューの定義]** 列で、サンプリングするコレクションの行の **[追加]** をクリックします。 次に **[ビューの定義]** ウィンドウで、次の操作をします。
1. **[新規]** をクリックしてビューの名前 (例：EmployeesfromSeattleView) を入力し、**[OK]** をクリックします。
2. **[ビューの編集]** ウィンドウで、DocumentDB クエリを入力します。 これは、たとえば `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Gender, c.Manager FROM c WHERE c.City = “Seattle”` のような DocumentDB SQL クエリである必要があります。次に **[OK]** をクリックします。

ビューは好きな数だけ作成できます。 ビューの定義が完了したら、データをサンプリングできます。 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>手順 5: Power BI Desktop などの BI ツールでデータを表示する

新しい DSN を使用して、DocumentDB を ODBC 準拠のツールと接続できます。この手順では、Power BI Desktop に接続して Power BI を視覚化する方法について簡単に説明します。

1. Power BI Desktop を開きます。
2. **[データの取得]** をクリックします。
3. **[データの取得]** ウィンドウで、**[Other]**(その他) | **[ODBC]** | **[接続]** の順にクリックします。
4. **[From ODBC]**(ODBC から) ウィンドウで作成したデータ ソース名を選択し、**[OK]** をクリックします。 **[詳細オプション]** エントリは空白のままにしておくことができます。
5. **[ODBC ドライバーを使用してデータ ソースにアクセスします]** ウィンドウで、**[既定またはカスタム]** を選択し、**[接続]** をクリックします。 **資格情報の接続文字列プロパティ**を含める必要はありません。
6. **[ナビゲーター]** ウィンドウの左ペインでデータベースとスキーマを展開し、テーブルを選択します。 結果ペインには作成したスキーマを使用するデータが含まれています。
7. Power BI Desktop でデータを視覚化するには、テーブル名の前のボックスをオンにし、**[読み込む]** をクリックします。
8. Power BI Desktop の左端の [データ] タブを選択して ![Power BI Desktop の [データ] タブ](./media/documentdb-nosql-odbc-driver/documentdb-nosql-odbc-driver-data-tab.png) データがインポートされたことを確認します。
9. [レポート] タブ ![Power BI Desktop の [レポート] タブ](./media/documentdb-nosql-odbc-driver/documentdb-nosql-odbc-driver-report-tab.png)をクリックし、**[新しいビジュアル]** をクリックしてタイルをカスタマイズすることにより、Power BI を使用してビジュアルを作成できます。 Power BI Desktop での視覚化の詳細については、「[Power BI での視覚化の種類](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/)」を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

次のエラーが発生した場合は、[手順 2](#connect) で、Azure Portal からコピーした**ホスト**と**アクセス キー**の値が正しいことを確認して再度試してください。 Azure Portal の**ホスト**と**アクセス キー**の値の右側にある [コピー] ボタンを使って、値を正しくコピーします。

    [HY000]: [Microsoft][DocumentDB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>次のステップ

DocumentDB の詳細については、「[DocumentDB とは?](documentdb-introduction.md)」を参照してください。

