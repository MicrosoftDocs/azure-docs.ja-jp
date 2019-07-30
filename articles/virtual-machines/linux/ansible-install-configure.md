---
title: クイックスタート - Azure で Linux 仮想マシンに Ansible をインストールする | Microsoft Docs
description: このクイックスタートでは、Ubuntu、CentOS、SLES で Azure リソースを管理するため、Ansible をインストールして構成する方法を説明します
keywords: Ansible, Azure, DevOps, Bash, Cloud Shell, プレイブック, Bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: d3302d999dd70a83be18ce610b9c3d44992c865c
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671833"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>クイック スタート:Azure で Linux 仮想マシンに Ansible をインストールする

Ansible を使用すると、環境でのリソースの展開と構成を自動化することができます。 この記事では、最も一般的ないくつかの Linux ディストリビューション用に Ansible を構成する方法を示します。 他のディストリビューションに Ansible をインストールするには、インストールするパッケージを特定のプラットフォーム用に調整してください。 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../../../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Linux または Linux 仮想マシンへのアクセス** - Linux マシンを所有していない場合は、[Linux 仮想マシン](/azure/virtual-network/quick-create-cli)を作成してください。

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Azure の Linux 仮想マシンへの Ansible のインストール

Ansible のインストール手順については、ご利用の Linux マシンにサインインし、次のいずれかのディストリビューションを選択してください。

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

このセクションでは、Ansible を使用するように CentOS を構成します。

1. ターミナル ウィンドウを開きます。

1. 次のコマンドを入力して、Azure Python SDK モジュール用に必要なパッケージをインストールします。

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. 次のコマンドを入力して、Ansible の必要なパッケージをインストールします。

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Azure 資格情報を作成します](#create-azure-credentials)。

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

このセクションでは、Ansible を使用するように Ubuntu を構成します。

1. ターミナル ウィンドウを開きます。

1. 次のコマンドを入力して、Azure Python SDK モジュール用に必要なパッケージをインストールします。

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. 次のコマンドを入力して、Ansible の必要なパッケージをインストールします。

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Azure 資格情報を作成します](#create-azure-credentials)。

### <a name="sles-12-sp2"></a>SLES 12 SP2

このセクションでは、Ansible を使用するように SLES を構成します。

1. ターミナル ウィンドウを開きます。

1. 次のコマンドを入力して、Azure Python SDK モジュール用に必要なパッケージをインストールします。

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. 次のコマンドを入力して、Ansible の必要なパッケージをインストールします。

    ```bash
    sudo pip install ansible[azure]
    ```

1. 次のコマンドを入力して、競合している Python 暗号パッケージを削除します。

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [Azure 資格情報を作成します](#create-azure-credentials)。

## <a name="create-azure-credentials"></a>Azure 資格情報の作成

Ansible の資格情報を構成するには、次の情報が必要です。

* Azure サブスクリプション ID 
* サービス プリンシパルの値

Ansible Tower または Jenkins を使っている場合は、サービス プリンシパルの値を環境変数として宣言します。

次のいずれかの方法を使って、Ansible の資格情報を構成します。

- [Ansible の資格情報ファイルの作成](#file-credentials)
- [Ansible 環境変数の使用](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Ansible の資格情報ファイルの作成

このセクションでは、Ansible に資格情報を提供するためのローカル資格情報ファイルを作成します。 

Ansible の資格情報の定義について詳しくは、「[Providing Credentials to Azure Modules (Azure モジュールに資格情報を提供する)](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules)」をご覧ください。

1. 開発環境の場合は、ホストの仮想マシンで `credentials` という名前のファイルを作成します。

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. そのファイルに次の行を挿入します。 プレースホルダーをサービス プリンシパルの値に置き換えます。

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. ファイルを保存して閉じます。

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Ansible 環境変数の使用

このセクションでは、Ansible の資格情報を構成するためにサービス プリンシパルの値をエクスポートします。

1. ターミナル ウィンドウを開きます。

1. サービス プリンシパルの値をエクスポートします。

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="verify-the-configuration"></a>構成を確認する

正しく構成されたことを確認するには、Ansible を使用して Azure リソース グループを作成します。

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [クイック スタート:Ansible を使用して Azure で Linux 仮想マシンを構成する](./ansible-create-vm.md)