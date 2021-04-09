---
title: チュートリアル:Azure Data Catalog でのデータ資産の登録
description: このチュートリアルでは、Azure データ カタログにデータ資産を登録する方法について説明します。
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: tutorial
ms.date: 08/01/2019
ms.openlocfilehash: de85cad8232d76706358c36806ce19a7c1df893f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104675007"
---
# <a name="tutorial-register-data-assets-in-azure-data-catalog"></a>チュートリアル:Azure Data Catalog でのデータ資産の登録

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

このチュートリアルでは、登録ツールを使用して、データベース サンプルのデータ資産をカタログに登録します。 登録は、データ ソースおよびそれに格納されている資産から、名前、型、場所などの重要な構造メタデータを抽出し、そのメタデータをカタログにコピーするプロセスです。 データ ソースとデータ資産はそのままの場所に残りますが、メタデータは、それらを簡単に検出して理解できるようにカタログで使用されます。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * データ資産の登録 
> * データ資産の検索
> * データ資産への注釈付け
> * データ資産への接続
> * データ資産の管理
> * データ資産の削除

## <a name="prerequisites"></a>前提条件

はじめに、[クイックスタート](register-data-assets-tutorial.md)を完了する必要があります。

* [Microsoft Azure](https://azure.microsoft.com/) サブスクリプション。
* 自分の [Azure Active Directory テナント](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)を用意する必要があります。

Data Catalog をセットアップするには、Azure サブスクリプションの所有者または共同所有者であることが必要です。

## <a name="register-data-assets"></a>データ資産の登録

### <a name="register-a-data-source"></a>データ ソースの登録

Azure SQL Database の[データベース サンプル](../azure-sql/database/single-database-create-quickstart.md)のデータ資産 (テーブル) を登録しますが、自分の役割に関連する使い慣れたデータを操作したい場合、サポートされている任意のデータ ソースを使用できます。 サポート対象のデータ ソースの一覧については、 [サポートされるデータ ソース](data-catalog-dsr.md)に関する記事を参照してください。

このチュートリアルで使用するデータベースの名前は *RLSTest* です。

これで、データベース サンプルのデータ資産を、Azure Data Catalog を使用して登録できます。

1. [Azure Data Catalog のホーム ページ](http://azuredatacatalog.com)に移動し、 **[データの発行]** を選択します。

   ![Azure Data Catalog--Publish Data button](media/register-data-assets-tutorial/data-catalog-publish-data.png)

2. **[アプリケーションを起動]** を選択して、お使いのコンピューターで登録ツールをダウンロード、インストール、実行します。

   ![Azure Data Catalog--Launch button](media/register-data-assets-tutorial/data-catalog-launch-application.png)

3. **[ようこそ]** ページで、 **[サインイン]** を選択し、自分の資格情報を入力します。

    ![Azure Data Catalog--Welcome page](media/register-data-assets-tutorial/data-catalog-welcome-dialog.png)

4. **[Microsoft Azure Data Catalog]** ページで、 **[SQL Server]** 、 **[次へ]** の順に選択します。

    ![Azure Data Catalog--data sources](media/register-data-assets-tutorial/data-catalog-data-sources.png)

5. Azure SQL Database のデータベース サンプルの SQL Server 接続プロパティを入力し、 **[接続]** を選択します。

   ![Azure Data Catalog--SQL Server connection settings](media/register-data-assets-tutorial/data-catalog-sql-server-connection.png)

6. データ資産のメタデータを登録します。 この例では、サンプル名前空間の **Product** オブジェクトを登録します。

   1. **[サーバー階層]** ツリーで、データベース サンプルを展開し、 **[SalesLT]** を選択します。

   2. Ctrl キーを押しながら、 **[Product]** 、 **[ProductCategory]** 、 **[ProductDescription]** 、 **[ProductModel]** を選択します。

   3. **選択項目の移動用の矢印** ( **>** ) を選択します。 この操作により、選択したすべてのオブジェクトが **[登録されるオブジェクト]** の一覧に移動されます。

      ![Azure Data Catalog tutorial--browse and select objects](media/register-data-assets-tutorial/data-catalog-server-hierarchy.png)

   4. **[プレビューを含める]** チェック ボックスをオンにして、データのスナップショット プレビューを含めます。 スナップショットには、各テーブルから最大 20 個のレコードが含まれており、これはカタログにコピーされます。

   5. **[データ プロファイルを含める]** チェック ボックスをオンにして、データ プロファイルのオブジェクト統計情報のスナップショットを含めます (列の最小値、最大値、平均値、行数など)。

   6. **[タグの追加]** フィールドに、「**sales, product, azure sql**」と入力します。 この操作により、これらのデータ資産に検索タグが追加されます。 タグは、ユーザーが登録されているデータ ソースを見つけるために役立つ優れた方法です。

   7. このデータの **エキスパート** の名前を指定します (オプション)。

      ![Azure Data Catalog tutorial--objects to be registered](media/register-data-assets-tutorial/data-catalog-objects-register.png)

   8. **[登録]** を選択します。 Azure Data Catalog は、選択したオブジェクトを登録します。 この演習では、データベース サンプルから選択したオブジェクトが登録されます。 登録ツールは、データ資産からメタデータを抽出し、そのデータを Azure Data Catalog サービスにコピーします。 データは現在存在する場所に残ります。 データは、元のシステムの管理者とポリシーの制御下に留まります。

      ![Azure Data Catalog--registered objects](media/register-data-assets-tutorial/data-catalog-registered-objects.png)

    9. 登録されているデータ ソース オブジェクトを表示するには、 **[ポータルの表示]** を選択します。 Azure Data Catalog ポータルで、4 つのテーブルとデータベースすべてがグリッド ビューに表示されていることを確認します (検索バーがクリアであることを確認してください)。

       ![Objects in the Azure Data Catalog portal](media/register-data-assets-tutorial/data-catalog-view-portal.png)

この演習では、Azure SQL Database のデータベース サンプルのオブジェクトを登録して、組織全体のユーザーがそれらを簡単に発見できるようにしました。

次の演習では、登録されているデータ資産を検出する方法を学習します。

## <a name="discover-data-assets"></a>データ資産の検出

Azure Data Catalog での検出では、検索とフィルター処理という 2 つの主要なメカニズムを使用します。

検索は、直感的かつ強力な検出方法です。 既定では、検索語句はカタログ内の任意のプロパティと照合されます (ユーザーが指定した注釈を含む)。

フィルター処理は、検索を補完するものです。 エキスパート、データ ソースの種類、オブジェクトの種類、タグなどの特定の特性を選択することで、一致するデータ資産を表示することや、検索結果を一致する資産に制限することができます。

検索とフィルター処理を組み合わせて使用することで、Azure Data Catalog に登録されたデータ ソースをすばやく移動できます。

この演習では、Azure Data Catalog ポータルを使用して、前の演習で登録したデータ資産を検出します。 検索構文の詳細については、「 [Data Catalog Search syntax reference (Data Catalog の検索構文のリファレンス)](/rest/api/datacatalog/#search-syntax-reference) 」を参照してください。

カタログ内のデータ資産の検出方法について、いくつかの例を以下に示します。  

### <a name="discover-data-assets-with-basic-search"></a>基本的な検索を使用したデータ資産の検出

基本的な検索では、1 つ以上の検索語句を使用してカタログを検索できます。 任意のプロパティで、指定した 1 つまたは複数の語句と一致する任意の資産が返されます。

1. Azure Data Catalog ポータルの **[ホーム]** を選択します。 Web ブラウザーを閉じた場合は、[Azure Data Catalog ホーム ページ](https://www.azuredatacatalog.com)に移動します。

2. 検索ボックスに「 `product` 」と入力し、 **Enter** キーを押します。

   ![Azure Data Catalog--basic text search](media/register-data-assets-tutorial/data-catalog-basic-text-search.png)

3. 4 つのテーブルすべてとデータベースが結果に表示されることを確認します。 以下の画像に示すように、ツール バーのボタンを選択することで、**グリッド ビュー** と **リスト ビュー** を切り替えることができます。 **[ハイライト]** オプションが **オン** になっているため、検索結果内で検索キーワードが強調表示されています。 検索結果で、 **ページごとの結果** の数を指定することもできます。

   ![Azure Data Catalog--basic text search results](media/register-data-assets-tutorial/data-catalog-basic-text-search-results.png)

   左側に **[検索]** パネル、右側に **[プロパティ]** パネルが表示されます。 **[検索]** パネルでは、検索条件を変更したり、結果をフィルター処理したりできます。 **[プロパティ]** パネルには、グリッドまたはリストで選択したオブジェクトのプロパティが表示されます。

4. 検索結果にある **[Product]** を選択します。 **[プレビュー]** 、 **[列]** 、 **[データ プロファイル]** 、 **[ドキュメント]** の各タブを選択するか、矢印を選択して、下部にあるウィンドウを展開します。  

   ![Azure Data Catalog--bottom pane](media/register-data-assets-tutorial/data-catalog-data-asset-preview.png)

   **[プレビュー]** タブには、**Product** テーブルのデータのプレビューが表示されます。

5. **[列]** タブを選択すると、データ資産の列の詳細 (**名前**、**データ型** など) がわかります。

6. **[データ プロファイル]** タブを選択すると、データ資産のデータのプロファイル (行数、データ サイズ、列の最小値など) が表示されます。

### <a name="discover-data-assets-with-property-scoping"></a>プロパティ スコープを使用したデータ資産の検出

プロパティ スコープを使用すると、指定したプロパティと検索語句が一致するデータ資産を検出できます。

1. **[フィルター]** で、 **[オブジェクトの種類]** の **[テーブル]** フィルターをオフにします。  

2. 検索ボックスに「 `tags:product` 」と入力し、 **Enter** キーを押します。 データ カタログの検索に使用できるすべてのプロパティについては、「 [Data Catalog Search syntax reference (Data Catalog の検索構文のリファレンス)](/rest/api/datacatalog/#search-syntax-reference) 」を参照してください。

3. テーブルとデータベースが結果に表示されることを確認します。  

   ![Data Catalog--property scoping search results](media/register-data-assets-tutorial/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>検索の保存

1. **[検索]** ウィンドウの **[現在の検索]** セクションで、検索の名前を入力して **[保存]** を選択します。

   ![Azure Data Catalog--save search](media/register-data-assets-tutorial/data-catalog-save-search.png)

2. 保存した検索条件が **[保存された検索条件]** の下に表示されていることを確認します。

3. 保存した検索条件に対して実行できるアクション ( **[名前の変更]** 、 **[削除]** 、 **[既定値として保存]** ) のうち、いずれかを選択します。

### <a name="grouping-with-parentheses"></a>かっこを使用したグループ化

かっこを使用したグループ化によって、クエリの一部をグループ化して論理的に分離できます。特にブール演算子と組み合わせて使用します。

1. 検索ボックスに「 `name:product AND (tags:product AND objectType:table)` 」と入力し、 **Enter** キーを押します。

2. 検索結果に **Product** テーブルのみが表示されていることを確認します。

   ![Azure Data Catalog--grouping search](media/register-data-assets-tutorial/data-catalog-grouping-search.png)

### <a name="comparison-operators"></a>比較演算子

比較演算子では、数値データ型および日付データ型を持つプロパティに対して、等値演算子以外の比較演算子を使用できます。

1. 検索ボックスに「 `lastRegisteredTime:>"06/09/2016"`」と入力します。

2. **[オブジェクトの種類]** で **[テーブル]** フィルターをオフにします。

3. **Enter** キーを押します。

4. 登録した **Product**、**ProductCategory**、**ProductDescription** の各テーブルと SQL データベースが検索結果に表示されていることを確認します。

   ![Azure Data Catalog--comparison search results](media/register-data-assets-tutorial/data-catalog-comparison-operator-results.png)

データ資産の発見の詳細については、[データ資産を発見する方法](data-catalog-how-to-discover.md)に関する記事を参照してください。 検索構文の詳細については、[Data Catalog の検索構文のリファレンス](/rest/api/datacatalog/#search-syntax-reference)を参照してください。

## <a name="annotate-data-assets"></a>データ資産への注釈付け

この演習では、Azure Data Catalog ポータルを使用して、カタログ内の既存のデータ資産に注釈を付けます (説明、タグ、エキスパートなどの情報を追加)。 注釈は、登録中にデータ ソースから抽出された構造メタデータを補完します。 注釈を利用すると、データ資産の発見と理解がはるかに容易になります。

この演習では、1 つのデータ資産 (ProductPhoto) に注釈を設定します。 ProductPhoto データ資産にフレンドリ名と説明を追加します。  

1. [Azure Data Catalog ホーム ページ](https://www.azuredatacatalog.com)に移動し、「`tags:product`」を検索して、登録したデータ資産を見つけます。

2. 検索結果にある **[ProductModel]** を選択します。  

3. **[フレンドリ名]** に「**Product images**」と入力し、 **[説明]** に「**Product photos for marketing materials**」と入力します。

    ![Azure Data Catalog--ProductPhoto description](media/register-data-assets-tutorial/data-catalog-productmodel-description.png)

    **[説明]** は、他のユーザーがデータ資産を検出したり、選択したデータ資産の使用理由や使用方法を理解したりするのに役立ちます。 さらに、タグとビュー列を追加することもできます。 カタログに追加した説明用のメタデータを使用して、データ ソースの検索とフィルター処理を行えます。

このページでは、次の手順も実行できます。

* データ資産のエキスパートを追加する。 **[エキスパート]** 領域の **[追加]** を選択します。

* データセット レベルでタグを追加する。 **[タグ]** 領域の **[追加]** を選択します。 タグは、ユーザー タグにも用語集タグにもすることができます。 Data Catalog Standard Edition には、カタログ管理者が主なビジネスの分類を定義するために利用できるビジネス用語集が含まれています。 そのため、Catalog ユーザーは用語集の用語を使ってデータ資産に注釈を付けることができます。 詳細については、「 [管理タグ付け用のビジネス用語集を設定する方法](data-catalog-how-to-business-glossary.md)

* 列レベルでタグを追加する。 注釈を付けたい列について、 **[タグ]** の **[追加]** を選択します。

* 列レベルで説明を追加する。 列の **[説明]** を入力します。 また、データ ソースから抽出された説明メタデータを表示することもできます。

* データ資産へのアクセスを要求する方法をユーザーに示す **[アクセスの要求]** 情報を追加する。
  
* **[ドキュメント]** タブを選択し、データ資産に関するドキュメントを指定する。 Azure Data Catalog での文書化により、データ カタログをコンテンツのリポジトリとして使用して、既にあるデータ資産を網羅した概要を作成できます。
  
複数のデータ資産に対して注釈を追加することもできます。 たとえば、登録したすべてのデータ資産を選択し、それらにエキスパートを指定することができます。

![Azure Data Catalog--annotate multiple data assets](media/register-data-assets-tutorial/data-catalog-multi-select-annotate.png)

Azure Data Catalog では、注釈に対するクラウドソーシング手法をサポートしています。 どの Data Catalog ユーザーでも、タグ (ユーザーまたは用語集)、説明、その他のメタデータを追加できます。 ユーザーはそうすることで、データ資産とその使用に関するパースペクティブを追加し、そのパースペクティブを他のユーザーと共有します。

データ資産への注釈付けの詳細については、 [データ資産に注釈を付ける方法](data-catalog-how-to-annotate.md) に関する記事を参照してください。

## <a name="connect-to-data-assets"></a>データ資産への接続

この演習では、統合クライアント ツール (Excel) と非統合ツール (SQL Server Management Studio) で接続情報を使ってデータ資産を開きます。

> [!NOTE]
> Azure Data Catalog では、実際のデータ ソースへのアクセス権が付与されるのではなく、単にそのデータ ソースの検出や把握が簡単になるだけであるという点に注意してください。 データ ソースへの接続時には、選択したクライアント アプリケーションにより Windows 資格情報が使用されるか、必要に応じて資格情報の入力が求められます。 事前にデータ ソースへのアクセス権が付与されていない場合、データ ソースに接続する前に、アクセス権の付与を受ける必要があります。

### <a name="connect-to-a-data-asset-from-excel"></a>Excel からのデータ資産への接続

1. 検索結果から **[Product]** を選択します。 ツール バーの **[開く]** を選択し、 **[Excel]** を選択します。

   ![Azure Data Catalog--connect to data asset](media/register-data-assets-tutorial/data-catalog-connect1.png)

2. ダウンロードのポップアップ ウィンドウで、 **[ファイルを開く]** を選択します。 この操作は、ブラウザーによって異なる場合があります。

3. **[Microsoft Excel Security Notice]\(Microsoft Excel のセキュリティに関する通知\)** ウィンドウで、 **[有効にする]** を選択します。

   ![Azure Data Catalog--Excel security popup](media/register-data-assets-tutorial/data-catalog-excel-security-popup.png)

4. **[データのインポート]** ダイアログ ボックスの既定値はそのままにして、 **[OK]** を選択します。

   ![Azure Data Catalog--Excel import data](media/register-data-assets-tutorial/data-catalog-excel-import-data.png)

5. データ ソースを Excel で確認します。

   ![Azure Data Catalog--product table in Excel](media/register-data-assets-tutorial/data-catalog-connect2.png)

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

この演習では、Azure Data Catalog を使用して検出されたデータ資産に接続しました。 Azure Data Catalog ポータルでは、 **[開く]** メニューに統合されたクライアント アプリケーションを使用して、データ資産に直接接続できます。 また、資産メタデータに含まれる接続場所の情報を使用して、任意のアプリケーションに接続することもできます。 たとえば、SQL Server Management Studio を使用すると、Azure SQL Database に接続し、このチュートリアルで登録されたデータ資産のデータにアクセスできます。

1. **SQL Server Management Studio** を開きます。

2. **[サーバーへの接続]** ダイアログ ボックスで、Azure Data Catalog ポータルの **[プロパティ]** ウィンドウに表示されているサーバー名を入力します。

3. 適切な認証と資格情報を使用してデータ資産にアクセスします。 アクセス権がない場合は、 **[アクセスの要求]** フィールドの情報を使用して取得します。

   ![Azure Data Catalog--request access](media/register-data-assets-tutorial/data-catalog-request-access.png)

**[接続文字列の表示]** を選択して、ADO.NET、ODBC、OLEDB の接続文字列を表示し、アプリケーションで使用するためにクリップボードにコピーします。

## <a name="manage-data-assets"></a>データ資産の管理

この手順では、データ資産のセキュリティを設定する方法を説明します。 Data Catalog のユーザーにデータ自体へのアクセス権は与えられません。 データ ソースの所有者がデータへのアクセスを管理します。

Data Catalog を通じてデータ ソースを検出し、カタログに登録されたソースに関連したメタデータを閲覧できます。 しかし、場合によっては、データ ソースの可視性を特定のユーザーまたは特定のグループのメンバーのみに限定する必要があります。 そのような場合は、Data Catalog を使用して登録済みデータ資産の所有権を取得し、所有する資産の可視性を制御することができます。

> [!NOTE]
> この演習で説明している管理機能は、Azure Data Catalog の Standard Edition でのみ使用できます。Free Edition では使用できません。
> Azure Data Catalog では、データ資産の所有権の取得、共同所有者のデータ資産への追加、データ資産の可視性の設定ができます。

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>データ資産の所有権の取得と可視性の制限

1. [Azure Data Catalog ホーム ページ](https://www.azuredatacatalog.com)に移動します。 **検索** ボックスに「`tags:cycles`」と入力し、**Enter** キーを押します。

2. 結果の一覧で項目を選択し、ツール バーの **[所有権の取得]** を選択します。

3. **[プロパティ]** パネルの **[管理]** セクションで、 **[所有権の取得]** を選択します。

    ![Azure Data Catalog--take ownership](media/register-data-assets-tutorial/data-catalog-take-ownership.png)

4. 可視性を制限するには、 **[表示]** セクションで **[所有者とこれらのユーザー]** を選択し、 **[追加]** を選択します。 テキスト ボックスにユーザーのメール アドレスを入力し、 **Enter** キーを押します。

    ![Azure Data Catalog--restrict access](media/register-data-assets-tutorial/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>データ資産の削除

この演習では、Azure Data Catalog ポータルを使用して、登録されているデータ資産からプレビュー データを削除し、カタログからデータ資産を削除します。

Azure Data Catalog では、個々の資産を削除することも、複数の資産を削除することもできます。

1. [Azure Data Catalog ホーム ページ](https://www.azuredatacatalog.com)に移動します。

2. **検索** ボックスに「`tags:cycles`」と入力し、**Enter** キーを押します。

3. 次の画像のように、結果の一覧でアイテムを選択し、ツール バーの **[削除]** を選択します。

   ![Azure Data Catalog--delete grid item](media/register-data-assets-tutorial/data-catalog-delete-grid-item.png)

   リスト ビューを使用している場合は、次の画像に示すように、チェック ボックスがアイテムの左側にあります。

   ![Azure Data Catalog--delete list item](media/register-data-assets-tutorial/data-catalog-delete-list-item.png)

   次の画像に示すように、複数のデータ資産を選択して削除することもできます。

   ![Azure Data Catalog--delete multiple data assets](media/register-data-assets-tutorial/data-catalog-delete-assets.png)

> [!NOTE]
> カタログの既定の動作は、すべてのユーザーが任意のデータ ソースを登録でき、すべてのユーザーが登録されている任意のデータ資産を削除できることです。 Azure Data Catalog の Standard Edition に含まれる管理機能には、資産の所有権の取得、資産を検出できるユーザーの制限、および資産を削除できるユーザーの制限を行うための追加のオプションがあります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

「[データ資産の削除](#remove-data-assets)」の手順に従って、このチュートリアルの実行中に使用したすべての資産をクリーンアップします。

## <a name="summary"></a>まとめ

このチュートリアルでは、Azure Data Catalog の重要な機能 (エンタープライズ データ資産の登録、注釈付け、検出、管理など) について学習しました。 これでチュートリアルは完了したので、使用を開始できます。 自分やチームが依存するデータ ソースを登録し、カタログを使用する同僚を招待することによって、今すぐ始めることができます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [サポートされるデータ ソース](data-catalog-dsr.md)
