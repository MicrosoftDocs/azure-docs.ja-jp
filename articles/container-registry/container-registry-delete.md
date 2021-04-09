---
title: 画像リソースを削除する
description: Azure CLI コマンドを使用してコンテナー イメージ データを削除することによって、レジストリのサイズを効果的に管理する方法について詳しく説明します。
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 449a1c09bf88e3e0e0aeca4d3b687371d2a6b91a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "78403350"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Azure CLI を使用して Azure Container Registry 内のコンテナー イメージを削除する

Azure コンテナー レジストリのサイズを管理するには、古いイメージ データを定期的に削除する必要があります。 運用環境にデプロイされるコンテナー イメージには、長期間保存する必要があるものと、通常は短期間で削除できるものがあります。 たとえば、ビルドとテストの自動化シナリオでは、デプロイされない可能性のあるイメージでレジストリが短時間のうちにいっぱいになることがあります。このようなイメージは、ビルドとテスト パスが完了したらすぐに消去できるものです。

イメージ データを削除する方法は複数あるので、各削除操作によるストレージの使用への影響を理解しておく必要があります。 この記事では、イメージ データを削除するためのいくつかの方法について説明します。

* [リポジトリ](#delete-repository)を削除する:リポジトリ内のイメージと一意のレイヤーがすべて削除されます。
* [タグ](#delete-by-tag)を指定して削除する:イメージ、タグ、イメージによって参照されている一意のレイヤーすべて、およびイメージに関連付けられている他のすべてのタグが削除されます。
* [マニフェスト ダイジェスト](#delete-by-manifest-digest)を指定して削除する:イメージ、イメージによって参照されている一意のレイヤーすべて、およびイメージに関連付けられているすべてのタグが削除されます。

削除操作の自動化を支援するために、サンプル スクリプトを用意しています。

これらの概念の概要については、「[レジストリ、リポジトリ、イメージについて](container-registry-concepts.md)」を参照してください。

## <a name="delete-repository"></a>リポジトリを削除する

リポジトリを削除すると、タグ、一意のレイヤー、マニフェストのすべてを含め、リポジトリ内のすべてのイメージが削除されます。 リポジトリを削除すると、そのリポジトリ内の一意レイヤーを参照するイメージによって使われていたストレージ領域が回復されます。

次の Azure CLI コマンドは、"acr-helloworld" リポジトリと、そのリポジトリ内のすべてタグとマニフェストを削除します。 削除されるマニフェストによって参照されているレイヤーが、レジストリ内の他のイメージによって参照されていない場合、そのレイヤーのデータも削除されて、ストレージ領域が回復されます。

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>タグで削除する

削除操作でリポジトリの名前とタグを指定することにより、リポジトリから個別のイメージを削除できます。 タグを指定して削除すると、イメージ内の一意レイヤー (レジストリ内の他のイメージによって共有されていないレイヤー) で使われているストレージ領域が回復されます。

タグを使用して削除するには、[az acr repository delete][az-acr-repository-delete] を使用し、`--image` パラメーターでイメージ名を指定します。 イメージに固有のすべてのレイヤーと、イメージに関連付けられている他のすべてのタグが削除されます。

たとえば、次の例では、レジストリ "myregistry" から "acr-helloworld:latest" イメージを削除しています。

```azurecli
az acr repository delete --name myregistry --image acr-helloworld:latest
```

```output
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n):
```

> [!TIP]
> "*タグによる*" 削除を、タグの削除 (タグ付けの解除) と混同しないでください。 タグの削除は、Azure CLI コマンド [az acr repository untag][az-acr-repository-untag] を使用して行うことができます。 イメージのタグを解除しても、[マニフェスト](container-registry-concepts.md#manifest)とレイヤーのデータがレジストリに残っているため、領域は解放されません。 タグ参照自体だけが削除されます。

## <a name="delete-by-manifest-digest"></a>マニフェスト ダイジェストで削除する

1 つの[マニフェスト ダイジェスト](container-registry-concepts.md#manifest-digest)を 1 つまたは複数のタグと関連付けることができ、マニフェスト ダイジェストをタグに関連付けないでおくこともできます。 ダイジェストを指定して削除すると、マニフェストによって参照されているすべてのタグが削除され、イメージに固有のレイヤーのレイヤー データも削除されます。 共有されているレイヤーのデータは削除されません。

ダイジェストを使用して削除するには、最初に、削除するイメージを含むリポジトリのマニフェスト ダイジェストのリストを取得します。 次に例を示します。

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  }
]
```

次に、[az acr repository delete][az-acr-repository-delete] コマンドで削除するダイジェストを指定します。 コマンドの形式は次のとおりです。

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

たとえば、前の出力のリストで最後のマニフェスト (タグ "v2" のもの) を削除するには、次のようにします。

```azurecli
az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
```

```output
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): 
```

`acr-helloworld:v2` イメージがレジストリから削除され、そのイメージに固有のレイヤー データも削除されます。 マニフェストが複数のタグに関連付けられている場合は、関連付けられているすべてのタグも削除されます。

## <a name="delete-digests-by-timestamp"></a>ダイジェストをタイムスタンプに基づいて削除する

リポジトリまたはレジストリのサイズを維持するためには、特定の日付よりも古いマニフェスト ダイジェストを定期的に削除する必要があります。

以下の Azure CLI コマンドは、リポジトリから、特定のタイムスタンプより古いすべてのマニフェスト ダイジェストを昇順に一覧表示するものです。 `<acrName>` と `<repositoryName>` は、環境に適した値に置き換えます。 タイムスタンプは、完全な日付/時刻表現の場合と日付の場合とがあります。その例を次に示します。

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

古いマニフェスト ダイジェストを特定した後、次の Bash スクリプトを実行することで、指定のタイムスタンプより古いマニフェスト ダイジェストを削除することができます。 Azure CLI と **xargs** が必要です。 既定では、このスクリプトは削除を実行しません。 イメージの削除を有効にするには、`ENABLE_DELETE` の値を `true` に変更します。

> [!WARNING]
> 次のサンプル スクリプトを使用するときは注意してください。削除したイメージ データを元に戻すことはできません。 (イメージ名ではなく) マニフェスト ダイジェストを使用してイメージをプルするシステムの場合は、これらのスクリプトを実行しないでください。 そのようなシステムでは、マニフェスト ダイジェストを削除すると、レジストリからイメージをプルできなくなります。 マニフェストでプルするのではなく、*一意のタグ付け* スキームの [推奨されるベスト プラクティス](container-registry-image-tag-version.md)を採用することを検討してください。 

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
# TIMESTAMP can be a date-time string such as 2019-03-15T17:55:00.
REGISTRY=myregistry
REPOSITORY=myrepository
TIMESTAMP=2019-04-05  

# Delete all images older than specified timestamp.

if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
    --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
   --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].[digest, timestamp]" -o tsv
fi
```

## <a name="delete-untagged-images"></a>タグの付いていないイメージを削除する

「[マニフェスト ダイジェスト](container-registry-concepts.md#manifest-digest)」セクションで説明したように、既存のタグを使用して変更されたイメージをプッシュすると、以前にプッシュされたイメージの **タグが解除** されて、孤立した (または "未解決の") イメージになります。 以前にプッシュされたイメージのマニフェストとそのレイヤー データは、レジストリに残っています。 次のような一連のイベントについて考えてみます。

1. **latest** というタグが付いたイメージ *acr-helloworld* をプッシュします。`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. リポジトリ *acr-helloworld* のマニフェストを確認します。

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   
   ```
   
   ```output
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. *acr-helloworld* Docker ファイルを変更します
1. **latest** というタグが付いたイメージ *acr-helloworld* をプッシュします。`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. リポジトリ *acr-helloworld* のマニフェストを確認します。

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   ```
   
   ```output
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

シーケンスの最後のステップの出力を見るとわかるように、`"tags"` プロパティが空のリストである孤立したマニフェストが存在します。 このマニフェストは、それが参照する一意レイヤーのデータと共に、レジストリ内に依然として存在します。 **このような孤立したイメージとそのレイヤー データを削除するには、マニフェスト ダイジェストを使って削除する必要があります**。

## <a name="delete-all-untagged-images"></a>タグの付いていないイメージをすべて削除する

次の Azure CLI コマンドを使用して、リポジトリ内に存在するタグの付いていないすべてのイメージを一覧表示できます。 `<acrName>` と `<repositoryName>` は、環境に適した値に置き換えます。

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

スクリプトでこのコマンドを使用すると、リポジトリ内のタグ付けされていないすべてのイメージを削除できます。

> [!WARNING]
> 次のサンプル スクリプトを使用するときは注意してください。削除したイメージ データを元に戻すことはできません。 (イメージ名ではなく) マニフェスト ダイジェストを使用してイメージをプルするシステムの場合は、これらのスクリプトを実行しないでください。 このようなシステムでは、タグの付いていないイメージを削除すると、レジストリからイメージをプルできなくなります。 マニフェストでプルするのではなく、*一意のタグ付け* スキームの [推奨されるベスト プラクティス](container-registry-image-tag-version.md)を採用することを検討してください。

**Bash での Azure CLI**

次の Bash スクリプトは、リポジトリからタグの付いていないイメージをすべて削除します。 Azure CLI と **xargs** が必要です。 既定では、このスクリプトは削除を実行しません。 イメージの削除を有効にするには、`ENABLE_DELETE` の値を `true` に変更します。

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags[0]==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**PowerShell での Azure CLI**

次の PowerShell スクリプトは、リポジトリからタグの付いていないイメージをすべて削除します。 PowerShell と Azure CLI が必要です。 既定では、このスクリプトは削除を実行しません。 イメージの削除を有効にするには、`$enableDelete` の値を `$TRUE` に変更します。

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted."
    Write-Host "Set `$enableDelete = `$TRUE to enable image deletion."
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null]" -o tsv
}
```


## <a name="automatically-purge-tags-and-manifests-preview"></a>タグとマニフェストの自動消去 (プレビュー)

Azure CLI コマンドのスクリプト作成の代わりに、オンデマンドまたはスケジュールされた ACR タスクを実行して、特定の期間よりも古い、または指定した名前フィルターに一致するすべてのタグを削除します。 詳細については、「[Azure コンテナー レジストリからイメージを自動的に消去する](container-registry-auto-purge.md)」を参照してください。

必要に応じて、タグなしマニフェストを管理するための[アイテム保持ポリシー](container-registry-retention-policy.md)をレジストリごとに設定します。 アイテム保持ポリシーを有効にすると、タグが一切関連付けられておらず、また基になるレイヤー データを持たないイメージのマニフェストが、設定した期間の経過後、レジストリから自動的に削除されます。

## <a name="next-steps"></a>次のステップ

Azure Container Registry でのイメージ ストレージの詳細については、「[Azure Container Registry へのコンテナー イメージの保存](container-registry-storage.md)」を参照してください。

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
