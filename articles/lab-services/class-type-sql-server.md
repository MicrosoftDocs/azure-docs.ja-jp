---
title: Azure SQL Database を使用して管理および開発するようにラボを設定する | Azure Lab Services
description: Azure SQL Database を使用して管理および開発するようにラボを設定する方法について説明します。
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 50f71ee1ce59f5809fe8905c58f0399cf484f11a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94659711"
---
# <a name="set-up-a-lab-to-manage-and-develop-with-sql-server"></a>SQL Server を使用して管理および開発するようにラボを設定する

この記事では、Azure Lab Services で基本的な SQL Server 管理および開発のクラス用にラボを設定する方法について説明します。  データベースの概念は、大学のほとんどのコンピューター サイエンス部門で教えられる入門コースの 1 つです。 構造化照会言語 (SQL) は、国際標準です。  SQL は、データベース内のコンテンツへの追加、アクセス、管理などの、リレーショナル データベース管理のための標準言語です。  これは、迅速な処理、実証された信頼性、使いやすさ、および柔軟性を備えています。

この記事では、[Visual Studio 2019](https://visualstudio.microsoft.com/vs/)、[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)、[Azure Data Studio](https://github.com/microsoft/azuredatastudio) を使用してラボに仮想マシン テンプレートを設定する方法について説明します。  このラボでは、ラボ全体で 1 つの共有 [SQL Server データベース](../azure-sql/database/sql-database-paas-overview.md)を使用します。 [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) は、Azure のサービスとしてのプラットフォーム (PaaS) データベース エンジン オファリングです。

## <a name="lab-configuration"></a>ラボの構成

このラボを設定するには、Azure サブスクリプションとラボ アカウントが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 Azure サブスクリプションを取得したら、Azure Lab Services で新しいラボ アカウントを作成できます。 新しいラボ アカウントの作成の詳細については、[ラボ アカウントを設定するためのチュートリアル](./tutorial-setup-lab-account.md)を参照してください。 既存のラボ アカウントを使用することもできます。

### <a name="lab-account-settings"></a>ラボ アカウントの設定

以下の表に記載されているラボ アカウントの設定を有効にします。 Marketplace イメージを有効にする方法の詳細については、「[ラボ作成者が利用できる Marketplace イメージを指定する](specify-marketplace-images.md)」を参照してください。

| ラボ アカウントの設定 | Instructions |
| ------------------- | ------------ |
| Marketplace イメージ | ラボ アカウント内で使用するために、"Visual Studio 2019 Community (最新リリース) on Windows 10 Enterprise N (x64)" イメージを有効にします。 |

### <a name="shared-resource-configuration"></a>共有リソースの構成

Lab Services で共有リソースを使用するには、最初に仮想ネットワークとリソース自体を作成する必要があります。  仮想ネットワークを作成してラボに接続するには、「[Azure Lab Services で共有リソースを使用してラボを作成する方法](how-to-create-a-lab-with-shared-resource.md)」に従ってください。  Lab Services の外部のリソースはすべて別個に課金され、ラボのコストの見積もりには含まれません。

>[!WARNING]
>ラボの共有リソースは、ラボを作成する前に設定する必要があります。  ラボを作成する "*前に*" VNet を [ラボ アカウントにピアリング](how-to-connect-peer-virtual-network.md)していないと、ラボは共有リソースにアクセスできません。

これでネットワーク面が処理されたので、SQL Server データベースを作成しましょう。  Azure SQL Database の最も迅速なデプロイ オプションであるため、[単一データベース](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal)を作成します。  その他のデプロイ オプションでは、[エラスティック プール](../azure-sql/database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)、[マネージド インスタンス](../azure-sql/managed-instance/instance-create-quickstart.md)、または [SQL 仮想マシン](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md)を作成します。

1. Azure portal のメニューから、 **[新しいリソースの作成]** を選択します。
2. **[SQL Database]** を選択し、 **[作成]** ボタンをクリックします。
3. **[SQL データベースの作成]** フォームの **[基本]** タブで、データベースのリソース グループを選択します。  *sqldb-rg* を使用します。
4. **[データベース名]** には、*classlabdb* と入力します。
5. **[サーバー]** 設定で **[新規作成]** をクリックして、データベースを保持する新しいサーバーを作成します。
6. **[新しいサーバー]** ポップアップで、サーバー名を入力します。  *classlabdbserver* を使用します。  このサーバー名はグローバルに一意である必要があります。
7. **[サーバー管理者のログイン]** には *azureuser* と入力します。
8. 覚えやすいパスワードを入力します。  パスワードは、8 文字以上の長さで、特殊文字を含んでいる必要があります。
9. **[場所]** のリージョンを選択します。  可能であれば、待機時間を最小にするために、ラボ アカウントおよびピアリングした VNet と同じ場所を入力してください。
10. **[OK]** をクリックして、 **[SQL データベースの作成]** フォームに戻ります。
11. **[コンピューティングとストレージ]** 設定で、 **[データベースの構成]** リンクをクリックします。
12. 必要に応じて、クラスのデータベース設定を変更します。  "プロビジョニング" と "サーバーレス" のオプションから選択できます。  この例では、最大仮想コア数が 4、最小仮想コア数が 1 である、自動スケーリングのサーバーレス オプションを使用します。 自動一時停止の設定は、最短 1 時間にします。 **[Apply]** をクリックします。
13. **[次へ: ネットワーク]** ボタンをクリックします。
14. [ネットワーク] タブで、 **[接続方法]** には [プライベート エンドポイント] を選択します。
15. **[プライベート エンドポイント]** セクションで、 **[プライベート エンドポイントの追加]** をクリックします。
16. **[プライベート エンドポイントの作成]** ポップアップで、ラボ アカウントにピアリングされている仮想ネットワークと同じリソース グループを選択します。
17. **[場所]** には、仮想ネットワークと同じ場所を選択します。
18. **[名前]** には、*labsql-endpt* と入力します。
19. ターゲット サブリソースは、SqlServer に設定したままにします。
20. **[仮想ネットワーク]** には、ラボ アカウントにピアリングされているのと同じ仮想ネットワークを選択します。
21. **[サブネット]** には、エンドポイントをホストするサブネットを選択します。  エンドポイントに割り当てられた IP は、そのサブネットに割り当てられている範囲からのものになります。
22. **[プライベート DNS ゾーンとの統合]** を **[いいえ]** に設定します。 わかりやすくするために、ここでは独自のプライベート DNS ゾーンまたは独自の DNS サーバーで Azure の DNS を使用します。
23. **[OK]** をクリックします。
24. **[次へ: 追加設定]** を選択します。
25. **[既存のデータを使用]** 設定では、 **[サンプル]** を選択します。  データベースが作成されると、AdventureWorksLT データベースのデータが使用されます。
26. **[Review + create]\(レビュー + 作成\)** をクリックします。
27. **Create** をクリックしてください。

SQL Database のデプロイが正常に完了したら、ラボを作成し、ラボのテンプレート マシンにソフトウェアをインストールできます。

### <a name="lab-settings"></a>ラボの設定

クラスルーム ラボを設定するときは、次の表の設定を使用します。 クラスルーム ラボの作成方法の詳細については、[クラスルーム ラボの設定に関するチュートリアル](tutorial-setup-classroom-lab.md)を参照してください。

| ラボの設定 | 値/説明 |
| ------------ | ------------------ |
| 仮想マシンのサイズ | 中。 このサイズは、リレーショナル データベース、メモリ内 Caching、および分析に最適です。 |
| 仮想マシン イメージ | Visual Studio 2019 Community (最新リリース) on Windows 10 Enterprise N (x64) |

これでラボが作成されたので、必要なソフトウェアを使ってテンプレート マシンを変更しましょう。

## <a name="visual-studio"></a>Visual Studio

上で選択したイメージには [Visual Studio 2019 Community](https://visualstudio.microsoft.com/vs/community/) が含まれています。  イメージには、すべてのワークロードとツールセットが既にインストールされています。  Visual Studio インストーラーを使用して、必要な[任意のオプション ツールをインストール](/visualstudio/install/modify-visual-studio?view=vs-2019)します。  [Visual Studio にサインイン](/visualstudio/ide/signing-in-to-visual-studio?view=vs-2019#how-to-sign-in-to-visual-studio)して、Community エディションのロックを解除します。

Visual Studio には、SQL Server Data Tools (SSDT) を含む **データ ストレージと処理** ツール セットが含まれています。  SSDT の機能の詳細については、[SQL Server Data Tools の概要](/sql/ssdt/sql-server-data-tools?view=sql-server-ver15)に関するページを参照してください。  クラスの共有 SQL Server への接続が正常に行われることを確認するには、「[データベースに接続し、既存のオブジェクトを参照する](/sql/ssdt/how-to-connect-to-a-database-and-browse-existing-objects?view=sql-server-ver15)」を参照してください。 メッセージが表示されたら、テンプレート マシンの IP を、SQL Server インスタンスに接続できる、[許可されたコンピューターの一覧](../azure-sql/database/firewall-configure.md)に追加します。

Visual Studio では、**Web とクラウド** および **デスクトップとモバイル** ワークロードを含め、いくつかのワークロードがサポートされています。  これらのワークロードはどちらも、データ ソースとして SQL Server をサポートしています。 SQL Server への ASP.NET Core の使用の詳細については、「[Azure App Service での ASP.NET Core および SQL Database アプリの作成](../app-service/tutorial-dotnetcore-sqldb-app.md)」チュートリアルを参照してください。  [System.Data.SqlClient](/dotnet/api/system.data.sqlclient) ライブラリを使用して、[Xamarin](/xamarin) アプリから SQL Database に接続します。

## <a name="install-azure-data-studio"></a>Azure Data Studio をインストールする

[Azure Data Studio](https://github.com/microsoft/azuredatastudio) は、Windows、macOS、Linux 上でオンプレミスおよびクラウド データ プラットフォームのファミリを使用するデータ プロフェッショナルを対象とした、マルチデータベースのクロスプラットフォーム デスクトップ環境です。

1. [Windows 用の Azure Data Studio *システム* インストーラー](https://go.microsoft.com/fwlink/?linkid=2127432) をダウンロードします。 サポートされているその他のオペレーティング システムのインストーラーについては、[Azure Data Studio](/sql/azure-data-studio/download) のダウンロード ページにアクセスしてください。
2. **[使用許諾契約書]** ページで、 **[同意する]** を選択します。 **[次へ]** をクリックします。
3. **[インストール先の選択]** ページで **[次へ]** をクリックします。
4. **[スタート メニューのフォルダーの選択]** ページで **[次へ]** をクリックします。
5. **[Select Additional Tasks]\(追加のタスクの選択\)** ページで、デスクトップ アイコンが必要な場合は **[Create a desktop icon]\(デスクトップ アイコンを作成する\)** を選択します。  **[次へ]** をクリックします。
6. **[インストールの準備完了]** で **[次へ]** をクリックします。
7. インストーラーが実行されるのを待ちます。  **[完了]** をクリックします。

これで Azure Data Studio インストールが完了したので、Azure SQL Database への接続を設定しましょう。

1. Azure Data Studio の **[ウェルカム]** ページで、 **[新しい接続]** リンクをクリックします。
2. **[接続の詳細]** ボックスに必要な情報を入力します。
    - **[サーバー]** を *classlabdbserver.database.windows.net* に設定します。
    - **[ユーザー]** 名を *azureuser* に設定します。
    - **[パスワード]** を、データベースの作成に使用するパスワードに設定します。
    - **[パスワードを記憶する]** をオンにします。
    - **[データベース]** には、*classlabdb* を選択します。
3. **[Connect]** をクリックします。

## <a name="install-sql-server-management-studio"></a>SQL Server Management Studio をインストールします。

[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15) は、SQL インフラストラクチャを管理するための統合環境です。  SSMS は、データベース管理者がデータ インフラストラクチャのデプロイ、監視、およびアップグレードを行うために使用するツールです。

1. [Sql Server Management Studio をダウンロード](https://aka.ms/ssmsfullsetup)します。 ダウンロードが完了したら、インストーラーを起動します。
2. **[ウェルカム]** ページで **[インストール]** をクリックします。
3. **[セットアップが完了しました]** ページで、 **[閉じる]** をクリックします。
4. SQL Server Management Studio を起動します。  
5. **[Dependency Configuration process]\(依存関係の構成のプロセス\)** ページで、 **[閉じる]** をクリックします。

SSMS がインストールされたので、[SQL Server に接続してクエリを実行](/sql/ssms/tutorials/connect-query-sql-server)することができます。 接続を設定するときは、次の値を使用します。

- サーバーの種類:データベース エンジン
- サーバー名: *classlabdbserver.database.windows.net*
- 認証: SQL Server 認証
- ログイン: *azureuser*
- パスワード: データベースの作成に使用されるパスワード。

## <a name="cost-estimate"></a>コストの見積もり

このクラスの考えられるコスト見積もりを検討しましょう。 見積もりには、SQL Server を実行するコストは含まれません。  データベース価格の現在の詳細については、[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database)に関するページを参照してください。

クラスの生徒数は 25 人とします。 予定された授業時間は 20 時間です。 また、各学生には、予定された授業時間の他に、宿題や課題を行うための時間として 10 時間が割り当てられます。 ここで選択した仮想マシンのサイズは、42 ラボ ユニットの "中" です。

このクラスで考えられるコスト見積もりの例を次に示します。

25 人の学生 \* (20 時間のスケジュールされた時間 \+ 10 時間のクォータ時間) * 0.42 USD/時間 = 315.00 USD

>[!IMPORTANT]
>コストの見積もりはあくまでも例です。 価格について詳しくは、「[Azure Lab Services の価格](https://azure.microsoft.com/pricing/details/lab-services/)」をご覧ください。

## <a name="next-steps"></a>次のステップ

次の手順は、すべてのラボの設定で共通です。

- [テンプレートを作成、管理、公開する](how-to-create-manage-template.md)
- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users)