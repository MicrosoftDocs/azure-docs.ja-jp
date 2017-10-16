---
title: "Azure 仮想マシンで使用するための Ansible のインストールと構成 |Microsoft ドキュメント"
description: "Ubuntu、CentOS、SLES で Azure リソースを管理するため、Ansible をインストールし構成する方法を説明します"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: iainfou
ms.openlocfilehash: 3fabfee169c3ebedc7bca81a6a6d9a44171daf20
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>Azure の仮想マシンを管理するための Ansible のインストールと構成
この記事では、最も一般的な Linux ディストリビューションに Ansible と必須の Azure Python SDK モジュールをインストールする方法について説明します。 インストールしたパッケージを特定のプラットフォーム用に合わせることで、他のディストリビューションに Ansible をインストールできます。 Azure リソースを安全に作成するため、Ansible で使用する資格情報を作成し定義する方法についても説明します。 

詳細なインストール オプションとプラットフォームを追加する手順については、[Ansible インストール ガイド](https://docs.ansible.com/ansible/intro_installation.html)に関するページを参照してください。


## <a name="install-ansible"></a>Ansible のインストール
最初に、[az group create](/cli/azure/group#create) を使用して、リソース グループを作成します。 次の例では、*myResourceGroupAnsible* という名前のリソース グループを場所 *eastus* に作成します。

```azurecli
az group create --name myResourceGroupAnsible --location eastus
```

ここで VM を作成し、次のディストリビューションのいずれかに Ansible をインストールします。

- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [CentOS 7.3](#centos-73)
- [SLES 12.2 SP2](#sles-122-sp2)

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS
[az vm create](/cli/azure/vm#create) を使用して VM を作成します。 次の例では、*myVMAnsible* という名前の VM を作成します。

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM 作成操作の出力に記されている `publicIpAddress` を使用して、VM に SSH を適用します。

```bash
ssh azureuser@<publicIpAddress>
```

VM で、Azure Python SDK モジュールに必要なパッケージと Ansible を、次のようにインストールします。

```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
pip install ansible[azure]
```

次に、[Azure 資格情報の作成](#create-azure-credentials)に進みます。


### <a name="centos-73"></a>CentOS 7.3
[az vm create](/cli/azure/vm#create) を使用して VM を作成します。 次の例では、*myVMAnsible* という名前の VM を作成します。

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM 作成操作の出力に記されている `publicIpAddress` を使用して、VM に SSH を適用します。

```bash
ssh azureuser@<publicIpAddress>
```

VM で、Azure Python SDK モジュールに必要なパッケージと Ansible を、次のようにインストールします。

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

次に、[Azure 資格情報の作成](#create-azure-credentials)に進みます。


### <a name="sles-122-sp2"></a>SLES 12.2 SP2
[az vm create](/cli/azure/vm#create) を使用して VM を作成します。 次の例では、*myVMAnsible* という名前の VM を作成します。

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image SLES \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM 作成操作の出力に記されている `publicIpAddress` を使用して、VM に SSH を適用します。

```bash
ssh azureuser@<publicIpAddress>
```

VM で、Azure Python SDK モジュールに必要なパッケージと Ansible を、次のようにインストールします。

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 python-devel \
    libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

次に、[Azure 資格情報の作成](#create-azure-credentials)に進みます。


## <a name="create-azure-credentials"></a>Azure 資格情報の作成
Ansible は、ユーザー名とパスワード、またはサービス プリンシパルを使用して Azure と通信します。 Azure のサービス プリンシパルは、アプリケーション、サービス、Ansible などのオートメーション ツールで使用できるセキュリティ ID です。 Azure でサービス プリンシパルが実行できる操作を設定するアクセス許可の制御と定義を行います。 この例では、ユーザー名とパスワードを提供するだけでなく、セキュリティを強化するために、基本的なサービス プリンシパルを作成します。

[az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) を使用してサービス プリンシパルを作成し、Ansible に必要な資格情報を出力します。

```azurecli
az ad sp create-for-rbac --query [appId,password,tenant]
```

上記のコマンドの出力例は、次のとおりです。

```json
[
  "eec5624a-90f8-4386-8a87-02730b5410d5",
  "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "72f988bf-86f1-41af-91ab-2d7cd011db47"
]
```

Azure に対して認証するには、[az account show](/cli/azure/account#show) で Azure サブスクリプション ID を取得する必要もあります。

```azurecli
az account show --query [id] --output tsv
```

次の手順で、これら 2 つのコマンドからの出力を使用します。


## <a name="create-ansible-credentials-file"></a>Ansible の資格情報ファイルの作成
Ansible に資格情報を提供するには、環境変数を定義するか、ローカル資格情報ファイルを作成します。 Ansible 資格情報を定義する方法の詳細については、「[Providing Credentials to Azure Modules (Azure モジュールに資格情報を提供する)](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules)」を参照してください。 

開発環境の場合は、次のようにホスト VM 上に Ansible の*資格情報*ファイルを作成します。

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

*資格情報*ファイル自体では、サービス プリンシパル作成時の出力とサブスクリプション ID を組み合わせます。 前述の [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) コマンドからの出力は、*client_id*、*secret*、*tenant* に必要な順序と同じになっています。 次の*資格情報*ファイル例は、これらの値が前述の出力と一致することを示しています。 実際の値を次のように入力します。

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```


## <a name="use-ansible-environment-variables"></a>Ansible 環境変数の使用
Anabilities Tower や Jenkins などのツールを使用する場合は、以下のように環境変数を定義します。 これらの変数は、サービス プリンシパル作成時の出力とサブスクリプション ID を組み合わせます。 前述の [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) コマンドからの出力は、*AZURE_CLIENT_ID*、*AZURE_SECRET*、*AZURE_TENANT* に必要な順序と同じになっています。 

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>次のステップ
これで、Ansible と必須の Azure Python SDK モジュールがインストールされ、Ansible 用に定義された資格情報を取得できました。 [Ansible を使用して VM を作成する](ansible-create-vm.md)方法を説明します。 また、[完全な Azure VM を作成し、Ansible でリソースをサポートする](ansible-create-complete-vm.md)方法についても説明します。