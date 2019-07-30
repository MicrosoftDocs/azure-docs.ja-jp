---
title: コンテナー サポート
titleSuffix: Azure Cognitive Services
description: Azure CLI から Azure コンテナー インスタンス リソースを作成する方法を説明します。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 5e7a3d849f726ae4dbbd559d541464404e427775
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717098"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Azure CLI から Azure コンテナー インスタンス リソースを作成する

次の YAML は、Azure コンテナー インスタンス リソースを定義します。 内容をコピーして新しいファイルに貼り付け、`my-aci.yaml` という名前を付け、コメント付きの値を独自の値に置き換えます。 有効な YAML については、[テンプレートの形式][テンプレートの形式] を参照してください。 使用可能なイメージ名と対応するリポジトリについては、[コンテナーのリポジトリとイメージ][repositories-and-images]を参照してください。

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials:
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
properties:
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> すべての場所で同じ CPU とメモリが使用できるわけではありません。 コンテナーで使用可能なリソースを場所と OS ごとにまとめた一覧については、[場所とリソース][location-to-resource]の表を参照してください。

作成した YAML ファイルを [`az container create`][azure-container-create] コマンドに使用します。 Azure CLI から、`<resource-group>` を独自の値に置き換えて `az container create` コマンドを実行します。 また、YAML デプロイ内の値をセキュリティで保護する方法については、[値をセキュリティで保護する][secure-values]を参照してください。

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

有効な場合、コマンドの出力は `Running...` となります。しばらくすると、出力は新しく作成された ACI リソースを表す JSON 文字列に変化します。 コンテナー イメージはしばらく使用できない可能性が高いですが、リソースは配置されています。

> [!TIP]
> YAML は場所と一致するように適切に調整する必要があるので、パブリック プレビューの Azure Cognitive Service のサービスの場所に十分注意してください。

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format

[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values