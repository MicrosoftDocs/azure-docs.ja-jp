---
title: Azure portal を使用して Azure ファイル共有を管理するためのクイック スタート
description: Azure portal で Azure ファイル共有を作成して管理する方法をご覧ください。 ストレージ アカウントを作成し、Azure ファイル共有を作成して、Azure ファイル共有を使用します。
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6a88124397812f7599ce54b46b23d22e626cf520
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629820"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-the-azure-portal"></a>クイック スタート: Azure portal を使用した Azure ファイル共有の作成および管理 
[Azure Files](storage-files-introduction.md) は、Microsoft の使いやすいクラウド ファイル システムです。 Azure ファイル共有は、Windows、Linux、macOS でマウントできます。 このガイドでは、[Azure Portal](https://portal.azure.com/) を使用して Azure ファイル共有を操作する方法の基本について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-an-azure-file-share"></a>Azure ファイル共有を作成する
Azure ファイル共有を作成するには:

1. ダッシュボードからストレージ アカウントを選択します。
2. ストレージ アカウント ページの **[サービス]** セクションで、**[ファイル]** を選択します。
    ![ストレージ アカウントのサービス セクションのスクリーンショット: Files サービスの選択](media/storage-how-to-use-files-portal/create-file-share-1.png)

3. **[File サービス]** ページの上部にあるメニューで、**[ファイル共有]** をクリックします。 **[新しいファイル共有]** ページが表示されます。
4. **[名前]** で、「*myshare*」と入力します。
5. **[OK]** をクリックして、Azure ファイル共有を作成します。

共有名は、すべて小文字の英字、数字、単一ハイフンにする必要があります。ただし、最初にハイフンを使用することはできません。 ファイル共有とファイルの名前付けの詳細については、「 [共有、ディレクトリ、ファイル、およびメタデータの名前付けおよび参照](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)」を参照してください。

## <a name="use-your-azure-file-share"></a>Azure ファイル共有を使用する
Azure Files では、業界標準の [Server Message Block (SMB) プロトコル](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)、ネットワーク ファイル システム (NFS) プロトコル (プレビュー)、および[ファイル REST プロトコル](/rest/api/storageservices/file-service-rest-api)の 3 つの方法で Azure ファイル共有内のファイルとフォルダーを操作できます。 

SMB を使用してファイル共有をマウントするには、お使いの OS に基づいて次のドキュメントを参照してください。
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-from-the-azure-portal"></a>Azure portal から Azure ファイル共有を使用する
Azure Portal 経由で実行されるすべての要求はファイル REST API で処理されるため、SMB アクセスのないクライアント上のファイルとディレクトリを作成、変更、削除できます。 ファイル REST プロトコルは直接使用する (つまり手動で REST HTTP 呼び出しを作成する) こともできますが、(Azure portal を使用する以外で) ファイル REST プロトコルを使用する最も一般的な方法は、[Azure PowerShell モジュール](storage-how-to-use-files-powershell.md)、[Azure CLI](storage-how-to-use-files-cli.md)、または Azure Storage SDK を使用することです。いずれの方法でも、任意のスクリプト/プログラミング言語でファイル REST プロトコルの便利なラッパーが提供されます。 

Azure Files のほとんどのユーザーは、SMB プロトコルを介して Azure ファイル共有を操作したいと考えていると予想されます。これにより、ユーザーが使用できると予想する既存のアプリケーションやツールを使用できるためです。しかし、SMB よりもファイル REST API を使用する方が有益な理由がいくつかあります。その理由を次に示します。

- SMB アクセスが不可能なラップトップ、タブレット、モバイル デバイスなど、外出先から Azure ファイル共有をすばやく変更する必要がある。
- ポート 445 がブロックされていないオンプレミスのクライアントなど、SMB 共有をマウントできないクライアントからスクリプトまたはアプリケーションを実行する必要がある。
- [Azure Functions](../../azure-functions/functions-overview.md) のようなサーバーレス リソースを利用している。 

次の例は、Azure portal を使用してファイル REST プロトコルで Azure ファイル共有を操作する方法を示しています。 

Azure ファイル共有を作成したところで、SMB でファイル共有を [Windows](storage-how-to-use-files-windows.md)、[Linux](storage-how-to-use-files-linux.md)、または [macOS](storage-how-to-use-files-mac.md) にマウントできます。 別の方法として、Azure Portal を使用して Azure ファイル共有を操作することもできます。 

#### <a name="create-a-directory"></a>ディレクトリを作成する
Azure ファイル共有のルートで *myDirectory* という名前の新しいディレクトリを作成するには:

1. **[File サービス]** ページで、**myshare** ファイル共有を選択します。 ファイル共有の名前ページが開きます。
2. ページ上部のメニューで、**[+ ディレクトリの追加]** を選択します。 **[新しいディレクトリ]** ページが表示されます。
3. 「*myDirectory*」と入力し、**[OK]** をクリックします。

#### <a name="upload-a-file"></a>ファイルをアップロードする 
ファイルのアップロードを行うには、最初にアップロードされるファイルを作成または選択する必要があります。 これは、必要に応じた方法で実行できます。 アップロードしたいファイルを選択したら:

1. **myDirectory** ディレクトリをクリックします。 **myDirectory** パネルが開きます。
2. 上部のメニューで **[アップロード]** をクリックします。 **[ファイルのアップロード]** パネルが開きます。  
    ![[ファイルのアップロード] パネルのスクリーンショット](media/storage-how-to-use-files-portal/upload-file-1.png)

3. フォルダー アイコンをクリックして、ローカル ファイルを参照するためのウィンドウを開きます。 
4. ファイルを選択して **[開く]** をクリックします。 
5. **[ファイルのアップロード]** ページで、ファイル名を確認して **[アップロード]** をクリックします。
6. 完了したら、ファイルが **myDirectory** ページの一覧に表示されます。

#### <a name="download-a-file"></a>ファイルをダウンロードする
ファイルを右クリックすると、アップロードしたファイルのコピーをダウンロードできます。 ダウンロード ボタンをクリックした後の正確なエクスペリエンスは、使用しているオペレーティング システムとブラウザーによって異なります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Files とは何ですか。](storage-files-introduction.md)