---
title: Azure Container Instances で環境変数を設定する
description: Azure Container Instances で実行するコンテナーで環境変数を設定する方法を説明します
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/21/2019
ms.author: danlep
ms.openlocfilehash: 2ea85b2b04600708381423e16408ba34b1e27566
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904901"
---
# <a name="set-environment-variables"></a>環境変数の設定

Container Instances で環境変数を設定すると、コンテナーによって実行されるアプリケーションまたはスクリプトの動的な構成を提供できます。 コンテナーで環境変数を設定するには、コンテナー インスタンスを作成するときに指定します。 環境変数の設定は、[Azure CLI](#azure-cli-example)、[Azure PowerShell](#azure-powershell-example)、および [Azure Portal](#azure-portal-example) を使用してコンテナーを開始するときに行えます。

たとえば、Microsoft [aci-wordcount][aci-wordcount] コンテナー イメージを実行する場合は、次の環境変数を指定することによって動作を変更できます。

*NumWords*:STDOUT に送信された単語の数。

*MinLength*:単語内のカウントする文字の最小数。 数値を大きくすると、"of" や "the" のようなよく使用される単語は無視されます。

シークレットを環境変数として渡す必要がある場合、Azure Container Instances が Windows と Linux の両方のコンテナーの[セキュリティで保護された値](#secure-values)をサポートします。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Azure CLI の例

[aci-wordcount][aci-wordcount] コンテナーの既定の出力を表示するには、まず、この [az container create][az-container-create] コマンドを (環境変数を指定しないで) 実行します。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

出力を変更するには、`--environment-variables` 引数を追加し、*NumWords* 変数と *MinLength* 変数の値を指定して 2 番目のコンテナーを開始します。 (この例では、Bash シェルまたは Azure Cloud Shell で CLI を実行しているものとします。 Windows コマンド プロンプトを使用する場合は、`--environment-variables "NumWords"="5" "MinLength"="8"` のように、変数を二重引用符で囲んで指定します。)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

両方のコンテナーの状態が *Terminated* と表示されたら ([az container show][az-container-show] を使用して状態を確認)、[az container logs][az-container-logs] を使用してログを表示し、出力を確認します。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

コンテナーの出力には、環境変数を設定して、2 番目のコンテナーのスクリプトの動作をどのように変更したが示されます。

```console
azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Azure PowerShell の例

PowerShell での環境変数の設定は CLI と似ていますが、`-EnvironmentVariable` コマンド ライン引数を使用します。

まず、この [New-AzContainerGroup][new-Azcontainergroup] コマンドを使用して、既定の構成で [aci-wordcount][aci-wordcount] コンテナーを起動します。

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

ここで、次の [New-AzContainerGroup][new-Azcontainergroup] コマンドを実行します。 これは、配列変数 `envVars` の設定後に、環境変数の *NumWords* と *MinLength* を指定しています。

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

両方のコンテナーの状態が *Terminated* になったら ([Get-AzContainerInstanceLog][azure-instance-log] を使用して状態を確認)、[Get-AzContainerInstanceLog][azure-instance-log] コマンドでログをプルします。

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

各コンテナーの出力には、そのコンテナーによって実行されるスクリプトを、環境変数を設定してどのように変更したが示されます。

```console
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

Azure:\
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Azure Portal の例

Azure Portal でコンテナーを開始するときに環境変数を設定するには、コンテナーを作成するときに **[構成]** ページで指定します。

ポータルでデプロイするときには、変数の数が現在は 3 つに制限されていて、次の形式で入力する必要があります:  `"variableName":"value"`

例を表示するには、*NumWords* 変数と *MinLength* 変数を指定して [aci-wordcount][aci-wordcount] コンテナーを開始します。

1. **[構成]** で、**[再起動ポリシー]** を *[On failure] (エラー時)* に設定します。
2. 最初の変数には `"NumWords":"5"` を入力し、**[Add additional environment variables] (他の環境変数を追加する)** で **[はい]** 選択して、2 番目の変数には `"MinLength":"8"` を入力します。 **[OK]** を選択して確認してから、コンテナーをデプロイします。

![環境変数の有効化ボタンとテキスト ボックスを表示しているポータル ページ][portal-env-vars-01]

コンテナーのログを表示するには、**[設定]** で **[コンテナー]** を選択し、**[ログ]** を選択します。 前の CLI と PowerShell のセクションで示した出力と同様に、環境変数によってスクリプトの動作がどのように変更されたかを表示できます。 5 つのワードだけが表示され、それぞれの最小文字数は 8 文字です。

![コンテナーのログ出力を表示しているポータル][portal-env-vars-02]

## <a name="secure-values"></a>セキュリティで保護された値

セキュリティで保護された値を持つオブジェクトは、アプリケーションのパスワードやキーなどの機微な情報を保持することを目的としています。 セキュリティで保護された値をコンテナーのイメージではなく環境変数に使用することで、より安全性と柔軟性を確保できます。 もう 1 つのオプションは、「[Azure Container Instances にシークレット ボリュームをマウントする](container-instances-volume-secret.md)」で説明するように、シークレット ボリュームを使用します。

セキュリティで保護された値を含んだ環境変数は、コンテナーのプロパティに表示されないため、その値はコンテナー内からのみアクセスできます。 たとえば Azure portal や Azure CLI からコンテナーのプロパティを表示すると、セキュリティで保護された変数の名前だけが表示され、その値は表示されません。

セキュリティで保護された環境変数は、変数の型に通常の `value` の代わりに `secureValue` プロパティを指定することで設定します。 次の YAML で定義されている 2 つの変数は、2 つの変数の型を示します。

### <a name="yaml-deployment"></a>YAML のデプロイ

次のコードを使用して `secure-env.yaml` ファイルを作成します。

```yaml
apiVersion: 2018-10-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

次のコマンドを実行して YAML でコンテナー グループをデプロイします (リソース グループの名前は必要に応じて調整してください)。

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>環境変数を確認する

コンテナーの環境変数を照会するには、[az container show][az-container-show] コマンドを実行します。

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

JSON の応答を見ると、セキュリティで保護されていない環境変数はキーと値の両方が確認できるのに対し、セキュリティで保護された環境変数は名前しか確認できません。

```json
[
  [
    {
      "name": "NOTSECRET",
      "secureValue": null,
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET",
      "secureValue": null,
      "value": null
    }
  ]
]
```

実行中のコンテナーでコマンドを実行できる [az container exec][az-container-exec] コマンドを使えば、セキュリティで保護された環境変数が設定されていることを確認できます。 次のコマンドを実行して、コンテナーで対話型の bash セッションを開始します。

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

コンテナー内の対話型のシェルを開くと、`SECRET` 変数の値にアクセスできます。

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>次の手順

いくつかのコンテナーがある大きなデータセットのバッチ処理など、タスク ベースのシナリオでは、実行時にカスタムの環境変数のメリットがあります。 タスク ベースのコンテナーの実行に関する詳細については、「[Azure Container Instances でコンテナー化タスクを実行する](container-instances-restart-policy.md)」を参照してください。

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
