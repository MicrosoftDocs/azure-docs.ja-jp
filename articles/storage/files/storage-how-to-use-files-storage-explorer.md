---
title: Azure Storage Explorer を使用した Azure ファイル共有の管理
description: このクイック スタートを使用して、Azure Storage Explorer を使用して Azure Files を管理する方法を学習します。
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dea88bf93000422ce1d832be4a0f60474ff27938
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629786"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-azure-storage-explorer"></a>クイック スタート: Azure Storage Explorer を使用した Azure ファイル共有の作成および管理
このガイドでは、[Azure Storage Explorer](storage-files-introduction.md) を使用して Azure ファイル共有を操作する方法の基本について説明します。 Azure ファイル共有は他のファイル共有と似ていますが、クラウドに格納され、Azure プラットフォームによって支えられています。 Azure ファイル共有は、業界標準の SMB プロトコルをサポートし、複数のマシン、アプリケーション、およびインスタンス間にわたってファイル共有を可能にします。 

Azure Storage Explorer は、Windows、macOS、および Linux で使用できる、広く使用されているクライアント ツールです。 Storage Explorer を使用すると、Azure ファイル共有やその他のストレージ リソースを管理することができます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件
このクイック スタートでは、Storage Explorer がインストールされていることを前提としています。 Azure Storage Explorer をダウンロードしてインストールするには、「[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)」にアクセスしてください。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成
Storage Explorer を使用して新しいリソースを作成することはできません。 このデモでは、[Azure Portal](https://portal.azure.com/) にストレージ アカウントを作成します。 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connect-storage-explorer-to-azure-resources"></a>Storage Explorer と Azure リソースの接続
Storage Explorer を初めて起動すると、 **[Microsoft Azure Storage Explorer - 接続]** ウィンドウが表示されます。 Storage Explorer には、ストレージ アカウントに対する接続方法がいくつか用意されています。 

- **Azure アカウントを使用してサインインする**: 所属する組織のユーザー資格情報または Microsoft アカウントを使用してログインできます。 
- **接続文字列または SAS トークンを使用して特定のストレージ アカウントに接続する**: 接続文字列は、ストレージ アカウント名とストレージ アカウント キー/SAS トークンを含む特別な文字列です。 Storage Explorer は、トークンを使用して、(単に Azure アカウント内のすべてのストレージ アカウントを表示するのではなく) ストレージ アカウントに直接アクセスします。 接続文字列の詳細については、「[Azure Storage の接続文字列を構成する](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」を参照してください。
- **ストレージ アカウントの名前とキーを使用して、特定のストレージ アカウントに接続する**: ストレージ アカウントの名前とキーを使用して、Azure Storage に接続します。

このクイック スタートでは、Azure アカウントを使用してサインインします。 **[Add an Azure Account]\(Azure アカウントの追加\)** を選択し、**[サインイン]** を選択します。 プロンプトに従って Azure アカウントにサインインします。

![[Microsoft Azure Storage Explorer - 接続] ウィンドウのスクリーンショット](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>ファイル共有を作成する
最初の Azure ファイル共有を `storageacct<random number>` ストレージ アカウント内に作成するには:

1. 作成したストレージ アカウントを展開します。
2. **[ファイル共有]** を右クリックして、**[ファイル共有の作成]** を選択します。  
    ![実際のファイル共有フォルダーとコンテキスト メニューのスクリーンショット](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. ファイル共有名として「*myshare*」と入力して、Enter キーを押します。

共有名には、小文字の英字、数字、単一ハイフンのみを使用することができます (ただし、名前をハイフンで始めることはできません)。 ファイル共有とファイルの名前付けの詳細については、「[Naming and referencing shares, directories, files, and metadata (共有、ディレクトリ、ファイル、およびメタデータの名前付けおよび参照)](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)」を参照してください。

ファイル共有が作成されると、ファイル共有のタブが右ウィンドウに表示されます。 

## <a name="use-your-azure-file-share"></a>Azure ファイル共有を使用する
Azure ファイル共有を作成したところで、SMB でファイル共有を [Windows](storage-how-to-use-files-windows.md)、[Linux](storage-how-to-use-files-linux.md)、または [macOS](storage-how-to-use-files-mac.md) にマウントできます。 別の方法として、Azure Storage Explorer を使用して Azure ファイル共有を操作することもできます。 SMB を使用してファイル共有をマウントする代わりに Azure Storage Explorer を使用する利点として、Azure Storage Explorer を使用して実行されるすべての要求がファイル REST API を使用して処理されることが挙げられます。 ファイル REST API を使用して、SMB アクセス権がないクライアント上のファイルとディレクトリを作成、変更、および削除することができます。

### <a name="create-a-directory"></a>ディレクトリを作成する
ディレクトリを追加すると、ファイル共有を管理するための階層構造が生じます。 ディレクトリには複数のレベルを作成できます。 ただし、サブディレクトリを作成する前に、親ディレクトリが存在することを確認する必要があります。 たとえば、パス myDirectory/mySubDirectory の場合は、最初に *myDirectory* ディレクトリを作成する必要があります。 その後、*mySubDirectory* を作成できます。 

1. ファイル共有のタブの上部のメニューで、**[新しいフォルダー]** ボタンを選択します。 **[新しいディレクトリの作成]** ウィンドウが開きます。
    ![コンテキストの [新しいフォルダー] ボタンのスクリーンショット](media/storage-how-to-use-files-storage-explorer/create-directory-1.png)

2. ディレクトリ名として「*myDirectory*」と入力し、**[OK]** を選択します。 

*myDirectory* ディレクトリが *myshare* ファイル共有のタブに表示されます。

### <a name="upload-a-file"></a>ファイルをアップロードする 
ローカル コンピューターからファイル共有の新しいディレクトリにファイルをアップロードできます。 フォルダー全体をアップロードすることも、単一のファイルをアップロードすることもできます。

1. 上部のメニューで、**[アップロード]** を選択します。 これにより、フォルダーまたはファイルをアップロードするオプションが表示されます。
2. **[ファイルのアップロード]** を選択して、ローカル コンピューターからアップロードするファイルを選択します。
3. **[Upload to a directory]\(ディレクトリにアップロードする\)** で「*myDirectory*」と入力し、**[アップロード]** を選択します。 

完了すると、ファイルが *myDirectory* ウィンドウの一覧に表示されます。

### <a name="download-a-file"></a>ファイルをダウンロードする
ファイル共有からファイルのコピーをダウンロードするには、ファイルを右クリックして、**[ダウンロード]** を選択します。 ファイルの保存先にするローカル コンピューター上の場所を選択して、**[保存]** を選択します。

ウィンドウの下部にある **[アクティビティ]** ウィンドウに、ダウンロードの進行状況が表示されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする
Storage Explorer を使用してリソースを削除することはできません。 このクイック スタートからクリーンアップするには、[Azure Portal](https://portal.azure.com/) を使用します。 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Files とは何ですか。](storage-files-introduction.md)