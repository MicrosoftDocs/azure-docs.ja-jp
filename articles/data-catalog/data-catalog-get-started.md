---
title: "Data Catalog を使ってみる | Microsoft Docs"
description: "Azure Data Catalog のシナリオと機能を紹介するエンド ツー エンドのチュートリアルです。"
documentationcenter: 
services: data-catalog
author: steelanddata
manager: jhubbard
editor: 
tags: 
ms.assetid: 03332872-8d84-44a0-8a78-04fd30e14b18
ms.service: data-catalog
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/19/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 2b128e2771ea89d1c0d6b8d52357d1bcc993115a
ms.openlocfilehash: a9629d0a4bfc44f5d035a29f8e97d76922cc0060
ms.lasthandoff: 02/16/2017


---
# <a name="get-started-with-azure-data-catalog"></a>Azure Data Catalog の概要
Azure Data Catalog は、完全に管理されたクラウド サービスで、エンタープライズ データ資産の登録システムと検出システムとして機能します。 詳細については、「 [Azure Data Catalog とは何ですか](data-catalog-what-is-data-catalog.md)」を参照してください。

このチュートリアルでは、Azure Data Catalog の概要について説明します。 このチュートリアルでは、以下の手順を実施します。

| 手順 | 説明 |
|:--- |:--- |
| [データ カタログのプロビジョニング](#provision-data-catalog) |この手順では、Azure Data Catalog をプロビジョニングまたはセットアップします。 この手順は、これまでにカタログを設定したことがない場合のみ実行します。 Azure アカウントに関連付けられているサブスクリプションが複数ある場合でも、持つことができるデータ カタログは組織 (Microsoft Azure Active Directory ドメイン) ごとに&1; つだけです。 |
| [データ資産の登録](#register-data-assets) |この手順では、AdventureWorks2014 サンプル データベースのデータ資産をデータ カタログに登録します。 登録とは、データ ソースから重要な構造メタデータ (名前、型、場所など) を抽出し、そのメタデータをカタログにコピーするプロセスです。 データ ソースとデータ資産はそのままの場所に残りますが、メタデータは、それらを簡単に検出して理解できるようにカタログで使用されます。 |
| [データ資産の検出](#discover-data-assets) |この手順では、Azure Data Catalog ポータルを使用して、前の手順で登録したデータ資産を検出します。 データ ソースが Azure Data Catalog に登録されると、そのメタデータにはサービスによってインデックスが付けられます。これにより、ユーザーは必要なデータを簡単に検索できるようになります。 |
| [データ資産への注釈付け](#annotate-data-assets) |この手順では、データ資産に注釈 (説明、タグ、ドキュメント、エキスパートなどの情報) を指定します。 この情報は、データ ソースから抽出されたメタデータを補足し、より多くの人がデータ ソースについて理解できるようにします。 |
| [データ資産への接続](#connect-to-data-assets) |この手順では、統合クライアント ツール (Excel や SQL Server Data Tools など) や非統合ツール (SQL Server Management Studio) でデータ資産を開きます。 |
| [データ資産の管理](#manage-data-assets) |この手順では、データ資産のセキュリティを設定します。 Data Catalog のユーザーにデータ自体へのアクセス権は与えられません。 データ ソースの所有者がデータへのアクセスを管理します。 <br/><br/> Data Catalog では、データ ソースを検出して、カタログに登録されたソースに関連する **メタデータ** を閲覧できます。 しかし、場合によっては、データ ソースの可視性を特定のユーザーまたは特定のグループのメンバーに限定する必要があります。 そのような場合は、Data Catalog を使用してカタログ内の登録済みデータ資産の所有権を取得し、所有する資産の可視性を制御することができます。 |
| [データ資産の削除](#remove-data-assets) |この手順では、データ カタログからデータ資産を削除する方法について説明します。 |

## <a name="tutorial-prerequisites"></a>チュートリアルの前提条件
### <a name="azure-subscription"></a>Azure サブスクリプション
Azure Data Catalog をセットアップするには、Azure サブスクリプションの所有者または共同所有者であることが必要です。

Azure サブスクリプションは、Azure Data Catalog のようなクラウド サービス リソースへのアクセスを整理するために役立ちます。 さらに、リソースの使用状況の報告、課金、および支払い方法の制御にも役立ちます。 サブスクリプションごとに異なる課金および支払いを設定することができるため、部門別、プロジェクト別、支社別などで、異なるサブスクリプションや異なるプランを利用することができます。 すべてのクラウド サービスがサブスクリプションに属しているため、Azure Data Catalog をセットアップする前に、サブスクリプションが必要です。 詳細については、 [アカウント、サブスクリプション、管理ロールの管理](../active-directory/active-directory-how-subscriptions-associated-directory.md)に関するページを参照してください。

サブスクリプションがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/) に関するページを参照してください。

### <a name="azure-active-directory"></a>Azure Active Directory
Azure Data Catalog をセットアップするには、Azure Active Directory (Azure AD) ユーザー アカウントでサインインする必要があります。 Azure サブスクリプションの所有者または共同所有者であることが必要です。  

Azure AD は、企業がクラウドとオンプレミス環境の両方で ID とアクセスを簡単に管理できるサービスです。 1 つの職場または学校アカウントを使用して、任意のクラウドまたはオンプレミス Web アプリケーションにサインインできます。 Azure Data Catalog は、Azure AD を使用してサインインを認証します。 詳細については、「 [Azure Active Directory とは](../active-directory/active-directory-whatis.md)」を参照してください。

### <a name="azure-active-directory-policy-configuration"></a>Azure Active Directory ポリシーの構成
Azure Data Catalog ポータルにはサインインできるものの、データ ソース登録ツールにサインインしようとするとエラー メッセージが表示され、サインインできないという状況に直面することがあります。 このようなエラーは、企業ネットワーク上にいる場合や、企業ネットワークの外部から接続している場合に発生する可能性があります。

登録ツールでは、" *フォーム認証* " を使用して、Azure Active Directory に対するユーザーのサインインを検証します。 サインインを成功させるためには、Azure Active Directory 管理者が、" *グローバル認証ポリシー*" でフォーム認証を有効にする必要があります。

グローバル認証ポリシーでは、次の画像に示すように、イントラネット接続とエクストラネット接続で個別に認証を有効にすることができます。 サインイン エラーは、接続元のネットワークで、フォーム認証が有効にされていない場合に発生する可能性があります。

 ![Azure Active Directory global authentication policy](./media/data-catalog-prerequisites/global-auth-policy.png)

詳細については、「 [認証ポリシーの構成](https://technet.microsoft.com/library/dn486781.aspx)」を参照してください。

## <a name="provision-data-catalog"></a>データ カタログのプロビジョニング
組織 (Azure Active Directory ドメイン) ごとにプロビジョニングできるデータ カタログは&1; つだけです。 そのため、この Azure Active Directory ドメインに属している、Azure サブスクリプションの所有者または共同所有者が既にカタログを作成している場合、複数の Azure サブスクリプションを所有していても、もう一度カタログを作成することはできません。 データ カタログが Azure Active Directory ドメイン内のユーザーによって作成されているかどうかをテストするには、 [Azure Data Catalog ホーム ページ](http://azuredatacatalog.com) に移動し、カタログが表示されるかどうかを確認してください。 カタログが既に作成されている場合は、以下の手順をスキップし、次のセクションに進んでください。    

1. [Data Catalog サービス ページ](https://azure.microsoft.com/services/data-catalog) に移動し、 **[はじめる]**をクリックします。
   
    ![Azure Data Catalog--marketing landing page](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)
2. Azure サブスクリプションの所有者または共同所有者であるユーザー アカウントを使用してサインインします。 サインイン後に、次のページが表示されます。
   
    ![Azure Data Catalog--provision data catalog](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)
3. データ カタログの**名前**、使用する**サブスクリプション**、カタログの**場所**を指定します。
4. **[価格設定]** を展開し、Azure Data Catalog の**エディション** (Free または Standard) を選択します。
    ![Azure Data Catalog--select edition](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)
5. **[カタログ ユーザー]** を展開し、**[追加]** をクリックしてデータ カタログのユーザーを追加します。 自身はこのグループに自動的に追加されます。
    ![Azure Data Catalog--users](media/data-catalog-get-started/data-catalog-add-catalog-user.png)
6. **[カタログの管理者]** を展開し、**[追加]** をクリックしてデータ カタログの他の管理者を追加します。 自身はこのグループに自動的に追加されます。
    ![Azure Data Catalog--administrators](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)
7. **[カタログの作成]** をクリックして、組織のデータ カタログを作成します。 データ カタログが作成されると、そのデータ カタログのホーム ページが表示されます。
    ![Azure Data Catalog--created](media/data-catalog-get-started/data-catalog-created.png)    

### <a name="find-a-data-catalog-in-the-azure-portal"></a>Azure ポータルでのデータ カタログの検索
1. Web ブラウザーの別のタブまたは別の Web ブラウザー ウィンドウで、 [Azure Portal](https://portal.azure.com) に移動し、前の手順でデータ カタログを作成する際に使用したのと同じアカウントでサインインします。
2. **[参照]** を選択し、**[Data Catalog]** をクリックします。
   
    ![Azure Data Catalog--browse Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png) 作成したデータ カタログが表示されます。
   
    ![Azure Data Catalog--view catalog in list](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)
3. 作成したカタログをクリックします。 ポータルに **[Data Catalog]** ブレードが表示されます。
   
   ![Azure Data Catalog--blade in portal ](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)
4. データ カタログのプロパティが表示され、更新することができます。 たとえば、 **[価格レベル]** をクリックし、エディションを変更します。
   
    ![Azure Data Catalog--pricing tier](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

### <a name="adventure-works-sample-database"></a>Adventure Works サンプル データベース
このチュートリアルでは、SQL Server データベース エンジンの AdventureWorks2014 サンプル データベースからデータ資産 (テーブル) を登録しますが、自分のロールに関連のある使い慣れたデータを使用したい場合は、サポートされている任意のデータ ソースも使用できます。 サポート対象のデータ ソースの一覧については、 [サポートされるデータ ソース](data-catalog-dsr.md)に関する記事を参照してください。

### <a name="install-the-adventure-works-2014-oltp-database"></a>Adventure Works 2014 OLTP データベースのインストール
Adventure Works データベースでは、製品、販売、購入を含む、架空の自転車メーカー (Adventure Works Cycles) の標準的なオンライン トランザクション処理シナリオをサポートしています。 このチュートリアルでは、製品に関する情報を Azure Data Catalog に登録します。

Adventure Works サンプル データベースのインストール方法を次に示します。

1. CodePlex で [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) をダウンロードします。
2. [SQL Server Management Studio を使用したデータベース バックアップの復元](http://msdn.microsoft.com/library/ms177429.aspx)に関する記事の手順、または以下の手順に従って、お使いのコンピューターにデータベースを復元します。
   1. SQL Server Management Studio を開き、SQL Server データベース エンジンに接続します。
   2. **[データベース]** を右クリックし、**[データベースの復元]** をクリックします。
   3. **[データベースの復元]** で、**[ソース]** の **[デバイス]** オプションを選択し、**[参照]** をクリックします。
   4. **[バックアップ デバイスの選択]** で、**[追加]** をクリックします。
   5. **AdventureWorks2014.bak** ファイルがあるフォルダーに移動して、そのファイルを選択し、**[OK]** をクリックして **[バックアップ ファイルの検索]** ダイアログ ボックスを閉じます。
   6. **[OK]** をクリックして **[バックアップ デバイスの選択]** ダイアログ ボックスを閉じます。    
   7. **[OK]** をクリックして **[データベースの復元]** ダイアログ ボックスを閉じます。

これで、Adventure Works サンプル データベースのデータ資産を、Azure Data Catalog を使用して登録することができます。

## <a name="register-data-assets"></a>データ資産の登録
この演習では、登録ツールを使用して、Adventure Works データベースのデータ資産をカタログに登録します。 登録は、データ ソースおよびそれに格納されている資産から、名前、型、場所などの重要な構造メタデータを抽出し、そのメタデータをカタログにコピーするプロセスです。 データ ソースとデータ資産はそのままの場所に残りますが、メタデータは、それらを簡単に検出して理解できるようにカタログで使用されます。

### <a name="register-a-data-source"></a>データ ソースの登録
1. [Azure Data Catalog ホーム ページ](http://azuredatacatalog.com) に移動し、 **[データの発行]**をクリックします。
   
   ![Azure Data Catalog--Publish Data button](media/data-catalog-get-started/data-catalog-publish-data.png)
2. **[アプリケーションを起動]** をクリックして、お使いのコンピューターで登録ツールをダウンロード、インストール、実行します。
   
   ![Azure Data Catalog--Launch button](media/data-catalog-get-started/data-catalog-launch-application.png)
3. **[ようこそ]** ページで、**[サインイン]** をクリックし、資格情報を入力します。     
   
    ![Azure Data Catalog--Welcome page](media/data-catalog-get-started/data-catalog-welcome-dialog.png)
4. **[Microsoft Azure Data Catalog]** ページで、**[SQL Server]** をクリックし、**[次へ]** をクリックします。
   
    ![Azure Data Catalog--data sources](media/data-catalog-get-started/data-catalog-data-sources.png)
5. **AdventureWorks2014** の SQL Server 接続のプロパティを入力し (以下の例を参照)、**[接続]** をクリックします。
   
   ![Azure Data Catalog--SQL Server connection settings](media/data-catalog-get-started/data-catalog-sql-server-connection.png)
6. データ資産のメタデータを登録します。 この例では、AdventureWorks Production 名前空間から **Production/Product** オブジェクトを登録します。
   
   1. **[サーバー階層]** のツリーで、**AdventureWorks2014** を展開し、**Production** をクリックします。
   2. Ctrl キーを押しながら、**[Product]**、**[ProductCategory]**、**[ProductDescription]**、**[ProductPhoto]** をクリックして選択します。
   3. **選択項目の移動用の矢印** (**>**) をクリックします。 この操作により、選択したすべてのオブジェクトが **[登録されるオブジェクト]** の一覧に移動されます。
      
      ![Azure Data Catalog tutorial--browse and select objects](media/data-catalog-get-started/data-catalog-server-hierarchy.png)
   4. **[プレビューを含める]** チェック ボックスをオンにして、データのスナップショット プレビューを含めます。 スナップショットには、各テーブルから最大 20 個のレコードが含まれており、これはカタログにコピーされます。
   5. **[データ プロファイルを含める]** チェック ボックスをオンにして、データ プロファイルのオブジェクト統計情報のスナップショットを含めます (列の最小値、最大値、平均値、行数など)。
   6. **[タグの追加]** フィールドに、「**adventure works, cycles**」と入力します。 この操作により、これらのデータ資産に検索タグが追加されます。 タグは、ユーザーが登録されているデータ ソースを見つけるために役立つ優れた方法です。
   7. このデータの **エキスパート** の名前を指定します (オプション)。
      
      ![Azure Data Catalog tutorial--objects to be registered](media/data-catalog-get-started/data-catalog-objects-register.png)
   8. [ **登録**] をクリックします。 Azure Data Catalog は、選択したオブジェクトを登録します。 この演習では、Adventure Works から選択したオブジェクトが登録されます。 登録ツールは、データ資産からメタデータを抽出し、そのデータを Azure Data Catalog サービスにコピーします。 データは現在存在する場所に残り、現在のシステムの管理者とポリシーの制御下に留まります。
      
      ![Azure Data Catalog--registered objects](media/data-catalog-get-started/data-catalog-registered-objects.png)
   9. 登録されているデータ ソース オブジェクトを表示するには、[ **ポータルの表示**] をクリックします。 Azure Data Catalog ポータルで、4 つのテーブルとデータベースすべてがグリッド ビューに表示されていることを確認します。
      
      ![Objects in the Azure Data Catalog portal ](media/data-catalog-get-started/data-catalog-view-portal.png)

この演習では、組織全体のユーザーがオブジェクトを簡単に検出できるように、Adventure Works サンプル データベースからオブジェクトを登録しました。 次の演習では、登録されているデータ資産を検出する方法を学習します。

## <a name="discover-data-assets"></a>データ資産の検出
Azure Data Catalog での検出では、検索とフィルター処理という&2; つの主要なメカニズムを使用します。

検索は、直感的かつ強力な検出方法です。 既定では、検索語句はカタログ内の任意のプロパティと照合されます (ユーザーが指定した注釈を含む)。

フィルター処理は、検索を補完するものです。 エキスパート、データ ソースの種類、オブジェクトの種類、タグなどの特定の特性を選択することで、一致するデータ資産を表示することや、検索結果を一致する資産に制限することができます。

検索とフィルター処理を組み合わせて使用することで、Azure Data Catalog に登録されたデータ ソースをすばやく移動し、必要なデータ資産を検出することができます。

この演習では、Azure Data Catalog ポータルを使用して、前の演習で登録したデータ資産を検出します。 検索構文の詳細については、「 [Data Catalog Search syntax reference (Data Catalog の検索構文のリファレンス)](https://msdn.microsoft.com/library/azure/mt267594.aspx) 」を参照してください。

カタログ内のデータ資産の検出方法について、いくつかの例を以下に示します。  

### <a name="discover-data-assets-with-basic-search"></a>基本的な検索を使用したデータ資産の検出
基本的な検索では、1 つ以上の検索語句を使用してカタログを検索できます。 任意のプロパティで、指定した&1; つまたは複数の語句と一致する任意の資産が返されます。

1. Azure Data Catalog ポータルの **[ホーム]** をクリックします。 Web ブラウザーを閉じた場合は、もう一度開いて [Azure Data Catalog ホーム ページ](https://www.azuredatacatalog.com)に移動します。
2. 検索ボックスに「 `cycles` 」と入力し、 **Enter**キーを押します。
   
    ![Azure Data Catalog--basic text search](media/data-catalog-get-started/data-catalog-basic-text-search.png)
3. 結果に&4; つのテーブルとデータベース (AdventureWorks2014) が表示されることを確認します。 以下の画像に示すように、ツール バーのボタンをクリックすることで、**グリッド ビュー**と**リスト ビュー**を切り替えることができます。 **[ハイライト]** オプションが**オン**になっているため、検索結果内で検索キーワードが強調表示されています。 検索結果で、 **ページごとの結果** の数を指定することもできます。
   
    ![Azure Data Catalog--basic text search results](media/data-catalog-get-started/data-catalog-basic-text-search-results.png)
   
    左側に **[検索]** パネル、右側に **[プロパティ]** パネルが表示されます。 **[検索]** パネルでは、検索条件を変更したり、結果をフィルター処理したりできます。 **[プロパティ]** パネルには、グリッドまたはリストで選択したオブジェクトのプロパティが表示されます。
4. 検索結果にある **[Product]** をクリックします。 **[プレビュー]**、**[列]**、**[データ プロファイル]**、**[ドキュメント]** の各タブをクリックするか、矢印をクリックして、下部にあるウィンドウを展開します。  
   
    ![Azure Data Catalog--bottom pane](media/data-catalog-get-started/data-catalog-data-asset-preview.png)
   
    **[プレビュー]** タブには、**Product** テーブルのデータのプレビューが表示されます。  
5. **[列]** タブをクリックすると、データ資産の列の詳細 (**名前**、**データ型**など) がわかります。
6. **[データ プロファイル]** タブをクリックすると、データ資産のデータのプロファイル (行数、データ サイズ、列の最小値など) が表示されます。
7. 左側にある **[フィルター]** を使用して、結果をフィルター処理します。 たとえば、**[オブジェクトの種類]** の **[テーブル]** をクリックすると、4 つのテーブルのみが表示され、データベースは表示されません。
   
    ![Azure Data Catalog--filter search results](media/data-catalog-get-started/data-catalog-filter-search-results.png)

### <a name="discover-data-assets-with-property-scoping"></a>プロパティ スコープを使用したデータ資産の検出
プロパティ スコープを使用すると、指定したプロパティと検索語句が一致するデータ資産を検出できます。

1. **[フィルター]** で、**[オブジェクトの種類]** の **[テーブル]** フィルターをオフにします。  
2. 検索ボックスに「 `tags:cycles` 」と入力し、 **Enter**キーを押します。 データ カタログの検索に使用できるすべてのプロパティについては、「 [Data Catalog Search syntax reference (Data Catalog の検索構文のリファレンス)](https://msdn.microsoft.com/library/azure/mt267594.aspx) 」を参照してください。
3. 結果に&4; つのテーブルとデータベース (AdventureWorks2014) が表示されることを確認します。  
   
    ![Data Catalog--property scoping search results](media/data-catalog-get-started/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>検索の保存
1. **[検索]** ウィンドウの **[現在の検索]** セクションで、検索条件の名前を入力して **[保存]** をクリックします。
   
    ![Azure Data Catalog--save search](media/data-catalog-get-started/data-catalog-save-search.png)
2. 保存した検索条件が **[保存された検索条件]**の下に表示されていることを確認します。
   
    ![Azure Data Catalog--saved searches](media/data-catalog-get-started/data-catalog-saved-search.png)
3. 保存した検索条件に対して実行できるアクション (**[名前の変更]**、**[削除]**、**[既定値として保存]**) のうち、いずれかを選択します。
   
    ![Azure Data Catalog--saved search options](media/data-catalog-get-started/data-catalog-saved-search-options.png)

### <a name="boolean-operators"></a>ブール演算子
ブール演算子を使用して、検索範囲を広げることや絞り込むことができます。

1. 検索ボックスに「 `tags:cycles AND objectType:table`」と入力し、 **Enter**キーを押します。
2. 結果にテーブルのみが表示され、データベースが表示されていないことを確認します。  
   
    ![Azure Data Catalog--Boolean operator in search](media/data-catalog-get-started/data-catalog-search-boolean-operator.png)

### <a name="grouping-with-parentheses"></a>かっこを使用したグループ化
かっこを使用したグループ化によって、クエリの一部をグループ化して論理的に分離できます。特にブール演算子と組み合わせて使用します。

1. 検索ボックスに「 `name:product AND (tags:cycles AND objectType:table)` 」と入力し、 **Enter**キーを押します。
2. 検索結果に **Product** テーブルのみが表示されていることを確認します。
   
    ![Azure Data Catalog--grouping search](media/data-catalog-get-started/data-catalog-grouping-search.png)   

### <a name="comparison-operators"></a>比較演算子
比較演算子では、数値データ型および日付データ型を持つプロパティに対して、等値演算子以外の比較演算子を使用できます。

1. 検索ボックスに「 `lastRegisteredTime:>"06/09/2016"`」と入力します。
2. **[オブジェクトの種類]** で **[テーブル]** フィルターをオフにします。
3. **Enter**キーを押します。
4. 検索結果に、登録した **Product**、**ProductCategory**、**ProductDescription**、**ProductPhoto** の各テーブルと AdventureWorks2014 データベースが表示されていることを確認します。
   
    ![Azure Data Catalog--comparison search results](media/data-catalog-get-started/data-catalog-comparison-operator-results.png)

データ資産の検出の詳細については「[データ ソースの探索方法](data-catalog-how-to-discover.md)」、検索構文については「[Data Catalog Search syntax reference (Data Catalog の検索構文のリファレンス)](https://msdn.microsoft.com/library/azure/mt267594.aspx)」を参照してください。

## <a name="annotate-data-assets"></a>データ資産への注釈付け
この演習では、Azure Data Catalog ポータルを使用して、前の手順でカタログに登録したデータ資産に注釈を設定 (説明、タグ、エキスパートなどの情報を追加) します。 注釈は、登録時にデータ ソースから抽出された構造型メタデータを補完、拡張して、データ資産をさらに検出および理解しやすくします。

この演習では、1 つのデータ資産 (ProductPhoto) に注釈を設定します。 ProductPhoto データ資産にフレンドリ名と説明を追加します。  

1. [Azure Data Catalog ホーム ページ](https://www.azuredatacatalog.com)に移動し、「`tags:cycles`」を検索して、登録したデータ資産を検出します。  
2. 検索結果で **ProductPhoto** をクリックします。  
3. **[フレンドリ名]** に「**Product images**」と入力し、**[説明]** に「**Product photos for marketing materials**」と入力します。
   
    ![Azure Data Catalog--ProductPhoto description](media/data-catalog-get-started/data-catalog-productphoto-description.png)
   
    **[説明]** は、他のユーザーがデータ資産を検出したり、選択したデータ資産の使用理由や使用方法を理解したりするのに役立ちます。 さらに、タグとビュー列を追加することもできます。 これで、カタログに追加した説明用のメタデータを使用して、データ資産を検出するために検索やフィルター処理を試してみることができます。

このページでは、次のことも実行できます。

* データ資産のエキスパートを追加する。 **[追加]** in the **[追加]** をクリックします。
* データセット レベルでタグを追加する。 **[追加]** in the **[追加]** をクリックします。 タグは、ユーザー タグにも用語集タグにもすることができます。 Data Catalog Standard Edition には、カタログ管理者が主なビジネスの分類を定義するために利用できるビジネス用語集が含まれています。 そのため、Catalog ユーザーは用語集の用語を使ってデータ資産に注釈を付けることができます。 詳細については、「 [管理タグ付け用のビジネス用語集を設定する方法](data-catalog-how-to-business-glossary.md)
* 列レベルでタグを追加する。 **[追加]** under **[追加]** をクリックします。
* 列レベルで説明を追加する。 列の **[説明]** を入力します。 また、データ ソースから抽出された説明メタデータを表示することもできます。
* データ資産へのアクセスを要求する方法をユーザーに示す **[アクセスの要求]** 情報を追加する。
  
    ![Azure Data Catalog--add tags, descriptions](media/data-catalog-get-started/data-catalog-add-tags-experts-descriptions.png)
* **[ドキュメント]** タブを選択し、データ資産に関するドキュメントを指定する。 Azure Data Catalog での文書化により、データ カタログをコンテンツのリポジトリとして使用して、既にあるデータ資産を網羅した概要を作成できます。
  
    ![Azure Data Catalog--Documentation tab](media/data-catalog-get-started/data-catalog-documentation.png)

複数のデータ資産に対して注釈を追加することもできます。 たとえば、登録したすべてのデータ資産を選択し、それらにエキスパートを指定することができます。

![Azure Data Catalog--annotate multiple data assets](media/data-catalog-get-started/data-catalog-multi-select-annotate.png)

Azure Data Catalog では、注釈に対するクラウドソーシング手法をサポートしています。 すべての Data Catalog ユーザーがタグ (ユーザーまたは用語集)、説明などのメタデータを追加できるため、データ資産とその使用に関して特定の見方を持つ任意のユーザーは、その見方を捉えて、他のユーザーが使用できるようにすることも可能です。

データ資産への注釈付けの詳細については、 [データ資産に注釈を付ける方法](data-catalog-how-to-annotate.md) に関する記事を参照してください。

## <a name="connect-to-data-assets"></a>データ資産への接続
この演習では、統合クライアント ツール (Excel) と非統合ツール (SQL Server Management Studio) で接続情報を使ってデータ資産を開きます。

> [!NOTE]
> Azure Data Catalog では、実際のデータ ソースへのアクセス権が付与されるのではなく、単にそのデータ ソースの検出や把握が簡単になるだけであるという点に注意してください。 データ ソースへの接続時には、選択したクライアント アプリケーションにより Windows 資格情報が使用されるか、必要に応じて資格情報の入力が求められます。 事前にデータ ソースへのアクセス権が付与されていない場合、データ ソースに接続する前に、アクセス権の付与を受ける必要があります。
> 
> 

### <a name="connect-to-a-data-asset-from-excel"></a>Excel からのデータ資産への接続
1. 検索結果から **[Product]** を選択します。 ツール バーの **[開く]** をクリックし、**[Excel]** をクリックします。
   
    ![Azure Data Catalog--connect to data asset](media/data-catalog-get-started/data-catalog-connect1.png)
2. ダウンロードのポップアップ ウィンドウで、 **[ファイルを開く]** をクリックします。 この操作は、ブラウザーによって異なる場合があります。
   
    ![Azure Data Catalog--downloaded Excel connection file](media/data-catalog-get-started/data-catalog-download-open.png)
3. **[Microsoft Excel Security Notice (Microsoft Excel のセキュリティに関する通知)]** ウィンドウで、**[有効にする]** をクリックします。
   
    ![Azure Data Catalog--Excel security popup](media/data-catalog-get-started/data-catalog-excel-security-popup.png)
4. **[データのインポート]** ダイアログ ボックスの既定値はそのままにして、**[OK]** をクリックします。
   
    ![Azure Data Catalog--Excel import data](media/data-catalog-get-started/data-catalog-excel-import-data.png)
5. データ ソースを Excel で確認します。
   
    ![Azure Data Catalog--product table in Excel](media/data-catalog-get-started/data-catalog-connect2.png)

この演習では、Azure Data Catalog を使用して検出されたデータ資産に接続しました。 Azure Data Catalog ポータルでは、 **[開く]** メニューに統合されたクライアント アプリケーションを使用して、データ資産に直接接続できます。 また、資産メタデータに含まれる接続場所の情報を使用して、任意のアプリケーションに接続することもできます。 たとえば、SQL Server Management Studio を使用すると、AdventureWorks2014 データベースに接続し、このチュートリアルで登録されたデータ資産のデータにアクセスできます。

1. **SQL Server Management Studio**を開きます。
2. **[サーバーへの接続]** ダイアログ ボックスで、Azure Data Catalog ポータルの **[プロパティ]** ウィンドウに表示されているサーバー名を入力します。
3. 適切な認証と資格情報を使用してデータ資産にアクセスします。 アクセス権がない場合は、 **[アクセスの要求]** フィールドの情報を使用して取得します。
   
    ![Azure Data Catalog--request access](media/data-catalog-get-started/data-catalog-request-access.png)

**[接続文字列の表示]** をクリックして、ADF.NET、ODBC、OLEDB の接続文字列を表示し、アプリケーションで使用するためにクリップボードにコピーします。

## <a name="manage-data-assets"></a>データ資産の管理
この手順では、データ資産のセキュリティを設定する方法を説明します。 Data Catalog のユーザーにデータ自体へのアクセス権は与えられません。 データ ソースの所有者がデータへのアクセスを管理します。

Data Catalog を通じてデータ ソースを検出し、カタログに登録されたソースに関連したメタデータを閲覧できます。 しかし、場合によっては、データ ソースの可視性を特定のユーザーまたは特定のグループのメンバーのみに限定する必要があります。 そのような場合は、Data Catalog を使用してカタログ内の登録済みデータ資産の所有権を取得したうえで、所有する資産の可視性を制御することができます。

> [!NOTE]
> この演習で説明している管理機能は、Azure Data Catalog の Standard Edition でのみ使用できます。Free Edition では使用できません。
> Azure Data Catalog では、データ資産の所有権の取得、共同所有者のデータ資産への追加、データ資産の可視性の設定ができます。
> 
> 

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>データ資産の所有権の取得と可視性の制限
1. [Azure Data Catalog ホーム ページ](https://www.azuredatacatalog.com)に移動します。 **検索**ボックスに「`tags:cycles`」と入力し、**Enter** キーを押します。
2. 結果の一覧でアイテムをクリックし、ツール バーの **[所有権の取得]** をクリックします。
3. **[プロパティ]** パネルの **[管理]** セクションで、**[所有権の取得]** をクリックします。
   
    ![Azure Data Catalog--take ownership](media/data-catalog-get-started/data-catalog-take-ownership.png)
4. 可視性を制限するには、**[表示]** セクションで **[所有者とこれらのユーザー]** を選択し、**[追加]** をクリックします。 テキスト ボックスにユーザーのメール アドレスを入力し、 **Enter**キーを押します。
   
    ![Azure Data Catalog--restrict access](media/data-catalog-get-started/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>データ資産の削除
この演習では、Azure Data Catalog ポータルを使用して、登録されているデータ資産からプレビュー データを削除し、カタログからデータ資産を削除します。

Azure Data Catalog では、個々の資産を削除することも、複数の資産を削除することもできます。

1. [Azure Data Catalog ホーム ページ](https://www.azuredatacatalog.com)に移動します。
2. **検索**ボックスに「`tags:cycles`」と入力し、**Enter** キーを押します。
3. 次の画像のように、結果の一覧でアイテムを選択し、ツール バーの **[削除]** をクリックします。
   
    ![Azure Data Catalog--delete grid item](media/data-catalog-get-started/data-catalog-delete-grid-item.png)
   
    リスト ビューを使用している場合は、次の画像に示すように、チェック ボックスがアイテムの左側にあります。
   
    ![Azure Data Catalog--delete list item](media/data-catalog-get-started/data-catalog-delete-list-item.png)
   
    次の画像に示すように、複数のデータ資産を選択して削除することもできます。
   
    ![Azure Data Catalog--delete multiple data assets](media/data-catalog-get-started/data-catalog-delete-assets.png)

> [!NOTE]
> カタログの既定の動作は、すべてのユーザーが任意のデータ ソースを登録でき、すべてのユーザーが登録されている任意のデータ資産を削除できることです。 Azure Data Catalog の Standard Edition に含まれる管理機能には、資産の所有権の取得、資産を検出できるユーザーの制限、および資産を削除できるユーザーの制限を行うための追加のオプションがあります。
> 
> 

## <a name="summary"></a>概要
このチュートリアルでは、Azure Data Catalog の重要な機能 (エンタープライズ データ資産の登録、注釈付け、検出、管理など) について学習しました。 これでチュートリアルは完了したので、使用を開始できます。 自分やチームが依存するデータ ソースを登録し、カタログを使用する同僚を招待することによって、今すぐ始めることができます。

## <a name="references"></a>参照
* [データ資産の登録方法](data-catalog-how-to-register.md)
* [データ資産の探索方法](data-catalog-how-to-discover.md)
* [データ資産に注釈を付ける方法](data-catalog-how-to-annotate.md)
* [データ資産を文書化する方法](data-catalog-how-to-documentation.md)
* [データ資産への接続方法](data-catalog-how-to-connect.md)
* [データ資産を管理する方法](data-catalog-how-to-manage.md)


