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
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 8a06e4b3f482943f55e998c3c68857d3530ff98a
ms.lasthandoff: 03/21/2017

---

# <a name="change-blob-path-from-the-default-private-preview"></a>BLOB パスを既定値から変更する (プライベート プレビュー)

この記事では、既定の BLOB ファイル パス名を変更するように Azure 関数を設定する方法について説明します。 

## <a name="prerequisites"></a>前提条件

開始する前に、次の要件が満たされていることを確認します。
* リソース グループ内のハイブリッド データ リソースでジョブ定義が正しく構成されている。

## <a name="create-an-azure-function"></a>Azure 関数の作成

Azure 関数を作成するには、次の手順を実行します。

#### <a name="to-create-an-azure-function"></a>Azure 関数を作成するには

1. [Azure ポータル](http://portal.azure.com/)に移動します。

2. 左上隅の **[+ 新規]** をクリックします。 **検索**ボックスに「Function app」と入力し、**Enter** キーを押します。

    ![Function App リソースに移動する](./media/storsimple-data-manager-change-default-blob-path/goto-function-app-resource.png)

3. 結果の **[Function App]** をクリックします。

    ![Function App リソースを選択する](./media/storsimple-data-manager-change-default-blob-path/select-function-app-resource.png)

4. **[Function App]** ウィンドウを開き、**[作成]** をクリックします。

    ![新しい Function App を作成する](./media/storsimple-data-manager-change-default-blob-path/create-new-function-app.png)

5. **[構成]** ブレードですべての値を入力し、**[作成]** をクリックします。

    1. アプリの名前
    2. サブスクリプション
    3. リソース グループ
    4. ホスティング プラン - **従量課金プラン**
    5. Location (場所)
    6. ストレージ アカウント - 既存のストレージ アカウントを使用するか、新しいストレージ アカウントを作成します。 ストレージ アカウントは内部で関数に使用されます。

        ![新しい Function App の構成データを入力する](./media/storsimple-data-manager-change-default-blob-path/enter-new-funcion-app-data.png)

6. Function App を作成したら、左下の **[その他のサービス >]** に移動します。 **[フィルター]** ボックスに「App Services」と入力し、**[App Services]** をクリックします。

    ![[その他のサービス >]](./media/storsimple-data-manager-change-default-blob-path/more-services.png)

7. App Services の一覧で **Function App 名**をクリックします。

8. **[+ 新しい関数]** をクリックします。 **[言語]** ボックスの一覧で **[C#]** を選択します。 テンプレートの一覧で **[QueueTrigger-CSharp]** を選択します。 すべての値を入力します。

   1. 名前 - 関数名を入力します。
   2. キュー名 - **データ変換ジョブ定義名**を入力します。
   3. ストレージ アカウント接続 - **[新規]** をクリックします。 データ変換ジョブに対応するアカウントを選択します。
      
      `Connection name` を書き留めておきます。 この名前は後で Azure 関数で必要になります。

   4. **[作成]** をクリックします。

       ![新しい C# 関数を作成する >](./media/storsimple-data-manager-change-default-blob-path/create-new-csharp-function.png)

9. **[関数]** ウィンドウで _.csx_ ファイルを実行します。 次のコードをコピーして貼り付けます。

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
            log.Info($"Blob Uri: {myQueueItem.TargetLocation}");
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            string uriString = "windows.net/";
            string containerName = myQueueItem.TargetLocation;
            containerName = containerName.Substring(containerName.IndexOf(uriString) + uriString.Length);
            containerName = containerName.Substring(0, containerName.IndexOf("/"));
            //log.Info($"Container name: {containerName}");

            CloudBlobContainer container = blobClient.GetContainerReference(containerName);

            if(!container.Exists())
            {
                log.Info($"Container - {containerName} not exists");
                return;
            }

            string containerUri = string.Format("{0}/", container.Uri.ToString());  // Reading container Uri
            string blobUri =  myQueueItem.TargetLocation;
            string blobName = blobUri.Replace(containerUri, string.Empty).Replace("%20", " "); // Reading existing file path after container name
            string newBlobName = blobName.Substring(blobName.IndexOf(string.Format("{0}/", container.Name)) + container.Name.Length + 1); // Reading actual File path after container name

            log.Info($"Blob name: {blobName}");
            log.Info($"New blob name: {newBlobName}");

            CloudBlockBlob blobCopy = container.GetBlockBlobReference(newBlobName);
            CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
            if (!blobCopy.Exists())
            {
                blobCopy.StartCopy(blob);
                // Delete old blob, after copy the blob
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

   1. 12 行目の **STORAGE_CONNECTIONNAME** をストレージ アカウント接続に置き換えます (手順 8. の c. を参照)。
   2. 左上の **[保存]** をクリックします。

       ![関数を保存する >](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

10.  右上の **[ファイルの表示]** をクリックします。

    ![[ファイルの表示]](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

   1. **[+ 追加]** をクリックします。 「**project.json**」と入力し、**Enter** キーを押します。
   2. 次のコードをコピーして、**project.json** ファイルに貼り付けます。

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

   2. [ **Save**] をクリックします。

Azure 関数が作成されました。 この関数は、データ変換ジョブによって新しい BLOB が生成されるたびにトリガーされます。

## <a name="next-steps"></a>次のステップ

[StorSimple Data Manger UI を使用してデータを変換します](storsimple-data-manager-ui.md)。

