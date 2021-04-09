---
title: Azure Storage Explorer を使用して Blob Storage データを移動する - Team Data Science Process
description: Azure Storage Explorer を使用して Azure Blob Storage からデータをアップロードおよびダウンロードする方法について説明します。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 53cb8cdd1c5f9824b07b16b8b6c70648603b9f38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98788911"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Azure Storage Explorer を使用して Azure BLOB Storage 間でデータを移動する
Azure Storage Explorer は、Windows、MacOS、Linux で Azure Storage のデータを操作できる Microsoft の無料ツールです。 このトピックでは、Azure Storage Explorer を使用して Azure Blob Storage からデータをアップロードおよびダウンロードする方法について説明します。 このツールは、 [Microsoft Azure Storage Explorer](https://storageexplorer.com/)のページからダウンロードできます。

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> [Azure のデータ サイエンス用仮想マシン](../data-science-virtual-machine/overview.md)によって提供されるスクリプトを使用してセットアップされた VM を使用している場合、Azure Storage Explorer は既に VM にインストールされています。
> 
> [!NOTE]
> Azure BLOB Storage の概要については、[Azure BLOB の基礎](../../storage/blobs/storage-quickstart-blobs-dotnet.md)に関する記事および [Azure BLOB サービス](/rest/api/storageservices/Blob-Service-Concepts)に関するページをご覧ください。   
> 
> 

## <a name="prerequisites"></a>前提条件
このドキュメントは、Azure サブスクリプション、ストレージ アカウント、そのアカウントに対応するストレージ キーがあることを前提としています。 データのアップロードまたはダウンロードを行う前に、Azure Storage のアカウント名とアカウント キーを確認しておく必要があります。 

* Azure サブスクリプションを設定するには、 [1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。
* ストレージ アカウントの作成と、アカウントとキー情報の取得の手順については、[Azure ストレージ アカウントについて](../../storage/common/storage-account-create.md)のページを参照してください。 ストレージ アカウントのアクセス キーをメモしておきます。Azure Storage Explorer ツールでアカウントに接続する際に、このキーが必要になります。
* Azure ストレージ エクスプローラー ツールは、 [Microsoft Azure Storage Explorer](https://storageexplorer.com/)のページからダウンロードできます。 インストール時に既定値をそのまま使用します。

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Azure Storage Explorer の使用
次の手順では、Azure Storage Explorer を使用してデータをアップロード/ダウンロードする方法について説明します。 

1. Microsoft Azure Storage Explorer を起動します。
2. **アカウントにサインイン...** ウィザードを起動するには、 **[Azure アカウントの設定]** アイコンをクリックし、 **[アカウントの追加]** をクリックして、資格情報を入力します。 
![Azure のストレージ アカウントの追加](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. **Azure Storage へ接続** ウィザードを起動するには、 **[Azure Storage へ接続]** アイコンをクリックします。 ![Azure Storage へ接続アイコンをクリックする](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. **Azure Storage へ接続** ウィザードで Azure ストレージ アカウントのアクセス キーを入力し、 **[次へ]** をクリックします。 ![Azure ストレージ アカウントのアクセス キーを入力する](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. **[アカウント名]** ボックスにストレージ アカウント名を入力し、 **[次へ]** をクリックします。 ![外部ストレージの接続](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. 追加されたストレージ アカウントが表示されます。 ストレージ アカウントに BLOB コンテナーを作成するには、そのアカウントの **[BLOB コンテナー]** ノードを右クリックして **[BLOB コンテナーの作成]** を選択し、名前を入力します。
7. コンテナーにデータをアップロードするには、対象のコンテナーを選択し、 **[アップロード]** ボタンをクリックします。
![ストレージ アカウント](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. **[ファイル]** ボックスの右側の **[...]** をクリックし、アップロードする 1 つ以上のファイルをファイル システムから選択します。 **[アップロード]** をクリックして、ファイルのアップロードを開始します。![ファイルのアップロード](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. データをダウンロードするには、対応するコンテナー内の BLOB を選択し、 **[ダウンロード]** をクリックします。 ![ファイルのダウンロード](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)
