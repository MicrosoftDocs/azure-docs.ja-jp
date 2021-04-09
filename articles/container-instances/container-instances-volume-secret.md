---
title: シークレット ボリュームをコンテナー グループにマウントする
description: シークレット ボリュームをマウントし、コンテナー インスタンスがアクセスする秘密情報を保存する方法について説明します。
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: ea82ba5994feaf102d4622eada284df431e004d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86169563"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Azure Container Instances にシークレット ボリュームをマウントする

"*シークレット*" ボリュームは、コンテナー グループ内のコンテナーに機微な情報を提供する目的で使います。 秘密情報は、"*シークレット*" ボリューム内のファイルに格納され、コンテナー グループ内のコンテナーからアクセスすることができます。 "*シークレット*" ボリュームに秘密情報を格納すれば、SSH キーやデータベースの資格情報など、機微なデータをアプリケーション コードに追加せずに済みます。

* コンテナー グループにシークレットと共にデプロイされたシークレット ボリュームは、"*読み取り専用*" になります。
* すべてのシークレット ボリュームは、RAM でバックアップされるファイル システムである [tmpfs][tmpfs] でバックアップされます。そのコンテンツは不揮発性の記憶域には書き込まれません。

> [!NOTE]
> "*シークレット*" ボリュームは、現在のところ Linux コンテナーに限定されています。 Windows と Linux の両方のコンテナーのセキュリティで保護された環境変数を渡す方法の詳細については、「[環境変数の設定](container-instances-environment-variables.md)」を参照してください。 Microsoft ではすべての機能を Windows コンテナーに取り入れるように取り組んでいますが、現在のプラットフォームの違いは、[概要](container-instances-overview.md#linux-and-windows-containers)に関するページで確認できます。

## <a name="mount-secret-volume---azure-cli"></a>シークレット ボリュームのマウント - Azure CLI

Azure CLI を使って、少なくとも 1 つのシークレットを含んだコンテナーをデプロイするには、[az container create][az-container-create] コマンドに `--secrets` パラメーターと `--secrets-mount-path` パラメーターを指定します。 この例では、シークレットを含む "mysecret1" と "mysecret2" の 2 つのファイルで構成される "*シークレット*" ボリュームを `/mnt/secrets` にマウントします。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

次の [az container exec][az-container-exec] の出力は、実行中のコンテナーのシェルを開いて、シークレット ボリューム内のファイルをリストした後、その内容を表示しています。

```azurecli
az container exec \
  --resource-group myResourceGroup \
  --name secret-volume-demo --exec-command "/bin/sh"
```

```output
/usr/src/app # ls /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>シークレット ボリュームのマウント - YAML

Azure CLI と [YAML テンプレート](container-instances-multi-container-yaml.md)を使ってコンテナー グループをデプロイすることもできます。 複数のコンテナーから成るコンテナー グループをデプロイするときは、YAML テンプレートによるデプロイ方法が推奨されます。

YAML テンプレートを使ってデプロイするときは、テンプレート内のシークレット値が **Base64 でエンコード** されている必要があります。 ただしコンテナーのファイル内では、シークレット値がプレーンテキストで表示されます。

以下の YAML テンプレートは、"*シークレット*" ボリュームを `/mnt/secrets` でマウントするコンテナーを 1 つ含んだコンテナー グループを定義しています。 このシークレット ボリュームには、シークレットを含む "mysecret1" と "mysecret2" の 2 つのファイルがあります。

```yaml
apiVersion: '2019-12-01'
location: eastus
name: secret-volume-demo
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /mnt/secrets
        name: secretvolume1
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: secretvolume1
    secret:
      mysecret1: TXkgZmlyc3Qgc2VjcmV0IEZPTwo=
      mysecret2: TXkgc2Vjb25kIHNlY3JldCBCQVIK
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

この YAML テンプレートを使ってデプロイするには、上記の YAML を `deploy-aci.yaml` という名前のファイルに保存し、`--file` パラメーターを指定して [az container create][az-container-create] コマンドを実行します。

```azurecli-interactive
# Deploy with YAML template
az container create \
  --resource-group myResourceGroup \
  --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>シークレット ボリュームのマウント - Resource Manager

コンテナー グループは、CLI と YAML によるデプロイに加え、Azure [Resource Manager テンプレート](/azure/templates/microsoft.containerinstance/containergroups)を使ってデプロイすることもできます。

最初に、テンプレートのコンテナー グループ `properties` セクションに `volumes` 配列を入力します。 Resource Manager テンプレートを使ってデプロイするときは、テンプレート内のシークレット値が **Base64 でエンコード** されている必要があります。 ただしコンテナーのファイル内では、シークレット値がプレーンテキストで表示されます。

次に、*シークレット* ボリュームをマウントするコンテナー グループ内の各コンテナーに対して、コンテナー定義の `properties` セクションで `volumeMounts` 配列を設定します。

以下の Resource Manager テンプレートは、"*シークレット*" ボリュームを `/mnt/secrets` でマウントするコンテナーを 1 つ含んだコンテナー グループを定義しています。 このシークレット ボリュームには、"mysecret1" と "mysecret2" の 2 つのシークレットがあります。

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

この Resource Manager テンプレートを使ってデプロイするには、上記の JSON を `deploy-aci.json` という名前のファイルに保存し、`--template-file` パラメーターを指定して [az deployment group create][az-deployment-group-create] コマンドを実行します。

```azurecli-interactive
# Deploy with Resource Manager template
az deployment group create \
  --resource-group myResourceGroup \
  --template-file deploy-aci.json
```

## <a name="next-steps"></a>次のステップ

### <a name="volumes"></a>ボリューム

Azure Container Instances にその他の種類のボリュームをマウントする方法について学習してください。

* [Azure Container Instances に Azure ファイル共有をマウントする](container-instances-volume-azure-files.md)
* [Azure Container Instances に emptyDir ボリュームをマウントする](container-instances-volume-emptydir.md)
* [Azure Container Instances に gitRepo ボリュームをマウントする](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>セキュリティで保護された環境変数

機微な情報をコンテナー (Windows コンテナーも含む) に提供する方法は他にもあります。[セキュリティで保護された環境変数](container-instances-environment-variables.md#secure-values)を使う方法です。

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
