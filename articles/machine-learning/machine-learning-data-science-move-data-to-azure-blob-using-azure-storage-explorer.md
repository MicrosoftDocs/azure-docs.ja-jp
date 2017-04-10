---
title: "Azure ストレージ エクスプローラーを使用して Blob Storage 間でデータを移動する | Microsoft Docs"
description: "Azure Storage Explorer を使用して Azure BLOB ストレージ間でデータを移動する"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 10bd283f-0875-4c67-af63-6492270b7656
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 69bb7e5fb0dafa3a9d0f6ad4eb271ab1af2edc50
ms.openlocfilehash: a258404e659bd6d6e7e0f468a5f3ce5e184be2a3
ms.lasthandoff: 01/31/2017


---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Azure Storage Explorer を使用して Azure BLOB Storage 間でデータを移動する
Azure ストレージ エクスプローラーは、Windows、MacOS、Linux で Azure Storage のデータを操作できる Microsoft の無料ツールです。 このトピックでは、Azure ストレージ エクスプローラーを使用して Azure BLOB ストレージからデータをアップロードおよびダウンロードする方法について説明します。 このツールは、 [Microsoft Azure ストレージ エクスプローラー](http://storageexplorer.com/)のページからダウンロードできます。

[!INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> [Azure のデータ サイエンス用仮想マシン](machine-learning-data-science-virtual-machines.md)によって提供されるスクリプトを使用してセットアップされた VM を使用している場合、Azure Storage Explorer は既に VM にインストールされています。
> 
> [!NOTE]
> Azure BLOB ストレージの概要については、[Azure BLOB の基礎](../storage/storage-dotnet-how-to-use-blobs.md)に関する記事および [Azure BLOB サービス](https://msdn.microsoft.com/library/azure/dd179376.aspx)に関するページをご覧ください。   
> 
> 

## <a name="prerequisites"></a>前提条件
このドキュメントは、Azure サブスクリプション、ストレージ アカウント、そのアカウントに対応するストレージ キーがあることを前提としています。 データのアップロード/ダウンロードを行う前に、Azure Storage のアカウント名とアカウント キーを確認しておく必要があります。 

* Azure サブスクリプションを設定するには、 [1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。
* ストレージ アカウントの作成と、アカウントとキー情報の取得についての手順については、「 [Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」を参照してください。 ストレージ アカウントのアクセス キーをメモしておきます。Azure ストレージ エクスプローラー ツールでアカウントに接続する際に、このキーが必要になります。
* Azure ストレージ エクスプローラー ツールは、 [Microsoft Azure ストレージ エクスプローラー](http://storageexplorer.com/)のページからダウンロードできます。 インストール時に既定値をそのまま使用します。

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Azure ストレージ エクスプローラーの使用
次の手順では、Azure ストレージ エクスプローラーを使用してデータをアップロード/ダウンロードする方法について説明します。 

1. Microsoft Azure ストレージ エクスプローラーを起動します。
2. **アカウントにサインイン...** ウィザードを起動するには、**[Azure アカウントの設定]** アイコンをクリックし、**[アカウントの追加]** をクリックして、資格情報を入力します。 ![Azure Storage アカウントの追加](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. **Azure Storage へ接続**ウィザードを起動するには、**[Azure Storage へ接続]** アイコンをクリックします。 ![Azure Storage への接続](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. **Azure Storage へ接続**ウィザードで Azure ストレージ アカウントのアクセス キーを入力し、**[次へ]** をクリックします。 ![Azure Storage への接続](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. **[アカウント名]** ボックスにストレージ アカウント名を入力し、**[次へ]** をクリックします。 ![外部ストレージの接続](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. 追加されたストレージ アカウントが表示されます。 ストレージ アカウントに BLOB コンテナーを作成するには、そのアカウントの **[BLOB コンテナー]** ノードを右クリックして **[BLOB コンテナーの作成]** を選択し、名前を入力します。
7. コンテナーにデータをアップロードするには、対象のコンテナーを選択し、**[アップロード]** をクリックします。![Storage アカウント](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. **[ファイル]** ボックスの右側の **[...]** をクリックし、アップロードする&1; つ以上のファイルをファイル システムから選択します。**[アップロード]** をクリックして、ファイルのアップロードを開始します。![ファイルのアップロード](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. データをダウンロードするには、対応するコンテナー内の BLOB を選択し、 **[ダウンロード]**をクリックします。 ![ファイルのダウンロード](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)


