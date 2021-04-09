---
title: Azure でテンプレートから Linux VM を作成する
description: Azure CLI を使用して Resource Manager テンプレートから Linux VM を作成する方法
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 03/22/2019
ms.author: cynthn
ms.openlocfilehash: a4e1bf56df52717255d2bae0a38186335d922ff1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554685"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して Linux 仮想マシンを作成する方法

Azure Resource Manager テンプレートと、Azure Cloud シェルの Azure CLI を使用して、Linux 仮想マシン (VM) を作成する方法を説明します。 Windows 仮想マシンを作成するには、「[Resource Manager テンプレートから Windows 仮想マシンを作成する](../windows/ps-template.md)」を参照してください。

別の方法としては、Azure portal からテンプレートをデプロイする方法があります。 ポータルでテンプレートを開くには、 **[Azure に配置する]** ボタンを選択します。

[![Azure へのデプロイ](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-sshkey%2Fazuredeploy.json)

## <a name="templates-overview"></a>テンプレートの概要

Azure Resource Manager テンプレートとは、Azure ソリューションのインフラストラクチャと構成を定義する JSON ファイルです。 テンプレートを使えば、ソリューションをそのライフサイクル全体で繰り返しデプロイできます。また、常にリソースが一貫した状態でデプロイされます。 テンプレートの形式とその構築方法の詳細については、「[クイック スタート:Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)」を参照してください。 リソースの種類に関して JSON 構文を確認するには、「[Define resources in Azure Resource Manager templates (Azure Resource Manager テンプレートのリソースの定義)](/azure/templates/microsoft.compute/allversions)」を参照してください。

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

通常、Azure 仮想マシンの作成には、次の 2 つの手順が含まれます。

1. リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 仮想マシンの前にリソース グループを作成する必要があります。
1. 仮想マシンを作成します。

次の例では、[Azure クイック スタート テンプレート](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json)から VM を作成します。 このデプロイでは、SSH 認証のみが許可されます。 値を求められたら、自らの SSH 公開キーの値 (*~/.ssh/id_rsa.pub* の内容など) を指定します。 SSH キー ペアを短時間で作成する必要がある場合は、[Azure に Linux VM 用の SSH キー ペアを作成して使用する方法](mac-create-ssh-keys.md)に関する記事をご覧ください。 テンプレートのコピーを次に示します。

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

CLI スクリプトを実行するには、**[使ってみる]** を選択して Azure Cloud Shell を開きます。 スクリプトを貼り付けるには、シェルを右クリックし、 **[貼り付け]** を選択します。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the project name (used for generating resource names):" &&
read projectName &&
echo "Enter the administrator username:" &&
read username &&
echo "Enter the SSH public key:" &&
read key &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey="$key" &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

最後の Azure CLI コマンドは、新しく作成された VM のパブリック IP アドレスを示しています。 仮想マシンに接続するには、このパブリック IP アドレスが必要です。 この記事の次のセクションを参照してください。

前の例では、GitHub に格納されているテンプレートが指定されています。 テンプレートをダウンロードまたは作成し、`--template-file` パラメーターを使用してローカル パスを指定することもできます。

次にその他のリソースを示します。

- Resource Manager テンプレートを開発する方法については、[Azure Resource Manager のドキュメント](../../azure-resource-manager/index.yml)を参照してください。
- Azure 仮想マシンのスキーマを表示するには、「[Azure テンプレート リファレンス](/azure/templates/microsoft.compute/allversions)」をご覧ください。
- さらに仮想マシン テンプレートのサンプルを表示するには、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)」を参照してください。

## <a name="connect-to-virtual-machine"></a>仮想マシンへの接続

通常どおりに、ご使用の VM に SSH 接続できます。 上記のコマンドで取得した独自のパブリック IP アドレスを指定します。

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>次のステップ

この例では、基本的な Linux VM を作成しました。 アプリケーション フレームワークを含むその他の Resource Manager テンプレートについて、またはさらに複雑な環境の作成方法については、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)」をご覧ください。

テンプレートの作成に関する詳細については、JSON 構文とデプロイしたリソースの種類のプロパティを参照してください。

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
