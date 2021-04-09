---
title: チュートリアル - Azure テンプレートを使用してスケール セットにアプリをインストールする
description: Azure Resource Manager テンプレートとカスタム スクリプト拡張機能を使用して、仮想マシン スケール セットにアプリケーションをインストールする方法について説明します
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: template
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: d5eba5486e7d26e62379e0112cd4b95322e6dae1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97705236"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-an-azure-template"></a>チュートリアル:Azure テンプレートを使用した仮想マシン スケール セットへのアプリケーションのインストール
スケール セット内の仮想マシン (VM) インスタンスでアプリケーションを実行する　には、まず、アプリケーション コンポーネントと必要なファイルをインストールする必要があります。 前のチュートリアルでは、カスタム VM イメージを作成および使用して VM インスタンスをデプロイする方法について学習しました。 このカスタム イメージには、手動によるアプリケーションのインストールと構成が含まれていました。 このほか、各 VM インスタンスがデプロイされた後のスケール セットへのアプリケーションのインストールを自動化したり、既にスケール セットで実行されているアプリケーションを更新したりできます。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * スケール セットへのアプリケーションの自動インストール
> * Azure カスタム スクリプト拡張機能の使用
> * スケール セットで実行中のアプリケーションの更新

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.29 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。


## <a name="what-is-the-azure-custom-script-extension"></a>Azure カスタム スクリプト拡張機能とは
カスタム スクリプト拡張機能は、Azure VM でスクリプトをダウンロードし、実行します。 この拡張機能は、デプロイ後の構成、ソフトウェアのインストール、その他の構成や管理タスクに役立ちます。 スクリプトは、Azure ストレージや GitHub からダウンロードできます。また、拡張機能の実行時に Azure Portal に提供することもできます。

カスタム スクリプト拡張機能は Azure Resource Manager テンプレートと統合されており、Azure CLI、Azure PowerShell、Azure portal、または REST API と組み合わせて使用することもできます。 詳細については、「[Windows のカスタム スクリプト拡張機能](../virtual-machines/extensions/custom-script-linux.md)」を参照してください。

カスタム スクリプト拡張機能が動作していることを確認するには、NGINX Web サーバーをインストールしてスケール セット VM インスタンスのホスト名を出力する機能を備えたスケール セットを作成します。 次のカスタム スクリプト拡張機能の定義によって、GitHub からサンプル スクリプトがダウンロードされ、必要なパッケージがインストールされた後、VM インスタンスのホスト名が基本的な HTML ページに書き込まれます。


## <a name="create-custom-script-extension-definition"></a>カスタム スクリプト拡張機能の定義の作成
Azure テンプレートを使用して仮想マシン スケール セットを定義する場合、*Microsoft.Compute/virtualMachineScaleSets* リソース プロバイダーに拡張機能のセクションを含めることができます。 *extensionsProfile* には、スケール セット内の VM インスタンスに適用される項目が詳しく記述されています。 カスタム スクリプト拡張機能を使用するには、発行元として *Microsoft.Azure.Extensions* を指定し、タイプとして *CustomScript* を指定します。

*fileUris* プロパティは、ソース インストール スクリプトまたはパッケージを定義するために使用します。 インストール プロセスを開始するために必要なスクリプトは、*commandToExecute* に定義します。 次の例では、NGINX Web サーバーをインストールおよび構成する GitHub のサンプル スクリプトを定義します。

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"
          ],
          "commandToExecute": "bash automate_nginx.sh"
        }
      }
    }
  ]
}
```

スケール セットとカスタム スクリプト拡張機能をデプロイする Azure テンプレートの完全な例については、[https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json) を参照してください。 次のセクションではこのサンプル テンプレートを使用します。


## <a name="create-a-scale-set"></a>スケール セットを作成する
サンプル テンプレートを使用してスケール セットを作成し、カスタム スクリプト拡張機能を適用しましょう。 最初に、[az group create](/cli/azure/group) を使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

ここでは、[az deployment group create](/cli/azure/deployment/group) を使用して仮想マシン スケール セットを作成します。 メッセージが表示されたら、独自のユーザー名と、各 VM インスタンスの資格情報として使用するパスワードを入力します。

```azurecli-interactive
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy.json
```

すべてのスケール セットのリソースと VM を作成および構成するのに数分かかります。

スケール セット内の各 VM インスタンスが、GitHub からスクリプトをダウンロードして実行します。 より複雑な例では、複数のアプリケーション コンポーネントおよびファイルをインストールできます。 スケール セットがスケールアップされた場合、新しい VM インスタンスで同じカスタム スクリプト拡張機能定義が自動的に適用され、必要なアプリケーションがインストールされます。


## <a name="test-your-scale-set"></a>スケール セットのテスト
Web サーバーが動いていることを確認するには、[az network public-ip show](/cli/azure/network/public-ip) でロード バランサーのパブリック IP アドレスを取得します。 次の例では、スケール セットの一部として作成された *myScaleSetPublicIP* の IP アドレスを取得します。

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetPublicIP \
  --query [ipAddress] \
  --output tsv
```

ロード バランサーのパブリック IP アドレスを Web ブラウザーに入力します。 ロード バランサーは、次の例に示すように、VM インスタンスのいずれかにトラフィックを配分します。

![Nginx の基本的な Web ページ](media/tutorial-install-apps-template/running-nginx.png)

次の手順で更新されたバージョンを確認できるように、Web ブラウザーを開いたままにしておきます。


## <a name="update-app-deployment"></a>アプリのデプロイの更新
スケール セットのライフサイクル全体にわたり、アプリケーションの更新されたバージョンをデプロイする必要があります。 カスタム スクリプト拡張機能を使用すると、更新されたデプロイ スクリプトを参照し、拡張機能をスケール セットに再適用することができます。 前の手順で作成したスケール セットでは、*upgradePolicy* が *Automatic* に設定されていました。 この設定により、スケール セット内の VM インスタンスは、自動的にアプリケーションを更新して最新バージョンを適用できます。

カスタム スクリプト拡張機能の定義を更新するために、新しいインストール スクリプトを参照するようにテンプレートを編集します。 この変更をカスタム スクリプト拡張機能に認識させるには、新しいファイル名を使用する必要があります。 カスタム スクリプト拡張機能には、スクリプトの内容を調べて変更を識別する機能はありません。 次の定義では、名前の末尾に *_v2* が付加された、更新されたインストール スクリプトを使用しています。

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"
          ],
          "commandToExecute": "bash automate_nginx_v2.sh"
        }
      }
    }
  ]
}
```

[az deployment group create](/cli/azure/deployment/group) を使用して、カスタム スクリプト拡張機能構成をスケール セット内の VM インスタンスに再度適用します。 この *azuredeployv2.json* テンプレートは、アプリケーションの更新されたバージョンを適用するために使用されています。 実際には、前のセクションに示すように、既存の *azuredeploy.json* テンプレートを編集して更新されたインストール スクリプトを参照します。 メッセージが表示されたら、スケール セットを最初に作成したときと同じユーザー名とパスワードの資格情報を入力します。

```azurecli-interactive
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy_v2.json
```

スケール セット内のすべての VM インスタンスが、サンプル Web ページの最新バージョンで自動的に更新されます。 更新されたバージョンを確認するには、ブラウザーで Web サイトを更新します。

![Nginx の更新された Web ページ](media/tutorial-install-apps-template/running-nginx-updated.png)


## <a name="clean-up-resources"></a>リソースをクリーンアップする
スケール セットと追加のリソースを削除するには、[az group delete](/cli/azure/group) を使用して、リソース グループとそのすべてのリソースを削除します。 `--no-wait` パラメーターは、操作の完了を待たずにプロンプトに制御を戻します。 `--yes` パラメーターは、追加のプロンプトを表示せずにリソースの削除を確定します。

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure テンプレートを使用して自動的にアプリケーションをスケール セットにインストールし、更新する方法について学習しました。

> [!div class="checklist"]
> * スケール セットへのアプリケーションの自動インストール
> * Azure カスタム スクリプト拡張機能の使用
> * スケール セットで実行中のアプリケーションの更新

次のチュートリアルに進み、スケール セットを自動的にスケーリングする方法を学習してください。

> [!div class="nextstepaction"]
> [スケール セットを自動的にスケーリングする](tutorial-autoscale-template.md)
