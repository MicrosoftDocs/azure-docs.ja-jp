---
title: チュートリアル:Azure での SSL 証明書を使用した Linux Web サーバーのセキュリティ保護
description: このチュートリアルでは、Azure CLI を使用して、Azure Key Vault に格納されている SSL 証明書を使って NGINX Web サーバーを実行する Linux 仮想マシンを保護する方法について説明します。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: aaa3f32cc48c6d051a2ff2a959372886435e5dcb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976164"
---
# <a name="tutorial-secure-a-web-server-on-a-linux-virtual-machine-in-azure-with-ssl-certificates-stored-in-key-vault"></a>チュートリアル:Key Vault に格納されている SSL 証明書を使って Azure 内の Linux 仮想マシン上の Web サーバーをセキュリティ保護する
Web サーバーをセキュリティ保護するには、Secure Sockets Layer (SSL) 証明書を使用した Web トラフィックの暗号化が利用できます。 これらの SSL 証明書は Azure Key Vault に格納できるため、Azure 上の仮想マシン (VM) に、セキュリティで保護された証明書のデプロイが可能になります。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure Key Vault を作成する
> * 証明書を生成したり、Key Vault にアップロードしたりする
> * VM の作成と NGINX Web サーバーのインストール
> * VM への証明書の取り込みと NGINX のSSL バインドの構成

このチュートリアルでは、[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) で CLI を使用します。このバージョンは常に更新され最新になっています。 Cloud Shell を開くには、コード ブロックの上部にある **[試してみる]** を選択します。

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.30 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。


## <a name="overview"></a>概要
Azure Key Vault では、証明書やパスワードなどの暗号化キーと秘密が保護されます。 Key Vault は、証明書の管理プロセスを合理化し、証明書にアクセスするキーの管理を維持するのに役立ちます。 Key Vault 内に自己署名証明書を作成したり、既に所有している、既存の信頼された証明書をアップロードしたりできます。

組み込みの証明書を含むカスタム VM イメージを使用するのではなく、実行中の VM に証明書を取り込みます。 このプロセスでは、デプロイ時に最新の証明書が Web サーバーにインストールされていることを確認します。 証明書を更新するか置き換える場合には、新しいカスタム VM イメージを作成する必要もありません。 追加の VM を作成すると、最新の証明書が自動的に取り込まれます。 すべてのプロセスにおいて、証明書が Azure プラットフォームから流出したり、スクリプト、コマンドラインの履歴、またはテンプレートで公開されたりすることはありません。


## <a name="create-an-azure-key-vault"></a>Azure Key Vault を作成する
Key Vault と証明書を作成する前に、[az group create](/cli/azure/group) を使用してリソース グループを作成します。 次の例では、*myResourceGroupSecureWeb* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroupSecureWeb --location eastus
```

次に、[az keyvault create](/cli/azure/keyvault) を使用して Key Vault を作成し、VM をデプロイするときに使用できるようにします。 各 Key Vault には一意の名前が必要であり、その名前はすべて小文字にする必要があります。 次の例の *\<mykeyvault>* は一意の Key Vault 名で置き換えてください。

```azurecli-interactive 
keyvault_name=<mykeyvault>
az keyvault create \
    --resource-group myResourceGroupSecureWeb \
    --name $keyvault_name \
    --enabled-for-deployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>証明書を生成して Key Vault に格納する
実際の運用では、[az keyvault certificate import](/cli/azure/keyvault/certificate) を使用して、信頼できるプロバイダーによって署名された有効な証明書をインポートする必要があります。 このチュートリアルでは、[az keyvault certificate create](/cli/azure/keyvault/certificate) で、既定の証明書ポリシーを使用する自己署名証明書を生成する方法を次の例に示します。

```azurecli-interactive 
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```

### <a name="prepare-a-certificate-for-use-with-a-vm"></a>VM で使用する証明書を準備する
VM の作成処理の際に証明書を使用するには、[az keyvault secret list-versions](/cli/azure/keyvault/secret) を使用して証明書の ID を取得します。 [az vm secret format](/cli/azure/vm/secret#az-vm-secret-format) を使用して証明書を変換します。 次の例では、以降の手順で使用しやすくするために、コマンドの出力を変数に割り当てています。

```azurecli-interactive 
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm secret format --secrets "$secret" -g myResourceGroupSecureWeb --keyvault $keyvault_name)
```

### <a name="create-a-cloud-init-config-to-secure-nginx"></a>NGINX をセキュリティで保護する cloud-init 構成を作成する
[cloud-Init](https://cloudinit.readthedocs.io) は、Linux VM を初回起動時にカスタマイズするために広く使用されているアプローチです。 cloud-init を使って、パッケージをインストールしてファイルを書き込んだり、ユーザーとセキュリティを構成したりすることができます。 初回起動処理中に cloud-init が実行されるので、構成を適用するために追加の手順や必要なエージェントはありません。

VM を作成するとき、証明書とキーは、保護された */var/lib/waagent/* ディレクトリに格納されます。 VM への証明書の追加と Web サーバーの構成を自動化するには、cloud-init を使用します。 この例では NGINX Web サーバーのインストールと構成を行います。 同じプロセスを使用して、Apache のインストールと構成を行えます。 

*cloud-init-web-server.txt* というファイルを作成し、次の構成を貼り付けます。

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
      }
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
```

### <a name="create-a-secure-vm"></a>セキュリティで保護された VM を作成する
ここで [az vm create](/cli/azure/vm) を使用して VM を作成します。 証明書のデータは、`--secrets` パラメーターを使用して Key Vault から挿入されます。 cloud-init コンフィグに `--custom-data` パラメーターを渡します。

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-web-server.txt \
    --secrets "$vm_secret"
```

VM が作成され、パッケージがインストールされて、アプリが開始されるには、数分かかります。 VM が作成されたら、Azure CLI によって表示される `publicIpAddress` をメモしてください。 このアドレスは、Web ブラウザーから自分のサイトにアクセスするために使用します。

セキュリティで保護された Web トラフィックが VM にアクセスできるようにするには、[az vm open-port](/cli/azure/vm) を使用してインターネットからポート 443 を開きます。

```azurecli-interactive 
az vm open-port \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --port 443
```


### <a name="test-the-secure-web-app"></a>セキュリティで保護された Web アプリをテストする
Web ブラウザーを開き、アドレス バーに「*https:\/\/\<publicIpAddress>* 」と入力できるようになりました。 VM 作成処理で取得した独自のパブリック IP アドレスを指定します。 自己署名証明書を使用した場合は、セキュリティ警告を受け入れます。

![Web ブラウザーのセキュリティ警告を受け入れる](./media/tutorial-secure-web-server/browser-warning.png)

セキュリティで保護された NGINX サイトは、次の例のように表示されます。

![セキュリティで保護された実行中の NGINX サイトの表示](./media/tutorial-secure-web-server/secured-nginx.png)


## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Key Vault に格納されている SSL 証明書を使用して NGINX Web サーバーを保護しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Azure Key Vault を作成する
> * 証明書を生成したり、Key Vault にアップロードしたりする
> * VM の作成と NGINX Web サーバーのインストール
> * VM への証明書の取り込みと NGINX のSSL バインドの構成

次のリンクをクリックして、あらかじめ用意されている仮想マシン スクリプト サンプルをご覧ください。

> [!div class="nextstepaction"]
> [Windows 仮想マシンのスクリプト サンプル](./cli-samples.md)
