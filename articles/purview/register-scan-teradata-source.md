---
title: Azure Purview に Teradata ソースを登録してスキャン (プレビュー) を設定する
description: この記事では、Azure Purview に Teradata ソースを登録し、スキャンを設定する方法について、概要を説明します。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 2008e014e9f160b643ed5f591fff81c0b215e24a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102175080"
---
# <a name="register-and-scan-teradata-source-preview"></a>Teradata ソースを登録し、スキャンする (プレビュー)

この記事では、Purview に Teradata ソースを登録し、スキャンを設定する方法について、概要を説明します。

## <a name="supported-capabilities"></a>サポートされる機能

Teradata ソースでは **フル スキャン** がサポートされており、Teradata データベースからメタデータを抽出し、データ資産間の **系列** をフェッチできます。

## <a name="prerequisites"></a>前提条件

1.  最新の[セルフホステッド統合ランタイム](https://www.microsoft.com/download/details.aspx?id=39717)を設定します。
    詳細については、「[セルフホステッド統合ランタイムを作成して共有する](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)」を参照してください。

2.  セルフホステッド統合ランタイムがインストールされている仮想マシンに [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) がインストールされていることを確認します。

3.  セルフホステッド統合ランタイム コンピューターに \"Visual C++ 再頒布可能パッケージ 2012 Update 4\" がインストールされていることを確認します。 まだインストールしていない場合は、[こちら](https://www.microsoft.com/download/details.aspx?id=30679)からダウンロードしてください。

4.  セルフホステッド統合ランタイムが実行されている仮想マシンで Teradata の JDBC Driver を手動でダウンロードする必要があります。
    実行可能 JAR ファイルは、Teradata の [Web サイト](https://downloads.teradata.com/)からダウンロードできます。

    > [!Note]
    > このドライバーは、VM 内のすべてのアカウントからアクセスできる必要があります。 これをユーザー アカウントにインストールしないようにしてください。

5.  サポートされている Teradata データベースのバージョンは 12.x から 16.x です。 スキャン対象の Teradata ソースに対する読み取りアクセス権があることを確認します。

## <a name="setting-up-authentication-for-a-scan"></a>スキャンの認証の設定

Teradata ソースでサポートされている認証は **基本認証** のみです。

## <a name="register-a-teradata-source"></a>Teradata ソースを登録する

新しい Teradata ソースをデータ カタログに登録するには、次の手順を実行します。

1.  ご自分の Purview アカウントに移動します。
2.  左側のナビゲーションで **[ソース]** を選択します。
3.  **[登録]** を選択します
4.  [Register sources]\(ソースの登録\) で、 **[Teradata]** を選択します **[続行]** を選択します。

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="Teradata オプションの登録" border="true":::

**[Register sources (Teradata)]\(ソースの登録 (Teradata)\)** 画面で、次の手順を実行します。

1.  データ ソースがカタログに表示される際の **名前** を入力します。

2.  Teradata ソースに接続するための **ホスト** 名を入力します。 サーバーの IP アドレスまたは完全修飾接続文字列を指定することもできます。

3.  コレクションを選択するか、新しいものを作成します (省略可能)

4.  データ ソースの登録を終了します。

    :::image type="content" source="media/register-scan-teradata-source/register-sources-2.png" alt-text="Teradata の登録" border="true":::

## <a name="creating-and-running-a-scan"></a>スキャンを作成し、実行する

新しいスキャンを作成して実行するには、次の操作を行います。

1.  管理センターで、 **[統合ランタイム]** をクリックします。 セルフホステッド統合ランタイムが設定されていることを確認してください。 設定されていない場合は、[こちら](https://docs.microsoft.com/azure/purview/manage-integration-runtimes)に記載されている手順を使用して、セルフホステッド統合ランタイムを設定します

2.  **[ソース]** に移動します

3.  登録されている Teradata ソースを選択します。

4.  **[+ 新しいスキャン]** を選択します

5.  次の詳細を指定します。

    a.  **[名前]** : スキャンの名前

    b.  **[Connect via integration runtime]\(統合ランタイム経由で接続\)** : 構成済みのセルフホステッド統合ランタイムを選択します。

    c.  **[資格情報]** : 対象のデータ ソースに接続するための資格情報を選択します。 次のことを確認します。

    -   資格情報を作成するときに [基本認証] を選択します。
    -   ユーザー名入力フィールドで、データベース サーバーに接続するためのユーザー名を指定します。
    -   秘密鍵にデータベース サーバーのパスワードを格納します。

        資格情報の詳細については、[こちら](https://docs.microsoft.com/azure/purview/manage-credentials)のリンクを参照してください。

6.  **[スキーマ]** : インポートするスキーマのサブセットをセミコロン区切りのリストとして指定します。 例: schema1;schema2。 リストが空の場合は、すべてのユーザー スキーマがインポートされます。 既定では、すべてのシステム スキーマ (SysAdmin など) とオブジェクトが無視されます。 リストが空の場合は、使用可能なすべてのスキーマがインポートされます。

    SQL LIKE 式の構文を使用したスキーマ名のパターンとして、% の使用も許容されます (例: A%; %B; %C%; D)
    - A で始まる、または    
    - B で終わる、または    
    - C を含む、または    
    - D と等しい

    NOT および特殊文字の使用は許容されません

7.  **[Driver location]\(ドライバーの場所\)** : セルフホステッド統合ランタイムが実行されている VM 内の JDBC ドライバーの場所へのパスを指定します。 これは、有効な JAR フォルダーの場所へのパスである必要があります。

8.  **[Maximum memory available]\(使用可能な最大メモリ\):** スキャン プロセスで使用される、顧客の VM で使用可能な最大メモリ (GB 単位)。 これは、スキャンする Teradata ソースのサイズによって異なります。

    > [!Note] 
    > 経験則として、テーブル 1,000 個ごとに 2 GB のメモリを用意してください

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="スキャンを設定する" border="true":::

6.  **[続行]** をクリックします。

7.  **スキャン トリガー** を選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

8.  スキャンを確認し、 **[保存および実行]** をクリックします。

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