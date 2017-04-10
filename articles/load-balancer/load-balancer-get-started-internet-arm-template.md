---
title: "インターネットに接続するロード バランサーの作成 - Azure テンプレート | Microsoft Docs"
description: "テンプレートを使用して、リソース マネージャーでインターネット接続ロード バランサーを作成する方法について説明します"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
tags: azure-resource-manager
ms.assetid: b24f4729-4559-4458-8527-71009d242647
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 3539d6f6d3741387174e80ecc132db782d7df9f0
ms.lasthandoff: 03/21/2017

---

# <a name="creating-an-internet-facing-load-balancer-using-a-template"></a>テンプレートを使用したインターネット接続ロード バランサーの作成

> [!div class="op_single_selector"]
> * [ポータル](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [テンプレート](../load-balancer/load-balancer-get-started-internet-arm-template.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

この記事では、リソース マネージャーのデプロイ モデルについて説明します。 [従来のデプロイ モデルを使用してインターネットに接続するロード バランサーを作成する方法](load-balancer-get-started-internet-classic-portal.md)

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>[クリックしてデプロイ] を使用してテンプレートをデプロイする

パブリック リポジトリで使用できるサンプル テンプレートは、上記のシナリオの生成に使用した既定値を含むパラメーター ファイルを使用します。 "クリックしてデプロイ" を使用してこのテンプレートをデプロイするには、 [このリンク](http://go.microsoft.com/fwlink/?LinkId=544801)に従って、 **[Azure へのデプロイ]**をクリックし、必要に応じて既定のパラメーター値を置き換えて、ポータルの指示に従います。

## <a name="deploy-the-template-by-using-powershell"></a>PowerShell を使用してテンプレートをデプロイする

PowerShell を使用してダウンロードしたテンプレートをデプロイするには、次の手順に従います。

1. Azure PowerShell を初めて使用する場合は、 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) を参照し、このページにある手順をすべて最後まで実行し、Azure にサインインしてサブスクリプションを選択します。
2. テンプレートを使用してリソース グループを作成するには、 **New-AzureRmResourceGroupDeployment** コマンドレットを実行します。

    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestRG -Location uswest `
        -TemplateFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' `
        -TemplateParameterFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json'
    ```

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Azure CLI を使用してテンプレートをデプロイする

Azure CLI を使用してテンプレートをデプロイするには、次の手順に従います。

1. Azure CLI を初めて使用する場合は、「 [Azure CLI のインストール](../cli-install-nodejs.md) 」を参照して、Azure のアカウントとサブスクリプションを選択する時点までの指示に従います。
2. 次に示すように、 **azure config mode** コマンドを実行してリソース マネージャー モードに切り替えます。

    ```azurecli
    azure config mode arm
    ```

    上記のコマンドで想定される出力を次に示します。

        info:    New mode is arm

3. ブラウザーで[クイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules)に移動し、json ファイルの内容をコピーして、コンピューター内の新しいファイルに貼り付けます。 このシナリオでは、次の値を **c:\lb\azuredeploy.parameters.json** という名前のファイルにコピーします。
4. 上記でダウンロードして変更したテンプレート ファイルとパラメーター ファイルを使用し、 **azure group deployment create** コマンドレットを実行して、新しいロード バランサーをデプロイします。 出力の後に表示される一覧では、使用されたパラメーターについて説明されています。

    ```azurecli
    azure group create --name TestRG --location westus --template-file 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' --parameters-file 'c:\lb\azuredeploy.parameters.json'
    ```

## <a name="next-steps"></a>次のステップ

[内部ロード バランサーの構成の開始](load-balancer-get-started-ilb-arm-ps.md)

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)

