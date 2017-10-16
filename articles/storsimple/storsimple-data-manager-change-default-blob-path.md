---
title: "BLOB パスを既定値から変更する | Microsoft Docs"
description: "BLOB ファイル パス名を変更するように Azure 関数を設定する方法について説明します (プライベート プレビュー)。"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/16/2017
ms.author: vidarmsft
ms.openlocfilehash: 057d4d7370207859617eb63238bf425bfa6d3e16
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="change-a-blob-path-from-the-default-path-private-preview"></a>BLOB パスを既定のパスから変更する (プライベート プレビュー)

この記事では、既定の BLOB ファイル パス名を変更するように Azure 関数を設定する方法について説明します。 

## <a name="prerequisites"></a>前提条件

リソース グループ内のハイブリッド データ リソースでジョブ定義が正しく構成されていることを確認してください。

## <a name="create-an-azure-function"></a>Azure 関数の作成

Azure 関数を作成するには、次の手順を実行します。

1. [Azure ポータル](http://portal.azure.com/)にアクセスします。

2. 左側のウィンドウの上部にある **[新規]** をクリックします、 

3. **検索** ボックスに「**Function App**」と入力し、Enter キーを押します。

    ![検索ボックスに「Function App」と入力](./media/storsimple-data-manager-change-default-blob-path/goto-function-app-resource.png)

4. **[結果]** 一覧で、**[Function App]** をクリックします。

    ![[結果] 一覧で Function App リソースを選択](./media/storsimple-data-manager-change-default-blob-path/select-function-app-resource.png)

    **[Function App]** ウィンドウが開きます。

5. **Create** をクリックしてください。

    ![[Function App] ウィンドウの [作成] ボタン](./media/storsimple-data-manager-change-default-blob-path/create-new-function-app.png)

6. **[Function App]** の構成ブレードで、次の操作を実行します。

    a. **[アプリ名]** ボックスにアプリの名前を入力します。
    
    b. **[サブスクリプション]** ボックスにサブスクリプションの名前を入力します。

    c. **[リソース グループ]** で、**[新規作成]** をクリックし、リソース グループの名前を入力します。

    d. **[ホスティング プラン]** ボックスに「**Consumption Plan**」と入力します。

    e. **[場所]** ボックスに場所を入力します。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[ストレージ アカウント]** で、既存のストレージ アカウントを選択するか、新しいストレージ アカウントを作成します。 ストレージ アカウントは内部で関数に使用されます。

    ![新しい Function App の構成データを入力する](./media/storsimple-data-manager-change-default-blob-path/enter-new-funcion-app-data.png)

7. **Create** をクリックしてください。  
    Function App が作成されます。

8. 左側のウィンドウで、**[その他のサービス]** をクリックして、次の操作を行います。
    
    a. **[フィルター]** ボックスに「**App services**」と入力します。
    
    b. **[App Services]** をクリックします。 

    ![左側のウィンドウの [その他のサービス] リンク](./media/storsimple-data-manager-change-default-blob-path/more-services.png)

9. App Services の一覧で、Function App の名前をクリックします。  
    Function App のページが開きます。

10. 左側のウィンドウで、**[新しい関数]** をクリックして、次の操作を行います。 

    a. **[言語]** 一覧で、**[C#]** を選択します。
    
    b. テンプレート タイルの配列で、**[QueueTrigger-CSharp]** を選択します。

    c. **[関数名の指定]** ボックスに関数の名前を入力します。

    d. **[キュー名]** ボックスにデータ変換ジョブ定義名を入力します。

    e. **[ストレージ アカウント接続]** で、**[新規]** をクリックし、データ変換ジョブに対応するアカウントを選択します。  
        接続文字列をメモします。 この名前は後で Azure 関数で必要になります。

       ![新しい C# 関数を作成する](./media/storsimple-data-manager-change-default-blob-path/create-new-csharp-function.png)

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **Create** をクリックしてください。  
    **[関数]** ウィンドウが開きます。

11. **[関数]** ウィンドウで、_.csx_ ファイルを実行し、次の操作を行います。

    a. 次のコードを貼り付けます。

    ```
    using System;
    using System.Configuration;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.WindowsAzure.Storage.Queue;
    using Microsoft.WindowsAzure.Storage;
    using System.Collections.Generic;
    using System.Linq;

    public static void Run(QueueItem myQueueItem, TraceWriter log)
    {
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

        string storageAccUriEndswith = "windows.net/";
        string uri = myQueueItem.TargetLocation.Replace("%20", " ");
        log.Info($"Blob Uri: {uri}");

        // Remove storage account uri string
        uri = uri.Substring(uri.IndexOf(storageAccUriEndswith) + storageAccUriEndswith.Length);

        string containerName = uri.Substring(0, uri.IndexOf("/")); 

        // Remove container name string
        uri = uri.Substring(containerName.Length + 1);

        // Current blob path
        string blobName = uri; 

        string volumeName = uri.Substring(containerName.Length + 1);
        volumeName = uri.Substring(0, uri.IndexOf("/"));

        // Remove volume name string
        uri = uri.Substring(volumeName.Length + 1);

        string newContainerName = uri.Substring(0, uri.IndexOf("/")).ToLower();
        string newBlobName = uri.Substring(newContainerName.Length + 1);

        log.Info($"Container name: {containerName}");
        log.Info($"Volume name: {volumeName}");
        log.Info($"New container name: {newContainerName}");

        log.Info($"Blob name: {blobName}");
        log.Info($"New blob name: {newBlobName}");

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Container reference
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlobContainer newContainer = blobClient.GetContainerReference(newContainerName);
        newContainer.CreateIfNotExists();

        if(!container.Exists())
        {
            log.Info($"Container - {containerName} not exists");
            return;
        }

        if(!newContainer.Exists())
        {
            log.Info($"Container - {newContainerName} not exists");
            return;
        }

        CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
        if (!blob.Exists())
        {
            // Skip to copy the blob to new container, if source blob doesn't exist
            log.Info($"The specified blob does not exist.");
            log.Info($"Blob Uri: {blob.Uri}");
            return;
        }

        CloudBlockBlob blobCopy = newContainer.GetBlockBlobReference(newBlobName);
        if (!blobCopy.Exists())
        {
            blobCopy.StartCopy(blob);
            // Delete old blob, after copy to new container
            blob.DeleteIfExists();
            log.Info($"Blob file path renamed completed successfully");
        }
        else
        {
            log.Info($"Blob file path renamed already done");
            // Delete old blob, if already exists.
            blob.DeleteIfExists();
        }
    }

    public class QueueItem
    {
        public string SourceLocation {get;set;}
        public long SizeInBytes {get;set;}
        public string Status {get;set;}
        public string JobID {get;set;}
        public string TargetLocation {get; set;}
    }

    ```

    b. 11 行目の **STORAGE_CONNECTIONNAME** をストレージ アカウント接続に置き換えます (手順 8. の c. を参照)。

    c. 左上にある **[保存]** をクリックします。

    ![関数を保存する](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. 関数を完了するには、次の手順に従ってもう 1 つのファイルを追加します。

    a. **[ファイルの表示]** をクリックします。

       ![[ファイルの表示] リンク](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    b. **[追加]**をクリックします。
    
    c. 「**project.json**」と入力し、Enter キーを押します。
    
    d. **project.json** ファイルに、次のコードを貼り付けます。

    ```
    {
    "frameworks": {
        "net46":{
        "dependencies": {
            "windowsazure.storage": "8.1.1"
        }
        }
    }
    }

    ```

    e. **[Save]**をクリックします。

Azure 関数が作成されました。 この関数は、データ変換ジョブによって新しい BLOB が生成されるたびにトリガーされます。

## <a name="next-steps"></a>次のステップ

[StorSimple Data Manger UI を使用してデータを変換する](storsimple-data-manager-ui.md)
