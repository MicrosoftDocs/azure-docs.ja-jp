---
title: Azure Container Registry のイメージ リソースを削除する
description: コンテナー イメージ データを削除することによって、レジストリのサイズを効果的に管理する方法について詳しく説明します。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 01/04/2019
ms.author: danlep
ms.openlocfilehash: f3206da25a3c0727e3f9fe12190580a6c28c81a3
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2019
ms.locfileid: "56983253"
---
# <a name="delete-container-images-in-azure-container-registry"></a>Azure Container Registry のコンテナー イメージを削除する

Azure コンテナー レジストリのサイズを管理するには、古いイメージ データを定期的に削除する必要があります。 運用環境にデプロイされるコンテナー イメージには、長期間保存する必要があるものと、通常は短期間で削除できるものがあります。 たとえば、ビルドとテストの自動化シナリオでは、デプロイされない可能性のあるイメージでレジストリが短時間のうちにいっぱいになることがあります。このようなイメージは、ビルドとテスト パスが完了したらすぐに消去できるものです。

イメージ データを削除する方法は複数あるので、各削除操作によるストレージの使用への影響を理解しておく必要があります。 この記事では、最初に Docker レジストリのコンポーネントとコンテナー イメージについて説明した後、イメージ データを削除するいくつかの方法について説明します。

## <a name="registry"></a>レジストリ

コンテナー "*レジストリ*" は、コンテナー イメージを格納して配布するサービスです。 Docker Hub がパブリックな Docker コンテナー レジストリであるのに対し、Azure Container Registry は Azure 内のプライベートな Docker コンテナー レジストリを提供します。

## <a name="repository"></a>リポジトリ

コンテナー レジストリは、名前が同じでタグが異なるコンテナー イメージのコレクションである "*リポジトリ*" を管理します。 たとえば、次の 3 つのイメージは "acr-helloworld" リポジトリ内にあります。

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

リポジトリ名には、[名前空間](container-registry-best-practices.md#repository-namespaces)を含めることもできます。 名前空間を使用すると、スラッシュで区切られたリポジトリ名を使用してイメージをグループ化できます。次に示すのはその例です。

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="components-of-an-image"></a>イメージのコンポーネント

レジストリ内のコンテナー イメージは、1 つまたは複数のタグに関連付けられており、1 つまたは複数のレイヤーを持ち、マニフェストによって識別されます。 これらのコンポーネントの相互関係を理解しておくと、レジストリ内の領域を解放する最良の方法を判断するのに役立ちます。

### <a name="tag"></a>タグ

イメージの "*タグ*" は、そのバージョンを指定します。 リポジトリ内の 1 つのイメージに、1 つまたは複数のタグを割り当てることができ、"タグを割り当てない" こともできます。 つまり、イメージからすべてのタグを削除しながら、イメージのデータ (そのレイヤー) はレジストリに引き続き残しておくことができます。

リポジトリ (またはリポジトリと名前空間) とタグの組み合わせで、イメージの名前が定義されます。 プッシュ操作またはプル操作で名前を指定することにより、イメージをプッシュおよびプルできます。

Azure Container Registry のようなプライベート レジストリでは、イメージ名にはレジストリ ホストの完全修飾名も含まれます。 ACR 内のイメージのレジストリ ホストは、*acrname.azurecr.io* という形式です。 たとえば、前のセクションの "marketing" 名前空間内の最初のイメージの完全な名前は、次のようになります。

```
myregistry.azurecr.io/marketing/campaign10-18/web:v2
```

イメージのタグ付けのベスト プラクティスの詳細については、次を参照してください: 「[Docker Tagging: Best practices for tagging and versioning docker images (Docker のタグ付け: Docker イメージのタグ付けとバージョン管理のベスト プラクティス)][tagging-best-practices]」 (MSDN のブログ投稿)。

### <a name="layer"></a>レイヤー

イメージは 1 つ以上の "*レイヤー*" で構成されており、各レイヤーがイメージを定義する Dockerfile 内の行に対応しています。 ストレージの効率が良くなるように、レジストリ内のイメージは共通レイヤーを共有しています。 たとえば、異なるリポジトリ内の複数のイメージが同じ Alpine Linux ベース レイヤーを共有している場合がありますが、レジストリに格納されるのはそのレイヤーの 1 つのコピーだけです。

レイヤーの共有により、共通レイヤーを共有する複数のイメージを含むノードへのレイヤーの配布も最適化されます。 たとえば、ノード上に既に存在するイメージがそのベースとして Alpine Linux レイヤーを含む場合、同じレイヤーを参照する異なるイメージをそれ以降にプルしても、レイヤーはノードに転送されません。 代わりに、ノードに既に存在するレイヤーが参照されます。

### <a name="manifest"></a>Manifest

コンテナー レジストリにプッシュされる各コンテナー イメージは、"*マニフェスト*" と関連付けられます。 イメージがプッシュされるときにレジストリによって生成されるマニフェストは、イメージを一意に示し、そのレイヤーを指定します。 Azure CLI コマンド [az acr repository show-manifests][az-acr-repository-show-manifests] を使用して、リポジトリのマニフェストを一覧表示できます。

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

たとえば、"acr-helloworld" リポジトリのマニフェスト ダイジェストの一覧表示は次のようになります。

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
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
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

ここで説明したマニフェストは、Azure portal または [docker manifest inspect][docker-manifest-inspect] で表示できるイメージ マニフェストとは異なります。 次のセクションで使われている "マニフェスト ダイジェスト" という用語は、イメージ マニフェストの *config.digest* ではなく、プッシュ操作によって生成されるダイジェストを示します。 config.digest ではなく、**マニフェスト ダイジェスト**によってイメージをプルおよび削除できます。 次の図では、2 種類のダイジェストが示されています。

![Azure portal でのマニフェスト ダイジェストと config.digest][manifest-digest]

### <a name="manifest-digest"></a>マニフェスト ダイジェスト

マニフェストは、一意の SHA-256 ハッシュつまり "*マニフェスト ダイジェスト*" によって識別されます。 各イメージは、タグ付きかどうかにかかわらず、そのダイジェストによって識別されます。 ダイジェストの値は、イメージのレイヤー データが別のイメージと同じ場合であっても一意です。 このメカニズムにより、同じタグが付けられたイメージをレジストリに繰り返しプッシュできます。 たとえば、各イメージはその一意のダイジェストによって識別されるため、`myimage:latest` をレジストリに繰り返しプッシュしてもエラーにはなりません。

プル操作ではダイジェストを指定することにより、レジストリからイメージをプルできます。 システムによっては、ダイジェストによってプルするように構成されている場合があります。同じタグが付けられたイメージが後でレジストリにプッシュされた場合でも、ダイジェストによって、プルされているイメージのバージョンが保証されるためです。

たとえば、マニフェスト ダイジェストによる "acr-helloworld" リポジトリからのイメージのプルは次のようになります。

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> 同じタグを持つ変更されたイメージを繰り返しプッシュする場合、孤立したイメージ (タグは付けられていないが、それでもレジストリの領域を消費するイメージ) が作成されることがあります。 Azure CLI または Azure portal でタグを使用してイメージを一覧表示または表示したとき、タグなしのイメージは表示されません。 ただし、それらのレイヤーはまだ存在し、レジストリの領域を消費します。 この記事の「[タグの付いていないイメージを削除する](#delete-untagged-images)」セクションでは、タグなしのイメージによって使用されている領域の解放について説明します。

## <a name="delete-image-data"></a>イメージ データを削除する

複数の方法で、コンテナー レジストリからイメージ データを削除できます。

* [リポジトリ](#delete-repository)を削除する:リポジトリ内のイメージと一意のレイヤーがすべて削除されます。
* [タグ](#delete-by-tag)を指定して削除する:イメージ、タグ、イメージによって参照されている一意のレイヤーすべて、およびイメージに関連付けられている他のすべてのタグが削除されます。
* [マニフェスト ダイジェスト](#delete-by-manifest-digest)を指定して削除する:イメージ、イメージによって参照されている一意のレイヤーすべて、およびイメージに関連付けられているすべてのタグが削除されます。

## <a name="delete-repository"></a>リポジトリを削除する

リポジトリを削除すると、タグ、一意のレイヤー、マニフェストのすべてを含め、リポジトリ内のすべてのイメージが削除されます。 リポジトリを削除すると、そのリポジトリに存在したイメージによって使われていたストレージ領域が回復されます。

次の Azure CLI コマンドは、"acr-helloworld" リポジトリと、そのリポジトリ内のすべてタグとマニフェストを削除します。 削除されるマニフェストによって参照されているレイヤーが、レジストリ内の他のイメージによって参照されていない場合、そのレイヤーのデータも削除されます。

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>タグで削除する

削除操作でリポジトリの名前とタグを指定することにより、リポジトリから個別のイメージを削除できます。 タグを指定して削除すると、イメージ内の一意レイヤー (レジストリ内の他のイメージによって共有されていないレイヤー) で使われているストレージ領域が回復されます。

タグを使用して削除するには、[az acr repository delete][az-acr-repository-delete] を使用し、`--image` パラメーターでイメージ名を指定します。 イメージに固有のすべてのレイヤーと、イメージに関連付けられている他のすべてのタグが削除されます。

たとえば、次の例では、レジストリ "myregistry" から "acr-helloworld:latest" イメージを削除しています。

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> "*タグによる*" 削除を、タグの削除 (タグ付けの解除) と混同しないでください。 タグの削除は、Azure CLI コマンド [az acr repository untag][az-acr-repository-untag] を使用して行うことができます。 イメージのタグを解除しても、[マニフェスト](#manifest)とレイヤーのデータがレジストリに残っているため、領域は解放されません。 タグ参照自体だけが削除されます。

## <a name="delete-by-manifest-digest"></a>マニフェスト ダイジェストで削除する

1 つの[マニフェスト ダイジェスト](#manifest-digest)を 1 つまたは複数のタグと関連付けることができ、マニフェスト ダイジェストをタグに関連付けないでおくこともできます。 ダイジェストを指定して削除すると、マニフェストによって参照されているすべてのタグが削除され、イメージに固有のレイヤーのレイヤー データも削除されます。 共有されているレイヤーのデータは削除されません。

ダイジェストを使用して削除するには、最初に、削除するイメージを含むリポジトリのマニフェスト ダイジェストのリストを取得します。 例: 

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
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

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

"acr-helloworld:v2" イメージがレジストリから削除され、そのイメージに固有のレイヤー データも削除されます。 マニフェストが複数のタグに関連付けられている場合は、関連付けられているすべてのタグも削除されます。

## <a name="delete-untagged-images"></a>タグの付いていないイメージを削除する

「[マニフェスト ダイジェスト](#manifest-digest)」セクションで説明したように、既存のタグを使用して変更されたイメージをプッシュすると、以前にプッシュされたイメージの**タグが解除**されて、孤立した (または "未解決の") イメージになります。 以前にプッシュされたイメージのマニフェストとそのレイヤー データは、レジストリに残っています。 次のような一連のイベントについて考えてみます。

1. **latest** というタグが付いたイメージ *acr-helloworld* をプッシュします。`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. リポジトリ *acr-helloworld* のマニフェストを確認します。

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
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

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
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

### <a name="list-untagged-images"></a>タグの付いていないイメージを一覧表示する

次の Azure CLI コマンドを使用して、リポジトリ内に存在するタグの付いていないすべてのイメージを一覧表示できます。 `<acrName>` と `<repositoryName>` は、環境に適した値に置き換えます。

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

### <a name="delete-all-untagged-images"></a>タグの付いていないイメージをすべて削除する

次のサンプル スクリプトを使用するときは注意してください。削除したイメージ データを元に戻すことはできません。

**Bash での Azure CLI**

次の Bash スクリプトは、リポジトリからタグの付いていないイメージをすべて削除します。 Azure CLI と **xargs** が必要です。 既定では、このスクリプトは削除を実行しません。 イメージの削除を有効にするには、`ENABLE_DELETE` の値を `true` に変更します。

> [!WARNING]
> (イメージ名ではなく) マニフェスト ダイジェストを使用してイメージをプルするシステムの場合は、このスクリプトを実行しないでください。 このようなシステムでは、タグの付いていないイメージを削除すると、レジストリからイメージをプルできなくなります。 マニフェストでプルするのではなく、*一意のタグ付け*スキームの[推奨されるベスト プラクティス][tagging-best-practices]を採用することを検討してください。

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
    echo "No data deleted. Set ENABLE_DELETE=true to enable image deletion."
fi
```

**PowerShell での Azure CLI**

次の PowerShell スクリプトは、リポジトリからタグの付いていないイメージをすべて削除します。 PowerShell と Azure CLI が必要です。 既定では、このスクリプトは削除を実行しません。 イメージの削除を有効にするには、`$enableDelete` の値を `$TRUE` に変更します。

> [!WARNING]
> (イメージ名ではなく) マニフェスト ダイジェストを使用してイメージをプルするシステムの場合は、このスクリプトを実行しないでください。 このようなシステムでは、タグの付いていないイメージを削除すると、レジストリからイメージをプルできなくなります。 マニフェストでプルするのではなく、*一意のタグ付け*スキームの[推奨されるベスト プラクティス][tagging-best-practices]を採用することを検討してください。

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
    Write-Host "No data deleted. Set `$enableDelete = `$TRUE to enable image deletion."
}
```

## <a name="next-steps"></a>次の手順

Azure Container Registry でのイメージ ストレージの詳細については、「[Azure Container Registry へのコンテナー イメージの保存](container-registry-storage.md)」を参照してください。

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com
[tagging-best-practices]: https://blogs.msdn.microsoft.com/stevelasker/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
