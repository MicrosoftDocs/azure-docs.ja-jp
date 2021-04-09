---
title: Data Lake Storage Gen1 リソースを管理する - Azure Storage Explorer
description: Azure Data Lake Storage Gen1 のデータやリソースに Azure Storage Explorer からアクセスして管理する方法について説明します
author: jejiang
ms.service: data-lake-store
ms.topic: how-to
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: 7f251e6ba2d94c0fcede3387ac12461951de40f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92108747"
---
# <a name="manage-azure-data-lake-storage-gen1-resources-by-using-storage-explorer"></a>Storage Explorer を使用して Azure Data Lake Storage Gen1 リソースを管理する

[Azure Data Lake Storage Gen1](./data-lake-store-overview.md) は、テキストやバイナリ データなどの大量の非構造化データを格納するためのサービスです。 そのデータには、HTTP または HTTPS を介してどこからでもアクセスすることができます。 Azure Storage Explorer 上で Data Lake Storage Gen1 を使用すると、BLOB やキューなど、他の Azure エンティティと共に、Data Lake Storage Gen1 のデータおよびリソースにアクセスし、管理できます。 さまざまな Azure エンティティを、同じツールを使用して 1 か所で管理できるようになりました。

もう 1 つの利点は、Data Lake Storage Gen1 データを管理するためにサブスクリプションのアクセス許可が不要であることです。 Storage Explorer では、アクセス許可が付与されていれば、 **[Local and Attached]\(ローカルで接続済み\)** ノードに Data Lake Storage Gen1 パスをアタッチできます。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次の前提条件を満たす必要があります。

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial)に関するページを参照してください。
* Data Lake Storage Gen1 アカウント。 これを作成する手順については、[Azure Data Lake Storage Gen1 の使用開始](./data-lake-store-get-started-portal.md)に関する記事をご覧ください。

## <a name="install-storage-explorer"></a>Storage Explorer のインストール

Azure Storage Explorer の最新版は、[製品 Web ページ](https://azure.microsoft.com/features/storage-explorer/)からインストールできます。 インストールは、Windows、Linux、Mac の各バージョンに対応しています。

## <a name="connect-to-an-azure-subscription"></a>Azure サブスクリプションに接続する

1. Storage Explorer の左側にあるプラグイン アイコンを選択します。

   ![プラグイン アイコン](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)

1. **[Add an Azure Account]\(Azure アカウントの追加\)** を選択し、 **[サインイン]** を選択します。

   ![[Azure Storage へ接続] ダイアログ ボックス](./media/data-lake-store-in-storage-explorer/connect-to-azure-subscription.png)

1. **[アカウントにサインイン]** ダイアログ ボックスで Azure の資格情報を入力します。

    ![Azure のサインイン ダイアログ ボックス](./media/data-lake-store-in-storage-explorer/sign-in.png)

1. 一覧からサブスクリプションを選択し、 **[適用]** を選択します。

    ![サブスクリプション情報と [適用] ボタン](./media/data-lake-store-in-storage-explorer/apply-subscription.png)

    **[エクスプローラー]** ウィンドウが更新され、選択したサブスクリプション内のアカウントが表示されます。

    ![アカウントの一覧](./media/data-lake-store-in-storage-explorer/account-list.png)

Data Lake Storage Gen1 から Azure サブスクリプションに正常に接続されています。

## <a name="connect-to-data-lake-storage-gen1"></a>Data Lake Storage Gen1 への接続

ご利用のサブスクリプションに存在しないリソースであっても、そのリソースの URI を支給してもらえば、アクセスすることができます。 サインイン後、その URI を使って Data Lake Storage Gen1 に接続することができます。

1. Storage Explorer を開きます。
2. 左側のウィンドウで **[Local and Attached]\(ローカルで接続済み\)** を展開します。
3. **[Data Lake Store]** を右クリックし、 **[Connect to Data Lake Store]\(Data Lake Store に接続する\)** を選択します。

      ![ショートカット メニューの [Connect to Data Lake Store]\(Data Lake Store に接続する\)](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. URI を入力します。 入力した URL の場所にツールが移動します。

      ![[Connect to Data Lake Store]\(Data Lake Store に接続する\) ダイアログ ボックスと URI を入力するためのテキスト ボックス](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![Data Lake Storage Gen1 に接続した結果](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-a-data-lake-storage-gen1-accounts-contents"></a>Data Lake Storage Gen1 アカウントの内容の表示

Data Lake Storage Gen1 アカウントのリソースには、フォルダーとファイルが含まれます。

次の手順では、Storage Explorer 内で Data Lake Storage Gen1 アカウントの内容を表示する方法を説明します。

1. Storage Explorer を開きます。
2. 左側のウィンドウで、表示する Data Lake Storage Gen1 アカウントが含まれているサブスクリプションを展開します。
3. **[Data Lake Store]** を展開します。
4. 表示する Data Lake Storage Gen1 アカウントのノードを右クリックし、 **[開く]** を選択します。 Data Lake Storage Gen1 アカウントをダブルクリックして開くこともできます。

   メイン ウィンドウには、Data Lake Storage Gen1 アカウントの内容が表示されます。

   ![メイン ウィンドウとフォルダーの一覧](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png)

## <a name="manage-resources-in-data-lake-storage-gen1"></a>Data Lake Storage Gen1 のリソースを管理する

次の操作を実行することで、Data Lake Storage Gen1 リソースを管理できます。

* 複数の Data Lake Storage Gen1 アカウントを対象に Data Lake Storage Gen1 のリソースを閲覧する。  
* 接続文字列を使用して、直接 Data Lake Storage Gen1 に接続し、管理する。
* 他のユーザーが ACL を使用して共有した Data Lake Storage Gen1 リソースを **[Local and Attached]\(ローカルで接続済み\)** に表示する。
* ファイルとフォルダーの CRUD 操作を実行する (再帰的フォルダーとファイルの複数選択に対応)。
* フォルダーをドラッグ アンド ドロップして、クイック アクセスの最近使用した場所に追加する。 この操作は、デスクトップのエクスプローラーの操作とよく似ています。
* Storage Explorer から 1 回のクリックで、Data Lake Storage Gen1 のハイパーリンクをコピーして開く。
* 右下のウィンドウにアクティビティ ログを表示し、アクティビティの状態を確認する。
* フォルダーの統計とファイルのプロパティを表示する。

## <a name="manage-resources-in-azure-storage-explorer"></a>Azure Storage Explorer でリソースを管理する

Data Lake Storage Gen1 アカウントを作成した後で、次の操作を実行できます。

* ローカル コンピューターでのフォルダーやファイルのアップロードとダウンロード、リソースの表示
* **[クイック アクセス]** のピン留め、新しいフォルダーの作成、URL のコピー、すべて選択
* コピー、貼り付け、名前の変更、削除、フォルダーの統計情報の取得、最新の情報への更新

次の項目では、Data Lake Storage Gen1 アカウント内でリソースを管理する方法を示します。 実行したいタスクの手順に従ってください。

### <a name="upload-files"></a>ファイルをアップロードする

1. メイン ウィンドウのツール バーで **[アップロード]** を選択し、ドロップダウン メニューで **[ファイルのアップロード]** を選択します。

   ![[ファイルのアップロード] メニュー項目](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-menu.png)

2. **[Select files to upload]\(アップロードするファイルを選択\)** ダイアログ ボックスで、アップロードするファイルを選択します。

   ![ファイルをアップロードするためのダイアログ ボックス](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-dialog.png)

3. **[開く]** を選択して、アップロードを開始します。

### <a name="upload-a-folder"></a>フォルダーをアップロードする

1. メイン ウィンドウのツール バーで **[アップロード]** を選択し、ドロップダウン メニューで **[フォルダーをアップロード]** を選択します。

   ![[フォルダをアップロード] メニュー項目](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-menu.png)

2. **[Select folder to upload]\(アップロードするフォルダーを選択\)** ダイアログ ボックスで、アップロードするファイルを選択します。 続けて **[フォルダーの選択]** をクリックします。

   ![フォルダーをアップロードするためのダイアログ ボックス](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-dialog.png)

   アップロードが開始されます。

   ![アップロードが進行中のダイアログ ボックス](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-drag.png)

> [!NOTE]
> ローカル コンピューター上のフォルダーとファイルは、アップロードを開始するために直接ドラッグできます。

### <a name="download-folders-or-files-to-your-local-computer"></a>フォルダーまたはファイルをローカル コンピューターにダウンロードする

1. ダウンロードするフォルダーまたはファイルを選択します。
2. メイン ウィンドウのツール バーで、 **[ダウンロード]** を選択します。
3. **[Select a folder to save the downloaded files into]\(ダウンロードしたファイルを保存するフォルダーの選択\)** ダイアログ ボックスで、場所と名前を指定します。
4. **[保存]** を選択します。

### <a name="open-a-folder-or-file-from-your-local-computer"></a>ローカル コンピューターからフォルダーまたはファイルを開く

1. 開くフォルダーまたはファイルを選択します。
2. メイン ウィンドウのツール バーで、 **[開く]** を選択します。 選択したフォルダーまたはファイルを右クリックし、ショートカット メニューの **[開く]** を選択します。

ファイルがダウンロードされ、その基になるファイルの種類に関連付けられているアプリケーションで開かれます。 または、メイン ウィンドウでフォルダーが開きます。

![開かれたファイル](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-open.png)

### <a name="copy-folders-or-files-to-the-clipboard"></a>フォルダーまたはファイルをクリップボードにコピーする

1. コピーするフォルダーまたはファイルを選択します。
2. メイン ウィンドウのツール バーで、 **[コピー]** を選択します。 選択したフォルダーまたはファイルを右クリックし、ショートカット メニューの **[コピー]** を選択します。
3. 左側のウィンドウで別の Data Lake Storage Gen1 アカウントを参照し、それをダブルクリックして、メイン ウィンドウに表示します。
4. メイン ウィンドウのツール バーで **[貼り付け]** を選択して、コピーを作成します。 または、コピー先のショートカット メニューで **[貼り付け]** を選択します。

![フォルダーをコピーするための選択](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

> [!NOTE]
> 異なる種類のストレージ間でのコピー/貼り付け操作はサポートされていません。 Data Lake Storage Gen1 のフォルダーまたはファイルをコピーして、それらを別の Data Lake Storage Gen1 アカウントに貼り付けることはできます。 一方、Data Lake Storage Gen1 のフォルダーやファイルをコピーして、Azure Blob Storage に貼り付けることも、その逆の操作を行うことも "*できません*"。
>
> コピー/貼り付け操作は、フォルダーまたはファイルをローカル コンピューターにダウンロードして、目的の場所にアップロードすることで動作します。 ツールがバックエンドでアクションを実行することは "*ありません*"。 大きなファイルのコピー/貼り付け操作には時間がかかります。 パフォーマンスの高いファイルのコピーと移動の最適化が行われています。

### <a name="delete-folders-or-files"></a>フォルダーまたはファイルを削除する

1. 削除するフォルダーまたはファイルを選択します。
2. メイン ウィンドウのツール バーで、 **[削除]** を選択します。 選択したフォルダーまたはファイルを右クリックし、ショートカット メニューの **[削除]** を選択します。
3. 確認のダイアログ ボックスで **[はい]** を選択します。

![フォルダーを削除するための選択](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-delete.png)

### <a name="pin-to-quick-access"></a>クイック アクセスにピン留め

1. ピン留めするフォルダーを選択します。
2. メイン ウィンドウのツール バーで **[クイック アクセスにピン留め]** を選択します。

   左側のウィンドウで、選択したフォルダーが **[クイック アクセス]** ノードに追加されます。

   ![[クイック アクセス] にフォルダーをピン留めするための選択](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)

**[クイック アクセス]** ノードにフォルダーをピン留めすると、リソースに簡単にアクセスできます。

### <a name="use-deep-links"></a>ディープ リンクを使用する

URL がある場合は、エクスプローラーまたはブラウザーのアドレス パスに URL を入力してください。 Storage Explorer.exe が自動的に実行されて、その URL の場所に移動します。

![エクスプローラーのディープ リンク](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)

## <a name="next-steps"></a>次のステップ

* [最新の Storage Explorer のリリース ノートとビデオ](https://www.storageexplorer.com)を確認します。
* [Azure Storage Explorer で Azure Cosmos DB を管理する](../cosmos-db/storage-explorer.md)方法を学習します。
* [Storage Explorer の概要](../vs-azure-tools-storage-manage-with-storage-explorer.md)を参照します。
* [Azure Data Lake Storage Gen1 の使用を開始する](./data-lake-store-overview.md)。
* [Azure Storage Explorer で Azure Cosmos DB を使用する方法に関する YouTube ビデオ](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be)を視聴します。