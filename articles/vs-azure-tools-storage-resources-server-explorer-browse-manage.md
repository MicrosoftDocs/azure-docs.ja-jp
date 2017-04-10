---
title: "サーバー エクスプローラーを使用したストレージ リソースの参照と管理 | Microsoft Docs"
description: "サーバー エクスプローラーを使用したストレージ リソースの参照と管理"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 658dc064-4a4e-414b-ae5a-a977a34c930d
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d35c9903fd68199f9decdf099a7e162fe664e4d5


---
# <a name="browsing-and-managing-storage-resources-with-server-explorer"></a>サーバー エクスプローラーを使用したストレージ リソースの参照と管理
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>概要
Azure Tools for Visual Studio をインストール済みである場合、Azure のストレージ アカウントから、BLOB、キュー、テーブルのデータを表示できます。 サーバー エクスプローラーの Azure の [Storage] ノードに、ローカル ストレージ エミュレーター アカウントをはじめとする Azure ストレージ アカウント内のデータが表示されます。

Visual Studio でサーバー エクスプローラーを表示するには、メニュー バーで **[表示]**、**[サーバー エクスプローラー]** の順にクリックします。 ストレージ ノードは、接続されている各 Azure サブスクリプション/証明書に存在するすべてのストレージ アカウントを示します。 ストレージ アカウントが表示されない場合は、 [このトピックの最後](#add-storage-accounts-by-using-server-explorer)に記載した手順で追加できます。

Azure SDK 2.7 以降では、新しいクラウド エクスプローラーを使用して Azure のリソースを表示したり管理したりすることもできます。 詳細については、「 [クラウド エクスプローラーを使用した Azure リソースの管理](vs-azure-tools-resources-managing-with-cloud-explorer.md) 」を参照してください。

## <a name="view-and-manage-storage-resources-in-visual-studio"></a>Visual Studio でのストレージ リソースの表示と管理
サーバー エクスプローラーには、ストレージ エミュレーター アカウント内の BLOB、キュー、テーブルの一覧が自動的に表示されます。 ストレージ エミュレーター アカウントは、サーバー エクスプローラーの [Storage] ノードの下に **[開発]** というノードとして表示されます。

ストレージ エミュレーター アカウントのリソースを表示するには、 **[開発]** ノードを展開します。 **[開発]** ノードを展開したとき、まだストレージ エミュレーターが起動していない場合は、自動的に起動します。 これには数秒かかる場合があります。 ストレージ エミュレーターの起動中、Visual Studio の他の領域では作業を続行できます。

ストレージ アカウントのリソースを表示するには、サーバー エクスプローラーでストレージ アカウントのノードを展開します。 次のサブノードが表示されます。

* BLOB
* キュー
* テーブル

## <a name="work-with-blob-resources"></a>BLOB リソースの操作
[BLOB] ノードには、選択したストレージ アカウントのコンテナーの一覧が表示されます。 BLOB コンテナーには BLOB ファイルが含まれており、これらの BLOB をフォルダーとサブフォルダーにまとめることができます。 詳細については、「 [.NET から BLOB ストレージを使用する方法](storage/storage-dotnet-how-to-use-blobs.md) 」を参照してください。

### <a name="to-create-a-blob-container"></a>BLOB コンテナーを作成するには
1. **[BLOB]** ノードのショートカット メニューを開き、**[BLOB コンテナーの作成]** をクリックします。
2. **[BLOB コンテナーの作成]** ダイアログ ボックスで新しいコンテナーの名前を入力し、**[OK]** をクリックします。
   
   > [!NOTE]
   > BLOB コンテナーの名前は、数字 (0 ～ 9) または小文字の英字 (a ～ z) で始める必要があります。
   > 
   > 

### <a name="to-delete-a-blob-container"></a>BLOB コンテナーを削除するには
* 削除する BLOB コンテナーのショートカット メニューを開き、 **[削除]**をクリックします。

### <a name="to-display-a-list-of-the-items-contained-in-a-blob-container"></a>BLOB コンテナーの内容を表示するには
* 一覧から BLOB コンテナー名のショートカット メニューを開き、 **[BLOB コンテナーの表示]**をクリックします。
  
    表示した BLOB コンテナーの内容は、BLOB コンテナーのビューと呼ばれるタブに表示されます。
  
    ![VST_SE_BlobDesigner](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC749016.png)
  
    BLOB コンテナーのビューの右上隅にあるボタンを使用して、BLOB に次の操作を実行できます。
  
  * フィルター値を入力して適用する
  * コンテナー内の BLOB の一覧を更新する
  * ファイルをアップロードする
  * BLOB を削除する
    
    > [!NOTE]
    > BLOB コンテナーからファイルを削除しても、基になるファイルは削除されません。BLOB コンテナーから除去されるだけです。
    > 
    > 
  * BLOB を開く
  * BLOB をローカル コンピューターに保存する

### <a name="to-create-a-folder-or-subfolder-in-a-blob-container"></a>BLOB コンテナーにフォルダーまたはサブフォルダーを作成するには
1. サーバー エクスプローラーで BLOB コンテナーを選択します。 コンテナー ウィンドウで **[BLOB のアップロード]** ボタンをクリックします。
   
    ![Uploading a file into a blob folder](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766037.png)
2. **[新しいファイルのアップロード]** ダイアログ ボックスで **[参照]** ボタンをクリックし、アップロードするファイルを指定してから **[フォルダー (省略可)]** ボックスにフォルダー名を入力します。
   
    同じ手順に従って、コンテナーのフォルダーにサブフォルダーを追加できます。 フォルダー名を指定しない場合、ファイルは BLOB コンテナーの最上位にアップロードされます。コンテナー内の指定したフォルダーにファイルが表示されます。
   
    ![Folder added to a blob container](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766038.png)
3. フォルダーをダブルクリックするか、Enter キーを押してフォルダーの内容を表示します。 コンテナーのフォルダーで、 **[親ディレクトリを開く]** (上矢印) ボタンをクリックすれば、コンテナーのルートに戻ることができます。

### <a name="to-delete-a-container-folder"></a>コンテナーのフォルダーを削除するには
* フォルダー内のファイルをすべて削除します。
  
  > [!NOTE]
  > BLOB コンテナー内のフォルダーは仮想フォルダーであるため、空のフォルダーを作成したり、フォルダーを削除してそのファイルの内容を削除したりすることはできません。 フォルダーを削除するには、フォルダーの内容全体を削除する必要があります。
  > 
  > 

### <a name="to-filter-blobs-in-a-container"></a>コンテナー内の BLOB をフィルター処理するには
共通のプレフィックスを指定すると、表示される BLOB をフィルター処理できます。

たとえば、`hello` というプレフィックスをフィルターのテキスト ボックスに入力し、**[実行]** (**!**) をクリックすると、'hello' で始まる BLOB のみが表示されます。

![VST_SE_FilterBlobs](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC519076.png)

> [!NOTE]
> フィルター フィールドでは大文字と小文字が区別され、ワイルド カード文字によるフィルター処理はサポートされていません。 BLOB はプレフィックスでのみフィルター処理できます。 仮想階層で区切り記号を使用して BLOB を整理している場合は、プレフィックスに区切り記号を含めることができます。 たとえば、「HelloFabric/」というプレフィックスでフィルター処理を行うと、この文字列で始まる BLOB がすべて返されます。
> 
> 

### <a name="to-download-blob-data"></a>BLOB データをダウンロードするには
* **サーバー エクスプローラー**で 1 つ以上の BLOB のショートカット メニューを開いて **[開く]** を選択するか、BLOB 名を選択して **[開く]** ボタンをクリックするか、BLOB 名をダブルクリックします。
  
    **[Azure のアクティビティ ログ]** ウィンドウに BLOB のダウンロードの進行状況が表示されます。
  
    その種類のファイルの既定のエディターで BLOB が開きます。 そのファイルの種類がオペレーティング システムで認識された場合、ファイルは、ローカルにインストールされているアプリケーションで開かれます。それ以外の場合は、BLOB のファイルの種類に対応したアプリケーションを選択するように求めるメッセージが表示されます。 BLOB をダウンロードしたときに作成されるローカル ファイルは、読み取り専用としてマークされます。
  
    BLOB データは、ローカルにキャッシュされ、BLOB サービス内の BLOB の最終更新時刻に照らしてチェックされます。 最後にダウンロードされたときから BLOB が更新されている場合は、BLOB が再度ダウンロードされます。それ以外の場合は、ローカル ディスクから BLOB が読み込まれます。 既定では、BLOB が一時ディレクトリにダウンロードされます。 特定のディレクトリに BLOB をダウンロードするには、選択した BLOB 名のショートカット メニューを開き、**[名前を付けて保存]** をクリックします。 この方法で BLOB を保存すると、BLOB ファイルは開かれません。また、読み取り/書き込み属性のローカル ファイルが作成されます。

### <a name="to-upload-blobs"></a>BLOB をアップロードするには
* BLOB コンテナーのビューで閲覧用にコンテナーを開き、 **[BLOB のアップロード]** をクリックします。
  
    アップロード対象ファイル (複数可) を選択し、任意の種類のファイルをアップロードできます。 **[Azure のアクティビティ ログ]** にアップロードの進行状況が表示されます。 BLOB データを操作する方法の詳細については、「 [.NET から BLOB ストレージを使用する方法](http://go.microsoft.com/fwlink/p/?LinkId=267911)」を参照してください。

### <a name="to-view-logs-transferred-to-blobs"></a>BLOB に転送されたログを表示するには
* Azure 診断を使用して Azure アプリケーションからデータのログを記録し、ログをストレージ アカウントに転送した場合は、これらのログ用に Azure で作成されたコンテナーが表示されます。 サーバー エクスプローラーでこれらのログを表示すれば、アプリケーション、特に Azure にデプロイされたアプリケーションの問題を簡単に特定できます。 Azure 診断の詳細については、「 [Azure 診断を使用したログ データの収集](https://msdn.microsoft.com/library/azure/gg433048.aspx)」を参照してください。

### <a name="to-get-the-url-for-a-blob"></a>BLOB の URL を取得するには
* BLOB のショートカット メニューを開き、 **[URL のコピー]**をクリックします。

### <a name="to-edit-a-blob"></a>BLOB を編集するには
* BLOB を選択し、 **[BLOB を開く]** をクリックします。
  
    ファイルは一時的な場所にダウンロードされ、ローカル コンピューター上で開かれます。 変更を行った後は、BLOB を再度アップロードする必要があります。

## <a name="work-with-queue-resources"></a>キュー リソースの操作
ストレージ サービスのキューは、ストレージ アカウント内でホストされています。ストレージ サービスを使用すると、メッセージを渡す機能によって、クラウド サービスのロールが互いにやり取りしたり、他のサービスとやり取りしたりすることができます。 キューは、クラウド サービス、および外部クライアントの Web サービスからプログラムによってアクセスできます。 キューは、Visual Studio のサーバー エクスプローラーを使用して直接アクセスすることもできます。

キューを使用するクラウド サービスを開発する場合は、コードを開発およびテストするときに、Visual Studio を使用してキューを対話的に作成したり操作したりすることができます。

サーバー エクスプローラーでは、ストレージ アカウント内のキューを表示したり、キューを作成 (または削除) したりすることができるほか、キューを開いてメッセージを表示したり、メッセージをキューに追加したりすることができます。 キューを閲覧用に開くと、個々のメッセージを表示できます。左上隅のボタンを使用して、キューに対して次の操作を実行することが可能です。

* キューの表示を更新する
* メッセージをキューに追加する
* 最上位のメッセージをデキューする
* キュー全体をクリアする

次の図は、2 つのメッセージを含むキューを示しています。

![Viewing a Queue](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC651470.png)

ストレージ サービス キューの詳細については、「 [.NET から Queue ストレージを使用する方法](http://go.microsoft.com/fwlink/?LinkID=264702)」を参照してください。 ストレージ サービス キューの Web サービスについては、「 [キュー サービスの概念](http://go.microsoft.com/fwlink/?LinkId=264788)」を参照してください。 Visual Studio を使用して、ストレージ サービス キューにメッセージを送信する方法については、「 [ストレージ サービスのキューにメッセージを送信する](https://msdn.microsoft.com/library/azure/jj649344.aspx)」を参照してください。

> [!NOTE]
> ストレージ サービスのキューは、Service Bus キューとは異なります。 Service Bus キューの詳細については、Service Bus のキュー、トピック、サブスクリプションに関するページを参照してください。
> 
> 

## <a name="work-with-table-resources"></a>テーブル リソースの操作
Azure Table Storage サービスは、大量の構造化データを格納します。 このサービスは、Azure クラウドの内部および外部からの認証された呼び出しを受け付ける NoSQL データストアです。 Azure のテーブルは、構造化された非リレーショナル データを格納するのに最適です。

### <a name="to-create-a-table"></a>テーブルを作成するには
1. サーバー エクスプローラーで、ストレージ アカウントの **[テーブル]** ノードを選択してから、**[テーブルの作成]** を選択します。
2. **[テーブルの作成]** ダイアログ ボックスでテーブルの名前を入力します。

### <a name="to-view-table-data"></a>テーブル データを表示するには
1. サーバー エクスプローラーで **[Azure]** ノードを開き、**[Storage]** ノードを開きます。
2. 目的のストレージ アカウント ノードを開き、 **[テーブル]** ノードを開いて、そのストレージ アカウントのテーブルの一覧を表示します。
3. テーブルのショートカット メニューを開き、 **[テーブルの表示]**を選択します。
   
    ![An Azure table in Solution Explorer](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744165.png)

テーブルは、エンティティ (行に表示) とプロパティ (列に表示) で構成されています。 **テーブル デザイナー**に一覧表示されたエンティティの例を次の図に示します。

### <a name="to-edit-table-data"></a>テーブル データを編集するには
1. **テーブル デザイナー**で、エンティティ (単一の行) またはプロパティ (単一のセル) のショートカット メニューを開き、**[編集]** を選択します。
   
    ![Add or Edit a Table Entity](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC656238.png)
   
    単一テーブルのエンティティが、同じ一連のプロパティ (列) を持っている必要はありません。 テーブル データの表示と編集には、次の制限があることに留意してください。
   
   * バイナリ データ (byte[] 型) は、表示することも編集することもできませんが、テーブルに格納することはできます。
   * **PartitionKey** と **RowKey** の値は編集できません。この操作は、Azure のテーブル ストレージではサポートされません。
   * Timestamp という名前のプロパティは作成できません。Azure ストレージ サービスで、同名のプロパティが使用されています。
   * DateTime 値を入力する場合、ご利用のコンピューターの地域と言語の設定に合った適切な形式で入力する必要があります (たとえば、英語 (U.S) の場合であれば、MM/DD/YYYY HH:MM:SS [AM|PM])。

### <a name="to-add-entities"></a>エンティティを追加するには
1. **テーブル デザイナー**で、テーブル ビューの右上隅近くにある **[エンティティの追加]** をクリックします。
   
    ![[エンティティの追加]](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655336.png)
2. **[エンティティの追加]** ダイアログ ボックスで、**PartitionKey** プロパティと **RowKey** プロパティの値を入力します。
   
    ![Add Entity Dialog Box](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655335.png)
   
    ダイアログ ボックスを閉じた後に変更することはできないので、値は慎重に入力してください。変更するには、エンティティを削除してから追加し直す必要があります。

### <a name="to-filter-entities"></a>エンティティをフィルター処理するには
クエリ ビルダーを使用する場合、テーブルに表示されるエンティティ セットをカスタマイズすることができます。

1. クエリ ビルダーを開くには、テーブルを閲覧用に開きます。
2. テーブル ビューのツール バーの右端にあるボタンをクリックします。
   
    **[クエリ ビルダー]** ダイアログ ボックスが表示されます。 次の図は、クエリ ビルダーで作成しているクエリを示しています。
   
    ![[クエリ ビルダー]](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC652231.png)
3. クエリの作成を終えたら、ダイアログ ボックスを閉じます。 クエリの結果であるテキスト形式が WCF Data Services フィルターとしてテキスト ボックスに表示されます。
4. クエリを実行するには、緑の三角形のアイコンをクリックします。
   
    また、 **テーブル デザイナー** に表示されるエンティティ データは、フィルター フィールドに WCF Data Services フィルター文字列を直接入力することでもフィルター処理できます。 この種の文字列は、SQL の WHERE 句に似ていますが、サーバーには HTTP 要求として送信されます。 フィルター文字列を作成する方法の詳細については、「 [テーブル デザイナー用のフィルター文字列の作成](https://msdn.microsoft.com/library/azure/ff683669.aspx)」を参照してください。
   
    有効なフィルター文字列の例を次の図に示します。
   
    ![VST_SE_TableFilter](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655337.png)

### <a name="refresh-storage-data"></a>ストレージ データの更新
サーバー エクスプローラーでストレージ アカウントからデータに接続したり、データを取得したりするときには、処理が完了するまでに最大で 1 分かかる場合があります。 接続できない場合は、操作がタイムアウトする可能性があります。 データの取得中、Visual Studio の他の部分では作業を続行できます。 操作に時間がかかりすぎる場合に操作を取り消すには、サーバー エクスプローラー ツール バーの **[更新の中止]** をクリックします。

#### <a name="to-refresh-blob-container-data"></a>BLOB コンテナー データを更新するには
* **[Storage]** の下にある **[BLOB]** ノードを選択し、サーバー エクスプローラー ツール バーの **[最新の情報に更新]** をクリックします。
* 表示される BLOB の一覧を更新するには、 **[実行]** をクリックします。

#### <a name="to-refresh-table-data"></a>テーブル データを更新するには
* **[Storage]** の下にある **[テーブル]** ノードを選択し、**[最新の情報に更新]** をクリックします。
* **[テーブル デザイナー]** に表示されるエンティティの一覧を更新するには、**テーブル デザイナー**の **[実行]** をクリックします。

#### <a name="to-refresh-queue-data"></a>キュー データを更新するには
* **[キュー]** ノードを選択し、**[最新の情報に更新]** をクリックします。

#### <a name="to-refresh-all-items-in-a-storage-account"></a>ストレージ アカウントのすべてのアイテムを更新するには
* アカウント名を選択し、サーバー エクスプローラーのツール バーの **[最新の情報に更新]** をクリックします。

### <a name="add-storage-accounts-by-using-server-explorer"></a>サーバー エクスプローラーを使用してストレージ アカウントを追加する
サーバー エクスプローラーを使用してストレージ アカウントを追加する方法は 2 つあります。 Azure サブスクリプションで新しいストレージ アカウントを作成することも、既存のストレージ アカウントを接続することもできます。

#### <a name="to-create-a-new-storage-account-by-using-server-explorer"></a>サーバー エクスプローラーを使用して新しいストレージ アカウントを作成するには
1. サーバー エクスプローラーで [Storage] ノードのショートカット メニューを開き、[ストレージ アカウントの作成] をクリックします。
   
    ![Create a new Azure storage account](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744166.png)
2. **[ストレージ アカウントの作成]** ダイアログ ボックスで、新しいストレージ アカウントの次の情報を選択または入力します。
   
   * ストレージ アカウントを追加する Azure サブスクリプション。
   * 新しいストレージ アカウントに使用する名前。
   * リージョンまたはアフィニティ グループ (米国西部や東アジアなど)。
   * geo 冗長など、ストレージ アカウントで使用するレプリケーションの種類。
3. **[作成]**を選択します。
   
    新しいストレージ アカウントがソリューション エクスプローラーの **[Storage]** ボックスの一覧に表示されます。

#### <a name="to-attach-an-existing-storage-account-by-using-server-explorer"></a>サーバー エクスプローラーを使用して既存のストレージ アカウントを接続するには
1. [サーバー エクスプローラー] で、[Azure Storage] ノードのショートカット メニューを開き、 **[外部ストレージのアタッチ]**をクリックします。
   
    ![Adding an existing storage account](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766039.png)
2. **[ストレージ アカウントの作成]** ダイアログ ボックスで、新しいストレージ アカウントの次の情報を選択または入力します。
   
   * 接続する既存のストレージ アカウントの名前。 名前を入力するか、一覧から選択できます。
   * 選択したストレージ アカウントのキー。 この値は通常、ストレージ アカウントを選択したときに提供されます。 ストレージ アカウントのキーを Visual Studio に記憶させるには、[アカウント キーを記憶する] チェック ボックスをオンにします。
   * HTTP、HTTPS、カスタム エンドポイントなど、ストレージ アカウントへの接続に使用するプロトコル。 カスタム エンドポイントの詳細については、 [接続文字列の構成方法](https://msdn.microsoft.com/library/azure/ee758697.aspx) に関するページを参照してください。

### <a name="to-view-the-secondary-endpoints"></a>セカンダリ エンドポイントを表示するには
* **[Read-Access Geo Redundant]** レプリケーション オプションを使用してストレージ アカウントを作成した場合は、そのセカンダリ エンドポイントを表示することができます。 アカウント名のショートカット メニューを開き、 **[プロパティ]**を選択します。
  
    ![Storage secondary endpoints](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766040.png)

### <a name="to-remove-a-storage-account-from-server-explorer"></a>サーバー エクスプローラーからストレージ アカウントを削除するには
* サーバー エクスプローラーでアカウント名のショートカット メニューを開き、 **[削除]**をクリックします。 ストレージ アカウントを削除すると、そのアカウントに関して保存されていたキー情報もすべて削除されます。
  
  > [!NOTE]
  > サーバー エクスプローラーからストレージ アカウントを削除しても、サーバー エクスプローラーからの参照が削除されるだけで、ストレージ アカウントやアカウントに含まれるデータへの影響はありません。 ストレージ アカウントを完全に削除するには、 [Azure クラシック ポータル](http://go.microsoft.com/fwlink/?LinkID=213885)を使用します。
  > 
  > 

## <a name="next-steps"></a>次のステップ
Azure ストレージ サービスの使用方法の詳細については、「 [Azure ストレージ サービスへのアクセス](https://msdn.microsoft.com/library/azure/ee405490.aspx)」を参照してください。




<!--HONumber=Nov16_HO3-->


