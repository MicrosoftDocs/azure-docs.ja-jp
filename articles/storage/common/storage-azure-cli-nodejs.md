---
title: Azure Storage での Azure クラシック CLI の使用 | Microsoft Docs
description: Azure Storage で Azure クラシック コマンド ライン インターフェイス (CLI) を使用して、ストレージ アカウントの作成と管理および Azure の BLOB やファイルの操作を行う方法について学習します。
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 01/30/2017
ms.author: seguler
ms.subservice: common
ms.openlocfilehash: 211051254e08d69c06afd4242599c909048e7e17
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464842"
---
# <a name="using-the-azure-classic-cli-with-azure-storage"></a>Azure Storage での Azure クラシック CLI の使用

## <a name="overview"></a>概要

Azure クラシック CLI は、Azure Platform で使用できるオープン ソース、クロスプラットフォームのコマンド群です。 豊富なデータ アクセス機能だけでなく、 [Azure ポータル](https://portal.azure.com) にあるものと同じ機能の多くを使用できます。

このガイドでは、[Azure クラシック CLI](../../cli-install-nodejs.md) を使用して、Azure Storage でさまざまな開発タスクや管理タスクを実行する方法について説明します。 このガイドを使用する前に、最新のクラシック CLI をダウンロードしてインストールするか、最新の Azure CLI にアップグレードすることをお勧めします。

このガイドでは、Azure Storage の基本概念を理解していることを前提としています。 また、クラシック CLI と Azure Storage を使用する方法を示すための多くのスクリプトを用意しています。 各スクリプトの実行前に、使用する構成に基づいてスクリプト変数を更新してください。

> [!NOTE]
> このガイドでは、クラシック ストレージ アカウントのクラシック CLI のコマンドとスクリプトの例について説明します。 Resource Manager ストレージ アカウントの Azure クラシック CLI コマンドについては、[Azure リソース管理での Mac、Linux、および Windows 用 Azure CLI の使用](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects)に関するページを参照してください。
>
>

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="get-started-with-azure-storage-and-the-azure-classic-cli-in-5-minutes"></a>5 分で始める Azure Storage と Azure クラシック CLI の使用
このガイドの例では Ubuntu を使用しますが、他の OS プラットフォームでも同様に動作します。

**Azure を初めて使用する場合:** Microsoft Azure サブスクリプションと、そのサブスクリプションに関連付けられた Microsoft アカウントを入手してください。 Azure の購入オプションについて詳しくは、[無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページ、[購入オプション](https://azure.microsoft.com/pricing/purchase-options/)に関するページ、[メンバー プラン](https://azure.microsoft.com/pricing/member-offers/) (MSDN、Microsoft Partner Network、BizSpark、その他の Microsoft プログラムのメンバー向け) に関するページをご覧ください。

Azure サブスクリプションの詳細については、「 [Azure Active Directory (Azure AD) の管理者ロールの割り当て](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) 」をご覧ください。

**Microsoft Azure アカウントとサブスクリプションを作成済みである場合:**

1. [Azure クラシック CLI のインストール](../../cli-install-nodejs.md)に関するページに記載されている手順に従って、Azure クラシック CLI をダウンロードしてインストールします。
2. クラシック CLI をインストールすると、コマンド ライン インターフェイス (Bash、Terminal、Command プロンプト) から azure コマンドを使用してクラシック CLI コマンドにアクセスできるようになります。 _azure_ コマンドを入力すると、次の出力が表示されます。

    ![Azure Command Output](./media/storage-azure-cli/azure_command.png)   
3. コマンド ライン インターフェイスで「`azure storage`」と入力すると、Azure Storage のすべてのコマンドの一覧が表示され、クラシック CLI に備わった機能の全体像が得られます。 コマンド名と **-h** パラメーター (たとえば `azure storage share create -h`) を入力すると、コマンドの構文の詳細を確認できます。
4. ここでは、Azure Storage にアクセスするための基本的なクラシック CLI コマンドを示すシンプルなスクリプトを取り上げます。 このスクリプトでは、まずストレージ アカウントとキーの 2 つの変数を設定するよう求められます。 次に、この新しいストレージ アカウントで新しいコンテナーが作成され、既存の画像ファイル (BLOB) がこのコンテナーにアップロードされます。 このスクリプトにより、コンテナー内のすべての BLOB が一覧表示されると、ローカル コンピューターにある格納先ディレクトリに画像ファイルがダウンロードされます。

    ```azurecli
    #!/bin/bash
    # A simple Azure storage example

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export image_to_upload=<image_to_upload>
    export destination_folder=<destination_folder>

    echo "Creating the container..."
    azure storage container create $container_name

    echo "Uploading the image..."
    azure storage blob upload $image_to_upload $container_name $blob_name

    echo "Listing the blobs..."
    azure storage blob list $container_name

    echo "Downloading the image..."
    azure storage blob download $container_name $blob_name $destination_folder

    echo "Done"
    ```

5. ローカル コンピューターで、任意のテキスト エディター (vim など) を開きます。 上記のスクリプトをテキスト エディターに入力します。
6. ここで、構成設定に基づいてスクリプト変数を更新する必要があります。

   * **<storage_account_name>**: スクリプトの所定の名前を使用するか、ストレージ アカウントの新しい名前を入力します。 **重要:** ストレージ アカウントの名前は、Azure 上で一意である必要があります。 また、小文字にする必要もあります。
   * **<storage_account_key>**: ストレージ アカウントのアクセス キー。
   * **<container_name>**: スクリプトの所定の名前を使用するか、コンテナーの新しい名前を入力します。
   * **<image_to_upload>**: ローカル コンピューター上の画像へのパスを入力します。たとえば、"~/images/HelloWorld.png" などです。
   * **<destination_folder>**: Azure Storage からダウンロードしたファイルを格納するローカル ディレクトリへのパスを入力します。たとえば、"~/downloadImages" などです。
7. vim で必要な変数を更新したら、"`ESC` キー、`:` キー、`wq!` キー" というキーの組み合わせを使用してスクリプトを保存します。
8. このスクリプトを実行するには、bash コンソールでスクリプト ファイル名を入力するだけです。 このスクリプトを実行すると、ダウンロードした画像ファイルを格納するローカルの格納先フォルダーの準備が整います。 次のスクリーンショットは、この出力の例を示しています。

スクリプトを実行すると、ダウンロードした画像ファイルを格納するローカルの格納先フォルダーの準備が整います。

## <a name="manage-storage-accounts-with-the-azure-classic-cli"></a>Azure クラシック CLI を使用してストレージ アカウントを管理する
### <a name="connect-to-your-azure-subscription"></a>Azure サブスクリプションへの接続
ほとんどのストレージ コマンドは、Azure サブスクリプションがなくても動作しますが、クラシック CLI からサブスクリプションに接続することをお勧めします。

### <a name="create-a-new-storage-account"></a>新しいストレージ アカウントの作成
Azure Storage を使用するには、ストレージ アカウントが必要です。 コンピューターを構成してサブスクリプションに接続できるようにすると、新しい Azure ストレージ アカウントを作成できます。

```azurecli
azure storage account create <account_name>
```

ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用する必要があります。

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>環境変数で既定の Azure ストレージ アカウントを設定する
サブスクリプションで複数のストレージ アカウントを持つことができます。 その中から 1 つを選択し、同じセッションのすべてのストレージ コマンドに対する環境変数にそれを設定できます。 そうすることにより、ストレージ アカウントとキーを明示的に指定しなくても、クラシック CLI のストレージ コマンドを実行できます。

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

既定のストレージ アカウントを設定するもう 1 つの方法として、接続文字列を使用します。 まず、次のコマンドで接続文字列を取得します。

```azurecli
azure storage account connectionstring show <account_name>
```

出力された接続文字列をコピーし、環境変数に設定します。

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

## <a name="create-and-manage-blobs"></a>BLOB を作成および管理する
Azure Blob Storage は、HTTP または HTTPS 経由で世界中のどこからでもアクセスできるテキストやバイナリ データなど、大量の非構造化データを格納するためのサービスです。 このセクションでは、Azure BLOB ストレージの概念について理解しているユーザーを対象としています。 詳しくは、「[.NET を使用して Azure Blob Storage を使用する](../blobs/storage-dotnet-how-to-use-blobs.md)」と「[BLOB サービスの概念](https://msdn.microsoft.com/library/azure/dd179376.aspx)」をご覧ください。

### <a name="create-a-container"></a>コンテナーを作成する
Azure Storage のすべての BLOB はコンテナーに格納する必要があります。 次の `azure storage container create` コマンドを使用して、プライベート コンテナーを作成できます。

```azurecli
azure storage container create mycontainer
```

> [!NOTE]
> 次の 3 つのレベルの匿名読み取りアクセスがあります:**Off**、**Blob**、**Container**。 BLOB に対する匿名アクセスを許可しない場合は、Permission パラメーターを **Off**に設定します。 既定では、新しいコンテナーはプライベートであり、アカウント所有者のみがアクセスできます。 BLOB リソースに対する匿名パブリック読み取りアクセスを許可するが、コンテナー メタデータまたはコンテナー内の BLOB の一覧に対するアクセスは許可しない場合は、Permission パラメーターを **BLOB**に設定します。 BLOB リソース、コンテナー メタデータ、コンテナー内の BLOB の一覧に対する完全パブリック読み取りアクセスを許可する場合は、Permission パラメーターを **Container**に設定します。 詳細については、「 [コンテナーと BLOB への匿名読み取りアクセスを管理する](../blobs/storage-manage-access-to-resources.md)」をご覧ください。
>
>

### <a name="upload-a-blob-into-a-container"></a>コンテナーに BLOB をアップロードする
Azure Blob Storage では、ブロック BLOB とページ BLOB がサポートされています。 詳細については、「 [ブロック BLOB、追加 BLOB、ページ BLOB について](https://msdn.microsoft.com/library/azure/ee691964.aspx)」を参照してください。

BLOB をコンテナーにアップロードするには、 `azure storage blob upload`を使用できます。 既定では、このコマンドにより、ローカル ファイルがブロック BLOB にアップロードされます。 BLOB の種類を指定するには、 `--blobtype` パラメーターを使用できます。

```azurecli
azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob
```

### <a name="download-blobs-from-a-container"></a>コンテナーから BLOB をダウンロードする
次の例は、BLOB をコンテナーからダウンロードする方法を示しています。

```azurecli
azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'
```

### <a name="copy-blobs"></a>BLOB をコピーする
BLOB は、ストレージ アカウント内、またはストレージ アカウントとリージョン間にまたがって非同期的にコピーできます。

次の例は、BLOB をあるストレージ アカウントから別のストレージ アカウントにコピーする方法を示しています。 この例では、匿名でパブリックに BLOB にアクセスできるコンテナーを作成します。

```azurecli
azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer
```

この例で実行するのは非同期コピーです。 各コピー操作の状態は、 `azure storage blob copy show` 操作を実行して監視できます。

コピー操作で指定されたソース URL は、パブリックにアクセスできるようにするか、SAS (Shared Access Signature) トークンを含める必要があります。

### <a name="delete-a-blob"></a>BLOB を削除する
BLOB を削除するには、次のコマンドを使用します。

```azurecli
azure storage blob delete mycontainer myBlockBlob2
```

## <a name="create-and-manage-file-shares"></a>ファイル共有を作成および管理する
Azure Files は、標準的な SMB プロトコルを使用して、アプリケーション用の共有ストレージを提供します。 Microsoft Azure の仮想マシンとクラウド サービスでは、オンプレミスのアプリケーションと同じように、ファイル データを共有できます。 ファイル共有とファイル データは、クラシック CLI を使用して管理できます。 Azure Files の詳細については、[Azure Files の概要](../files/storage-files-introduction.md)に関する記事をご覧ください。

### <a name="create-a-file-share"></a>ファイル共有を作成する
Azure ファイル共有は、Azure 内の SMB ファイル共有です。 ディレクトリとファイルはすべて、ファイル共有に作成する必要があります。 アカウントに含まれる共有の数と、共有に格納できるファイル数には制限がなく、ストレージ アカウントの容量の上限まで増やすことができます。 次の例では、 **myshare**という名前のファイル共有を作成します。

```azurecli
azure storage share create myshare
```

### <a name="create-a-directory"></a>ディレクトリを作成する
ディレクトリは、Azure ファイル共有の任意の階層構造を示します。 次の例では、ファイル共有に **myDir** という名前のディレクトリを作成します。

```azurecli
azure storage directory create myshare myDir
```

ディレクトリ パスには複数のレベルを含めることができます ( *例*: **a/b**)。 ただし、すべての親ディレクトリが存在することを確認する必要があります。 たとえば、パス **a/b** の場合、最初に**a** ディレクトリを作成した後、**b** ディレクトリを作成する必要があります。

### <a name="upload-a-local-file-to-directory"></a>ディレクトリにローカル ファイルをアップロードする
次の例では、**~/temp/samplefile.txt** から **myDir** ディレクトリにファイルをアップロードします。 ファイル パスを編集して、ローカル マシン上の有効なファイルを指定してください。

```azurecli
azure storage file upload '~/temp/samplefile.txt' myshare myDir
```

共有内のファイル サイズは最大 1 TB です。

### <a name="list-the-files-in-the-share-root-or-directory"></a>共有ルートまたはディレクトリ内のファイルの一覧を表示する
次のコマンドを使用して、共有ルートまたはディレクトリ内のサブディレクトリとファイルの一覧を表示できます。

```azurecli
azure storage file list myshare myDir
```

一覧表示操作では、ディレクトリ名を省略できます。 省略した場合は、共有のルート ディレクトリの内容が一覧表示されます。

### <a name="copy-files"></a>ファイルのコピー
クラシック CLI のバージョン 0.9.8 以降では、ファイルを別のファイルにコピーしたり、ファイルを BLOB にコピーしたり、BLOB をファイルにコピーしたりすることができます。 次に、CLI コマンドを使用してこれらのコピー操作を実行する方法を示します。 ファイルを新しいディレクトリにコピーするには、次の操作を実行します。

```azurecli
azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare
    --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

BLOB をファイル ディレクトリにコピーするには、次の操作を実行します。

```azurecli
azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello
    --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

## <a name="next-steps"></a>次の手順

ストレージ リソースを操作するための Azure クラシック CLI コマンド リファレンスは、以下のページにあります。

* [Resource Manager モードでの Azure クラシック CLI コマンド](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects)
* [Azure サービス管理モードでの Azure クラシック CLI コマンド](../../cli-install-nodejs.md)

また、Resource Manager のデプロイ モデルで [Azure CLI](../storage-azure-cli.md) の現在のバージョンを使用することもできます。
