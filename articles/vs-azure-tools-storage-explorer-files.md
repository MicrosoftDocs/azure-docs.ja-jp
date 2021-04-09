---
title: Azure File Storage での Storage Explorer の使用 | Microsoft Docs
description: Storage Explorer を使用してファイル共有やファイルを操作する方法について説明します。
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/09/2017
ms.author: cawa
ms.openlocfilehash: 84f6473c25a5be11eeda7cd2b311d93a7226a78c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96488393"
---
# <a name="using-storage-explorer-with-azure-file-storage"></a>Azure File Storage での Storage Explorer の使用

Azure File Storage は、標準のサーバー メッセージ ブロック (SMB) プロトコルを使用してクラウドでファイル共有を提供するサービスです。 SMB 2.1 と SMB 3.0 の両方がサポートされます。 Azure File Storage を使用すると、コストがかかる書き換えを行わずに、ファイル共有に依存しているレガシ アプリケーションをすばやく Azure に移行することができます。 File Storage を使用すると、データを一般に公開することも、アプリケーション データを公開せずに格納することもできます。 この記事では、Storage Explorer を使用してファイル共有とファイルを操作する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のことが必要になります。

- [Storage Explorer をダウンロードしてインストールする](https://www.storageexplorer.com/)

- [Azure ストレージ アカウントまたはサービスに接続する](./vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-file-share"></a>ファイル共有の作成

すべてのファイルは、ファイル共有 (ファイルをわかりやすく論理的にまとめたもの) に存在している必要があります。 アカウントに含めることができるファイル共有の数には制限がなく、各共有に格納できるファイルの数にも制限はありません。

次の手順では、Storage Explorer 内にファイル共有を作成する方法を説明します。

1. ストレージ エクスプローラーを開きます。

1. 左側のウィンドウで、ファイル共有を作成するストレージ アカウントを展開します。

1. **[ファイル共有]** を右クリックし、コンテキスト メニューから **[ファイル共有の作成]** を選択します。

    ![ファイル共有の作成](media/vs-azure-tools-storage-explorer-files/image1.png)

1. **[ファイル共有]** フォルダーの下に、テキスト ボックスが表示されます。 ファイル共有の名前を入力します。 ファイル共有の名前付けに関する規則と制限の一覧については、[共有の名前付け規則](./storage/blobs/storage-quickstart-blobs-dotnet.md)に関するセクションをご覧ください。

    ![共有の名前付け](media/vs-azure-tools-storage-explorer-files/image2.png)

1. 作業を終えたら、**Enter** キーを押してファイル共有を作成するか、**Esc** キーを押して取り消します。 ファイル共有は、正常に作成されると、選択されたストレージ アカウントの **[ファイル共有]** フォルダーの下に表示されます。

    ![新しい共有](media/vs-azure-tools-storage-explorer-files/image3.png)

## <a name="view-a-file-shares-contents"></a>ファイル共有の内容の表示

ファイル共有には、ファイルとフォルダーが存在し、さらにそのフォルダーにファイルが存在することもあります。

次の手順では、Storage Explorer 内でファイル共有の内容を表示する方法を説明します。

1. ストレージ エクスプローラーを開きます。

1. 左側のウィンドウで、表示するファイル共有が含まれているストレージ アカウントを展開します。

1. ストレージ アカウントの **[ファイル共有]** を展開します。

1. 表示するファイル共有を右クリックし、コンテキスト メニューの **[開く]** を選択します。 表示するファイル共有をダブルクリックすることもできます。

    ![共有を開く](media/vs-azure-tools-storage-explorer-files/image4.png)

1. ファイル共有の内容がメイン ウィンドウに表示されます。
    
    ![Storage Explorer のファイル共有のメイン ウィンドウのスクリーンショット。共有の内容を確認できます。](media/vs-azure-tools-storage-explorer-files/image5.png)

## <a name="delete-a-file-share"></a>ファイル共有の削除

ファイル共有は、必要に応じて簡単に作成したり削除したりすることができます (個々のファイルを削除する方法については、[ファイル共有内のファイル管理](./vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container)に関するセクションをご覧ください)。

次の手順では、Storage Explorer からファイル共有を削除する方法を説明します。

1. ストレージ エクスプローラーを開きます。

1. 左側のウィンドウで、表示するファイル共有が含まれているストレージ アカウントを展開します。

1. ストレージ アカウントの **[ファイル共有]** を展開します。

1. 削除するファイル共有を右クリックし、コンテキスト メニューの **[削除]** を選択します。 また、**Delete** キーを押して、現在選択されているファイル共有を削除することもできます。

    ![削除](media/vs-azure-tools-storage-explorer-files/image6.png)

1. 確認のダイアログで **[はい]** を選択します。
    
    ![確認のダイアログ](media/vs-azure-tools-storage-explorer-files/image7.png)

## <a name="copy-a-file-share"></a>ファイル共有のコピー

Storage Explorer を使用すると、ファイル共有をクリップボードにコピーし、そのファイル共有を別のストレージ アカウントに貼り付けることができます (個々のファイルをコピーする方法については、[ファイル共有内のファイル管理](./vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container)に関するセクションをご覧ください)。

次の手順では、あるストレージ アカウントから別のストレージ アカウントにファイル共有をコピーする方法を説明します。

1. ストレージ エクスプローラーを開きます。

1. 左側のウィンドウで、コピーするファイル共有が含まれているストレージ アカウントを展開します。

1. ストレージ アカウントの **[ファイル共有]** を展開します。

1. コピーするファイル共有を右クリックし、コンテキスト メニューから **[Copy File Share (ファイル共有のコピー)]** を選択します。

    ![ファイル共有のコピー](media/vs-azure-tools-storage-explorer-files/image8.png)

1. ファイル共有の貼り付け先の "ターゲット" ストレージ アカウントを右クリックし、コンテキスト メニューの **[Paste File Share (ファイル共有の貼り付け)]** を選択します。

    ![ファイル共有の貼り付け](media/vs-azure-tools-storage-explorer-files/image9.png)

## <a name="get-the-sas-for-a-file-share"></a>ファイル共有の SAS を取得する

[shared access signature (SAS)](./storage/common/storage-sas-overview.md) を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。 つまり、ストレージ アカウントのオブジェクトへの制限付きアクセス許可を、期間とアクセス許可セットを指定してクライアントに付与できます。また、アカウント アクセス キーを共有する必要はありません。

次の手順では、ファイル共有の SAS を作成する方法を説明します。

1. ストレージ エクスプローラーを開きます。

1. 左側のウィンドウで、SAS を取得するファイル共有が含まれているストレージ アカウントを展開します。

1. ストレージ アカウントの **[ファイル共有]** を展開します。

1. 目的のファイル共有を右クリックし、コンテキスト メニューの **[Get Shared Access Signature (Shared Access Signature の取得)]** を選択します。

    ![Shared Access Signature の取得](media/vs-azure-tools-storage-explorer-files/image10.png)

1. **[Shared Access Signature]** ダイアログで、リソースのポリシー、開始日と有効期限日、タイム ゾーン、アクセス レベルを指定します。

    ![SAS ダイアログ](media/vs-azure-tools-storage-explorer-files/image11.png)

1. SAS オプションの指定が完了したら、 **[作成]** を選択します。

1. 2 番目の **[Shared Access Signature]** ダイアログが表示されます。このダイアログには、ファイル共有と共に、ストレージ リソースへのアクセスに使用できる URL とクエリ文字列が表示されます。 クリップボードにコピーする URL の横にある **[コピー]** を選択します。
    
    ![2 番目の SAS ダイアログ](media/vs-azure-tools-storage-explorer-files/image12.png)

1. 完了したら、 **[閉じる]** を選択します。

## <a name="manage-access-policies-for-a-file-share"></a>ファイル共有のアクセス ポリシー管理

次の手順では、ファイル共有のアクセス ポリシーを管理 (追加と削除) する方法を説明します。 アクセス ポリシーは、決められた期間、ストレージ ファイル リソースにアクセスするために利用者が使用する SAS URL の作成に使用されます。

1. ストレージ エクスプローラーを開きます。

1. 左側のウィンドウで、アクセス ポリシーを管理するファイル共有が含まれているストレージ アカウントを展開します。

1. ストレージ アカウントの **[ファイル共有]** を展開します。

1. 目的のファイル共有を選択し、コンテキスト メニューの **[Manage Access Policies (アクセス ポリシーの管理)]** を選択します。

    ![Manage access policies context menu](media/vs-azure-tools-storage-explorer-files/image13.png)

1. **[アクセス ポリシー]** ダイアログには、選択したファイル共有用に既に作成されているアクセス ポリシーの一覧が表示されます。
    
    ![アクセス ポリシー](media/vs-azure-tools-storage-explorer-files/image14.png)

1. アクセス ポリシーの管理タスクに応じて、以下の手順に従います。
    
    - **新しいアクセス ポリシーを追加する** - **[追加]** を選択します。 生成後、 **[アクセス ポリシー]** ダイアログには、新しく追加されたアクセス ポリシー (既定の設定) が表示されます。

    - **アクセス ポリシーを編集する** - 必要な編集を行い、 **[保存]** を選択します。

    - **アクセス ポリシーを削除する** - 削除するアクセス ポリシーの横にある **[削除]** を選択します。

1. 先ほど作成したアクセス ポリシーを使用して新しい SAS URL を作成します。
    
    ![SAS の取得](media/vs-azure-tools-storage-explorer-files/image15.png)
    
    ![SAS の名前とプロパティ](media/vs-azure-tools-storage-explorer-files/image16.png)

## <a name="managing-files-in-a-file-share"></a>ファイル共有内のファイルの管理

ファイル共有を作成したら、そのファイル共有にファイルをアップロードする、ローカル コンピューターにファイルをダウンロードする、ローカル コンピューターでファイルを開くなどの操作が可能になります。

次の手順では、ファイル共有内のファイル (とフォルダー) を管理する方法を説明します。

1.  ストレージ エクスプローラーを開きます。

1.  左側のウィンドウで、管理するファイル共有が含まれているストレージ アカウントを展開します。

1.  ストレージ アカウントの **[ファイル共有]** を展開します。

1.  表示するファイル共有をダブルクリックします。

1.  ファイル共有の内容がメイン ウィンドウに表示されます。

    ![Storage Explorer のファイル共有 myazurefileshare のメイン ウィンドウのスクリーンショット。共有の内容を確認できます。最初のフォルダーが選択されています。](media/vs-azure-tools-storage-explorer-files/image17.png)

1.  ファイル共有の内容がメイン ウィンドウに表示されます。

1.  実行するタスクに応じて、以下の手順に従います。

    - **ファイル共有にファイルをアップロードする**

        a.  メイン ウィンドウのツール バーで **[アップロード]** を選択し、ドロップダウン メニューで **[Upload Files (ファイルのアップロード)]** を選択します。

        ![ファイルをアップロードする](media/vs-azure-tools-storage-explorer-files/image18.png)
        
        b. **[Upload Files (ファイルのアップロード)]** ダイアログで、 **[ファイル]** ボックスの右側にある省略記号 ( **[...]** ) ボタンをクリックし、アップロードするファイルを選択します。

        ![ファイルの追加](media/vs-azure-tools-storage-explorer-files/image19.png)

        c. **[アップロード]** を選択します。

    - **ファイル共有にフォルダーをアップロードする**
        
        a. メイン ウィンドウのツール バーで **[アップロード]** を選択し、ドロップダウン メニューで **[Upload Folder (フォルダーのアップロード)]** を選択します。

        ![Upload folder menu](media/vs-azure-tools-storage-explorer-files/image20.png)

        b. **[Upload Folder (フォルダーのアップロード)]** ダイアログで、 **[フォルダー]** ボックスの右側にある省略記号 ( **[...]** ) ボタンをクリックし、アップロードする内容を含むフォルダーを選択します。

        c. 必要に応じて、選択したフォルダーの内容のアップロード先となるターゲット フォルダーを指定します。 ターゲット フォルダーが存在しない場合は、作成されます。

        d. **[アップロード]** を選択します。

    - **ローカル コンピューターにファイルをダウンロードする**
        
        a. ダウンロードするファイルを選択します。
        
        b. メイン ウィンドウのツール バーで、 **[ダウンロード]** を選択します。
        
        c. **[Specify where to save the downloaded file (ダウンロードしたファイルを保存する場所の指定)]** ダイアログで、ファイルのダウンロード先の場所と、その場所に付ける名前を指定します。

        d. **[保存]** を選択します。

    - **ローカル コンピューターでファイルを開く**
        
        a.  開くファイルを選択します。
        
        b.  メイン ウィンドウのツール バーで、 **[開く]** を選択します。
        
        c.  ファイルがダウンロードされ、その基になるファイルの種類に関連付けられているアプリケーションを使用して開かれます。

    - **ファイルをクリップボードにコピーする**

        a. コピーするファイルを選択します。

        b. メイン ウィンドウのツール バーで、 **[コピー]** を選択します。

        c. 左側のウィンドウで別のファイル共有に移動し、それをダブルクリックして、メイン ウィンドウに表示します。

        d. メイン ウィンドウのツール バーで **[貼り付け]** を選択して、ファイルのコピーを作成します。

    - **ファイルを削除する**

        a. 削除するファイルを選択します。

        b. メイン ウィンドウのツール バーで、 **[削除]** を選択します。

        c. 確認のダイアログで **[はい]** を選択します。

## <a name="next-steps"></a>次のステップ

- [最新の Storage Explorer のリリース ノートとビデオ](https://www.storageexplorer.com/)を確認します。

- [Azure BLOB、テーブル、キュー、ファイルを使ってアプリケーションを作成する](https://azure.microsoft.com/documentation/services/storage/)方法について学習します。