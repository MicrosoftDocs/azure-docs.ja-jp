---
title: Azure Purview に Oracle ソースを登録してスキャン (プレビュー) を設定する
description: この記事では、Azure Purview に Oracle ソースを登録し、スキャンを設定する方法について、概要を説明します。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 37f6a779e7dd83a6aa61de9850ad3b49b57393f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103010454"
---
# <a name="register-and-scan-oracle-source-preview"></a>Oracle ソースを登録してスキャンする (プレビュー)

この記事では、Purview に Oracle データベースを登録し、スキャンを設定する方法について、概要を説明します。

## <a name="supported-capabilities"></a>サポートされる機能

Oracle ソースでは Oracle データベースからメタデータを抽出するための **フル スキャン** がサポートされており、データ資産間の **系列** がフェッチされます。

## <a name="prerequisites"></a>前提条件

1.  最新の[セルフホステッド統合ランタイム](https://www.microsoft.com/download/details.aspx?id=39717)を設定します。
    詳細については、「[セルフホステッド統合ランタイムを作成して共有する](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)」を参照してください。

2.  セルフホステッド統合ランタイムがインストールされている仮想マシンに [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) がインストールされていることを確認します。

3.  セルフホステッド統合ランタイム コンピューターに \"Visual C++ 再頒布可能パッケージ 2012 Update 4\" がインストールされていることを確認します。 まだインストールしていない場合は、[こちら](https://www.microsoft.com/download/details.aspx?id=30679)からダウンロードしてください。

4.  セルフホステッド統合ランタイムが実行されている仮想マシンに、[こちら](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html)から Oracle JDBC ドライバーを手動でダウンロードする必要があります。

    > [!Note] 
    > このドライバーは、VM 内のすべてのアカウントからアクセスできる必要があります。 それをユーザー アカウントにインストールしないでください。

5.  サポートされている Oracle データベースのバージョンは、6i から 19c です。

6.  ユーザーのアクセス許可: 初回のスキャンが正常に行われるようにするには、完全なシステム管理者という種類のアクセス許可が必要です。

    後続のスキャンでは、システム テーブルへの読み取り専用アクセスが必要です。 ユーザーには、セッションを作成するためのアクセス許可、および SELECT\_CATALOG\_ROLE というロールが割り当てられている必要があります。 または、このコネクタでメタデータを照会する各システム テーブルに対する SELECT アクセス許可をユーザーに付与することもできます。
       > grant create session to \[user\];\
        grant select on all\_users to \[user\];\
        grant select on dba\_objects to \[user\];\
        grant select on dba\_tab\_comments to \[user\];\
        grant select on dba\_external\_locations to \[user\];\
        grant select on dba\_directories to \[user\];\
        grant select on dba\_mviews to \[user\];\
        grant select on dba\_clu\_columns to \[user\];\
        grant select on dba\_tab\_columns to \[user\];\
        grant select on dba\_col\_comments to \[user\];\
        grant select on dba\_constraints to \[user\];\
        grant select on dba\_cons\_columns to \[user\];\
        grant select on dba\_indexes to \[user\];\
        grant select on dba\_ind\_columns to \[user\];\
        grant select on dba\_procedures to \[user\];\
        grant select on dba\_synonyms to \[user\];\
        grant select on dba\_views to \[user\];\
        grant select on dba\_source to \[user\];\
        grant select on dba\_triggers to \[user\];\
        grant select on dba\_arguments to \[user\];\
        grant select on dba\_sequences to \[user\];\
        grant select on dba\_dependencies to \[user\];\
        grant select on V\_\$INSTANCE to \[user\];\
        grant select on v\_\$database to \[user\];
    
## <a name="setting-up-authentication-for-a-scan"></a>スキャンの認証の設定

Oracle ソースでサポートされている認証は **基本認証** のみです。

## <a name="register-an-oracle-source"></a>Oracle ソースを登録する

新しい Oracle ソースをデータ カタログに登録するには、次のようにします。

1.  ご自分の Purview アカウントに移動します。
2.  左側のナビゲーションで **[ソース]** を選択します。
3.  **[登録]** を選択します
4.  [Register sources]\(ソースの登録\) で、 **[Oracle]** を選択します。 **[続行]** をクリックします。

    :::image type="content" source="media/register-scan-oracle-source/register-sources.png" alt-text="ソースを登録する" border="true":::

**[Register sources (Oracle)]\(ソースの登録 (Oracle)\)** 画面で、次のようにします。

1.  データ ソースがカタログに一覧表示されるときの **名前** を入力します。

2.  Oracle ソースに接続するための **ホスト** 名を入力します。 これは、次のいずれかにすることができます。
    - データベース サーバーに接続するために JDBC で使用されるホスト名。 たとえば、MyDatabaseServer.com または
    - IP アドレス。 たとえば、192.169.1.2 または
    - 完全修飾の JDBC 接続文字列。 たとえば、
        jdbc:oracle:thin:@(DESCRIPTION=(LOAD\_BALANCE=on)(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver1)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver2)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver3)(PORT=1521))(CONNECT\_DATA=(SERVICE\_NAME=orcl)))

3.  データベース サーバーに接続するために JDBC で使用される **ポート番号** (Oracle の場合、既定では 1521) を入力します。

4.  データベース サーバーに接続するために JDBC で使用される **Oracle サービス名** を入力します。

5.  コレクションを選択するか、新しいものを作成します (省略可能)

6.  データ ソースの登録を終了します。

    :::image type="content" source="media/register-scan-oracle-source/register-sources-2.png" alt-text="ソースの登録のオプション" border="true":::

## <a name="creating-and-running-a-scan"></a>スキャンを作成し、実行する

新しいスキャンを作成して実行するには、次の操作を行います。

1.  管理センターで、 [統合ランタイム] をクリックします。 セルフホステッド統合ランタイムが設定されていることを確認してください。 設定されていない場合は、[こちら](https://docs.microsoft.com/azure/purview/manage-integration-runtimes)に記載されている手順を使用して、セルフホステッド統合ランタイムを作成します。

2.  **[ソース]** に移動します。

3.  登録されている Oracle ソースを選択します。

4.  **[+ 新しいスキャン]** を選択します。

5.  次の詳細を指定します。

    a.  **[名前]** : スキャンの名前

    b.  **[Connect via integration runtime]\(統合ランタイム経由で接続\)** : 構成済みのセルフホステッド統合ランタイムを選択します

    c.  **[資格情報]** : 対象のデータ ソースに接続するための資格情報を選択します。 次のことを確認します。
    - 資格情報を作成するときに [基本認証] を選択します。        
    - ユーザー名入力フィールドで、データベース サーバーに接続するために JDBC で使用されるユーザー名を指定します        
    - データベース サーバーに接続するために JDBC で使用されるユーザー パスワードを、秘密鍵に格納します。

    d.  **[スキーマ]** : インポートするスキーマのサブセットをセミコロン区切りのリストとして指定します。 たとえば、schema1; schema2 のようにします。 リストが空の場合は、すべてのユーザー スキーマがインポートされます。 既定では、すべてのシステム スキーマ (SysAdmin など) とオブジェクトが無視されます。 リストが空の場合は、使用可能なすべてのスキーマがインポートされます。
        SQL LIKE 式の構文を使用したスキーマ名のパターンとして、% の使用も許容されます (例: A%; %B; %C%; D)。
       -   A で始まる、または        
       -   B で終わる、または        
       -   C を含む、または        
       -   D と等しい

    NOT および特殊文字の使用は許容されません。

6.  **[Driver location]\(ドライバーの場所\)** : セルフホステッド統合ランタイムが実行されている VM 内の JDBC ドライバーの場所へのパスを指定します。 これは、有効な JAR フォルダーの場所へのパスである必要があります。

7.  **[Maximum memory available]\(使用可能な最大メモリ\):** スキャン プロセスで使用される、顧客の VM で使用可能な最大メモリ (GB 単位)。 これは、スキャンする SAP S/4HANA ソースのサイズによって異なります。

    :::image type="content" source="media/register-scan-oracle-source/scan.png" alt-text="oracle をスキャンする" border="true":::

8.  **[続行]** をクリックします。

9.  **スキャン トリガー** を選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

10.  スキャンを確認し、 **[保存および実行]** をクリックします。

## <a name="viewing-your-scans-and-scan-runs"></a>スキャンとスキャンの実行を確認する

1. 管理センターに移動します。 **[Sources and scanning]\(ソースとスキャン\)** セクションの **[データ ソース]** を選択します。

2. 目的のデータ ソースを選択します。 そのデータ ソースに対する既存のスキャンの一覧が表示されます。

3. 結果を表示するスキャンを選択します。

4. このページには、前回のすべてのスキャン実行と、各スキャン実行のメトリックと状態が表示されます。 また、そのスキャンがスケジュールされたスキャンと手動スキャンのどちらかであるか、分類が適用された資産の数、検出された資産の合計数、スキャンの開始時刻と終了時刻、スキャンの実行時間の合計も表示されます。

## <a name="manage-your-scans"></a>スキャンを管理する

スキャンを管理または削除するには、次の操作を行います。

1. 管理センターに移動します。 **[Sources and scanning]\(ソースとスキャン\)** セクションの **[データ ソース]** を選択し、目的のデータ ソースを選択します。

2. 管理するスキャンを選択します。 スキャンを編集するには、 **[編集]** を選択します。

3. スキャンを削除するには、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)