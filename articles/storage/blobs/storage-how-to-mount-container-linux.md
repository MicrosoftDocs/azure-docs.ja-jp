---
title: Linux 上で Azure Blob Storage をファイル システムとしてマウントする方法 | Microsoft Docs
description: Linux 上で FUSE を使用して Azure Blob Storage コンテナーをマウントする
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 2/1/2019
ms.author: seguler
ms.openlocfilehash: eadf52afd115eb1cb642082cea4b9f338bd44914
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521655"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>blobfuse を使用して Blob Storage をファイル システムとしてマウントする方法

## <a name="overview"></a>概要
[blobfuse](https://github.com/Azure/azure-storage-fuse) は Azure Blob Storage 用の仮想ファイル システム ドライバーです。 blobfuse を使用すると、Linux ファイル システムからお客様のストレージ アカウント内の既存のブロック BLOB データにアクセスできます。 Azure Blob Storage はオブジェクト ストレージ サービスであり、階層型名前空間を持っていません。 blobfuse は、フォワードスラッシュ '/' を区切り記号として使用して、仮想ディレクトリ スキームによってこの名前空間を提供します。  

このガイドでは、blobfuse を使用し、Linux 上で Blob Storage コンテナーをマウントしてデータにアクセスする方法を示します。 blobfuse の詳細については、「[the blobfuse repository (blobfuse リポジトリ)](https://github.com/Azure/azure-storage-fuse)」にある詳細を参照してください。

> [!WARNING]
> blobfuse は、単に要求を [BLOB REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) に変換するだけなので、100% の POSIX 準拠は保証されません。 たとえば、名前変更操作は POSIX ではアトミックですが、blobfuse では違います。
> ネイティブなファイル システムと blobfuse の違いの完全な一覧については、[blobfuse ソース コード リポジトリ](https://github.com/azure/azure-storage-fuse)にアクセスしてください。
> 

## <a name="install-blobfuse-on-linux"></a>Linux に blobfuse をインストールする
blobfuse バイナリは、Linux の Ubuntu および RHEL ディストリビューション用の [Microsoft ソフトウェア リポジトリ](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software)で入手できます。 このようなディストリビューションに blobfuse をインストールするには、一覧からいずれかのリポジトリを構成します。 使用しているディストリビューション用のバイナリがない場合は、[Azure Storage のインストール手順](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source)に従ってソース コードからバイナリをビルドすることもできます。

blobfuse では、Ubuntu 14.04、16.04、および 18.04 へのインストールをサポートしています。 次のコマンドを実行して、これらのバージョンのいずれかがデプロイされていることを確認します。
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>Microsoft パッケージ リポジトリを構成する
[Microsoft 製品用の Linux パッケージ リポジトリ](https://docs.microsoft.com/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software)を構成します。

例として、Enterprise Linux 6 ディストリビューション上では、次のようになります。
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

同様に、`.../rhel/7/...` の URL を Enterprise Linux 7 ディストリビューションを指すように変更します。

Ubuntu 14.04 ディストリビューション上での別の例を次に示します。
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

同様に、URL を `.../ubuntu/16.04/...` または `.../ubuntu/18.04/...` に変更して別のバージョンの Ubuntu を参照します。

### <a name="install-blobfuse"></a>blobfuse をインストールする

Ubuntu/Debian ディストリビューション上では、次のようになります。
```bash
sudo apt-get install blobfuse
```

Enterprise Linux ディストリビューション上では、次のようになります。
```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>マウントの準備
blobfuse では、ファイル システム内に、すべての開かれたファイルをバッファリングおよびキャッシュするための一時パスを必要とすることで、ネイティブのようなパフォーマンスを提供します。 この一時パスには、最もパフォーマンスの高いディスクを選択するか、または最高のパフォーマンスを得るために RAM ディスクを使用します。 

> [!NOTE]
> blobfuse は、すべての開かれたファイルの内容を一時パスに格納します。 すべての開かれたファイルを収容するための十分な領域を確保するようにしてください。 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(省略可能) 一時パスに RAM ディスクを使用する
次の例では、16 GB の RAM ディスクと blobfuse 用のディレクトリを作成します。 ニーズに基づいてサイズを選択します。 この RAM ディスクにより、blobfuse は、サイズが最大 16 GB のファイルを開くことができます。 
```bash
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>一時パスとして SSD を使用する
Azure では、blobfuse に待機時間の短いバッファを提供するために、VM 上で使用可能な一時ディスク (SSD) を使用できます。 Ubuntu ディストリビューションでは、この一時ディスクは '/mnt' にマウントされます。 Red Hat および CentOS ディストリビューションでは、ディスクは '/mnt/resource/' にマウントされます。

ユーザーが一時パスにアクセスできることを確認してください。
```bash
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>ストレージ アカウントの資格情報を構成する
blobfuse では、資格情報が次の形式でテキスト ファイルに格納されている必要があります。 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```
`accountName` は完全な URL ではなく、ご自分のストレージ アカウントのプレフィックスです。

次を使用してこのファイルを作成します。

```
touch ~/fuse_connection.cfg
```

このファイルを作成して編集したら、他のユーザーが読み取れないよう、必ずアクセスを制限してください。
```bash
chmod 600 fuse_connection.cfg
```

> [!NOTE]
> Windows で構成ファイルを作成した場合は、`dos2unix` を実行してこのファイルをサニタイズし、Unix 形式に変換します。 
>

### <a name="create-an-empty-directory-for-mounting"></a>マウント用の空のディレクトリを作成する
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>マウントする

> [!NOTE]
> マウント オプションの完全な一覧については、「[the blobfuse repository (blobfuse リポジトリ)](https://github.com/Azure/azure-storage-fuse#mount-options)」を確認してください。  
> 

blobfuse をマウントするには、ユーザーが次のコマンドを実行します。 このコマンドは、'/path/to/fuse_connection.cfg' で指定されたコンテナーを '/mycontainer' の場所にマウントします。

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

これで、通常のファイル システム API を使用してブロック BLOB にアクセスできるようになりました。 既定で、ディレクトリをマウントしたユーザーがそのディレクトリにアクセスできる唯一の者であるため、アクセスがセキュリティ保護されます。 すべてのユーザーにアクセスを許可するには、オプション ```-o allow_other``` を使用してマウントできます。 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>次の手順

* [blobfuse ホーム ページ](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [blobfuse の問題を報告する](https://github.com/Azure/azure-storage-fuse/issues) 

