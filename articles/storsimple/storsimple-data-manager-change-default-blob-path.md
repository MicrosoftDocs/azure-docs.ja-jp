---
title: BLOB パスを既定値から変更する
description: 既定の BLOB ファイル パス名を変更するように Azure 関数を設定して、BLOB を別の場所に移動する方法について説明します。
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 6af095c7abdb9aa61e57d543ff2ab2f9192dadc8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011440"
---
# <a name="change-a-blob-path-from-the-default-path"></a>BLOB パスを既定のパスから変更する

StorSimple Data Manager サービスでデータを変換すると、変換後の BLOB が格納される既定の場所は、ターゲット リポジトリの作成時に指定したストレージ コンテナーになります。 この場所に格納された BLOB を、別の場所に移動することが必要な場合があります。 この記事では、既定の BLOB ファイル パス名を変更するように Azure 関数を設定して、BLOB を別の場所に移動する方法について説明します。

## <a name="prerequisites"></a>前提条件

StorSimple Data Manager サービスに正しく構成されたジョブ定義があることを確認します。

## <a name="create-an-azure-function"></a>Azure 関数の作成

Azure 関数を作成するには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com/)にアクセスします。

2. **[+ リソースの作成]** をクリックします。 **[検索]** ボックスに「**Function App**」と入力し、**Enter** キーを押します。 表示されるアプリの一覧で、 **[Function App]** を選んでクリックします。

    ![検索ボックスに「Function App」と入力](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. **Create** をクリックしてください。

    ![[Function App] ウィンドウの [作成] ボタン](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. **[Function App]** 構成ブレードで、次の手順を実行します。

    1. 一意の **[アプリ名]** を指定します。
    2. ドロップダウン リストから、 **[サブスクリプション]** を選びます。 StorSimple Data Manager サービスに関連付けられているものと同じサブスクリプションを選ぶ必要があります。
    3. リソース グループの **[新規作成]** を選びます。
    4. **[ホスティング プラン]** ドロップダウン リストで、 **[従量課金プラン]** を選びます。
    5. 関数を実行する場所を指定します。 StorSimple Data Manager サービスおよびジョブ定義に関連付けられたストレージ アカウントが存在するのと同じリージョンを選びます。
    6. 既存のストレージ アカウントを選択するか、新しいストレージ アカウントを作成します。 ストレージ アカウントは内部で関数に使用されます。

        ![新しい Function App の構成データを入力する](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. **Create** をクリックしてください。 Function App が作成されます。
     
        ![作成された Function App](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. **[関数]** を選び、 **[+ 新しい関数]** をクリックします。

    ![[+ 新しい関数] をクリックする](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. 言語として **[C#]** を選びます。 テンプレート タイルの配列で、 **[QueueTrigger-CSharp]** タイルの **[C#]** を選びます。

7. **[Queue trigger]\(キュー トリガー\)** で次のようにします。

    1. 関数の **[名前]** を入力します。
    2. **[キュー名]** ボックスに、データ変換ジョブ定義の名前を入力します。
    3. **[ストレージ アカウント接続]** で、 **[新規]** をクリックします。 ストレージ アカウントの一覧から、ジョブ定義に関連付けられているアカウントを選びます。 接続名をメモします (強調表示されている部分)。 この名前は後で Azure 関数で必要になります。

        ![新しい C# 関数を作成する](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. **Create** をクリックしてください。 **関数** が作成されます。

     
10. [関数] ウィンドウで _.csx_ ファイルを実行します。

    ![新しい C# 関数を作成する 2](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
    次の手順に従います。

    1. 次のコードを貼り付けます。

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

    2. 11 行目の **STORAGE_CONNECTIONNAME** をストレージ アカウント接続に置き換えます (手順 7c を参照)。

        ![ストレージ接続名をコピーする](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. 関数を **保存** します。

        ![関数を保存する](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. 関数を完成させるには、次の手順に従ってもう 1 つのファイルを追加します。

    1. **[ファイルの表示]** をクリックします。

       ![[ファイルの表示] リンク](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. **[+ 追加]** をクリックします。
        
        ![新しい関数の追加、ファイルの追加](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. 「**project.json**」と入力し、**Enter** キーを押します。 **project.json** ファイルに、次のコードを貼り付けます。

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

    
    4. **[保存]** をクリックします。

        ![新しい関数プロジェクト json](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

Azure 関数が作成されました。 この関数は、データ変換ジョブによって新しい BLOB が生成されるたびにトリガーされます。

## <a name="next-steps"></a>次のステップ

[StorSimple Data Manger UI を使用してデータを変換する](storsimple-data-manager-ui.md)
