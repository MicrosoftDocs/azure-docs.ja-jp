---
title: Storage Explorer を使用して Azure Blob Storage リソースを管理する | Microsoft Docs
description: Storage Explorer を使用して Azure BLOB コンテナーと BLOB を管理する
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: 2f09e545-ec94-4d89-b96c-14783cc9d7a9
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2019
ms.author: cawa
ms.openlocfilehash: 56c20c995a95058b5039b7268c7b7b1426e900fa
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442980"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>Storage Explorer を使用して Azure Blob Storage リソースを管理する

## <a name="overview"></a>概要

[Azure Blob Storage](storage/blobs/storage-dotnet-how-to-use-blobs.md) は、HTTP または HTTPS 経由で世界中のどこからでもアクセスできるテキストやバイナリ データなど、大量の非構造化データを格納するためのサービスです。
BLOB ストレージを使用すると、データを一般に公開することも、アプリケーション データを公開せずに格納することもできます。 この記事では、Storage Explorer を使用して BLOB コンテナーおよび BLOB を操作する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のことが必要になります。

* [Storage Explorer をダウンロードしてインストールする](https://www.storageexplorer.com)
* [Azure ストレージ アカウントまたはサービスに接続する](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>BLOB コンテナーを作成する

すべての BLOB は、BLOB コンテナー内にある必要があります。BLOB コンテナーは、BLOB の論理的なグループにすぎません。 アカウントに含めることができるコンテナーの数には制限がなく、各コンテナーに格納できる BLOB の数にも制限はありません。

次の手順では、Storage Explorer 内に BLOB コンテナーを作成する方法を説明します。

1. ストレージ エクスプローラーを開きます。
2. 左側のウィンドウで、BLOB コンテナーを作成するストレージ アカウントを展開します。
3. **[BLOB コンテナー]** を右クリックし、コンテキスト メニューの **[BLOB コンテナーの作成]** を選択します。

   ![Create blob containers context menu][0]
4. **[BLOB コンテナー]** フォルダーの下に、テキスト ボックスが表示されます。 BLOB コンテナーの名前を入力します。 BLOB コンテナーの名前付けに関する規則と制限については、「[コンテナーを作成する](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container)」を参照してください。

   ![Create Blob Containers text box][1]
5. 作業を終えたら、**Enter** キーを押して BLOB コンテナーを作成するか、**Esc** キーを押して取り消します。 BLOB コンテナーは、正常に作成されると、選択されたストレージ アカウントの **[BLOB コンテナー]** フォルダーの下に表示されます。

   ![Blob Container created][2]

## <a name="view-a-blob-containers-contents"></a>BLOB コンテナーの内容を表示する

BLOB コンテナーには、BLOB とフォルダーが含まれます (フォルダーにも BLOB を含めることができます)。

次の手順では、Storage Explorer 内で BLOB コンテナーの内容を表示する方法を説明します。

1. ストレージ エクスプローラーを開きます。
2. 左側のウィンドウで、表示する BLOB コンテナーが含まれているストレージ アカウントを展開します。
3. ストレージ アカウントの **[BLOB コンテナー]** を展開します。
4. 表示する BLOB コンテナーを右クリックし、コンテキスト メニューの **[BLOB コンテナー エディターを開く]** を選択します。
   表示する BLOB コンテナーをダブルクリックすることもできます。

   ![Open blob container editor context menu][19]
5. メイン ウィンドウに、BLOB コンテナーの内容が表示されます。

   ![Blob container editor][3]

## <a name="delete-a-blob-container"></a>BLOB コンテナーを削除する

BLOB コンテナーは、必要に応じて、簡単に作成および削除できます (個々の BLOB を削除する方法については、「[BLOB コンテナー内の BLOB を管理する](#managing-blobs-in-a-blob-container)」セクションをご覧ください)。

次の手順では、Storage Explorer 内の BLOB コンテナーを削除する方法を説明します。

1. ストレージ エクスプローラーを開きます。
2. 左側のウィンドウで、表示する BLOB コンテナーが含まれているストレージ アカウントを展開します。
3. ストレージ アカウントの **[BLOB コンテナー]** を展開します。
4. 削除する BLOB コンテナーを右クリックし、コンテキスト メニューの **[削除]** を選択します。
   また、 **Del** キーを押して、現在選択されている BLOB コンテナーを削除することもできます。

   ![Delete blob container context menu][4]
5. 確認のダイアログで **[はい]** を選択します。

   ![Delete blob Container confirmation][5]

## <a name="copy-a-blob-container"></a>BLOB コンテナーをコピーする

Storage Explorer を使用すると、BLOB コンテナーをクリップボードにコピーし、その BLOB コンテナーを別のストレージ アカウントに貼り付けることができます (個々の BLOB をコピーする方法については、「[BLOB コンテナー内の BLOB を管理する](#managing-blobs-in-a-blob-container)」セクションをご覧ください)。

次の手順では、あるストレージ アカウントから別のストレージ アカウントに BLOB コンテナーをコピーする方法を説明します。

1. ストレージ エクスプローラーを開きます。
2. 左側のウィンドウで、コピーする BLOB コンテナーが含まれているストレージ アカウントを展開します。
3. ストレージ アカウントの **[BLOB コンテナー]** を展開します。
4. コピーする BLOB コンテナーを右クリックし、コンテキスト メニューの **[Copy Blob Container (BLOB コンテナーのコピー)]** を選択します。

   ![Copy blob container context menu][6]
5. BLOB コンテナーの貼り付け先の "ターゲット" ストレージ アカウントを右クリックし、コンテキスト メニューの **[Paste Blob Container (BLOB コンテナーの貼り付け)]** を選択します。

   ![Paste blob container context menu][7]

## <a name="get-the-sas-for-a-blob-container"></a>BLOB コンテナーの SAS を取得する

[shared access signature (SAS)](storage/common/storage-dotnet-shared-access-signature-part-1.md) を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。
つまり、ストレージ アカウントのオブジェクトへの制限付きアクセス許可を、期間とアクセス許可セットを指定してクライアントに付与できます。また、アカウント アクセス キーを共有する必要はありません。

次の手順では、BLOB コンテナーの SAS を作成する方法を説明します。

1. ストレージ エクスプローラーを開きます。
2. 左側のウィンドウで、SAS を取得する BLOB コンテナーが含まれているストレージ アカウントを展開します。
3. ストレージ アカウントの **[BLOB コンテナー]** を展開します。
4. 目的の BLOB コンテナーを右クリックし、コンテキスト メニューの **[Get Shared Access Signature (Shared Access Signature の取得)]** を選択します。

   ![Get SAS context menu][8]
5. **[Shared Access Signature]** ダイアログで、リソースのポリシー、開始日と有効期限日、タイム ゾーン、アクセス レベルを指定します。

   ![Get SAS options][9]
6. SAS オプションの指定が完了したら、 **[作成]** を選択します。
7. 2 番目の **[Shared Access Signature]** ダイアログが表示されます。このダイアログには、BLOB コンテナーと共に、ストレージ リソースへのアクセスに使用できる URL とクエリ文字列が表示されます。
   クリップボードにコピーする URL の横にある **[コピー]** を選択します。

   ![Copy SAS URLs][10]
8. 完了したら、 **[閉じる]** を選択します。

## <a name="manage-access-policies-for-a-blob-container"></a>BLOB コンテナーのアクセス ポリシーを管理する

次の手順では、BLOB コンテナーのアクセス ポリシーを管理 (追加と削除) する方法を説明します。

1. ストレージ エクスプローラーを開きます。
2. 左側のウィンドウで、アクセス ポリシーを管理する BLOB コンテナーが含まれているストレージ アカウントを展開します。
3. ストレージ アカウントの **[BLOB コンテナー]** を展開します。
4. 目的の BLOB コンテナーを選択し、コンテキスト メニューの **[Manage Access Policies (アクセス ポリシーの管理)]** を選択します。

   ![Manage access policies context menu][11]
5. **[アクセス ポリシー]** ダイアログには、選択した BLOB コンテナー用に既に作成されているアクセス ポリシーの一覧が表示されます。

   ![Access Policy options][12]
6. アクセス ポリシーの管理タスクに応じて、以下の手順に従います。

   * **新しいアクセス ポリシーを追加する** - **[追加]** を選択します。 生成後、 **[アクセス ポリシー]** ダイアログには、新しく追加されたアクセス ポリシー (既定の設定) が表示されます。
   * **アクセス ポリシーを編集する** - 必要な編集を行い、 **[保存]** を選択します。
   * **アクセス ポリシーを削除する** - 削除するアクセス ポリシーの横にある **[削除]** を選択します。

## <a name="set-the-public-access-level-for-a-blob-container"></a>BLOB コンテナーのパブリック アクセス レベルを設定する

既定では、すべての BLOB コンテナーが "パブリック アクセスなし" に設定されています。

次の手順は、BLOB コンテナーのパブリック アクセス レベルを指定する方法を説明します。

1. ストレージ エクスプローラーを開きます。
2. 左側のウィンドウで、アクセス ポリシーを管理する BLOB コンテナーが含まれているストレージ アカウントを展開します。
3. ストレージ アカウントの **[BLOB コンテナー]** を展開します。
4. 目的の BLOB コンテナーを選択し、コンテキスト メニューの **[Set Public Access Level (パブリック アクセス レベルの設定)]** を選択します。

   ![Set public access level context menu][13]
5. **[Set Container Public Access Level (コンテナーのパブリック アクセス レベルの設定)]** ダイアログで、目的のアクセス レベルを指定します。

   ![Set public access level options][14]
6. **[適用]** を選択します。

## <a name="managing-blobs-in-a-blob-container"></a>BLOB コンテナー内の BLOB を管理する

BLOB コンテナーを作成したら、その BLOB コンテナーに BLOB をアップロードする、ローカル コンピューターに BLOB をダウンロードする、ローカル コンピューターで BLOB を開くなどの操作が可能になります。

次の手順では、BLOB コンテナー内の BLOB (およびフォルダー) を管理する方法を説明します。

1. ストレージ エクスプローラーを開きます。
2. 左側のウィンドウで、管理する BLOB コンテナーが含まれているストレージ アカウントを展開します。
3. ストレージ アカウントの **[BLOB コンテナー]** を展開します。
4. 表示する BLOB コンテナーをダブルクリックします。
5. メイン ウィンドウに、BLOB コンテナーの内容が表示されます。

   ![View blob container][3]
6. メイン ウィンドウに、BLOB コンテナーの内容が表示されます。
7. 実行するタスクに応じて、以下の手順に従います。

   * **BLOB コンテナーにファイルをアップロードする**

     1. メイン ウィンドウのツール バーで **[アップロード]** を選択し、ドロップダウン メニューで **[Upload Files (ファイルのアップロード)]** を選択します。

        ![Upload files menu][15]
     2. **[Upload Files (ファイルのアップロード)]** ダイアログで、 **[ファイル]** ボックスの右側にある省略記号 ( **[...]** ) ボタンをクリックし、アップロードするファイルを選択します。

        ![Upload files options][16]
     3. **[BLOB の種類]** に種類を指定します。 詳細については、「[コンテナーを作成する](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container)」を参照してください。
     4. 必要に応じて、選択したファイルのアップロード先となるターゲット フォルダーを指定します。 ターゲット フォルダーが存在しない場合は、作成されます。
     5. **[アップロード]** を選択します。
   * **BLOB コンテナーにフォルダーをアップロードする**

     1. メイン ウィンドウのツール バーで **[アップロード]** を選択し、ドロップダウン メニューで **[Upload Folder (フォルダーのアップロード)]** を選択します。

        ![Upload folder menu][17]
     2. **[Upload Folder (フォルダーのアップロード)]** ダイアログで、 **[フォルダー]** ボックスの右側にある省略記号 ( **[...]** ) ボタンをクリックし、アップロードする内容を含むフォルダーを選択します。

        ![Upload folder options][18]
     3. **[BLOB の種類]** に種類を指定します。 詳細については、「[コンテナーを作成する](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container)」を参照してください。
     4. 必要に応じて、選択したフォルダーの内容のアップロード先となるターゲット フォルダーを指定します。 ターゲット フォルダーが存在しない場合は、作成されます。
     5. **[アップロード]** を選択します。
   * **ローカル コンピューターに BLOB をダウンロードする**

     1. ダウンロードする BLOB を選択します。
     2. メイン ウィンドウのツール バーで、 **[ダウンロード]** を選択します。
     3. **[Specify where to save the downloaded blob (ダウンロードした BLOB を保存する場所の指定)]** ダイアログで、BLOB のダウンロード先の場所と、その場所に付ける名前を指定します。  
     4. **[保存]** を選択します。
   * **ローカル コンピューターで BLOB を開く**

     1. 開く BLOB を選択します。
     2. メイン ウィンドウのツール バーで、 **[開く]** を選択します。
     3. BLOB がダウンロードされ、BLOB の基になるファイルの種類に関連付けられているアプリケーションを使用して開かれます。
   * **BLOB をクリップボードにコピーする**

     1. コピーする BLOB を選択します。
     2. メイン ウィンドウのツール バーで、 **[コピー]** を選択します。
     3. 左側のウィンドウで別の BLOB コンテナーに移動し、それをダブルクリックして、メイン ウィンドウに表示します。
     4. メイン ウィンドウのツール バーで **[貼り付け]** を選択して、BLOB のコピーを作成します。
   * **BLOB を削除する**

     1. 削除する BLOB を選択します。
     2. メイン ウィンドウのツール バーで、 **[削除]** を選択します。
     3. 確認のダイアログで **[はい]** を選択します。

## <a name="next-steps"></a>次の手順

* [最新の Storage Explorer のリリース ノートとビデオ](https://www.storageexplorer.com)を確認します。
* [Azure BLOB、テーブル、キュー、ファイルを使ってアプリケーションを作成する](https://azure.microsoft.com/documentation/services/storage/)方法について学習します。

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png
