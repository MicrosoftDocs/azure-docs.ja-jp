---
title: レジストリ、リポジトリ、イメージ、成果物について
description: Azure コンテナー レジストリ、リポジトリ、コンテナー イメージなどの主要な成果物の概念について紹介します。
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 991be79b10b6061f2034eb19e4e139af65aef3cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578083"
---
# <a name="about-registries-repositories-and-artifacts"></a>レジストリ、リポジトリ、成果物について

この記事では、コンテナー レジストリ、リポジトリ、およびコンテナー イメージと関連する成果物の主要な概念について紹介します。 

:::image type="content" source="media/container-registry-concepts/registry-elements.png" alt-text="レジストリ、リポジトリ、成果物":::

## <a name="registry"></a>レジストリ

コンテナー "*レジストリ*" は、コンテナー イメージおよび関連する成果物を格納して配布するサービスです。 Docker Hub は、Docker コンテナー イメージの一般的なカタログとして機能するパブリック コンテナー レジストリの例です。 Azure Container Registry では、統合認証、ネットワーク上の近い場所へのデプロイに対応したグローバルな配布と信頼性をサポートする [geo レプリケーション](container-registry-geo-replication.md)、[Private Link を使用した仮想ネットワーク構成](container-registry-private-link.md)、[タグ ロック](container-registry-image-lock.md)、その他多くの拡張機能を使用して、ユーザーがコンテナー コンテンツを直接制御できるようにします。 

Docker 対応コンテナー イメージに加え、Azure Container Registry では、Helm chart と Open Container Initiative (OCI) イメージ形式を含む、さまざまな[コンテンツ成果物](container-registry-image-formats.md)がサポートされます。

## <a name="repository"></a>リポジトリ

"*リポジトリ*" は、名前が同じでタグが異なる、レジストリ内のコンテナー イメージまたはその他の成果物のコレクションです。 たとえば、次の 3 つのイメージは `acr-helloworld` リポジトリ内にあります。

- *acr-helloworld:latest*
- *acr-helloworld:v1*
- *acr-helloworld:v2*

リポジトリ名には、[名前空間](container-registry-best-practices.md#repository-namespaces)を含めることもできます。 名前空間を使用すると、スラッシュ区切りの名前を使用して、組織内の関連するリポジトリと成果物の所有権を識別できます。 ただし、レジストリでは、階層としてではなく、すべてのリポジトリが個別に管理されます。 次に例を示します。

- *marketing/campaign10-18/web:v2*
- *marketing/campaign10-18/api:v3*
- *marketing/campaign10-18/email-sender:v2*
- *product-returns/web-submission:20180604*
- *product-returns/legacy-integrator:20180715*

リポジトリ名には、小文字の英数字、ピリオド、ダッシュ、アンダースコア、およびスラッシュのみを含めることができます。 

リポジトリの完全な名前付け規則については、[Open Container Initiative Distribution Specification](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview) (オープン コンテナー イニシアチブの配布仕様) を参照してください。

## <a name="artifact"></a>アーティファクト

レジストリ内のコンテナー イメージまたはその他の成果物は、1 つまたは複数のタグに関連付けられており、1 つまたは複数のレイヤーを持ち、マニフェストによって識別されます。 これらのコンポーネントの相互関係を理解しておくと、レジストリを効率的に管理するのに役立ちます。

### <a name="tag"></a>タグ

イメージまたはその他の成果物の "*タグ*" は、そのバージョンを指定します。 リポジトリ内の 1 つの成果物に、1 つまたは多数のタグを割り当てることができ、"タグを割り当てない" ことも可能です。 つまり、イメージからすべてのタグを削除しながら、イメージのデータ (そのレイヤー) はレジストリに引き続き残しておくことができます。

リポジトリ (またはリポジトリと名前空間) とタグの組み合わせで、イメージの名前が定義されます。 プッシュ操作またはプル操作で名前を指定することにより、イメージをプッシュおよびプルできます。 Docker コマンドにタグが指定されていない場合、既定ではタグ `latest` が使用されます。

コンテナー イメージにタグを付ける方法は、コンテナー イメージを開発またはデプロイするシナリオによって決まります。 たとえば、基本イメージを維持する場合は安定したタグ、イメージをデプロイする場合は一意のタグが推奨されます。 詳細については、「[コンテナー イメージのタグ付けとバージョン管理に関する推奨事項](container-registry-image-tag-version.md)」を参照してください。

タグの名前付け規則については、[Docker のドキュメント](https://docs.docker.com/engine/reference/commandline/tag/)を参照してください。

### <a name="layer"></a>レイヤー

コンテナー イメージと成果物は、1 つ以上の "*レイヤー*"" で構成されます。 さまざまな成果物の種類でレイヤーを定義する方法が異なります。 たとえば、Docker コンテナー イメージでは、各レイヤーは、イメージを定義する Dockerfile 内の行に対応します。

:::image type="content" source="media/container-registry-concepts/container-image-layers.png" alt-text="コンテナー イメージのレイヤー":::

ストレージの効率が良くなるように、レジストリ内の成果物は共通レイヤーを共有しています。 たとえば、異なるリポジトリ内の複数のイメージが共通の ASP.NET Core ベース レイヤーを保持している場合がありますが、レジストリに格納されるのはそのレイヤーの 1 つのコピーだけです。 レイヤーの共有により、複数の成果物が共通レイヤーを共有することで、ノードへのレイヤーの配布も最適化されます。 ノード上に既に存在するイメージがそのベースとして ASP.NET Core レイヤーを含む場合、同じレイヤーを参照する異なるイメージをそれ以降にプルしても、レイヤーはノードに転送されません。 代わりに、ノードに既に存在するレイヤーが参照されます。

安全な分離を確保し潜在的なレイヤー操作から保護するために、レイヤーはレジストリ間で共有されません。

### <a name="manifest"></a>Manifest

コンテナー レジストリにプッシュされる各コンテナー イメージまたは成果物は、"*マニフェスト*" と関連付けられます。 コンテンツがプッシュされるときにレジストリによって生成されるマニフェストは、コンテンツを一意に示し、レイヤーを指定します。 Azure CLI コマンド [az acr repository show-manifests][az-acr-repository-show-manifests] を使用して、リポジトリのマニフェストを一覧表示できます。 

Linux `hello-world` イメージの基本的なマニフェストは、次のようになります。

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

Azure CLI コマンド [az acr repository show-manifests][az-acr-repository-show-manifests] を使用して、リポジトリのマニフェストを一覧表示できます。

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

たとえば、"acr-helloworld" リポジトリのマニフェストの一覧は次のようになります。

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

### <a name="manifest-digest"></a>マニフェスト ダイジェスト

マニフェストは、一意の SHA-256 ハッシュつまり "*マニフェスト ダイジェスト*" によって識別されます。 各イメージまたは成果物は、タグ付きかどうかにかかわらず、そのダイジェストによって識別されます。 ダイジェストの値は、成果物のレイヤー データが別の成果物と同じ場合であっても一意です。 このメカニズムにより、同じタグが付けられたイメージをレジストリに繰り返しプッシュできます。 たとえば、各イメージはその一意のダイジェストによって識別されるため、`myimage:latest` をレジストリに繰り返しプッシュしてもエラーにはなりません。

プル操作ではダイジェストを指定することにより、レジストリから成果物をプルできます。 システムによっては、ダイジェストによってプルするように構成されている場合があります。同じタグが付けられたイメージが後でレジストリにプッシュされた場合でも、ダイジェストによって、プルされているイメージのバージョンが保証されるためです。

> [!IMPORTANT]
> 同じタグを持つ変更された成果物を繰り返しプッシュする場合、"孤立" (タグが付けられていない成果物) が作成されることがありますが、それでもレジストリの領域は消費されます。 Azure CLI または Azure portal でタグを使用してイメージを一覧表示または表示したとき、タグなしのイメージは表示されません。 ただし、それらのレイヤーはまだ存在し、レジストリの領域を消費します。 特定のレイヤーを指すマニフェストが他に存在しなければ (つまり最後のマニフェストであれば)、タグの付いていないイメージを削除することで、レジストリの領域は解放されます。 タグの付いていないイメージによって使用されている領域を開放する方法の詳細については、「[Azure Container Registry のコンテナー イメージを削除する](container-registry-delete.md)」を参照してください。

## <a name="addressing-an-artifact"></a>成果物のアドレス指定

Docker またはその他のクライアント ツールを使用したプッシュおよびとプル操作のためにレジストリ成果物をアドレス指定するには、完全修飾レジストリ名、リポジトリ名 (該当する場合は名前空間パスを含む)、成果物タグまたはマニフェスト ダイジェストを結合します。 これらの用語の説明については、前のセクションを参照してください。

  **タグによるアドレス**: `[loginServerUrl]/[repository][:tag]`
    
  **ダイジェストによるアドレス**: `[loginServerUrl]/[repository@sha256][:digest]`  

Docker またはその他のクライアント ツールを使用して、成果物を Azure コンテナー レジストリにプルまたはプッシュする場合は、レジストリの完全修飾 URL ("*ログイン サーバー*" 名とも呼ばれます) を使用します。 Azure クラウドでは、Azure コンテナー レジストリの完全修飾 URL の形式は `myregistry.azurecr.io` (すべて小文字) です。

> [!NOTE]
> * `myregistry.azurecr.io:443` のように、レジストリ ログイン サーバーの URL にポート番号を指定することはできません。 
> * コマンドにタグが指定されていない場合、既定ではタグ `latest` が使用されます。  

   
### <a name="push-by-tag"></a>タグによるプッシュ

例 : 

   `docker push myregistry.azurecr.io/samples/myimage:20210106`

   `docker push myregistry.azurecr.io/marketing/email-sender`

### <a name="pull-by-tag"></a>タグによるプル

例: 

  `docker pull myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2`

### <a name="pull-by-manifest-digest"></a>マニフェスト ダイジェストによるプル


例:

  `docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`



## <a name="next-steps"></a>次のステップ

Azure Container Registry での[レジストリ ストレージ](container-registry-storage.md)と[サポートされているコンテンツの形式](container-registry-image-formats.md)について詳細を確認します。

Azure Container Registry に対して[イメージをプッシュおよびプル](container-registry-get-started-docker-cli.md)する方法を確認します。

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


