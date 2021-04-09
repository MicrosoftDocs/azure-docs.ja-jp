---
title: Azure DNS ゾーンとレコードを作成する - Azure Resource Manager テンプレート (ARM テンプレート)
titleSuffix: Azure DNS
description: Azure DNS で、DNS ゾーンとレコードを作成する方法について説明します。 これは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して最初の DNS ゾーンとレコードを作成して管理するための詳細なクイックスタートです。
services: dns
author: duongau
ms.service: dns
ms.topic: quickstart
ms.date: 09/8/2020
ms.author: duau
ms.custom: subject-armqs
ms.openlocfilehash: 24460167e2279e7d3001d0bc16d050beb5b55289
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92791003"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-an-arm-template"></a>ARM テンプレートを使用して Azure DNS ゾーンとレコードを作成する

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、DNS ゾーンとその中の `A` レコードを作成する方法を説明します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、**[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-dns-new-zone%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-azure-dns-new-zone)からのものです。

このクイックスタートでは、サフィックスが `azurequickstart.org` である一意の DNS ゾーンを作成します。 2 つの IP アドレスを指す `A` レコードも、ゾーン内に配置されます。

:::code language="json" source="~/quickstart-templates/101-azure-dns-new-zone/azuredeploy.json":::

テンプレートでは、2 つの Azure リソースが定義されています。

- [Microsoft.Network**/dnsZones/*
- [**Microsoft.Network/dnsZones/A**](/azure/templates/microsoft.network/dnsZones/A): ゾーンに `A` レコードを作成するために使用されます。

Azure Traffic Manager に関連するテンプレートをさらに探すには、「[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)」を参照してください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. 次のコード ブロックの **[使ってみる]** を選択して Azure Cloud Shell を開き、指示に従って Azure にサインインします。

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-dns-new-zone/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    コンソールからプロンプトが表示されるまで待ちます。

1. 前のコード ブロックから **[コピー]** を選択して、PowerShell スクリプトをコピーします。

1. シェル コンソール ウィンドウを右クリックし、 **[貼り付け]** を選択します。

1. 値を入力します。

    テンプレートのデプロイでは、2 つの IP アドレスを指す 1 つの `A` レコードを含むゾーンが作成されます。 リソース グループの名前は、**rg** が付加されたプロジェクト名です。

    テンプレートのデプロイには数秒かかります。 完了すると、次のように出力されます。

    :::image type="content" source="./media/dns-getstarted-template/create-dns-zone-powershell-output.png" alt-text="Azure DNS ゾーンの Resource Manager テンプレートの PowerShell デプロイ出力":::

テンプレートをデプロイするには Azure PowerShell を使用します。 Azure PowerShell だけでなく、Azure portal、Azure CLI、および REST API を使用することもできます。 他のデプロイ方法については、「[テンプレートのデプロイ](../azure-resource-manager/templates/deploy-portal.md)」を参照してください。

## <a name="validate-the-deployment"></a>デプロイの検証

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 左側のウィンドウから **[リソース グループ]** を選択します。

1. 前のセクションで作成したリソース グループを選択します 既定のリソース グループ名は、プロジェクト名に **rg** が追加されたものです。

1. リソース グループには、次の画面に示したリソースが含まれている必要があります。

    :::image type="content" source="./media/dns-getstarted-template/resource-group-dns-zone.png" alt-text="DNS ゾーンのデプロイ リソース グループ":::

1. `azurequickstart.org` というサフィックスの DNS ゾーンを選択し、値 `1.2.3.4` と `1.2.3.5` を参照する `A` レコードがあるゾーンが正しく作成されていることを確認します。

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-overview.png" alt-text="DNS ゾーンのデプロイ":::

1. 前のステップから、ネーム サーバーの名前の 1 つをコピーします。

1. コマンド プロンプトを開いて、次のコマンドを実行します。

   ```cmd
   nslookup www.<dns zone name> <name server name>
   ```

   次に例を示します。

   ```cmd
   nslookup www.2lwynbseszpam.azurequickstart.org ns1-09.azure-dns.com.
   ```

   次のような画面が表示されます。

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-validation.png" alt-text="DNS ゾーンの nslookup":::

ホスト名 `www.2lwynbseszpam.azurequickstart.org` は、構成したとおり、`1.2.3.4` と `1.2.3.5` に解決されています。 この結果で、名前解決が正常に機能していることを確認できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

DNS ゾーンと共に作成したリソースが不要になった場合は、リソース グループを削除します。 これにより、DNS ゾーンとすべての関連リソースが削除されます。

リソース グループを削除するには、`Remove-AzResourceGroup` コマンドレットを呼び出します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、次のものを作成しました。

- [DNS ゾーン]
- `A` レコード

これで、ARM テンプレートを使用して最初の DNS ゾーンとレコードを作成できました。カスタム ドメインで Web アプリのレコードを作成できます。

> [!div class="nextstepaction"]
> [カスタム ドメインにおける Web アプリの DNS レコードの作成](./dns-web-sites-custom-domain.md)
