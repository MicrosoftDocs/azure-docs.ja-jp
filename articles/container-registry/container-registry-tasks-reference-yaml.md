---
title: YAML リファレンス - ACR タスク
description: YAML で ACR タスク用のタスクを定義するための参照 (タスクのプロパティ、ステップの種類、ステップのプロパティ、およびビルトイン変数など)。
ms.topic: article
ms.date: 07/08/2020
ms.openlocfilehash: 042310d29f5561c2cd77b0b9cccfc587ca4aa767
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88067585"
---
# <a name="acr-tasks-reference-yaml"></a>ACR タスクの参照:YAML

ACR タスクでの複数ステップのタスク定義では、コンテナーのビルド、テスト、および修正プログラムの適用に重点を置いたコンテナーを中心としたコンピューティング プリミティブを提供します。 この記事では、複数ステップのタスクを定義する YAML ファイルのコマンド、パラメーター、プロパティ、および構文について説明します。

この記事には、ACR タスク用の複数ステップのタスクの YAML ファイルを作成するための参照が含まれています。 ACR タスクの概要については、[ACR タスクの概要](container-registry-tasks-overview.md)に関するページを参照してください。

## <a name="acr-taskyaml-file-format"></a>acr-task.yaml ファイルの形式

ACR タスクでは、標準の YAML 構文で複数ステップのタスク宣言をサポートしています。 タスクのステップは YAML ファイルで定義します。 その後、ファイルを [az acr run][az-acr-run] コマンドに渡すことでタスクを手動で実行できます。 または、このファイルを使用して、Git コミットやベース イメージの更新時、またはスケジュールに基づいて自動的にトリガーされる [az acr task create][az-acr-task-create] でタスクを作成します。 この記事では、ステップを含むファイルとして `acr-task.yaml` を参照していますが、ACR タスクは、[サポートされる拡張子](#supported-task-filename-extensions)を持つ任意の有効なファイル名をサポートしています。

最上位レベルの `acr-task.yaml` プリミティブは、**タスク プロパティ**、**ステップの種類**、および **ステップ プロパティ** です。

* [タスク プロパティ](#task-properties)は、タスクの実行を通じてすべてのステップに適用されます。 次を含む複数のグローバル タスク プロパティがあります。
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [タスク ステップの種類](#task-step-types)は、タスクで実行できるアクションの種類を表します。 次の 3 つのステップの種類があります。
  * `build`
  * `push`
  * `cmd`
* [タスク ステップ プロパティ](#task-step-properties)は、個々のステップに適用されるパラメーターです。 次を含む複数のステップ プロパティがあります。
  * `startDelay`
  * `timeout`
  * `when`
  * その他。

いくつかの共通のステップ プロパティを含む、`acr-task.yaml` ファイルの基本形式を次に示します。 これは、利用可能なすべてのステップ プロパティまたはステップの種類の使用方法の完全に表しているわけではありませんが、基本的なファイル形式の簡単な概要を提供します。

```yml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
steps: # A collection of image or container actions.
  - build: # Equivalent to "docker build," but in a multi-tenant environment
  - push: # Push a newly built or retagged image to a registry.
    when: # Step property that defines either parallel or dependent step execution.
  - cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
    startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>サポートされているタスクのファイル名拡張子

ACR タスクでは、タスク ファイルとして処理する、複数のファイル名拡張子 (`.yaml` など) が予約されています。 次の一覧内に *ない* 拡張子は、ACR タスクではすべて Dockerfile と見なされます: .yaml .yml、.toml、.json、.sh、.bash、.zsh、.ps1、.ps、.cmd、.bat、.ts、.js、.php、.py、.rb .lua

YAML は、ACR タスクで現在サポートされている唯一のファイル形式です。 その他のファイル名拡張子は、将来のサポートの可能性のために予約されています。

## <a name="run-the-sample-tasks"></a>サンプル タスクを実行する

この記事の以降のセクションには、参照されるいくつかのサンプル タスク ファイルがあります。 サンプル タスクは、パブリックの GitHub リポジトリ ([Azure-Samples/acr-tasks][acr-tasks]) にあります。 これらは Azure CLI コマンド [az acr run][az-acr-run] で実行できます。 サンプル コマンドは、次のようになります。

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

サンプル コマンドの書式設定では、Azure CLI で既定のレジストリが構成されていることを前提としているため、`--registry` パラメーターが省略されています。 既定のレジストリを構成するには、`acr=REGISTRY_NAME` 値を受け入れる `--defaults` パラメーターを使用して、[az configure][az-configure] コマンドを使用します。

たとえば、Azure CLI を "myregistry" という名前の既定のレジストリで構成するには、次のようにします。

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>タスク プロパティ

タスク プロパティは通常、`acr-task.yaml` ファイルの上部に表示されます。これらは、タスク ステップの完全な実行を通じて適用されるグローバル プロパティです。 これらのグローバル プロパティの一部は、個々のステップ内でオーバーライドできます。

| プロパティ | Type | 省略可能 | 説明 | オーバーライドのサポート | 既定値 |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | string | はい | ACR タスク サービスによって解析される `acr-task.yaml` ファイルのバージョン。 ACR タスクは、下位互換性の維持に努めていますが、この値により ACR タスクが定義されたバージョン内で互換性を維持することが可能になります。 指定しない場合は、既定値の最新バージョンになります。 | いいえ | なし |
| `stepTimeout` | int (秒) | はい | ステップが実行できる最大秒数。 プロパティがタスクで指定されている場合は、すべてのステップの既定の `timeout` プロパティが設定されます。 `timeout` プロパティがステップで指定されている場合は、タスクによって提供されたプロパティがオーバーライドされます。 | はい | 600 (10 分) |
| `workingDirectory` | string | はい | 実行時のコンテナーの作業ディレクトリ。 プロパティがタスクで指定されている場合は、すべてのステップの既定の `workingDirectory` プロパティが設定されます。 ステップで指定されている場合は、タスクによって提供されたプロパティがオーバーライドされます。 | はい | Windows では `c:\workspace`、Linux では `/workspace` |
| `env` | [string, string, ...] | はい |  タスクの環境変数を定義する `key=value` 形式での文字列の配列。 プロパティがタスクで指定されている場合は、すべてのステップの既定の `env` プロパティが設定されます。 ステップに指定した場合は、タスクから継承されたすべての環境変数がオーバーライドされます。 | はい | なし |
| `secrets` | [secret, secret, ...] | はい | [secret](#secret) オブジェクトの配列。 | いいえ | なし |
| `networks` | [network, network, ...] | はい | [network](#network) オブジェクトの配列。 | いいえ | なし |
| `volumes` | [volume, volume, ...] | Yes | [volume](#volume) オブジェクトの配列。 ステップにマウントするソース コンテンツを含むボリュームを指定します。 | いいえ | なし |

### <a name="secret"></a>secret

シークレット オブジェクトには、次のプロパティがあります。

| プロパティ | Type | 省略可能 | 説明 | 既定値 |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | string | いいえ | シークレットの識別子。 | なし |
| `keyvault` | string | はい | Azure Key Vault のシークレット URL。 | なし |
| `clientID` | string | はい | Azure リソース用の[ユーザー割り当てのマネージド ID](container-registry-tasks-authentication-managed-identity.md) のクライアント ID。 | なし |

### <a name="network"></a>ネットワーク

ネットワーク オブジェクトには、次のプロパティがあります。

| プロパティ | Type | 省略可能 | 説明 | 既定値 |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | string | いいえ | ネットワークの名前。 | なし |
| `driver` | string | はい | ネットワークを管理するドライバー。 | なし |
| `ipv6` | [bool] | はい | IPv6 ネットワークが有効になっているかどうか。 | `false` |
| `skipCreation` | [bool] | はい | ネットワークの作成をスキップするかどうか。 | `false` |
| `isDefault` | [bool] | はい | ネットワークが Azure Container Registry で提供されている既定のネットワークかどうか。 | `false` |

### <a name="volume"></a>ボリューム

ボリューム オブジェクトには、次のプロパティがあります。

| プロパティ | Type | 省略可能 | 説明 | 既定値 |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | string | No | マウントするボリュームの名前。 英数字、'-'、および '_' のみを含めることができます。 | なし |
| `secret` | map[string]string | No | マップの各キーは、ボリュームに作成されて設定されたファイルの名前です。 各値は、シークレットの文字列バージョンです。 シークレット値は Base64 でエンコードする必要があります。 | なし |

## <a name="task-step-types"></a>タスク ステップの種類

ACR タスクでは、次の 3 種類のステップがサポートされています。 ステップの種類はそれぞれ複数のプロパティをサポートしており、各ステップの種類のセクションで詳しく説明されています。

| ステップの種類 | 説明 |
| --------- | ----------- |
| [`build`](#build) | 使い慣れた `docker build` 構文を使用してコンテナー イメージをビルドします。 |
| [`push`](#push) | 新しくビルドされたイメージ、またはタグが付け直されたイメージのコンテナー レジストリへの `docker push` を実行します。 Azure Container Registry、その他のプライベート レジストリ、およびパブリック Docker Hub がサポートされています。 |
| [`cmd`](#cmd) | コンテナーの `[ENTRYPOINT]` に渡されるパラメーターを使用して、コンテナーをコマンドとして実行します。 `cmd` ステップの種類は、`env` や `detach` などのパラメーターと、その他の使い慣れた `docker run` コマンド オプションをサポートしており、コンテナーの同時実行を使用した単体テストと機能テストを有効にします。 |

## <a name="build"></a>build

コンテナー イメージをビルドします。 `build` ステップの種類は、マルチ テナントを表します。これはファースト クラスのプリミティブとしてクラウド内で `docker build` を実行する安全な手段です。

### <a name="syntax-build"></a>構文: build

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

`build` ステップの種類では、次の表に示すパラメーターがサポートされています。 `build` ステップの種類では、[docker build](https://docs.docker.com/engine/reference/commandline/build/) コマンドのすべての build 操作 (build-time 変数などを設定する `--build-arg` など) もサポートされます。

| パラメーター | 説明 | 省略可能 |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | ビルドされたイメージの完全修飾 `image:tag` を定義します。<br /><br />イメージは機能テストなどの内部タスクの検証に使用することができるため、すべてのイメージでレジストリへの `push` が必要なわけではありません。 ただし、タスクの実行内でイメージをインスタンス化するには、イメージに参照する名前が必要です。<br /><br />`az acr build` とは異なり、ACR タスクの実行では、既定のプッシュ動作は提供されません。 ACR タスクでは、既定のシナリオは、イメージをビルド、検証してからプッシュできることを前提としています。 必要に応じてビルドされたイメージをプッシュする方法については、「[push](#push)」を参照してください。 | はい |
| `-f` &#124; `--file` | `docker build` に渡される Dockerfile を指定します。 指定しない場合、コンテキストのルートにある既定の Dockerfile が想定されます。 Dockerfile を指定するには、コンテキストのルートへの相対ファイル名を渡します。 | はい |
| `context` | `docker build` に渡されるルート ディレクトリ。 各タスクのルート ディレクトリは、共有の [workingDirectory](#task-step-properties) に設定され、関連付けられた Git の複製されたディレクトリのルートが含まれます。 | いいえ |

### <a name="properties-build"></a>プロパティ: build

`build` ステップの種類では、次のプロパティをサポートしています。 これらプロパティの詳細については、この記事の「[タスク ステップ プロパティ](#task-step-properties)」を参照してください。

| Properties | Type | 必須 |
| -------- | ---- | -------- |
| `detach` | [bool] | 省略可能 |
| `disableWorkingDirectoryOverride` | [bool] | 省略可能 |
| `entryPoint` | string | 省略可能 |
| `env` | [string, string, ...] | 省略可能 |
| `expose` | [string, string, ...] | 省略可能 |
| `id` | string | 省略可能 |
| `ignoreErrors` | [bool] | 省略可能 |
| `isolation` | string | 省略可能 |
| `keep` | [bool] | 省略可能 |
| `network` | object | 省略可能 |
| `ports` | [string, string, ...] | 省略可能 |
| `pull` | [bool] | 省略可能 |
| `repeat` | INT | 省略可能 |
| `retries` | INT | 省略可能 |
| `retryDelay` | int (秒) | 省略可能 |
| `secret` | object | 省略可能 |
| `startDelay` | int (秒) | 省略可能 |
| `timeout` | int (秒) | 省略可能 |
| `volumeMount` | object | 省略可能 |
| `when` | [string, string, ...] | 省略可能 |
| `workingDirectory` | string | 省略可能 |

### <a name="examples-build"></a>例: build

#### <a name="build-image---context-in-root"></a>イメージのビルド - ルートのコンテキスト

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>イメージのビルド - サブディレクトリのコンテキスト

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>push

1 つ以上のビルドされたイメージ、またはタグが付け直されたイメージをコンテナー レジストリにプッシュします。 Azure Container Registry などのプライベート レジストリや、パブリック Docker Hub へのプッシュをサポートします。

### <a name="syntax-push"></a>構文: push

`push` ステップの種類は、イメージのコレクションをサポートしています。 YAML コレクション構文では、インラインと入れ子になった形式をサポートしています。 1 つのイメージのプッシュは通常、インライン構文を使用して表されます。

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

わかりやすくするため、複数のイメージをプッシュするときに、入れ子になった構文を使用します。

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>プロパティ: push

`push` ステップの種類では、次のプロパティをサポートしています。 これらプロパティの詳細については、この記事の「[タスク ステップ プロパティ](#task-step-properties)」を参照してください。

| プロパティ | Type | 必須 |
| -------- | ---- | -------- |
| `env` | [string, string, ...] | 省略可能 |
| `id` | string | 省略可能 |
| `ignoreErrors` | [bool] | 省略可能 |
| `startDelay` | int (秒) | 省略可能 |
| `timeout` | int (秒) | 省略可能 |
| `when` | [string, string, ...] | 省略可能 |

### <a name="examples-push"></a>例: push

#### <a name="push-multiple-images"></a>複数のイメージをプッシュする

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>ビルド、プッシュ、実行

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

`cmd` ステップの種類は、コンテナーを実行します。

### <a name="syntax-cmd"></a>構文: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>プロパティ: cmd

`cmd` ステップの種類では、次のプロパティをサポートしています。

| プロパティ | Type | 必須 |
| -------- | ---- | -------- |
| `detach` | [bool] | 省略可能 |
| `disableWorkingDirectoryOverride` | [bool] | 省略可能 |
| `entryPoint` | string | 省略可能 |
| `env` | [string, string, ...] | 省略可能 |
| `expose` | [string, string, ...] | 省略可能 |
| `id` | string | 省略可能 |
| `ignoreErrors` | [bool] | 省略可能 |
| `isolation` | string | 省略可能 |
| `keep` | [bool] | 省略可能 |
| `network` | object | 省略可能 |
| `ports` | [string, string, ...] | 省略可能 |
| `pull` | [bool] | 省略可能 |
| `repeat` | INT | 省略可能 |
| `retries` | INT | 省略可能 |
| `retryDelay` | int (秒) | 省略可能 |
| `secret` | object | 省略可能 |
| `startDelay` | int (秒) | 省略可能 |
| `timeout` | int (秒) | 省略可能 |
| `volumeMount` | object | 省略可能 |
| `when` | [string, string, ...] | 省略可能 |
| `workingDirectory` | string | 省略可能 |

これらプロパティの詳細については、この記事の「[タスク ステップ プロパティ](#task-step-properties)」を参照してください。

### <a name="examples-cmd"></a>例: cmd

#### <a name="run-hello-world-image"></a>hello-world イメージの実行

このコマンドは、Docker Hub 上の [hello-world](https://hub.docker.com/_/hello-world/) イメージを参照する `hello-world.yaml` タスク ファイルを実行します。

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>bash イメージとエコー "hello world" の実行

このコマンドは、Docker Hub 上の [bash](https://hub.docker.com/_/bash/) イメージを参照する `bash-echo.yaml` タスク ファイルを実行します。

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>特定の bash イメージ タグを実行する

イメージの特定のバージョンを実行するには、`cmd` でタグを指定します。

このコマンドは、Docker Hub 上の [bash:3.0](https://hub.docker.com/_/bash/) イメージを参照する `bash-echo-3.yaml` タスク ファイルを実行します。

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>カスタム イメージを実行する

`cmd` ステップの種類は、標準の `docker run` 形式を使用してイメージを参照します。 レジストリで始まらないイメージは、docker.io から始まると見なされます。 前の例は、次のように表すこともできます。

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

標準の `docker run` イメージの参照規約を使用することで、`cmd` で任意のプライベート レジストリまたはパブリック Docker Hub からのイメージを実行できます。 ACR タスクが実行されているのと同じレジストリ内のイメージを参照している場合は、レジストリ資格情報を指定する必要はありません。

* Azure コンテナー レジストリからイメージを実行します。 次の例では、`myregistry` という名前のレジストリと、カスタム イメージ `myimage:mytag` があるものとします。

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Run 変数またはエイリアスでレジストリの参照を汎用化する

    `acr-task.yaml` ファイルにレジストリ名をハードコーディングする代わりに、[Run 変数](#run-variables)または[エイリアス](#aliases)を使用して移植性を高めることができます。 `Run.Registry` 変数または `$Registry` エイリアスは、実行時に、タスクが実行されているレジストリの名前に展開されます。

    たとえば、前のタスクを任意の Azure コンテナー レジストリで動作するように汎用化するには、イメージ名で $Registry 変数を参照します。

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

#### <a name="access-secret-volumes"></a>シークレット ボリュームにアクセスする

`volumes` プロパティを使用すると、タスクの `build` および `cmd` ステップに対して、ボリュームとそのシークレット コンテンツを指定できます。 各ステップ内では、省略可能な `volumeMounts` プロパティに、そのステップでコンテナーにマウントするボリュームと対応するコンテナー パスが一覧表示されます。 シークレットは、各ボリュームのマウント パスでファイルとして提供されます。

タスクを実行し、2 つのシークレットを 1 つのステップにマウントします。キー コンテナーに格納されたものと、コマンド ラインで指定されたものです。

```azurecli
az acr run -f mounts-secrets.yaml --set-secret mysecret=abcdefg123456 https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/mounts-secrets.yaml -->
[!code-yml[task](~/acr-tasks/mounts-secrets.yaml)]

## <a name="task-step-properties"></a>タスク ステップ プロパティ

各ステップの種類は、その種類に適した複数のプロパティをサポートしています。 次の表では、利用可能なすべてのステップのプロパティを定義します。 すべてのステップの種類が、すべてのプロパティをサポートしているわけではありません。 各ステップの種類でこれらのプロパティが利用可能かどうかを確認するには、「[cmd](#cmd)」、「[build](#build)」、「[push](#push)」 のステップの種類の参照セクションを参照してください。

| プロパティ | Type | 省略可能 | 説明 | 既定値 |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | [bool] | はい | 実行時にコンテナーをデタッチする必要があるかどうか。 | `false` |
| `disableWorkingDirectoryOverride` | [bool] | はい | `workingDirectory` オーバーライド機能を無効にするかどうか。 これを `workingDirectory` と組み合わせて使用して、コンテナーの作業ディレクトリを完全に制御します。 | `false` |
| `entryPoint` | string | はい | ステップのコンテナーの `[ENTRYPOINT]` をオーバーライドします。 | なし |
| `env` | [string, string, ...] | はい | ステップの環境変数を定義する `key=value` 形式での文字列の配列。 | なし |
| `expose` | [string, string, ...] | はい | コンテナーから公開されているポートの配列。 |  なし |
| [`id`](#example-id) | string | はい | タスク内のステップを一意に識別します。 タスク内のその他のステップでは、`when` での依存関係のチェックなどのために、ステップの `id` を参照できます。<br /><br />`id` は実行中のコンテナーの名前でもあります。 タスク内のその他のコンテナーで実行されているプロセスは、その DNS ホスト名として、または docker ログ [id] などでアクセスするために `id` を参照できます。 | `acb_step_%d`。ここで、`%d` は、YAML ファイルのステップのトップダウンの 0 から始まるインデックスです |
| `ignoreErrors` | [bool] | はい | コンテナーの実行中にエラーが発生したかどうかに関係なく、ステップを成功としてマークするかどうか。 | `false` |
| `isolation` | string | はい | コンテナーの分離レベル。 | `default` |
| `keep` | [bool] | はい | 実行後にステップのコンテナーを保持する必要があるかどうか。 | `false` |
| `network` | object | はい | コンテナーが実行されるネットワークを識別します。 | なし |
| `ports` | [string, string, ...] | はい | コンテナーからホストに公開されているポートの配列。 |  なし |
| `pull` | [bool] | はい | キャッシュ動作を防ぐために、実行前にコンテナーを強制的にプルするかどうか。 | `false` |
| `privileged` | [bool] | はい | コンテナーを特権モードで実行するかどうか。 | `false` |
| `repeat` | INT | はい | コンテナーの実行を繰り返すための再試行回数。 | 0 |
| `retries` | INT | はい | コンテナーの実行に失敗した場合の再試行回数。 再試行は、コンテナーの終了コードがゼロ以外の場合にのみ行われます。 | 0 |
| `retryDelay` | int (秒) | はい | コンテナーの実行の再試行間の遅延 (秒)。 | 0 |
| `secret` | object | はい | Azure Key Vault シークレットまたは [Azure リソースのマネージド ID](container-registry-tasks-authentication-managed-identity.md) を識別します。 | なし |
| `startDelay` | int (秒) | はい | コンテナーの実行を遅らせる秒数。 | 0 |
| `timeout` | int (秒) | はい | ステップが終了されるまでに実行できる最大秒数。 | 600 |
| [`when`](#example-when) | [string, string, ...] | はい | タスク内で 1 つ以上のその他のステップに対するステップの依存関係を構成します。 | なし |
| `user` | string | はい | ユーザー名またはコンテナーの UID | なし |
| `workingDirectory` | string | はい | ステップ用の作業ディレクトリを設定します。 既定では、ACR タスクは作業ディレクトリとしてルート ディレクトリを作成します。 ただし、ビルドに複数のステップがある場合は、同じ作業ディレクトリを指定することで、前のステップは後のステップと成果物を共有することができます。 | Windows では `c:\workspace`、Linux では `/workspace` |

### <a name="volumemount"></a>volumeMount

volumeMount オブジェクトには、次のプロパティがあります。

| プロパティ | Type | 省略可能 | 説明 | 既定値 |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | string | No | マウントするボリュームの名前。 `volumes` プロパティの名前と正確に一致している必要があります。 | なし |
| `mountPath`   | string | no | コンテナー内のファイルをマウントするための絶対パス。  | なし |

### <a name="examples-task-step-properties"></a>例 :タスク ステップ プロパティ

#### <a name="example-id"></a>例: id

機能テスト イメージをインスタンス化する 2 つのイメージをビルドします。 各ステップは、タスク内のその他のステップがその `when` プロパティ内で参照する一意の `id` で識別されます。

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>例: when

`when` プロパティは、タスク内のその他のステップに対するステップの依存関係を指定します。 次の 2 つのパラメーター値がサポートされます。

* `when: ["-"]`: 他のステップへの依存関係がないことを示します。 `when: ["-"]` を指定するステップは、すぐに実行が開始され、同時ステップの実行を有効にします。
* `when: ["id1", "id2"]`: ステップが `id` "id1" と `id` "id2" のステップに依存していることを示します。 このステップは、"id1" と "id2" の両方のステップが完了するまで実行されません。

`when` がステップで指定されていない場合、そのステップは `acr-task.yaml` ファイル内の前のステップの完了に依存します。

`when` を使用しないシーケンシャル ステップの実行:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

`when` を使用したシーケンシャル ステップの実行:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

並列イメージのビルド:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

並列イメージのビルドと依存のテスト:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Run 変数

ACR タスクには、タスク ステップを実行するときに使用できる変数の既定のセットが含まれています。 これらの変数は、`{{.Run.VariableName}}` の形式を使用してアクセスできます。`VariableName` は次のいずれかです。

* `Run.ID`
* `Run.SharedVolume`
* `Run.Registry`
* `Run.RegistryName`
* `Run.Date`
* `Run.OS`
* `Run.Architecture`
* `Run.Commit`
* `Run.Branch`
* `Run.TaskName`

通常、変数名を見ればそれがどういうものかわかります。 以下では、よく使用される変数について詳しく説明します。 YAML バージョン `v1.1.0` では、ほとんどの Run 変数の代わりに、省略された定義済みの[タスク エイリアス](#aliases)を使用できます。 たとえば、`{{.Run.Registry}}` の代わりに、`$Registry` エイリアスを使用します。

### <a name="runid"></a>Run.ID

`az acr run` による各 Run、または `az acr task create` により作成されたタスクのトリガー ベースの実行には、一意の ID があります。 ID は、現在実行中の Run を表します。

通常、イメージに一意にタグ付けするために使用されます。

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runsharedvolume"></a>Run.SharedVolume

すべてのタスク手順によってアクセス可能な共有ボリュームの一意の識別子。 ボリュームは、Windows では `c:\workspace`、Linux では `/workspace` にマウントされます。 

### <a name="runregistry"></a>Run.Registry

レジストリの完全修飾サーバー名。 通常、タスクが実行されているレジストリをまとめて参照するために使用されます。

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Run.RegistryName

コンテナー レジストリの名前。 通常、完全修飾サーバー名を必要としないタスク ステップで使用されます。たとえば、レジストリで Azure CLI コマンドを実行する `cmd` の手順などです。

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Run.Date

実行が開始された現在の UTC 時間。

### <a name="runcommit"></a>Run.Commit

GitHub リポジトリへのコミットによってトリガーされるタスクの場合、コミット ID。

### <a name="runbranch"></a>Run.Branch

GitHub リポジトリへのコミットによってトリガーされるタスクの場合、ブランチ名。

## <a name="aliases"></a>エイリアス

`v1.1.0` の時点で、ACR タスクでは、タスク ステップを実行するときに使用できるエイリアスがサポートされています。 エイリアスは、概念的には、bash や他のコマンド シェルでサポートされているエイリアス (コマンド ショートカット) と似ています。 

エイリアスを使用すると、1 つの単語を入力することで、任意のコマンドまたはコマンドのグループ (オプションやファイル名を含む) を起動できます。

ACR タスクでは、いくつかの定義済みエイリアスと、ユーザーが作成するカスタム エイリアスがサポートされています。

### <a name="predefined-aliases"></a>定義済みのエイリアス

次のタスク エイリアスは、[Run 変数](#run-variables)の代わりに使用できます。

| エイリアス | Run 変数 |
| ----- | ------------ |
| `ID` | `Run.ID` |
| `SharedVolume` | `Run.SharedVolume` |
| `Registry` | `Run.Registry` |
| `RegistryName` | `Run.RegistryName` |
| `Date` | `Run.Date` |
| `OS` | `Run.OS` |
| `Architecture` | `Run.Architecture` |
| `Commit` | `Run.Commit` |
| `Branch` | `Run.Branch` |

次の例のように、タスク ステップでは、エイリアスの前に `$` ディレクティブを付けます。

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>イメージ エイリアス

次の各エイリアスでは、Microsoft Container Registry (MCR) 内の安定したイメージが指し示されています。 タスク ファイルの `cmd` セクションでは、ディレクティブを使用せずに参照できます。

| エイリアス | Image |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

次のタスクの例では、複数のエイリアスを使用して、実行レジストリのリポジトリ `samples/hello-world` 内にある 7 日以上経過したイメージ タグを[消去](container-registry-auto-purge.md)しています。

```yml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>カスタム エイリアス

次の例で示すように、YAML ファイルでカスタム エイリアスを定義して使用します。 エイリアスでは、英数字のみを使用できます。 エイリアスを展開する既定のディレクティブは `$` 文字です。

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

カスタム エイリアスの定義では、リモートまたはローカルの YAML ファイルにリンクできます。 次の例では、Azure Blob Storage 内の YAML ファイルにリンクしています。

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>次のステップ

複数ステップのタスクの概要については、「[Run multi-step build, test, and patch tasks in ACR Tasks](container-registry-tasks-multi-step.md)」 (ACR タスクで複数ステップのビルド、テスト、修正プログラムの適用タスクを実行する) を参照してください。

シングル ステップのビルドについては、[ACR タスクの概要](container-registry-tasks-overview.md)に関するページを参照してください。



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure
