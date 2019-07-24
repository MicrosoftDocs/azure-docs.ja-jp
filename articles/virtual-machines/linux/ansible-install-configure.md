---
title: Azure 仮想マシンへの Ansible のインストール
description: Ubuntu、CentOS、SLES で Azure リソースを管理するため、Ansible をインストールし構成する方法を説明します
ms.service: virtual-machines-linux
keywords: Ansible, Azure, DevOps, Bash, Cloud Shell, プレイブック, Bash
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/21/2018
ms.openlocfilehash: 38a1ffdc815b357f7bb7ebe2c337b55a738fb6b5
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57790434"
---
# <a name="install-ansible-on-azure-virtual-machines"></a>Azure 仮想マシンへの Ansible のインストール

Ansible を使用すると、環境でのリソースの展開と構成を自動化することができます。 Ansible を使用すると、他のリソースと同じように、Azure で仮想マシン (VM) を管理できます。 この記事では、最も一般的な Linux ディストリビューションに Ansible と必須の Azure Python SDK モジュールをインストールする方法について説明します。 インストールしたパッケージを特定のプラットフォーム用に合わせることで、他のディストリビューションに Ansible をインストールできます。 Azure リソースを安全に作成するため、Ansible で使用する資格情報を作成し定義する方法についても説明します。 Cloud Shell で使用可能なその他のツールの一覧については、[Azure Cloud Shell の Bash の機能とツール](../../cloud-shell/features.md#tools)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション** - Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。

- **Linux または Linux 仮想マシンへのアクセス** - Linux マシンを所有していない場合は、[Linux 仮想マシン](https://docs.microsoft.com/azure/virtual-network/quick-create-cli)を作成してください。

- **Azure サービス プリンシパル**:「[Azure CLI 2.0 で Azure サービス プリンシパルを作成する](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)」の「**サービス プリンシパルを作成する**」セクションの指示に従ってください。 **appId**、**displayName**、**password**、および **tenant** の値を書き留めます。

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Azure の Linux 仮想マシンへの Ansible のインストール

Ansible のインストール手順については、ご利用の Linux マシンにサインインし、次のいずれかのディストリビューションを選択してください。

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

ターミナルまたは Bash ウィンドウから次のコマンドを入力して、Azure Python SDK モジュールの必須パッケージと Ansible をインストールします。

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

「[Azure 資格情報の作成](#create-azure-credentials)」のセクションに記載された手順に従います。

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

ターミナルまたは Bash ウィンドウから次のコマンドを入力して、Azure Python SDK モジュールの必須パッケージと Ansible をインストールします。


```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

「[Azure 資格情報の作成](#create-azure-credentials)」のセクションに記載された手順に従います。

### <a name="sles-12-sp2"></a>SLES 12 SP2

ターミナルまたは Bash ウィンドウから次のコマンドを入力して、Azure Python SDK モジュールの必須パッケージと Ansible をインストールします。

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

「[Azure 資格情報の作成](#create-azure-credentials)」のセクションに記載された手順に従います。

## <a name="create-azure-credentials"></a>Azure 資格情報の作成

Ansible の資格情報は、サービス プリンシパルの作成から得られた情報とサブスクリプション ID の組み合わせを使用して、次の 2 とおりの方法で構成します。

- [Ansible の資格情報ファイルの作成](#file-credentials)
- [Ansible 環境変数の使用](#env-credentials)

Ansible Tower や Jenkins などのツールを使用する場合は、サービス プリンシパルの値を環境変数として宣言する方法を選ぶ必要があります。

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Ansible の資格情報ファイルの作成

このセクションでは、Ansible に資格情報を与えるローカルの資格情報ファイルの作成方法について説明します。 Ansible 資格情報を定義する方法の詳細については、「[Providing Credentials to Azure Modules (Azure モジュールに資格情報を提供する)](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules)」を参照してください。

開発環境の場合は、次のようにホスト仮想マシン上に Ansible の*資格情報*ファイルを作成します。

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

*credentials* ファイルに次の行を挿入します。プレースホルダーは、サービス プリンシパルの作成から得られた情報に置き換えてください。

```bash
[default]
subscription_id=<your-subscription_id>
client_id=<security-principal-appid>
secret=<security-principal-password>
tenant=<security-principal-tenant>
```

ファイルを保存して閉じます。

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Ansible 環境変数の使用

このセクションでは、Ansible の資格情報を環境変数としてエクスポートすることによって構成する方法について説明します。

ターミナルまたは Bash ウィンドウで、次のコマンドを入力します。

```bash
export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
export AZURE_CLIENT_ID=<security-principal-appid>
export AZURE_SECRET=<security-principal-password>
export AZURE_TENANT=<security-principal-tenant>
```

## <a name="verify-the-configuration"></a>構成を確認する
正しく構成されたことを確認するために、ここで Ansible を使ってリソース グループを作成してみましょう。

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [Ansible を使用して Azure に Linux 仮想マシンを作成する](./ansible-create-vm.md)
