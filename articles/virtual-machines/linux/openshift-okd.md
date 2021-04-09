---
title: Azure で OKD をデプロイする
description: Azure で OKD をデプロイします。
author: haroldwongms
manager: joraio
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/15/2019
ms.author: haroldw
ms.openlocfilehash: dc14b10081cf175581d29524dcea60c52763b03c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667219"
---
# <a name="deploy-okd-in-azure"></a>Azure で OKD をデプロイする

Azure で OKD (旧称 OpenShift Origin) をデプロイするには、2 つの方法のいずれかを使用できます。

- 必要なすべての Azure インフラストラクチャ コンポーネントを手動でデプロイしてから、OKD の[ドキュメント](https://docs.okd.io)に従います。
- 既存の [Resource Manager テンプレート](https://github.com/Microsoft/openshift-origin)を使用して、OKD クラスターのデプロイを簡略化することもできます。

## <a name="deploy-using-the-okd-template"></a>OKD テンプレートを使用してデプロイする

Resource Manager テンプレートを使用してデプロイするには、パラメーター ファイルを使用して入力パラメーターを指定します。 デプロイをさらにカスタマイズするには、GitHub リポジトリをフォークし、適切な項目を変更します。

一般的なカスタマイズ オプションには以下のような項目がありますが、この限りではありません。

- Bastion VM サイズ (azuredeploy.json 内の変数)
- 名前付け規則 (azuredeploy.json 内の変数)
- OpenShift クラスターの詳細 - ホスト ファイル (deployOpenShift.sh) 経由で変更済み

[OKD テンプレート](https://github.com/Microsoft/openshift-origin)には、さまざまなバージョンの OKD に使用可できる複数の分岐があります。  ニーズに基づいて、リポジトリから直接デプロイすることも、リポジトリをフォークし、デプロイする前にカスタムの変更を加えることもできます。

以前に作成したサービス プリンシパルの値 `appId` を `aadClientId` パラメーターに使用します。

次の例は、必要なすべての入力を含む azuredeploy.parameters.json という名前のパラメーター ファイルの例です。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "storageKind": {
            "value": "managed"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

パラメーターをユーザー固有の情報に置き換えます。

リリースによってパラメーターは異なっている可能性があるため、お使いの分岐に必要なパラメーターを確認してください。

### <a name="deploy-using-azure-cli"></a>Azure CLI を使用したデプロイ


> [!NOTE] 
> 次のコマンドには、Azure CLI 2.0.8 以降が必要です。 CLI のバージョンは、`az --version` コマンドを使用して確認できます。 CLI のバージョンを更新するには、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

次の例では、OKD クラスターとすべての関連リソースを openshiftrg という名前のリソース グループに myOpenShiftCluster のデプロイ名でデプロイします。 このテンプレートは、azuredeploy.parameters.json ファイルという名前のローカル パラメーター ファイルを使用しているときに、GitHub リポジトリから直接参照されます。

```azurecli 
az deployment group create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

デプロイされるノードの総数に応じて、このデプロイが完了するには少なくとも 30 分かかります。 デプロイが完了すると、OpenShift コンソールの URL と OpenShift マスターの DNS 名がターミナルに出力されます。 また、Azure portal からのデプロイの出力セクションを表示することもできます。

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

デプロイの完了を待ちながらコマンド ラインを関連付けたくない場合は、グループのデプロイのオプションの 1 つとして `--no-wait` を追加します。 デプロイからの出力は、リソース グループのデプロイ セクションで、Azure portal から取得できます。

## <a name="connect-to-the-okd-cluster"></a>OKD クラスターに接続する

デプロイが完了したら、ブラウザーで `OpenShift Console Url` を使用して、OpenShift コンソールに接続します。 または、OKD マスターに SSH 接続することもできます。 次に、デプロイからの出力を使用する例を示します。

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループ、OpenShift クラスター、およびすべての関連リソースが不要になったら、[az group delete](/cli/azure/group) コマンドを使用して削除できます。

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>次のステップ

- [デプロイ後タスク](./openshift-container-platform-3x-post-deployment.md)
- [OpenShift デプロイのトラブルシューティング](./openshift-container-platform-3x-troubleshooting.md)
- [OKD の概要](https://docs.okd.io)
