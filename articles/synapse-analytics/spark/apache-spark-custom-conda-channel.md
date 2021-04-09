---
title: パッケージ管理のためのカスタム Conda チャネルを作成する
description: パッケージ管理のためのカスタム Conda チャネルを作成する方法について説明します
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 528ba4a1be3650a81772d78a438f03611b9bd761
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107215"
---
# <a name="create-a-custom-conda-channel-for-package-management"></a>パッケージ管理のためのカスタム Conda チャネルを作成する 
Python パッケージのインストール時に、Conda パッケージ マネージャーでは、パッケージを探すためにチャネルが使用されます。 場合によっては、さまざまな理由でカスタム Conda チャネルを作成する必要があります。 たとえば、次のようなことが考えられます。

- ご利用のワークスペースはデータ流出から保護されていて、アウトバウンド接続はブロックされている。  
- パブリック リポジトリにアップロードしたくないパッケージがある。
- ご利用のワークスペース内で、ユーザー用に代替リポジトリを設定したい。

この記事では、ご利用の Azure Data Lake Storage アカウント内でカスタム Conda チャネルを作成するのに役立つステップバイステップ ガイドを示します。

## <a name="set-up-your-local-machine"></a>ご利用のローカル コンピューターを設定する

1. ローカル コンピューターに Conda をインストールします。同じランタイムで使用される Conda バージョンを特定するには、[Azure Synapse Spark ランタイム](./apache-spark-version-support.md)に関するページを参照してください。
   
2. カスタム チャネルを作成するには、conda-build をインストールします。
```
conda install conda-build
```
3. サービスを提供するプラットフォームにすべてのパッケージを整理します。 この例では、Anaconda アーカイブをローカル コンピューターにインストールします。

```
sudo wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh 
sudo chmod +x Anaconda3-4.4.0-Linux-x86_64.sh  
sudo bash Anaconda3-4.4.0-Linux-x86_64.sh -b -p /usr/lib/anaconda3 
export PATH="/usr/lib/anaconda3/bin:$PATH" 
sudo chmod 777 -R /usr/lib/anaconda3a.  
```
## <a name="mount-the-storage-account-onto-your-machine"></a>ストレージ アカウントをコンピューターにマウントする
次に、Azure Data Lake Storage Gen2 アカウントをローカル コンピューターにマウントします。 このプロセスは WASB アカウントを使用して実行することもできます。ただし、紹介するのは、ADLSg2 アカウントの例です。 
 
ローカル コンピューターにストレージ アカウントをマウントする方法の詳細については、[こちらのページ](https://github.com/Azure/azure-storage-fuse#blobfuse )を参照してください。 

1. blobfuse は、Microsoft 製品用の Linux ソフトウェア リポジトリからインストールできます。

```
wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb 
sudo dpkg -i packages-microsoft-prod.deb 
sudo apt-get update 
sudo apt-get install blobfuse fuse 
export AZURE_STORAGE_ACCOUNT=<<myaccountname>
export AZURE_STORAGE_ACCESS_KEY=<<myaccountkey>>
export AZURE_STORAGE_BLOB_ENDPOINT=*.dfs.core.windows.net 
```

2. 自分のマウントポイント (```mkdir /path/to/mount```) を作成し、blobfuse を使用して BLOB コンテナーをマウントします。 この例では、**mycontainer** 変数の値として **privatechannel** を使用してみましょう。
   
```
blobfuse /path/to/mount --container-name=mycontainer --tmp-path=/mnt/blobfusetmp --use-adls=true --log-level=LOG_DEBUG 
sudo mkdir -p /mnt/blobfusetmp
sudo chown <myuser> /mnt/blobfusetmp
```
## <a name="create-the-channel"></a>チャネルを作成する
次の一連の手順では、カスタム Conda チャネルを作成します。 

1. ご利用のローカル コンピューター上で、自分のカスタム チャネル用のすべてのパッケージを整理するためのディレクトリを作成します。
   
```
mkdir /home/trusted-service-user/privatechannel 
cd ~/privatechannel/ 
mkdir channel1/linux64 
```

2. https://repo.anaconda.com/pkgs/main/linux-64/ からのすべての ```tar.bz2``` パッケージをサブディレクトリに整理します。 依存する tar.bz2 パッケージもすべて必ず含めてください。 

```
cd channel1 
mkdir noarch 
echo '{}' > noarch/repodata.json 
bzip2 -k noarch/repodata.json 

// Create channel 

conda index channel1/noarch 
conda index channel1/linux-64 
conda index channel1 
```

カスタム チャネルを作成するため詳細については、[Conda ユーザーガイド](https://docs.conda.io/projects/conda/latest/user-guide/tasks/create-custom-channels.html)も参照してください。 

## <a name="storage-account-permissions"></a>ストレージ アカウントのアクセス許可
次に、ストレージ アカウントに対するアクセス許可を検証する必要があります。 これらのアクセス許可を設定するには、カスタム チャネルを作成するパスに移動します。 次に、読み取り、一覧表示、実行の各アクセス許可を持つ ```privatechannel``` 用の SAS トークンを作成します。 

チャネル名は、このプロセスで生成され、blob SAS URL になります。  

## <a name="create-a-sample-conda-environment-configuration-file"></a>サンプルの Conda 環境構成ファイルを作成する
最後に、サンプルの Conda ```environment.yml``` ファイルを作成してインストール プロセスを確認します。 DEP 対応ワークスペースがある場合は、環境ファイル内で ``nodefaults`` チャネルを指定する必要があります。

Conda 構成ファイルの例を次に示します。
```
name: sample 
channels: 
  - https://<<storage account name>>.blob.core.windows.net/privatechannel/channel?<<SAS Token>
  - nodefaults 
dependencies: 
  - openssl 
  - ncurses 
```
サンプルの Conda ファイルを作成したら、Conda 仮想環境を作成できます。 

```
conda env create –file sample.yml  
source activate env 
conda list 
```
カスタム チャネルの確認が完了したので、[Python プール管理](./apache-spark-manage-python-packages.md)プロセスを使用することで、Apache Spark プール上でライブラリを更新できます。

## <a name="next-steps"></a>次のステップ
- 既定のライブラリを確認します: [Apache Spark バージョンのサポート](apache-spark-version-support.md)
- Python パッケージを管理する: [Python パッケージの管理](./apache-spark-manage-python-packages.md)

