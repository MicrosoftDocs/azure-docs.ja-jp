---
title: "Generic SQL コネクタ | Microsoft Docs"
description: "この記事では、Microsoft の Generic SQL コネクタを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: fd8ccef3-6605-47ba-9219-e0c74ffc0ec9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 7185ab62ee0e4383a7128fe731bd68da0ae87e66
ms.lasthandoff: 03/14/2017


---
# <a name="generic-sql-connector-technical-reference"></a>Generic SQL コネクタに関するテクニカル リファレンス
この記事では Generic SQL コネクタについて説明します。 この記事は次の製品に適用されます。

* Microsoft Identity Manager 2016 (MIM2016)
* Forefront Identity Manager 2010 R2 (FIM2010R2)
  * 4.1.3671.0 以降の修正プログラム [KB3092178](https://support.microsoft.com/kb/3092178)を使用する必要があります。

MIM2016 と FIM2010R2 の場合、コネクタは [Microsoft ダウンロード センター](http://go.microsoft.com/fwlink/?LinkId=717495)からダウンロードして入手できます。

このコネクタの動作を確認するには、「 [Generic SQL Connector step-by-step (Generic SQL コネクタのステップ バイ ステップ)](active-directory-aadconnectsync-connector-genericsql-step-by-step.md) 」の記事を参照してください。

## <a name="overview-of-the-generic-sql-connector"></a>Generic SQL コネクタの概要
Generic SQL コネクタを使用すると、ODBC 接続を提供するデータベース システムに同期サービスを統合することができます。  

包括的な観点から見た場合、コネクタの現在のリリースでサポートされている機能は次のとおりです。

| 機能 | サポート |
| --- | --- |
| 接続先のデータ ソース |コネクタは、すべての 64 ビット ODBC ドライバーでサポートされています。 次のデータ ソースでテスト済みです。 <li>Microsoft SQL Server および SQL Azure</li><li>IBM DB2 10.x</li><li>IBM DB2 9.x</li><li>Oracle 10 および 11 g</li><li>MySQL 5.x</li> |
| シナリオ |<li>オブジェクトのライフサイクル管理</li><li>パスワード管理</li> |
| 操作 |<li>フル インポートと差分インポート、エクスポート</li><li>エクスポートの場合: 追加、削除、更新、および置換</li><li>パスワードの設定、パスワードの変更</li> |
| スキーマ |<li>オブジェクトと属性の動的な検出</li> |

### <a name="prerequisites"></a>前提条件
コネクタを使用する前に、次のものが同期サーバーにインストールされていることを確認してください。

* Microsoft .NET 4.5.2 Framework 以降
* 64 ビット ODBC クライアント ドライバー

### <a name="permissions-in-connected-data-source"></a>接続先データ ソースでのアクセス許可
Generic SQL コネクタでサポートされているタスクのどれかを作成または実行するには、次のものが必要です。

* db_datareader
* db_datawriter

### <a name="ports-and-protocols"></a>ポートとプロトコル
ODBC ドライバーが動作するために必要なポートについては、データベース ベンダーのドキュメントを参照してください。

## <a name="create-a-new-connector"></a>新しいコネクタの作成
Generic SQL コネクタを作成するには、**[同期サービス]** で **[管理エージェント]** を選択し、**[作成]** を選択します。 **Generic SQL (Microsoft)** コネクタを選択します。

![コネクタの作成](./media/active-directory-aadconnectsync-connector-genericsql/createconnector.png)

### <a name="connectivity"></a>接続
コネクタでは、接続のために ODBC DSN ファイルを使用します。 **[管理ツール]** の [スタート] メニューで見つかった **[ODBC データ ソース]** を使用して DSN ファイルを作成します。 管理ツールで、 **ファイル DSN** を作成してコネクタに提供できるようにします。

![コネクタの作成](./media/active-directory-aadconnectsync-connector-genericsql/connectivity.png)

新しい Generic SQL コネクタを作成すると、[接続] 画面が最初に表示されます。 まず次の情報を指定する必要があります。

* DSN ファイル パス
* 認証
  * ユーザー名
  * パスワード

データベースは、次の認証方法のいずれかをサポートしている必要があります。

* **Windows 認証**: 認証データベースは、Windows 資格情報を使用してユーザーを検証します。 指定されたユーザー名/パスワードは、データベースとの認証に使用されます。 このアカウントには、データベースへのアクセス許可が必要です。
* **SQL 認証**: 認証データベースでは、[接続] 画面で定義されたユーザー名/パスワードを使用してデータベースに接続します。 DSN ファイルにユーザー名/パスワードを格納する場合、[接続] 画面で指定する資格情報には優先順位があります。
* **Azure SQL Database 認証**: 詳細については、 [Azure Active Directory 認証を使用した SQL Database への接続](../../sql-database/sql-database-aad-authentication.md)に関するページを参照してください。

**DN はアンカー**: このオプションを選択した場合、DN はまた、アンカー属性として使用されます。 これは、単純な実装に使用できるものの、次の制限事項があります。

* コネクタはオブジェクトの種類を&1; つのみサポートします。 したがって、参照属性は、同じオブジェクトの種類しか参照できません。

**エクスポートの種類: オブジェクトの置換**: エクスポート中には、一部の属性しか変更されていなくても、オブジェクト全体がすべての属性を伴ってエクスポートされ、既存のオブジェクトに置き換わります。

### <a name="schema-1-detect-object-types"></a>スキーマ 1 (オブジェクトの種類の検出)
このページでは、データベース内のさまざまなオブジェクトの種類をコネクタで検索する方法を構成します。

オブジェクトの種類はすべてパーティションとして表示され、 **[Configure Partitions and Hierarchies (パーティションの構成と階層)]**でさらに構成できます。

![スキーマ1a](./media/active-directory-aadconnectsync-connector-genericsql/schema1a.png)

**オブジェクトの種類の検出方法**: コネクタでは、次に示すオブジェクトの種類の検出方法をサポートしています。

* **固定値**: コンマ区切りの形式でオブジェクトの種類のリストを指定します。 たとえば、「 `User,Group,Department`」のように入力します。  
  ![スキーマ1b](./media/active-directory-aadconnectsync-connector-genericsql/schema1b.png)
* **テーブル/ビュー/ストアド プロシージャ**: テーブル/ビュー/ストアド プロシージャの名前を指定し、次にオブジェクトの種類の一覧を指定する列名を指定します。 ストアド プロシージャを使用する場合はさらに、そのパラメーターを **[Name]:[Direction]:[Value]**の形式で指定します。 各パラメーターを個別の行に指定します (新しい行を取得するには、Ctrl + Enter キーを使用します)。  
  ![スキーマ1c](./media/active-directory-aadconnectsync-connector-genericsql/schema1c.png)
* **SQL クエリ**: このオプションでは、オブジェクトの種類を含んでいる&1; つの列を返す SQL クエリを指定できます (例: `SELECT [Column Name] FROM TABLENAME`)。 返される列は、文字列型 (varchar) でなければなりません。

### <a name="schema-2-detect-attribute-types"></a>スキーマ 2 (属性の型を検出する)
このページでは、属性の名前と型を検出する方法を構成します。 前のページで検出されたすべてのオブジェクトについて構成オプションが一覧表示されます。

![スキーマ2a](./media/active-directory-aadconnectsync-connector-genericsql/schema2a.png)

**属性の型の検出方法**: コネクタでは、[スキーマの 1] 画面で検出されたすべてのオブジェクトで属性の型を検出する以下の方法をサポートしています。

* **テーブル/ビュー/ストアド プロシージャ**: 属性の名前を検索する場合に使用する必要があるテーブル/ビュー/ストアド プロシージャの名前を指定します。 ストアド プロシージャを使用する場合はさらに、そのパラメーターを **[Name]:[Direction]:[Value]**の形式で指定します。 各パラメーターを個別の行に指定します (新しい行を取得するには、Ctrl + Enter キーを使用します)。 複数値の属性内で属性名を検出するには、テーブルまたはビューのコンマ区切りのリストを指定します。 親テーブルと子テーブルの列名が同じである場合、複数値のシナリオはサポートされません。
* **SQL クエリ**: このオプションでは、属性名を含んでいる&1; つの列を返す SQL クエリを指定できます (例: `SELECT [Column Name] FROM TABLENAME`)。 返される列は、文字列型 (varchar) でなければなりません。

### <a name="schema-3-define-anchor-and-dn"></a>スキーマ 3 (アンカーと DN を定義する)
このページでは、検出されたオブジェクトの種類ごとにアンカーと DN 属性を構成することができます。 複数の属性を選択することでアンカーを一意にできます。

![スキーマ3a](./media/active-directory-aadconnectsync-connector-genericsql/schema3a.png)

* 複数値の属性とブール型の属性は表示されません。
* [接続] ページで **[DN はアンカー]** が選択されていない限り、DN とアンカーに対して同じ属性を使用することはできません。
* [接続] ページで **[DN はアンカー]** が選択されている場合、このページでは DN 属性のみが必要です。 DN 属性は、アンカー属性としても使用されます。
  ![スキーマ3b](./media/active-directory-aadconnectsync-connector-genericsql/schema3b.png)

### <a name="schema-4-define-attribute-type-reference-and-direction"></a>スキーマ4 (属性の型、参照、および方向を定義する)
このページでは、属性の型 (整数、バイナリ、ブール値など) と方向を属性ごとに構成することができます。 **[スキーマ 2]** ページからの属性がすべて (複数値の属性も含む)、一覧表示されます。

![スキーマ4a](./media/active-directory-aadconnectsync-connector-genericsql/schema4a.png)

* **DataType**: 属性の型を、同期エンジンで認識されている種類にマップするために使用します。 既定では SQL スキーマで検出された型と同じ型を使用しますが、DateTime 型と参照型は簡単に検出されません。 そのような場合は、**DateTime** または**参照**を指定する必要があります。
* **方向**: 属性の方向は Import、Export、または ImportExport に設定することができます。 ImportExport が既定値です。
  ![スキーマ4b](./media/active-directory-aadconnectsync-connector-genericsql/schema4b.png)

注:

* 属性の型がコネクタで検出不可能な型である場合、コネクタは文字列データ型を使用します。
* **入れ子になったテーブル** は、1 列のデータベース テーブルと見なすことができます。 Oracle では、入れ子になったテーブルの行を任意の順序で格納します。 ただし、入れ子になったテーブルを取得して PL/SQL 変数に入れる場合、行には 1 から始まる連続した添字が与えられます。 これにより個々の行への配列様式のアクセスが可能になります。
* **VARRYS** はコネクタでサポートされていません。

### <a name="schema-5-define-partition-for-reference-attributes"></a>スキーマ 5 (参照属性のパーティションを定義する)
このページでは、すべての参照属性について、属性が参照するパーティション (オブジェクトの種類) を構成します。

![スキーマ5](./media/active-directory-aadconnectsync-connector-genericsql/schema5.png)

**[DN is anchor (DN はアンカー)]**を使用している場合、参照元のオブジェクトの種類と同じ種類を使用する必要があります。 別のオブジェクトの種類を参照することはできません。

>[!NOTE]
2017 年 3 月の更新以降、[*] オプションを使用できるようになりました。このオプションを選択すると、すべての可能なメンバーの種類がインポートされます。

![グローバルパラメーター3](./media/active-directory-aadconnectsync-connector-genericsql/any-option.png)


インポートが完了すると、次の図のような情報が表示されます。

  ![グローバルパラメーター3](./media/active-directory-aadconnectsync-connector-genericsql/after-import.png)



### <a name="global-parameters"></a>グローバル パラメーター
[グローバル パラメーター] ページでは、差分インポート、日付/時刻形式、およびパスワード方法を構成します。

![グローバルパラメーター1](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters1.png)

>[!IMPORTANT]
 エクスポート/削除操作では、"*"、つまり "**任意のオプション*" は使用できません。

Generic SQL コネクタでは、差分インポートの次の方法をサポートしています。

* **トリガー**: 「 [トリガーを使用した差分ビューの生成](https://technet.microsoft.com/library/cc708665.aspx)」を参照してください。
* **透かし**: これは、任意のデータベースで使用できる一般的な手法です。 データベース ベンダーに基づいて透かしクエリがあらかじめ設定されています。 使用するすべてのテーブル/ビューに、透かし列が存在する必要があります。 この列では、テーブルとその依存 (複数値または子) テーブルへの挿入と更新を追跡する必要があります。 同期サービスとデータベース サーバーとの間で時間を同期させる必要があります。 そうしないと、差分インポートの一部のエントリが省略される場合があります。  
  制限事項:
  * 透かし手法では、削除されたオブジェクトはサポートされていません。
* **スナップショット**: (Microsoft SQL Server でのみ動作)。「[Generating Delta Views Using Snapshots (スナップショットを使用した差分ビューの生成)](https://technet.microsoft.com/library/cc720640.aspx)」を参照してください
* **変更の追跡**: (Microsoft SQL Server でのみ動作)。「[変更の追跡について](https://msdn.microsoft.com/library/bb933875.aspx)」を参照してください  
  制限事項: 
  * アンカー & DN 属性は、テーブル内の選択されたオブジェクトのプライマリ キーの一部を成している必要があります。
  * [変更の追跡] を使用したインポートおよびエクスポート中に、SQL クエリはサポートされていません。

**追加パラメーター**: データベース サーバーが置かれている場所を示すデータベース サーバーのタイム ゾーンを指定します。 この値は、日付属性と時刻属性のさまざまな形式をサポートするために使用されます。

コネクタでは常に、日付と時刻が UTC 形式で格納されます。 日付と時刻を正しく変換できるようにするには、データベース サーバーのタイム ゾーンと、使用する形式を指定する必要があります。 形式は .Net 形式で表現する必要があります。

エクスポート時には、日時の属性のすべてを UTC 時刻形式でコネクタに指定する必要があります。

![グローバルパラメーター2](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters2.png)

**パスワード構成**: コネクタは、パスワード同期機能を備え、パスワードの設定および変更をサポートしています。

このコネクタでは、2 つの方法でパスワード同期をサポートしています。

* **ストアド プロシージャ**: この方法では、パスワードの設定と変更に対応するために&2; つのストアド プロシージャが必要です。 次の例に示すように、**[Set Password SP Parameters (パスワード SP パラメーターの設定)]** と **[Change Password SP Parameters (パスワード SP パラメーターの変更)]** に、パスワードの追加操作とパスワードの変更操作に必要なすべてのパラメーターを入力します。
  ![グローバルパラメーター3](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters3.png)
* **パスワード拡張**: この方法ではパスワード拡張 DLL が必要です ( [IMAExtensible2Password](https://msdn.microsoft.com/library/microsoft.metadirectoryservices.imaextensible2password.aspx) インターフェイスを実装する拡張 DLL 名を指定する必要があります)。 コネクタが実行時に DLL を読み込むことができるように、拡張フォルダーにパスワード拡張アセンブリを配置する必要があります。
  ![グローバルパラメーター4](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters4.png)

また、 **[拡張の構成]** ページでパスワードの管理を有効にする必要があります。
![グローバルパラメーター5](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters5.png)

### <a name="configure-partitions-and-hierarchies"></a>[Configure Partitions and Hierarchies (パーティションの構成と階層)]
パーティションと階層のページでは、すべてのオブジェクトの種類を選択します。 各オブジェクトの種類はそれぞれの独自のパーティションです。

![パーティション1](./media/active-directory-aadconnectsync-connector-genericsql/partitions1.png)

**[接続]** ページまたは **[グローバル パラメーター]** ページで定義されている値を上書きすることもできます。

![パーティション2](./media/active-directory-aadconnectsync-connector-genericsql/partitions2.png)

### <a name="configure-anchors"></a>アンカーの構成
アンカーは既に定義されているので、このページは読み取り専用です。 選択したアンカー属性は常にオブジェクトの種類に付加され、それがオブジェクトの種類全体の中で確実に一意のままであるようにします。

![アンカー](./media/active-directory-aadconnectsync-connector-genericsql/anchors.png)

## <a name="configure-run-step-parameter"></a>実行ステップ パラメーターの構成
次の手順は、コネクタの実行プロファイルで構成します。 これらの構成によって、データのインポートとエクスポートの実際の作業が実行されます。

### <a name="full-and-delta-import"></a>フル インポートと差分インポート
Generic SQL コネクタでは、次の方法を使用したフル インポートと差分インポートをサポートしています。

* テーブル
* 表示
* ストアド プロシージャ
* SQL クエリ

![実行手順1](./media/active-directory-aadconnectsync-connector-genericsql/runstep1.png)

**テーブル/ビュー**  
オブジェクトの複数値の属性をインポートするには、**[Name of Multi-Valued table/views (複数値のテーブル/ビューの名前)]** にコンマ区切りのテーブル/ビュー名を指定し、**[結合条件]** に親テーブルとのそれぞれの結合条件を指定します。

例: Employee オブジェクトとそのすべての複数値属性をインポートします。 Employee という名前のテーブル (メイン テーブル) と Department という名前のテーブル (複数値テーブル) があります。
以下の手順を実行します。

* **[テーブル/ビュー/SP]** に「**Employee**」と入力します。
* **[複数値のテーブル/ビューの名前]**に「Department」と入力します。
* **[結合条件]** に Employee と Department の結合条件を入力します。たとえば、`Employee.DEPTID=Department.DepartmentID` のようになります。
  ![実行手順2](./media/active-directory-aadconnectsync-connector-genericsql/runstep2.png)

**ストアド プロシージャ**  
![実行手順3](./media/active-directory-aadconnectsync-connector-genericsql/runstep3.png)

* データの量が多い場合は、ストアド プロシージャを使用して、改ページ調整を実装することをお勧めします。
* ストアド プロシージャで改ページ調整をサポートするには、開始インデックスと終了インデックスを指定する必要があります。 「 [Efficiently Paging Through Large Amounts of Data (大量データの効率的なページング)](https://msdn.microsoft.com/library/bb445504.aspx)」を参照してください。
* @StartIndex と @EndIndex は実行時に、**[Configure Step (手順の構成)]** ページで構成したそれぞれのページ サイズ値に置き換えられます。 たとえば、コネクタが取得した最初のページのサイズが 500 に設定されているとします。このような場合、@StartIndex は 1、@EndIndex は 500 になります。 これらの値は、コネクタが後続のページを取得するに従って大きくなり、@StartIndex 値と @EndIndex 値が変更されます。
* パラメーター化されたストアド プロシージャを実行するには、 `[Name]:[Direction]:[Value]` 形式でパラメーターを指定します。 各パラメーターを個別の行に入力します (新しい行を取得するには、Ctrl + Enter キーを使用します)。
* Generic SQL コネクタでは、Microsoft SQL Server 内のリンク サーバーからのインポート操作もサポートされています。 リンク サーバー内のテーブルから情報が取得される場合、テーブルは次の形式で提供されます。`[ServerName].[Database].[Schema].[TableName]`
* Generic SQL コネクタでは、実行手順の情報とスキーマ検出との間で構造 (エイリアス名とデータ型の両方) が類似しているオブジェクトのみがサポートされます。 スキーマで選択したオブジェクトと、実行手順で指定した情報が異なる場合、このタイプのシナリオは SQL コネクタでサポートされません。

**SQL クエリ**  
![実行手順4](./media/active-directory-aadconnectsync-connector-genericsql/runstep4.png)

![実行手順5](./media/active-directory-aadconnectsync-connector-genericsql/runstep5.png)

* 複数の結果セット クエリはサポートされていません。
* SQL クエリでは、改ページ調整をサポートしており、改ページ調整をサポートする変数として開始インデックスと終了インデックスを提供しています。

### <a name="delta-import"></a>差分インポート
![実行手順6](./media/active-directory-aadconnectsync-connector-genericsql/runstep6.png)

差分インポートの構成には、フル インポートと比較して追加の構成が必要です。

* 方法としてトリガーまたはスナップショットを選択して差分変更を追跡する場合、 **[History Table or Snapshot database name (履歴テーブルまたはスナップショットのデータベース名)]** ボックスで履歴テーブルまたはスナップショットのデータベースを指定します。
* また、履歴テーブルと親テーブルとの間の結合条件を指定する必要があります (たとえば、 `Employee.ID=History.EmployeeID`
* 履歴テーブルから親テーブルに対するトランザクションを追跡するには、操作情報 (追加/更新/削除) が含まれている列名を指定する必要があります。
* 透かしで差分変更を追跡する場合、操作情報が含まれている列名を **[Water Mark Column Name (透かし列名)]**に指定する必要があります。
* 変更の種類については、 **変更の種類の属性** 列が必要です。 この列により、プライマリ テーブルまたは複数値のテーブル内で発生した変更が、差分ビュー内の変更の種類にマップされます。 この列には、属性レベルの変更に対しては Modify_Attribute という変更の種類を含め、オブジェクト レベルの変更に対しては Add、Modify、または Delete という変更の種類を含めることができます。 変更の種類が既定値の Add、Modify、または Delete 以外の種類である場合、このオプションを使用してそれらの値を定義できます。

### <a name="export"></a>エクスポート
![実行手順7](./media/active-directory-aadconnectsync-connector-genericsql/runstep7.png)

Generic SQL コネクタでは、次に示す&4; つのサポートされた方法を使用したエクスポートをサポートしています。

* テーブル
* 表示
* ストアド プロシージャ
* SQL クエリ

**テーブル/ビュー**  
[テーブル/ビュー] オプションを選択した場合、コネクタによって各クエリが生成され、エクスポートが実行されます。

**ストアド プロシージャ**  
![実行手順8](./media/active-directory-aadconnectsync-connector-genericsql/runstep8.png)

[ストアド プロシージャ] オプションを選択した場合、エクスポートには、挿入/更新/削除の操作を実行する&3; 種類のストアド プロシージャが必要です。

* **[Add SP Name (SP 名の追加)]**: この SP は、それぞれのテーブルで挿入を行うために任意のオブジェクトがコネクタに渡された場合に実行されます。
* **[Update SP Name (SP 名の更新)]**: この SP は、それぞれのテーブルで更新を行うために任意のオブジェクトがコネクタに渡された場合に実行されます。
* **[Delete SP Name (SP 名の削除)]**: この SP は、それぞれのテーブルで削除を行うために任意のオブジェクトがコネクタに渡された場合に実行されます。
* ストアド プロシージャへのパラメーター値として使用されるスキーマから選択した属性です。 たとえば、 `EmployeeName: INPUT: @EmployeeName` (コネクタのスキーマで EmployeeName が選択されると、コネクタはエクスポートの実行中にそれぞれの値を置換します) です。
* パラメーター化されたストアド プロシージャを実行するには、 `[Name]:[Direction]:[Value]` 形式でパラメーターを指定します。 各パラメーターを個別の行に入力します (新しい行を取得するには、Ctrl + Enter キーを使用します)。

**SQL query**  
![実行手順9](./media/active-directory-aadconnectsync-connector-genericsql/runstep9.png)

[SQL クエリ] オプションを選択した場合、エクスポートには、挿入/更新/削除の操作を実行する&3; 種類のクエリが必要です。

* **[挿入クエリ]**: このクエリは、それぞれのテーブルで挿入を行うためにコネクタに任意のオブジェクトが渡された場合に実行されます。
* **[更新クエリ]**: このクエリは、それぞれのテーブルで更新を行うためにコネクタに任意のオブジェクトが渡された場合に実行されます。
* **[削除クエリ]**: このクエリは、それぞれのテーブルで削除を行うためにコネクタに任意のオブジェクトが渡された場合に実行されます。
* クエリへのパラメーター値として使用されるスキーマから選択された属性です (たとえば `Insert into Employee (ID, Name) Values (@ID, @EmployeeName)`

## <a name="troubleshooting"></a>トラブルシューティング
* コネクタのトラブルシューティングを行うためにログ記録を有効にする方法については、「 [How to Enable ETW Tracing for Connectors (コネクタの ETW トレースを有効にする方法)](http://go.microsoft.com/fwlink/?LinkId=335731)」を参照してください。

