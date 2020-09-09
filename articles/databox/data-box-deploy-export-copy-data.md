---
title: Azure Data Box から SMB 経由でデータをコピーするチュートリアル | Microsoft Docs
description: Azure Data Box に SMB 経由でデータをコピーする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 1d39b29ba340b34d6f0add8ff5da473408bd6360
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259120"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-smb-preview"></a>チュートリアル:Azure Data Box から SMB 経由でデータをコピーする (プレビュー)

このチュートリアルでは、Data Box に接続し、ローカル Web UI を使用して Data Box のデータをオンプレミスのデータ サーバーにコピーする方法について説明します。 Data Box デバイスには、Azure Storage アカウントからエクスポートされたデータが含まれています。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * 前提条件
> * Data Box に接続する
> * Data Box からデータをコピーする

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. Azure Data Box を注文済みであること。
    - インポート注文の場合は、「[チュートリアル: Azure Data Box を注文する](data-box-deploy-ordered.md)を完了していること。
    - エクスポート注文の場合は、「[チュートリアル: Azure Data Box を注文する](data-box-deploy-export-ordered.md)を完了していること。
2. Data Box の受け取りが済んでいて、ポータル内での注文の状態が **[配信済み]** であること。
3. Data Box のデータのコピー先となるホスト コンピューターが用意されていること。 このホスト コンピューターは次の条件を満たしている必要があります。
   * [サポート対象のオペレーティング システム](data-box-system-requirements.md)が実行されていること。
   * 高速ネットワークに接続していること。 10 GbE 接続を少なくとも 1 つ利用することを強くお勧めします。 10 GbE 接続を利用できない場合は、1 GbE データ リンクを使用します。ただし、コピー速度は低下します。

## <a name="connect-to-data-box"></a>Data Box に接続する

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

Windows Server ホスト コンピューターを使用している場合は、次の手順に従って Data Box に接続します。

1. 最初に、認証を行ってセッションを開始します。 **[接続とコピー]** に移動します。 **[資格情報の取得]** を選択して、ストレージ アカウントに関連付けられている共有用のアクセス資格情報を取得します。 

    ![共有の資格情報を取得する 1](media/data-box-deploy-export-copy-data/get-share-credentials-1.png)

2. [共有にアクセスしてデータをコピーする] ダイアログ ボックスで、共有に対応する **[ユーザー名]** と **[パスワード]** をコピーします。 **[OK]** を選択します。
    
    ![共有の資格情報を取得する 2](media/data-box-deploy-export-copy-data/get-share-credentials-2.png)

3. 自分のストレージ アカウントに関連付けられている共有 (次の例の場合は、*exportbvtdataset2*) にホスト コンピューターからアクセスするために、コマンド ウィンドウを開きます。 コマンド プロンプトに、次のコマンドを入力します。

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    データ形式に応じて、共有パスは次のようになっています。
    - Azure ブロック BLOB - `\\169.254.143.85\exportbvtdataset2_BlockBlob`
    - Azure ページ BLOB - `\\169.254.143.85\exportbvtdataset2_PageBlob`
    - Azure Files - `\\169.254.143.85\exportbvtdataset2_AzFile`

4. メッセージに従って共有のパスワードを入力します。 上記のコマンドを使用して共有に接続する例を次に示します。

    ```
    C:\Users\Databoxuser>net use \\169.254.143.85\exportbvtdataset2_BlockBlob /u:exportbvtdataset2
    Enter the password for 'exportbvtdataset2' to connect to '169.254.143.85':
    The command completed successfully.
    ```

4. Windows キーを押しながら R キーを押します。 **[ファイル名指定して実行]** ウィンドウで、「`\\<device IP address>`」と入力します。 **[OK]** を選択して、エクスプローラーを開きます。
    
    ![エクスプローラーで共有に接続する 2](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-1.png)

    共有がフォルダーとして表示されます。
    
    ![エクスプローラーで共有に接続する 2](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-2.png)

    
Linux クライアントを使用している場合は、次のコマンドを使用して、SMB 共有をマウントします。 以下の "vers" パラメーターは、Linux ホストがサポートしている SMB のバージョンです。 次のコマンドで、適切なバージョンを指定します。 Data Box でサポートされている SMB のバージョンについては、「[Linux クライアントでサポートされるファイル システム](https://docs.microsoft.com/azure/databox/data-box-system-requirements#supported-file-systems-for-linux-clients)」を参照してください 

```console
sudo mount -t nfs -o vers=2.1 169.254.143.85:/exportbvtdataset2_BlockBlob /home/databoxubuntuhost/databox
```

## <a name="copy-data-from-data-box"></a>Data Box からデータをコピーする

Data Box 共有に接続したら、次にデータをコピーします。

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]


 SMB 共有に接続した後、データのコピーを開始します。 Robocopy などの SMB 互換ファイル コピー ツールを使用して、データをコピーできます。 Robocopy を使用して、複数のコピー ジョブを開始できます。 


Robocopy コマンドについて詳しくは、「[Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx)」(Robocopy といくつかの例) をご覧ください。

コピーが完了したら、**ダッシュボード**に移動し、デバイス上の使用済み領域と空き領域を確認します。

これで、Microsoft への Data Box の発送に進むことができます。


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Data Box に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
>
> * 前提条件
> * Data Box に接続する
> * Data Box からデータをコピーする

次のチュートリアルに進み、お客様の Data Box を Microsoft に返送する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Data Box を Microsoft に発送する](./data-box-deploy-export-picked-up.md)

