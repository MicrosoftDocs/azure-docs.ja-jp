---
title: レジストリのマルチアーキテクチャ イメージ
description: Azure Container Registry を使用して、マルチアーキテクチャ (multi-arch) イメージを構築、インポート、格納、デプロイします
ms.topic: article
ms.date: 02/07/2021
ms.custom: ''
ms.openlocfilehash: 520518a275e0a9d191ae770e560a0e6e8278eae2
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802456"
---
# <a name="multi-architecture-images-in-your-azure-container-registry"></a>Azure Container Registry のマルチアーキテクチャ イメージ

この記事では、*マルチアーキテクチャ* (*multi-arch*) イメージと、Azure Container Registry の機能を使用して、それらを作成、格納、使用する方法について説明します。 

マルチアーキテクチャ イメージは、さまざまなアーキテクチャおよび場合によってはさまざまなオペレーティング システムのバリアントを組み合わせることができるコンテナー イメージの一種です。 マルチアーキテクチャをサポートするイメージを実行すると、コンテナー クライアントによって、OS とアーキテクチャに一致するイメージ バリアントが自動的に選択されます。

## <a name="manifests-and-manifest-lists"></a>マニフェストとマニフェスト一覧

マルチアーキテクチャ イメージは、イメージ マニフェストとマニフェスト一覧に基づいています。

### <a name="manifest"></a>Manifest

各コンテナーイメージは、[マニフェスト](container-registry-concepts.md#manifest)によって表されます。 マニフェストは、イメージを一意に識別する JSON ファイルで、そのレイヤーとそれらに対応するサイズを参照します。 

Linux `hello-world` イメージの基本のマニフェストは、次のようになります。

  ```json
  {
    "schemaVersion": 2,
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "config": {
        "mediaType": "application/vnd.docker.container.image.v1+json",
        "size": 1510,
        "digest": "sha256:fbf289e99eb9bca977dae136fbe2a82b6b7d4c372474c9235adc1741675f587e"
      },
    "layers": [
        {
          "mediaType": "application/vnd.docker.image.rootfs.diff.tar.gzip",
          "size": 977,
          "digest": "sha256:2c930d010525941c1d56ec53b97bd057a67ae1865eebf042686d2a2d18271ced"
        }
      ]
  }
  ```
    
Azure Container Registry のマニフェストを表示するには、Azure portal または Azure CLI の [az acr repository show-manifests](/cli/azure/acr/repository#az_acr_repository_show_manifests) コマンドなどのツールを使用します。

### <a name="manifest-list"></a>マニフェスト一覧

マルチアーキテクチャ イメージの *マニフェスト一覧* (より一般的には、OCI イメージの場合に、[イメージ インデックス](https://github.com/opencontainers/image-spec/blob/master/image-index.md)と呼ばれる) はイメージのコレクション (インデックス) であり、それを作成するには、1 つまたは複数のイメージ名を指定します。 これには、サポートされている OS およびアーキテクチャ、サイズ、マニフェスト ダイジェストなどの各イメージの詳細が含まれます。 マニフェスト一覧は、`docker pull` コマンドと `docker run` コマンドでイメージ名と同じように使用できます。 

`docker` CLI では、[docker manifest](https://docs.docker.com/engine/reference/commandline/manifest/) コマンドを使用して、マニフェストとマニフェスト一覧を管理します。

> [!NOTE]
> 現時点で、`docker manifest` コマンドとサブコマンドは試験段階です。 試験段階のコマンドの使用の詳細については、Docker のドキュメントを参照してください。

マニフェスト一覧を表示するには、`docker manifest inspect` コマンドを使用します。 マルチアーキテクチャ イメージ `mcr.microsoft.com/mcr/hello-world:latest` の出力を次に示します。これには、Linux OS アーキテクチャ用に 2 つ、および Windows アーキテクチャ用に 1 つの 3 つのマニフェストがあります。
```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.docker.distribution.manifest.list.v2+json",
  "manifests": [
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 524,
      "digest": "sha256:83c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a",
      "platform": {
        "architecture": "amd64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 525,
      "digest": "sha256:873612c5503f3f1674f315c67089dee577d8cc6afc18565e0b4183ae355fb343",
      "platform": {
        "architecture": "arm64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 1124,
      "digest": "sha256:b791ad98d505abb8c9618868fc43c74aa94d08f1d7afe37d19647c0030905cae",
      "platform": {
        "architecture": "amd64",
        "os": "windows",
        "os.version": "10.0.17763.1697"
      }
    }
  ]
}
```

マルチアーキテクチャ マニフェスト一覧が Azure Container Registry に格納されている場合、Azure portal または [az acr repository show manifest](/cli/azure/acr/repository#az_acr_repository_how_manifests) コマンドなどのツールを使用して、マニフェスト一覧を表示することもできます。

## <a name="import-a-multi-arch-image"></a>マルチアーキテクチャ イメージのインポート 

既存のマルチアーキテクチャ イメージを Azure Container Registry にインポートするには、[az acr import](/cli/azure/acr#az_acr_import) コマンドを使用します。 イメージのインポートの構文は、単一アーキテクチャ イメージの場合と同じです。 単一アーキテクチャ イメージのインポートと同様に、マルチアーキテクチャ イメージのインポートでは、Docker コマンドを使用しません。 

詳細については、「[コンテナー レジストリにコンテナー イメージをインポートする](container-registry-import-images.md)」を参照してください。

## <a name="push-a-multi-arch-image"></a>マルチアーキテクチャ イメージのプッシュ

さまざまなアーキテクチャのコンテナー イメージを作成するワークフローを構築している場合は、次の手順に従って、マルチアーキテクチャ イメージを Azure Container Registry にプッシュします。

1. 各アーキテクチャ固有のイメージにタグを付けて、コンテナー レジストリにプッシュします。 次の例では、2 つの Linux アーキテクチャ (arm64 と amd64) を想定しています。 

   ```console
   docker tag myimage:arm64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:arm64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:arm64
 
   docker tag myimage:amd64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:amd64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ``` 

1. `docker manifest create` を実行して、マニフェスト一覧を作成し、前のイメージをマルチアーキテクチャ イメージに結合します。

   ```console
   docker manifest create myregistry.azurecr.io/multi-arch-samples/myimage:multi \
    myregistry.azurecr.io/multi-arch-samples/myimage:arm64 \
    myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ```

1. `docker manifest push` を使用して、コンテナー レジストリにマニフェストをプッシュします。

   ```console
   docker manifest push myregistry.azurecr.io/multi-arch-samples/myimage:multi
   ```

1. `docker manifest inspect` コマンドを使用して、マニフェスト一覧を表示します。 コマンド出力の例は、前のセクションに示しています。

マルチアーキテクチャ マニフェストをレジストリにプッシュしたら、単一アーキテクチャ イメージを処理する同じ方法で、マルチアーキテクチャ イメージを操作します。 たとえば、`docker pull` を使用してイメージをプルし、[az acr repository](/cli/azure/acr/repository#az_acr_repository) コマンドを使用して、イメージのタグ、マニフェスト、その他のプロパティを表示します。

## <a name="build-and-push-a-multi-arch-image"></a>マルチアーキテクチャ イメージの構築とプッシュ

[ACR タスク](container-registry-tasks-overview.md)の機能を使用すると、マルチアーキテクチャ イメージを構築して、Azure Container Registry にプッシュできます。 たとえば、Linux マルチアーキテクチャ イメージを構築する[複数ステップ タスク](container-registry-tasks-multi-step.md)を YAML ファイルに定義します。

次の例では、2 つのアーキテクチャ (arm64 と amd64) 用の個別の Dockerfile があることを前提としています。 これは、アーキテクチャ固有のイメージを構築してプッシュし、次に `latest` タグがあるマルチアーキテクチャ マニフェストを作成してプッシュします。

```yml
version: v1.1.0

steps:
- build: -t {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64 -f dockerfile.arm64 . 
- build: -t {{.Run.Registry}}/multi-arch-samples/myyimage:{{.Run.ID}}-arm64 -f dockerfile.amd64 . 
- push: 
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: >
    docker manifest create
    {{.Run.Registry}}/multi-arch-samples/myimage:latest
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: docker manifest push --purge {{.Run.Registry}}/multi-arch-samples/myimage:latest
- cmd: docker manifest inspect {{.Run.Registry}}/multi-arch-samples/myimage:latest
```

## <a name="next-steps"></a>次のステップ

* [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) を使用して、さまざまなアーキテクチャのコンテナー イメージを構築します。
* 試験段階の Docker [buildx](https://docs.docker.com/buildx/working-with-buildx/) プラグインを使用したマルチプラットフォーム イメージの構築について確認します。

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
