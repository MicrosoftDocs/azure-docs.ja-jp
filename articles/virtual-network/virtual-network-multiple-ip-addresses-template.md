---
title: "Azure 仮想マシンの複数の IP アドレス - テンプレート | Microsoft Docs"
description: "Azure Resource Manager テンプレートを使用して仮想マシンに複数の IP アドレスを割り当てる方法を説明します。"
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: 5267ed6a2d9278b79e9fb98c1067eceae819cb6f
ms.lasthandoff: 03/22/2017


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して仮想マシンに複数の IP アドレスを割り当てる

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

この記事では、Resource Manager テンプレートを使用して Azure Resource Manager デプロイメント モデルで仮想マシン (VM) を作成する方法について説明します。 従来のデプロイ モデルで VM をデプロイする場合は、複数のパブリック IP アドレスやプライベート IP アドレスを同じ NIC に割り当てることはできません。 Azure のデプロイ モデルの詳細については、[デプロイ モデルの概要](../resource-manager-deployment-model.md)に関する記事をご覧ください。

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="template-description"></a>テンプレートの説明

テンプレートをデプロイすると、さまざまな構成値を持つ Azure リソースを連続して迅速に作成できます。 Azure Resource Manager テンプレートについて詳しくは、[Resource Manager テンプレートのチュートリアル](../azure-resource-manager/resource-manager-template-walkthrough.md?toc=%2fazure%2fvirtual-network%2ftoc.json)の記事をご覧ください。 この記事では、[Deploy a VM with multiple IPs](https://azure.microsoft.com/resources/templates/101-vm-multiple-ipconfig)テンプレートが使用されています。

<a name="resources"></a>テンプレートをデプロイすると次のリソースが作成されます。

|リソース|名前|説明|
|---|---|---|
|ネットワーク インターフェイス|*myNic1*|この記事のシナリオ セクションで説明されている 3 つの IP 構成が作成され、この NIC に割り当てられています。|
|パブリック IP アドレス リソース|*myPublicIP* と *myPublicIP2* の 2 つが作成されます。|これらのリソースは、割り当て済みの静的パブリック IP アドレスであり、このシナリオで説明されている *IPConfig 1* と *IPConfig 2* の IP 構成に割り当てられます。|
|VM|*myVM1*|Standard DS3 仮想マシンです。|
|Virtual Network|*myVNet1*|*mySubnet*という名前のサブネットを 1 つ持つ仮想ネットワークです。|
|ストレージ アカウント|デプロイごとに一意です|ストレージ アカウントです。|

<a name="parameters"></a>テンプレートをデプロイする場合は、次のパラメーターの値を指定する必要があります。

|名前|Description|
|---|---|
|adminUsername|管理者のユーザー名です。 ユーザー名は [Azure のユーザー名要件](../virtual-machines/virtual-machines-windows-faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に準拠する必要があります。|
|adminPassword|管理者パスワードです。パスワードは [Azure のパスワード要件](../virtual-machines/virtual-machines-windows-faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)に準拠する必要があります。|
|dnsLabelPrefix|PublicIPAddressName1 の DNS 名です。 DNS 名は、VM に割り当てられているパブリック IP アドレスのいずれかに解決されます。 名前は、VM を作成する Azure リージョン (場所) 内で一意である必要があります。|
|dnsLabelPrefix1|PublicIPAddressName2 の DNS 名です。 DNS 名は、VM に割り当てられているパブリック IP アドレスのいずれかに解決されます。 名前は、VM を作成する Azure リージョン (場所) 内で一意である必要があります。|
|OSVersion|VM の Windows または Linux バージョンです。 オペレーティング システムは、選択した Windows または Linux バージョンの完全にパッチが適用されたイメージです。|
|imagePublisher|選択した VM の Windows/Linux イメージ発行元です。|
|imageOffer|選択した VM の Windows/Linux イメージです。|

テンプレートで展開された各リソースは、いくつかの既定の設定が構成されます。 これらの設定は、次のいずれかの方法を使用して確認できます。

- **GitHub でテンプレートを確認する:** テンプレートを使い慣れている場合は、[テンプレート](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json)内で設定を確認できます。 
- **デプロイ後に設定を確認する:** テンプレートに慣れていない場合は、次のいずれかのセクションを使用してテンプレートをデプロイした後に、設定を確認できます。

テンプレートのデプロイには、Azure Portal、PowerShell、Azure コマンド ライン インターフェイス (CLI) を使用できます。 いずれの方法を使用しても同じ結果が得られます。 テンプレートをデプロイするには、次のいずれかのセクションの手順を実行します。

## <a name="deploy-using-the-azure-portal"></a>Azure Portal を使用したデプロイ

Azure Portal を使用してテンプレートをデプロイするには、次の手順を実行します。

1. 必要に応じて、テンプレートを変更します。 テンプレートによって、この記事の「[リソース](#resources)」セクションの一覧にあるリソースと設定がデプロイされます。 テンプレートとその作成方法の詳細については、「[Azure Resource Manager のテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」の記事をご覧ください。
2. 次のいずれかの方法を使用して、テンプレートをデプロイします。
    - **ポータルでテンプレートを選択する:** 「[Deploy resources from custom template (カスタム テンプレートからのリソースのデプロイ)](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template)」の記事の手順を実行します。 *101-vm-multiple-ipconfig*という名前の既存のテンプレートを選択します。
    - **直接:** 次のボタンをクリックして、ポータルで直接テンプレートを開きます。<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-multiple-ipconfig%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

選択した方法に関係なく、この記事で説明済みの[パラメーター](#parameters)の値を指定する必要があります。 VM をデプロイしたら、この記事の「[VM オペレーティング システムに IP アドレスを追加する](#os-config)」セクションに記載された手順を実行して、プライベート IP アドレスをデプロイした VM オペレーティング システムに追加します。 オペレーティング システムにパブリック IP アドレスは追加しないでください。

## <a name="deploy-using-powershell"></a>PowerShell を使用したデプロイ

PowerShell を使用してテンプレートをデプロイするには、次の手順を実行します。

1. 「[Deploy a template with PowerShell (PowerShell を使用したテンプレートのデプロイ)](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy)」の記事の手順を実行して、テンプレートをデプロイします。 この記事では、テンプレートをデプロイする際に使用する複数のオプションについて説明されています。 `-TemplateUri parameter`を使用してデプロイする場合は、このテンプレートの URI は *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json* になります。 `-TemplateFile` パラメーターを使用してデプロイする場合は、GitHub の[テンプレート ファイル](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) の内容を自分のコンピューター上の新しいファイルにコピーします。 必要に応じて、テンプレートの内容を変更します。 テンプレートによって、この記事の「[リソース](#resources)」セクションの一覧にあるリソースと設定がデプロイされます。 テンプレートとその作成方法の詳細については、「[Azure Resource Manager のテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)」の記事をご覧ください。

    テンプレートのデプロイに使用するオプションに関係なく、この記事の「[パラメーター](#parameters)」セクションの一覧にあるパラメーター値を指定する必要があります。 パラメーター ファイルを使用してパラメーターを指定する場合は、GitHub の[パラメーター ファイル](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json)の内容を自分のコンピューター上の新しいファイルにコピーします。 ファイル内の値を変更します。 作成したファイルを、`-TemplateParameterFile` パラメーターの値として使用します。
    
    OSVersion、ImagePublisher、imageOffer のパラメーターの有効な値を取得するには、「[Navigate and select Windows VM images (Windows VM イメージの検索と選択)](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md)」の記事の手順を実行します。

    >[!TIP]
    >dnslabelprefix を使用できるかどうかわからない場合は、`Test-AzureRmDnsAvailability -DomainNameLabel <name-you-want-to-use> -Location <location>` コマンドを入力して確認します。 使用できる場合は、コマンドが `True` を返します。

2. VM をデプロイしたら、この記事の「[VM オペレーティング システムに IP アドレスを追加する](#os-config)」セクションに記載された手順を実行して、プライベート IP アドレスをデプロイした VM オペレーティング システムに追加します。 オペレーティング システムにパブリック IP アドレスは追加しないでください。

## <a name="deploy-using-the-azure-cli"></a>Azure CLI を使用したデプロイ

Azure CLI 1.0 を使用してテンプレートをデプロイするには、次の手順を実行します。

1. 「[Deploy a template with Azure CLI (Azure CLI を使用したテンプレートのデプロイ)](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy)」の記事の手順を実行して、テンプレートをデプロイします。 この記事では、テンプレートをデプロイする際に使用する複数のオプションについて説明されています。 `--template-uri` (-f) を使用してデプロイする場合は、このテンプレートの URI は *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json* になります。 `--template-file` (-f) パラメーターを使用してデプロイする場合は、GitHub の[テンプレート ファイル](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) の内容を自分のコンピューター上の新しいファイルにコピーします。 必要に応じて、テンプレートの内容を変更します。 テンプレートによって、この記事の「[リソース](#resources)」セクションの一覧にあるリソースと設定がデプロイされます。 テンプレートとその作成方法の詳細については、「[Azure Resource Manager のテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)」の記事をご覧ください。

    テンプレートのデプロイに使用するオプションに関係なく、この記事の「[パラメーター](#parameters)」セクションの一覧にあるパラメーター値を指定する必要があります。 パラメーター ファイルを使用してパラメーターを指定する場合は、GitHub の[パラメーター ファイル](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json)の内容を自分のコンピューター上の新しいファイルにコピーします。 ファイル内の値を変更します。 作成したファイルを、`--parameters-file` (-e) パラメーターの値として使用します。
    
    OSVersion、ImagePublisher、imageOffer のパラメーターの有効な値を取得するには、「[Navigate and select Windows VM images (Windows VM イメージの検索と選択)](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md)」の記事の手順を実行します。

2. VM をデプロイしたら、この記事の「[VM オペレーティング システムに IP アドレスを追加する](#os-config)」セクションに記載された手順を実行して、プライベート IP アドレスをデプロイした VM オペレーティング システムに追加します。 オペレーティング システムにパブリック IP アドレスは追加しないでください。

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]

