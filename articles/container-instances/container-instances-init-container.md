---
title: init コンテナーを実行する
description: アプリケーション コンテナーが実行される前に、Azure Container Instances で init コンテナーを実行して、コンテナー グループでセットアップ タスクを実行します。
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 5a729263ee632eb9227694ec8684eb6889c6324b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85954283"
---
# <a name="run-an-init-container-for-setup-tasks-in-a-container-group"></a>コンテナー グループでのセットアップ タスクのために init コンテナーを実行する

Azure Container Instances では、コンテナー グループ内の "*init コンテナー*" がサポートされます。 init コンテナーは、アプリケーション コンテナーが開始される前に、完了するまで実行されます。 [Kubernetes init コンテナー](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)と同様に、1 つ以上の init コンテナーを使用して、アカウントの設定、セットアップ スクリプトの実行、またはデータベースの構成など、アプリ コンテナーの初期化ロジックを実行します。

この記事では、Azure Resource Manager テンプレートを使用して、init コンテナーを含むコンテナー グループを構成する方法について説明します。

## <a name="things-to-know"></a>注意事項

* **API バージョン** - init コンテナーをデプロイするには、少なくとも Azure Container Instances API バージョン 2019-12-01 が必要です。 [YAML ファイル](container-instances-multi-container-yaml.md)または [Resource Manager テンプレート](container-instances-multi-container-group.md)で `initContainers` プロパティを使用してデプロイします。
* **実行順序** - init コンテナーは、テンプレートに指定された順序で、他のコンテナーの前に実行されます。 既定では、1 つのコンテナー グループあたり最大で 59 個の init コンテナーを指定できます。 グループには、init 以外のコンテナーが少なくとも 1 つ含まれている必要があります。
* **ホスト環境** - init コンテナーは、グループ内の残りのコンテナーと同じハードウェア上で実行されます。
* **リソース** - init コンテナーのリソースは指定しません。 コンテナー グループで使用可能な CPU やメモリなどの合計リソースが付与されます。 init コンテナーが実行されている間、グループ内の他のコンテナーは実行されません。
* **サポートされているプロパティ** - init コンテナーは、ボリューム、シークレット、マネージド ID などのグループ プロパティを使用できます。 ただし、コンテナー グループに対して構成されている場合、ポートや IP アドレスを使用することはできません。 
* **再起動ポリシー** - 各 init コンテナーは、グループ内の次のコンテナーが開始される前に正常に終了する必要があります。 init コンテナーが正常に終了しない場合、その再起動アクションは、グループに対して構成されている[再起動ポリシー](container-instances-restart-policy.md)によって決まります。

    |グループ内のポリシー  |init 内のポリシー  |
    |---------|---------|
    |Always (常に)     |OnFailure         |
    |OnFailure     |OnFailure         |
    |なし     |なし         |
* **料金** - コンテナー グループでは、init コンテナーの最初のデプロイから料金が発生します。

## <a name="resource-manager-template-example"></a>Resource Manager テンプレートの例

まず、次の JSON を `azuredeploy.json` という名前の新しいファイルにコピーします。 このテンプレートは、1 つの init コンテナーと 2 つのアプリケーション コンテナーを含むコンテナー グループを設定します。

* *init1* コンテナーは、Docker Hub から [busybox](https://hub.docker.com/_/busybox) イメージを実行します。 60 秒間スリープ状態になってから、[emptyDir ボリューム](container-instances-volume-emptydir.md)内のファイルにコマンドライン文字列を書き込みます。
* どちらのアプリケーション コンテナーでも、Microsoft の `aci-wordcount` コンテナー イメージが実行されます。
    * *hamlet* コンテナーは、既定の構成でワードカウント アプリを実行し、シェイクスピアの戯曲 "*ハムレット*" での単語の頻度をカウントします。
    * *juliet* アプリ コンテナーは、emptDir ボリュームからコマンドライン文字列を読み取って、代わりにシェイクスピアの "*ロミオとジュリエット*" でワードカウント アプリを実行します。

`aci-wordcount` イメージの使用の詳細と例については、「[コンテナー インスタンスで環境変数を設定する](container-instances-environment-variables.md)」を参照してください。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Standard",
                "initContainers": [
                {
                    "name": "init1",
                    "properties": {
                        "image": "busybox",
                        "environmentVariables": [],
                        "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                         "command": [
                            "/bin/sh",
                            "-c",
                            "sleep 60; echo python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html > /mnt/emptydir/command_line.txt"
                        ]
                    }
                }
            ], 
                "containers": [
                    {
                        "name": "hamlet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    },
                    {
                        "name": "juliet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                            ],
                            "command": [
                                "/bin/sh",
                                "-c",
                                "$(cat /mnt/emptydir/command_line.txt)"
                            ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "OnFailure",
                "osType": "Linux",
                "volumes": [
                    {
                        "name": "emptydir1",
                        "emptyDir": {}
                    }
                ]           
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-the-template"></a>テンプレートのデプロイ

[az group create][az-group-create] コマンドを使用して、リソース グループを作成します。

```azurecli
az group create --name myResourceGroup --location eastus
```

[az deployment group create][az-deployment-group-create] コマンドを使用してテンプレートをデプロイします。

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

init コンテナーを含むグループでは、init コンテナーが完了するまでに要する時間が原因で、デプロイ時間が増加します。


## <a name="view-container-logs"></a>コンテナー ログの表示

init コンテナーが正常に実行されたことを確認するには、[az container logs][az-container-logs] コマンドを使用して、アプリ コンテナーのログ出力を表示します。 `--container-name` 引数は、プルするログが含まれるコンテナーを指定します。 この例では、異なるコマンド出力を表示する *hamlet* と *juliet* コンテナーのログをプルします。

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name hamlet
```

出力:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name juliet
```

出力:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>次のステップ

init コンテナーは、アプリケーション コンテナーのセットアップと初期化タスクの実行に役立ちます。 タスク ベースのコンテナーの実行に関する詳細については、「[再起動ポリシーによるコンテナー化タスクの実行](container-instances-restart-policy.md)」を参照してください。

Azure Container Instances には、アプリケーション コンテナーの動作を変更するための他のオプションが用意されています。 たとえば、次のようになります。

* [コンテナー インスタンスで環境変数を設定する](container-instances-environment-variables.md)
* [コンテナー インスタンスにコマンド ラインを設定して既定のコマンド ライン操作をオーバーライドする](container-instances-start-command.md)


[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-container-logs]: /cli/azure/container#az-container-logs
