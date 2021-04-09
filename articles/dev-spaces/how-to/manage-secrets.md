---
title: Azure Dev Space を操作する場合のシークレットを管理する方法
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Azure Dev Spaces を使用してアプリケーションを開発する際に、実行時またはビルド時に Kubernetes シークレットを使用する方法について説明します
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, コンテナー
ms.custom: devx-track-js
ms.openlocfilehash: 8791480f420dfd76d5291ce82e8ebf7412a41326
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91972970"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Azure Dev Space を操作する場合のシークレットを管理する方法

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

サービスで、データベースやその他のセキュリティ保護された Azure サービスなどの特定のパスワード、接続文字列、およびその他のシークレットが必要になることがあります。 構成ファイルにこれらのシークレットの値を設定して、コードで環境変数としてそれらを使用できるようにすることができます。  これらの構成ファイルは、シークレットのセキュリティを損なわないように注意して扱う必要があります。

## <a name="storing-and-using-runtime-secrets"></a>ランタイム シークレットの格納と使用

Azure Dev Spaces では、Azure Dev Spaces クライアント ツールによって生成された Helm チャートにシークレットを格納するための 2 つの推奨されるオプションが用意されています。`values.dev.yaml` ファイル内と `azds.yaml` にインラインで直接格納する方法です。 `values.yaml` にシークレットを格納することはお勧めしません。

> [!NOTE]
> 以降の手法では、クライアント ツールによって生成された Helm チャート用にシークレットを格納して使用する方法について説明します。 独自の Helm チャートを作成した場合は、その Helm チャートを直接使用して、シークレットを管理および格納できます。

### <a name="using-valuesdevyaml"></a>values.dev.yaml を使用する

Azure Dev Spaces を利用して既に準備したプロジェクトでは、`azds.yaml` と同じフォルダーに `values.dev.yaml` ファイルを作成して、シークレット キーと値を定義します。 次に例を示します。

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

`?` を使用して、オプションで `azds.yaml` ファイルの参照 `values.dev.yaml` を確認します。 次に例を示します。

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

追加のシークレット ファイルがある場合は、ここに追加することもできます。

サービスからシークレットが環境変数として参照されている状態を、更新または確認します。 次に例を示します。

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
`azds up` を使用して、更新されたサービスを実行します。

```console
azds up
```
 
`kubectl` を使用して、シークレットが作成されたことを確認します。

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> ソース管理にシークレットを格納することはお勧めしません。 Git を使用している場合は、ソース管理でのシークレットのコミットを回避するために、`values.dev.yaml` を `.gitignore` ファイルに追加します。

### <a name="using-azdsyaml"></a>azds.yaml を使用する

Azure Dev Spaces を使用して既に準備したプロジェクトでは、 *$PLACEHOLDER* 構文を使用して、`azds.yaml` 内の *configurations.develop.install.set* 下にシークレット キーと値を追加します。 次に例を示します。

```yaml
configurations:
  develop:
    ...
    install:
      set:
        secrets:
          redis:
            port: "$REDIS_PORT"
            host: "$REDIS_HOST"
            key: "$REDIS_KEY"
```

> [!NOTE]
> `azds.yaml`内には、 *$PLACEHOLDER* 構文を使用せずに、直接シークレット値を入力できます。 ただし、`azds.yaml` はソース管理に格納されるため、この手法はお勧めしません。
     
`azds.yaml` と同じフォルダーに `.env` ファイルを作成して、 *$PLACEHOLDER* 値を定義します。 次に例を示します。

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> ソース管理にシークレットを格納することはお勧めしません。 Git を使用している場合は、ソース管理でのシークレットのコミットを回避するために、`.env` を `.gitignore` ファイルに追加します。

サービスからシークレットが環境変数として参照されている状態を、更新または確認します。 次に例を示します。

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
`azds up` を使用して、更新されたサービスを実行します。

```console
azds up
```
 
`kubectl` を使用して、シークレットが作成されたことを確認します。

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>ビルド引数としてシークレットを使用する

前のセクションでは、コンテナーの実行時に使うシークレットを格納して使用する方法について説明しました。 `azds.yaml` を使用して、プライベート NuGet 用のパスワードなど、コンテナーのビルド時に任意のシークレットを使用することもできます。

`azds.yaml` で、`<variable name>: ${secret.<secret name>.<secret key>}` 構文を使用し、*configurations.develop.build.args* でビルド時のシークレットを設定します。 次に例を示します。

```yaml
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
        MYTOKEN: ${secret.mynugetsecret.pattoken}
```

上の例では、*mynugetsecret* は既存のシークレットであり、*pattoken* は既存のキーです。

>[!NOTE]
> シークレットの名前とキーには `.` 文字を含めることができます。 ビルド引数としてシークレットを渡すときには、`\` を使用して `.` をエスケープすることができます。 たとえば、*foo. bar* という名前のシークレットと *token*: `MYTOKEN: ${secret.foo\.bar.token}` のキーを渡すには、次のように指定します。 また、プレフィックスと postfix テキストを使用してシークレットを評価することもできます。 たとえば、「 `MYURL: eus-${secret.foo\.bar.token}-version1` 」のように入力します。 また、親と親の親のスペースで使用できるシークレットは、ビルド引数として渡すことができます。

Dockerfile で、*ARG* ディレクティブを使用してシークレットを使用し、その同じ変数を Dockerfile で後で使用します。 次に例を示します。

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

クラスターで実行されているサービスをこれらの変更で更新します。 コマンド ラインで、次のコマンドを実行します。

```
azds up
```

## <a name="next-steps"></a>次のステップ

これらの方法によって、データベース、Azure Cache for Redis に安全に接続したり、セキュリティ保護された Azure サービスにアクセスしたりできます。
 
