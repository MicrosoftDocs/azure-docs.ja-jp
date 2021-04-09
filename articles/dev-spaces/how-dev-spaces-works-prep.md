---
title: Azure Dev Spaces のためにプロジェクトを準備する方法
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Azure Dev Spaces でプロジェクトを準備する方法について説明します
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
ms.openlocfilehash: d2da69dd8a8c2683ff584dfd0ffc61cb023f2ece
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91968159"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>Azure Dev Spaces のためにプロジェクトを準備する方法

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces には、Kubernetes アプリケーションを迅速に反復処理してデバッグし、Azure Kubernetes Service (AKS) クラスターでチームと共同作業を行うための複数の方法が用意されています。 Dev Spaces では、プロジェクトの Dockerfile と Helm チャートを生成できます。 また、Dev Spaces では、AKS で Kubernetes アプリケーションをデプロイ、実行、およびデバッグするための構成ファイルが作成されて使用されます。 これらのファイルはすべて、アプリケーションのコードと共に存在し、バージョン コントロール システムに追加することができます。

この記事では、Dev Spaces を使用して AKS で実行するためにプロジェクトを準備する方法について説明します。

## <a name="prepare-your-code"></a>コードを準備する

開発スペースでアプリケーションを実行するには、アプリケーションをコンテナー化する必要があります。また、アプリケーションを Kubernetes にデプロイする方法を定義する必要があります。 アプリケーションをコンテナー化するには、Dockerfile が必要です。 アプリケーションを Kubernetes にデプロイする方法を定義するには、[Helm チャート](https://docs.helm.sh/)が必要です。 アプリケーション用の Dockerfile と Helm チャートの作成を支援するため、クライアント側ツールには `prep` コマンドが用意されています。

```cmd
azds prep --enable-ingress
```

`prep` コマンドは、プロジェクト内のファイルを確認して、アプリケーションを Kubernetes で実行するための Dockerfile と Helm チャートの作成を試行します。 現時点では、`prep` コマンドは次の言語で Dockerfile と Helm チャートを生成します。

* Java
* Node.js
* .NET Core

ソース コードを含むディレクトリから `prep` コマンドを実行する "*必要があります*"。 正しいディレクトリから `prep` コマンドを実行すると、クライアント側ツールで言語を識別し、アプリケーションのコンテナー化に適した Dockerfile を作成できます。 Java プロジェクト用の *pom.xml* ファイルを含むディレクトリから `prep` コマンドを実行することもできます。

ソース コードが含まれないディレクトリから `prep` コマンドを実行すると、クライアント側ツールでは Dockerfile が生成されません。 また、"*Dockerfile could not be generated due to unsupported language (サポートされていない言語が原因で Dockerfile を生成できませんでした)* " というエラーが表示されます。 このエラーは、クライアント側ツールでプロジェクト タイプが認識されない場合にも発生します。

`prep` コマンドの実行時には、`--enable-ingress` フラグを指定することができます。 このフラグは、インターネットにアクセス可能なエンドポイントをこのサービス用に作成するようコントローラーに指示します。 このフラグを指定しない場合は、クラスター内から、またはクライアント側ツールで作成された localhost トンネルを使用してサービスにアクセスする必要があります。 生成された Helm チャートを更新することで、`prep` コマンドの実行後にこの動作を有効または無効にすることができます。

`prep` コマンドでは、プロジェクト内の既存の Dockerfile または Helm チャートが置き換えられません。 既存の Dockerfile または Helm チャートにおいて `prep` コマンドで生成されたファイルと同じ名前付け規則が使用される場合、`prep` コマンドはこれらのファイルの生成をスキップします。 それ以外の場合、`prep` コマンドは独自の Dockerfile または Helm チャートを既存のファイルと並行して生成します。

> [!IMPORTANT]
> Azure Dev Spaces ではプロジェクトの Dockerfile と Helm チャートを使用してコードのビルドと実行が行われますが、プロジェクトのビルドおよび実行方法を変更する場合は、これらのファイルを変更することができます。

また、`prep` コマンドではプロジェクトのルートに `azds.yaml` ファイルも生成されます。 Azure Dev Spaces では、このファイルを使用してアプリケーションをビルド、インストール、構成、および実行します。 この構成ファイルでは、Dockerfile と Helm チャートの場所が示されており、それらの成果物に加えて追加の構成も提供されます。

[.NET Core サンプル アプリケーション](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend)を使用して作成する azds.yaml ファイルの例を次に示します。

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
        # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
        # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
        # For more information see https://aka.ms/devspaces/routing
        - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
```

`prep` コマンドで生成される `azds.yaml` ファイルは、シンプルな単一のプロジェクト開発シナリオに適しています。 特定のプロジェクトの複雑さが増大した場合は、`prep` コマンドの実行後にこのファイルの更新が必要になる可能性があります。 たとえば、プロジェクトでは、開発やデバッグのニーズに基づいて、ビルドや起動のプロセスを変更することが必要になる場合があります。 また、複数のビルド プロセスまたは別のビルド コンテンツが必要な複数のアプリケーションがプロジェクトに存在する場合などです。

## <a name="next-steps"></a>次のステップ

開発スペースでコードを実行する方法について詳しくは、「[Azure Dev Spaces でコードを実行する方法][how-it-works-up]」をご覧ください。

[how-it-works-up]: how-dev-spaces-works-up.md