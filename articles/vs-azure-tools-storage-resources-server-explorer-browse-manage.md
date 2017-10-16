---
title: "サーバー エクスプローラーを使用したストレージ リソースの参照と管理 | Microsoft Docs"
description: "サーバー エクスプローラーを使用したストレージ リソースの参照と管理"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 658dc064-4a4e-414b-ae5a-a977a34c930d
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/24/2017
ms.author: kraigb
ms.openlocfilehash: 456daea494ed1daed9bd809b68ae12fceb800299
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="browse-and-manage-storage-resources-by-using-server-explorer"></a>サーバー エクスプローラーを使用したストレージ リソースの参照と管理
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>概要
Azure Tools for Microsoft Visual Studio をインストール済みである場合、Azure のストレージ アカウントから、BLOB、キュー、テーブルのデータを表示できます。 サーバー エクスプローラーの [Azure] の **[ストレージ]** ノードに、ローカル ストレージ エミュレーター アカウントをはじめとする Azure ストレージ アカウント内のデータが表示されます。

Visual Studio でサーバー エクスプローラーを表示するには、メニュー バーの **[表示]** > **[サーバー エクスプローラー]** を選択します。 **[ストレージ]** ノードには、接続先の各 Azure サブスクリプションまたは証明書に存在するすべてのストレージ アカウントが表示されます。 ストレージ アカウントが表示されない場合は、 [このトピックの最後](#add-storage-accounts-by-using-server-explorer)に記載した手順で追加できます。

Azure SDK 2.7 以降では、Cloud Explorer を使用して Azure リソースを表示および管理することもできます。 詳細については、[Cloud Explorer を使用した Azure リソースの管理](vs-azure-tools-resources-managing-with-cloud-explorer.md)に関する記事をご覧ください。

## <a name="view-and-manage-storage-resources-in-visual-studio"></a>Visual Studio でのストレージ リソースの表示と管理
サーバー エクスプローラーには、ストレージ エミュレーター アカウント内の BLOB、キュー、テーブルの一覧が自動的に表示されます。 ストレージ エミュレーター アカウントは、サーバー エクスプローラーの **[ストレージ]** ノードの下に **[開発]** ノードとして表示されます。

ストレージ エミュレーター アカウントのリソースを表示するには、 **[開発]** ノードを展開します。 **[開発]** ノードを展開したとき、まだストレージ エミュレーターが起動していない場合は、自動的に起動します。 これには数秒かかる場合があります。 ストレージ エミュレーターの起動中、Visual Studio の他の領域では作業を続行できます。

ストレージ アカウントのリソースを表示するには、サーバー エクスプローラーでストレージ アカウントのノードを展開します。 次のサブノードが表示されます。

* **BLOB**
* **キュー**
* **テーブル**

## <a name="work-with-blob-resources"></a>BLOB リソースの操作
**[BLOB]** ノードには、選択したストレージ アカウントのコンテナーの一覧が表示されます。 BLOB コンテナーには BLOB ファイルが含まれており、これらの BLOB をフォルダーとサブフォルダーにまとめることができます。 詳細については、「 [.NET から BLOB ストレージを使用する方法](storage/blobs/storage-dotnet-how-to-use-blobs.md)」を参照してください。

### <a name="to-create-a-blob-container"></a>BLOB コンテナーを作成するには
1. **[BLOB]** ノードのショートカット メニューを開き、**[BLOB コンテナーの作成]** を選択します。
2. **[BLOB コンテナーの作成]** ダイアログ ボックスで新しいコンテナーの名前を入力します。  
3. キーボードの Enter キーを押すか、名前フィールドの外側をクリックまたはタップして BLOB コンテナーを保存します。
   
   > [!NOTE]
   > BLOB コンテナーの名前は、数字 (0 ～ 9) または小文字の英字 (a ～ z) で始める必要があります。
   > 
   > 

### <a name="to-delete-a-blob-container"></a>BLOB コンテナーを削除するには
* 削除する BLOB コンテナーのショートカット メニューを開き、**[削除]** を選択します。

### <a name="to-display-a-list-of-the-items-in-a-blob-container"></a>BLOB コンテナー内の項目の一覧を表示するには
* 一覧で BLOB コンテナー名のショートカット メニューを開き、**[開く]** を選択します。
  
表示した BLOB コンテナーの内容は、BLOB コンテナー ビューと呼ばれるタブに表示されます。
  
![BLOB コンテナー ビュー](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC749016.png)
  
BLOB コンテナー ビューの右上隅にあるボタンを使用して、BLOB に対して次の操作を実行できます。
  
* フィルター値を入力して適用する
* コンテナー内の BLOB の一覧を更新する
* ファイルをアップロードします。
* BLOB を削除する
    
  > [!NOTE]
  > BLOB コンテナーからファイルを削除しても、基になるファイルは削除されません。 BLOB コンテナーから削除されるだけです。
  > 
  > 
* BLOB を開く
* BLOB をローカル コンピューターに保存する

### <a name="to-create-a-folder-or-subfolder-in-a-blob-container"></a>BLOB コンテナーにフォルダーまたはサブフォルダーを作成するには
1. Cloud Explorer で BLOB コンテナーを選択します。 コンテナー ウィンドウで **[BLOB のアップロード]** をクリックします。
   
2. **[新しいファイルのアップロード]** ダイアログ ボックスで、**[参照]** をクリックしてアップロードするファイルを指定し、**[フォルダー (省略可)]** ボックスにフォルダー名を入力します。
   
   ![Uploading a file into a blob folder](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766037.png)
    
   同じ手順に従って、コンテナーのフォルダーにサブフォルダーを追加できます。 フォルダー名を指定していない場合、ファイルは BLOB コンテナーの最上位にアップロードされます。 コンテナー内の指定したフォルダーにファイルが表示されます。
   
   ![Folder added to a blob container](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766038.png)
3. フォルダーをダブルクリックするか、Enter キーを押してフォルダーの内容を表示します。 コンテナーのフォルダー内で、**[親ディレクトリを開く]** (矢印) ボタンをクリックすると、コンテナーのルートに戻ることができます。

### <a name="to-delete-a-container-folder"></a>コンテナーのフォルダーを削除するには
* フォルダー内のファイルをすべて削除します。
  
> [!NOTE]
> BLOB コンテナー内のフォルダーは仮想フォルダーであるため、空のフォルダーを作成することはできません。 また、フォルダーを削除して、フォルダーのファイルの内容を削除することはできません。 フォルダー自体を削除するには、フォルダーの内容全体を削除する必要があります。
> 
> 

### <a name="to-filter-blobs-in-a-container"></a>コンテナー内の BLOB をフィルター処理するには
共通のプレフィックスを指定すると、表示される BLOB をフィルター処理できます。

たとえば、フィルター テキスト ボックスに **hello** というプレフィックスを入力し、**[実行]** (**!**) ボタンをクリックすると、"hello" で始まる BLOB だけが表示されます。

![フィルター テキスト ボックス](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC519076.png)

> [!NOTE]
> フィルター テキスト ボックスでは大文字と小文字が区別され、ワイルド カード文字によるフィルター処理はサポートされていません。 BLOB はプレフィックスでのみフィルター処理できます。 仮想階層で区切り記号を使用して BLOB を整理している場合は、プレフィックスに区切り記号を含めることができます。 たとえば、"HelloFabric/" というプレフィックスでフィルター処理すると、この文字列で始まるすべての BLOB が返されます。
> 
> 

### <a name="to-download-blob-data"></a>BLOB データをダウンロードするには
Cloud Explorer で次のいずれかの方法を使用します。
* 1 つ以上の BLOB のショートカット メニューを開き、**[開く]** を選択します。
* BLOB 名を選択し、**[開く]** をクリックします。
* BLOB 名をダブルクリックします。
  
**[Azure のアクティビティ ログ]** ウィンドウに BLOB のダウンロードの進行状況が表示されます。
  
その種類のファイルの既定のエディターで BLOB が開きます。 オペレーティング システムによってファイルの種類が認識された場合は、ローカルにインストールされているアプリケーションでファイルが開きます。 それ以外の場合は、BLOB のファイルの種類に適したアプリケーションを選択するよう求められます。 BLOB をダウンロードしたときに作成されるローカル ファイルは、読み取り専用としてマークされます。
  
BLOB データはローカルにキャッシュされ、Azure Blob Storage での BLOB の最終更新時刻に照らしてチェックされます。 BLOB が前回のダウンロード後に更新されている場合は、再度ダウンロードされます。 それ以外の場合は、ローカル ディスクから BLOB が読み込まれます。 

既定では、BLOB が一時ディレクトリにダウンロードされます。 特定のディレクトリに BLOB をダウンロードするには、選択した BLOB 名のショートカット メニューを開き、**[名前を付けて保存]** を選択します。 この方法で BLOB を保存すると、BLOB ファイルは開かれず、読み取り/書き込み属性のローカル ファイルが作成されます。

### <a name="to-upload-blobs"></a>BLOB をアップロードするには
* コンテナーを開き、BLOB コンテナー ビューに表示されたら、**[BLOB のアップロード]** をクリックします
  
アップロード対象ファイル (複数可) を選択し、任意の種類のファイルをアップロードできます。 **[Azure のアクティビティ ログ]** ウィンドウにアップロードの進行状況が表示されます。 BLOB データを操作する方法の詳細については、[.NET で Azure Blob Storage を使用する方法](http://go.microsoft.com/fwlink/p/?LinkId=267911)に関する記事をご覧ください。

### <a name="to-view-logs-transferred-to-blobs"></a>BLOB に転送されたログを表示するには
Azure 診断を使用して Azure アプリケーションのデータをログに記録し、ログをストレージ アカウントに転送した場合、Azure によってこれらのログ用に作成されたコンテナーが表示されます。 サーバー エクスプローラーでこれらのログを表示すれば、アプリケーション、特に Azure にデプロイされたアプリケーションの問題を簡単に特定できます。 

Azure 診断の詳細については、「 [Azure 診断を使用したログ データの収集](https://msdn.microsoft.com/library/azure/gg433048.aspx)」を参照してください。

### <a name="to-get-the-url-for-a-blob"></a>BLOB の URL を取得するには
* BLOB のショートカット メニューを開き、**[URL のコピー]** を選択します。

### <a name="to-edit-a-blob"></a>BLOB を編集するには
* BLOB を選択し、**[BLOB を開く]** をクリックします。
  
ファイルは一時的な場所にダウンロードされ、ローカル コンピューター上で開かれます。 変更を行った後は、BLOB を再度アップロードする必要があります。

## <a name="work-with-queue-resources"></a>キュー リソースの操作
ストレージ サービスのキューは、Azure ストレージ アカウント内でホストされています。 これらのキューを使用すると、メッセージを渡すメカニズムによって、クラウド サービス ロールが相互にやり取りしたり、他のサービスとやり取りしたりすることができます。 キューは、クラウド サービス、および外部クライアントの Web サービスからプログラムによってアクセスできます。 キューは、Visual Studio のサーバー エクスプローラーを使用して直接アクセスすることもできます。

キューを使用するクラウド サービスを開発する場合は、コードを開発およびテストするときに、Visual Studio を使用してキューを対話的に作成したり操作したりすることができます。

サーバー エクスプローラーでは、ストレージ アカウント内のキューを表示したり、キューを作成 (または削除) したりすることができるほか、キューを開いてメッセージを表示したり、メッセージをキューに追加したりすることができます。 キューを閲覧用に開くと、個々のメッセージを表示できます。また、左上隅のボタンを使用して、キューに対して次の操作を実行できます。

* キューの表示を更新する
* メッセージをキューに追加します。
* 最上位のメッセージをデキューする
* キュー全体をクリアする

次の画像は、2 つのメッセージを含むキューを示しています。

![キューの表示](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC651470.png)

ストレージ サービスのキューの詳細については、「[.NET を使用して Azure Queue Storage を使用する](http://go.microsoft.com/fwlink/?LinkID=264702)」をご覧ください。 ストレージ サービス キューの Web サービスについては、「 [キュー サービスの概念](http://go.microsoft.com/fwlink/?LinkId=264788)」を参照してください。 Visual Studio を使用して、ストレージ サービス キューにメッセージを送信する方法については、「 [ストレージ サービスのキューにメッセージを送信する](https://msdn.microsoft.com/library/azure/jj649344.aspx)」を参照してください。

> [!NOTE]
> ストレージ サービスのキューは、Azure Service Bus キューとは異なります。 Service Bus キューの詳細については、「[Service Bus のキュー、トピック、サブスクリプション](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-queues-topics-subscriptions)」をご覧ください。
> 
> 

## <a name="work-with-table-resources"></a>テーブル リソースの操作
Azure Table Storage は、大量の構造化データを格納します。 このサービスは、Azure クラウドの内部および外部からの認証された呼び出しを受け付ける NoSQL データストアです。 Azure のテーブルは、構造化された非リレーショナル データを格納するのに最適です。

### <a name="to-create-a-table"></a>テーブルを作成するには
1. Cloud Explorer でストレージ アカウントの **[テーブル]** ノードを選択し、**[テーブルの作成]** を選択します。
2. **[テーブルの作成]** ダイアログ ボックスでテーブルの名前を入力します。

### <a name="to-view-table-data"></a>テーブル データを表示するには
1. Cloud Explorer で **[Azure]** ノードを開き、**[Storage]** ノードを開きます。
2. 目的のストレージ アカウント ノードを開き、 **[テーブル]** ノードを開いて、そのストレージ アカウントのテーブルの一覧を表示します。
3. テーブルのショートカット メニューを開き、**[テーブルの表示]** を選択します。
   
    ![An Azure table in Solution Explorer](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744165.png)

テーブルは、エンティティ (行に表示) とプロパティ (列に表示) で構成されています。 たとえば、次の図はテーブル デザイナーに一覧表示されたエンティティを示しています。

### <a name="to-edit-table-data"></a>テーブル データを編集するには
* テーブル デザイナーで、エンティティ (単一の行) またはプロパティ (単一のセル) のショートカット メニューを開き、**[編集]** を選択します。
   
    ![テーブル エンティティの追加または編集](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC656238.png)
   
単一テーブルのエンティティが、同じ一連のプロパティ (列) を持っている必要はありません。 テーブル データの表示と編集には、次の制限があることに留意してください。
   
* バイナリ データ (`type byte[]`) は、表示することも編集することもできませんが、テーブルに格納することは可能です。
* **PartitionKey** と **RowKey** の値は編集できません。この操作は、Azure の Table Storage ではサポートされていません。
* **Timestamp** という名前のプロパティは作成できません。 Azure Storage サービスでこの名前のプロパティが使用されています。
* **DateTime** 値を入力する場合は、ご使用のコンピューターの地域と言語の設定に適した形式に従う必要があります (たとえば、英語 (米国) の場合は、MM/DD/YYYY HH:MM:SS [AM|PM])。

### <a name="to-add-entities"></a>エンティティを追加するには
1. テーブル デザイナー で **[エンティティの追加]** をクリックします。
   
    ![[エンティティの追加] ボタン](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655336.png)
2. **[エンティティの追加]** ダイアログ ボックスで、**PartitionKey** プロパティと **RowKey** プロパティの値を入力します。
   
    ![[エンティティの追加] ダイアログ ボックス](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655335.png)
   
    値は慎重に入力してください。 ダイアログ ボックスを閉じた後に値を変更することはできません。変更するには、エンティティを削除し、追加し直す必要があります。

### <a name="to-filter-entities"></a>エンティティをフィルター処理するには
クエリ ビルダーを使用する場合、テーブルに表示されるエンティティ セットをカスタマイズすることができます。

1. クエリ ビルダーを開くには、テーブルを閲覧用に開きます。
2. テーブル ビューのツール バーの **[クエリ ビルダー]** をクリックします。
   
    **[クエリ ビルダー]** ダイアログ ボックスが表示されます。 次の図は、クエリ ビルダーで作成しているクエリを示しています。
   
    ![クエリ ビルダー](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC652231.png)
3. クエリの作成を終えたら、ダイアログ ボックスを閉じます。 クエリの結果であるテキスト形式が WCF Data Services フィルターとしてテキスト ボックスに表示されます。
4. クエリを実行するには、緑の三角形のアイコンをクリックします。
   
また、フィルター テキスト ボックスに WCF Data Services フィルター文字列を直接入力することで、テーブル デザイナーに表示されるエンティティ データをフィルター処理することもできます。 この種の文字列は、SQL の WHERE 句に似ていますが、サーバーには HTTP 要求として送信されます。 フィルター文字列を作成する方法の詳細については、「 [テーブル デザイナー用のフィルター文字列の作成](https://msdn.microsoft.com/library/azure/ff683669.aspx)」を参照してください。
   
有効なフィルター文字列の例を次の図に示します。
   
![フィルター文字列](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC655337.png)

## <a name="refresh-storage-data"></a>ストレージ データの更新
サーバー エクスプローラーでストレージ アカウントに接続したり、ストレージ アカウントからデータを取得したりする場合、操作が完了するまでに最大で 1 分かかることがあります。 サーバー エクスプローラーが接続できない場合、操作がタイムアウトする可能性があります。データの取得中、Visual Studio の他の部分では作業を続行できます。 操作に時間がかかりすぎる場合に操作を取り消すには、サーバー エクスプローラー ツール バーの **[更新の中止]** をクリックします。

### <a name="to-refresh-blob-container-data"></a>BLOB コンテナー データを更新するには
* **[ストレージ]** の下の **[BLOB]** ノードを選択し、サーバー エクスプローラー ツール バーの **[最新の情報に更新]** をクリックします。
* 表示されている BLOB の一覧を更新するには、**[実行]** をクリックします。

### <a name="to-refresh-table-data"></a>テーブル データを更新するには
* **[ストレージ]** の下の **[テーブル]** ノードを選択し、サーバー エクスプローラー ツール バーの **[最新の情報に更新]** をクリックします。
* テーブル デザイナーに表示されているエンティティの一覧を更新するには、テーブル デザイナーで **[実行]** をクリックします。

### <a name="to-refresh-queue-data"></a>キュー データを更新するには
* **[ストレージ]** の下の **[キュー]** ノードを選択し、サーバー エクスプローラー ツール バーの **[最新の情報に更新]** をクリックします。

### <a name="to-refresh-all-items-in-a-storage-account"></a>ストレージ アカウントのすべてのアイテムを更新するには
* アカウント名を選択し、サーバー エクスプローラーのツール バーの **[最新の情報に更新]** をクリックします。

## <a name="add-storage-accounts-by-using-server-explorer"></a>サーバー エクスプローラーを使用してストレージ アカウントを追加する
サーバー エクスプローラーを使用してストレージ アカウントを追加する方法は 2 つあります。 Azure サブスクリプションでストレージ アカウントを作成することも、既存のストレージ アカウントを接続することもできます。

### <a name="to-create-a-storage-account-by-using-server-explorer"></a>サーバー エクスプローラーを使用してストレージ アカウントを作成するには
1. サーバー エクスプローラーで **[ストレージ]** ノードのショートカット メニューを開き、**[ストレージ アカウントの作成]** を選択します。
   
2. **[ストレージ アカウントの作成]** ダイアログ ボックスで、次の情報を選択または入力します。
   
   * ストレージ アカウントを追加する Azure サブスクリプション。
   * 新しいストレージ アカウントに使用する名前。
   * リージョンまたはアフィニティ グループ (米国西部や東アジアなど)。
   * ローカル冗長など、ストレージ アカウントで使用するレプリケーションの種類。

   ![Azure のストレージ アカウントの作成](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC744166.png)

3. **[作成]**を選択します。
   
新しいストレージ アカウントがソリューション エクスプローラーの **[Storage]** ボックスの一覧に表示されます。

### <a name="to-attach-an-existing-storage-account-by-using-server-explorer"></a>サーバー エクスプローラーを使用して既存のストレージ アカウントを接続するには
1. サーバー エクスプローラーで、[Azure] の **[ストレージ]** ノードのショートカット メニューを開き、**[外部ストレージのアタッチ]** を選択します。
   
    ![Adding an existing storage account](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766039.png)
2. **[ストレージ アカウントの作成]** ダイアログ ボックスで、次の情報を選択または入力します。
   
   * 接続する既存のストレージ アカウントの名前。
   * 選択したストレージ アカウントのキー。 この値は通常、ストレージ アカウントを選択したときに提供されます。 ストレージ アカウント キーを Visual Studio に記憶させる場合は、**[アカウント キーを記憶する]** チェック ボックスをオンにします。
   * HTTP、HTTPS、カスタム エンドポイントなど、ストレージ アカウントへの接続に使用するプロトコル。 カスタム エンドポイントの詳細については、[接続文字列の構成方法](https://msdn.microsoft.com/library/azure/ee758697.aspx)に関する記事をご覧ください。

### <a name="to-view-the-secondary-endpoints"></a>セカンダリ エンドポイントを表示するには
**[読み取りアクセス Geo 冗長]** レプリケーション オプションを使用してストレージ アカウントを作成した場合は、そのセカンダリ エンドポイントを表示できます。

* アカウント名のショートカット メニューを開き、**[プロパティ]** を選択します。
  
![Storage secondary endpoints](./media/vs-azure-tools-storage-resources-server-explorer-browse-manage/IC766040.png)

### <a name="to-remove-a-storage-account-from-server-explorer"></a>サーバー エクスプローラーからストレージ アカウントを削除するには
* サーバー エクスプローラーでアカウント名のショートカット メニューを開き、**[削除]** を選択します。 

ストレージ アカウントを削除すると、そのアカウントに関して保存されていたキー情報もすべて削除されます。
  
> [!NOTE]
> サーバー エクスプローラーからストレージ アカウントを削除しても、ストレージ アカウントやアカウントに含まれているデータには影響しません。 サーバー エクスプローラーからの参照が削除されるだけです。 ストレージ アカウントを完全に削除するには、 [Azure クラシック ポータル](http://go.microsoft.com/fwlink/?LinkID=213885)を使用します。
> 
> 

## <a name="next-steps"></a>次のステップ
Azure Storage サービスの使用方法の詳細については、[Azure Storage サービスへのアクセス](https://msdn.microsoft.com/library/azure/ee405490.aspx)に関するページをご覧ください。

