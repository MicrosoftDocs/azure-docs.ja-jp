---
title: Azure Dev Spaces でのコード実行のしくみ
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Azure Dev Spaces による Azure Kubernetes Service でのコード実行のプロセスについて説明します
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
ms.openlocfilehash: 1cace325f9415d46210636e5c04cc2d75589cc11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014433"
---
# <a name="how-running-your-code-with-azure-dev-spaces-works"></a>Azure Dev Spaces でのコード実行のしくみ

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces には、Kubernetes アプリケーションを迅速に反復処理してデバッグし、Azure Kubernetes Service (AKS) クラスターでチームと共同作業を行うための複数の方法が用意されています。 [プロジェクトを開発スペースで実行する準備が整ったら][how-it-works-prep]、Dev Spaces を使用してプロジェクトをビルドし、AKS クラスターで実行することができます。

この記事では、Dev Spaces を使用した AKS でのコード実行について説明します。

## <a name="run-your-code"></a>コードを実行する

開発スペースでコードを実行するには、`azds.yaml` ファイルと同じディレクトリで `up` コマンドを実行します。

```cmd
azds up
```

`up` コマンドでは、プロジェクトのビルドと実行に必要なアプリケーションのソース ファイルとその他の成果物が開発スペースにアップロードされます。 開発スペース内のコントローラーは次の処理を実行します。

1. アプリケーションにデプロイする Kubernetes オブジェクトを作成する。
1. アプリケーション用のコンテナーをビルドする。
1. アプリケーションを開発スペースにデプロイする。
1. アプリケーション エンドポイント (構成済みの場合) 用に、パブリックにアクセス可能な DNS 名を作成する。
1. *port-forward* を使用して、 http://localhost によるアプリケーション エンドポイントへのアクセスを提供する。
1. stdout と stderr をクライアント側ツールに転送する。


## <a name="starting-a-service"></a>サービスを開始する

開発スペースでサービスを開始する場合は、クライアント側ツールとコントローラーが連携して、ソース ファイルを同期し、コンテナーと Kubernetes オブジェクトを作成し、アプリケーションを実行します。

より詳細なレベルでは、`azds up` の実行時には次の処理が行われます。

1. ユーザーのコンピューターから、ユーザーの AKS クラスターに固有の Azure ファイル ストレージに[ファイルが同期][sync-section]されます。 ソース コード、Helm チャート、および構成ファイルがアップロードされます。
1. 新しいセッションを開始する要求がコントローラーによって作成されます。 この要求には、一意の ID、スペース名、ソース コードのパス、デバッグ フラグなどの複数のプロパティが含まれます。
1. コントローラーによって、Helm チャート内の *$(tag)* プレースホルダーが一意のセッション ID に置き換えられ、サービス用の Helm チャートがインストールされます。 一意のセッション ID への参照を Helm チャートに追加すると、この特定のセッション用に AKS クラスターにデプロイされたコンテナーをセッション要求と関連情報に再び関連付けることができます。
1. Helm チャートのインストール時には、Kubernetes webhook 受付サーバーによって、インストルメンテーションおよびプロジェクトのソース コードにアクセスするためのコンテナーがアプリケーションのポッドに追加されます。 HTTP トレースとスペースのルーティングを提供するために、devspaces-proxy コンテナーと devspaces-proxy-init コンテナーが追加されます。 アプリケーションのコンテナーのビルドに必要な Docker インスタンスとプロジェクトのソース コードへのアクセスをポッドに提供するために、devspaces-build コンテナーが追加されます。
1. アプリケーションのポッドが開始されると、devspaces-build コンテナーと devspaces-proxy-init コンテナーを使用してアプリケーション コンテナーがビルドされます。 次に、アプリケーション コンテナーと devspaces-proxy コンテナーが開始されます。
1. アプリケーション コンテナーの開始後、クライアント側の機能で Kubernetes の *port-forward* 機能が使用され、 http://localhost を経由してアプリケーションへの HTTP アクセスが提供されます。 このポート フォワーディングでは、開発スペース内のサービスに開発用コンピューターを接続します。
1. ポッド内のすべてのコンテナーが開始されると、サービスが実行されています。 この時点で、クライアント側の機能は HTTP トレース、stdout、および stderr のストリーミングを開始します。 この情報は、クライアント側の機能によって開発者向けに表示されます。

## <a name="updating-a-running-service"></a>実行中のサービスを更新する

Azure Dev Spaces では、いずれかのプロジェクトのソース コードが変更された場合に、実行中のサービスを更新することができます。 また、Dev Spaces では、変更されたファイルの種類に応じてサービスの更新をさまざまな方法で処理します。 Dev Spaces では、次に示す 3 つの方法で実行中のサービスを更新できます。

* ファイルを直接更新する
* 実行中のアプリケーションのコンテナー内でアプリケーションのプロセスをリビルドして再起動する
* アプリケーションのコンテナーをリビルドして再デプロイする

![Azure Dev Spaces のファイル同期](media/how-dev-spaces-works/file-sync.svg)

静的な資産である特定のプロジェクト ファイル (html、css、cshtml ファイルなど) をアプリケーションのコンテナー内で直接更新できます。何かを再起動する必要はありません。 静的な資産が変更されると、新しいファイルが開発スペースに同期され、実行中のコンテナーで使用されます。

ソース コードやアプリケーションの構成ファイルなどに対する変更は、実行中のコンテナー内でアプリケーションのプロセスを再起動すると適用されます。 これらのファイルが同期されると、*devhostagent* プロセスを使用して、実行中のコンテナー内でアプリケーションのプロセスが再起動されます。 アプリケーションのコンテナーを最初に作成する際は、コントローラーによって、アプリケーションのスタートアップ コマンドが別のプロセス (*devhostagent*) に置き換えられます。 続いて、アプリケーションの実際のプロセスが子プロセスとして *devhostagent* で実行され、その出力が *devhostagent* の出力を使用して行われます。 また、*devhostagent* プロセスは Dev Spaces の一部であり、Dev Spaces の代わりに実行中のコンテナーでコマンドを実行できます。 再起動の実行時には、*devhostagent* によって次の処理が行われます。

* アプリケーションに関連付けられている現在のプロセスを停止する
* アプリケーションをリビルドする
* アプリケーションに関連付けられているプロセスを再起動する

*devhostagent* による先行手順の実行方法は [`azds.yaml` で構成][azds-yaml-section]されます。

プロジェクト ファイル (Dockerfile、csproj ファイル、Helm チャートの一部など) に対する更新を行うには、アプリケーションのコンテナーをリビルドおよび再デプロイする必要があります。 これらのいずれかのファイルが開発スペースに同期されると、コントローラーによって [helm upgrade][helm-upgrade] コマンドが実行され、アプリケーションのコンテナーがリビルドおよび再デプロイされます。

## <a name="file-synchronization"></a>ファイルの同期

開発スペースで初めてアプリケーションを起動するときには、アプリケーションのソース ファイルがすべてアップロードされます。 アプリケーションの実行中および以降の再起動の際は、変更されたファイルのみアップロードされます。 このプロセスを調整するために 2 つのファイル (クライアント側ファイルとコントローラー側ファイル) が使用されます。

クライアント側ファイルは一時ディレクトリに格納され、開発スペースで実行中のプロジェクト ディレクトリのハッシュに基づいて命名されます。 たとえば、Windows におけるプロジェクト用のファイルは *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* のようになります。 Linux では、クライアント側ファイルは */tmp* ディレクトリに格納されます。 `echo $TMPDIR` コマンドを実行すると、macOS でこのディレクトリを探すことができます。

このファイルは JSON 形式であり、次のものが含まれています。

* 開発スペースと同期される各プロジェクト ファイルのエントリ
* 同期 ID
* 最後の同期操作のタイムスタンプ

プロジェクト ファイルの各エントリには、ファイルのパスとファイルのタイムスタンプが含まれています。

コントローラー側ファイルは、AKS クラスターに格納されます。 このファイルには、同期 ID と最後の同期のタイムスタンプが含まれています。

同期は、クライアント側ファイルとコントローラー側ファイルの間で同期のタイムスタンプが一致しない場合に発生します。 同期の際は、クライアント側ツールによってクライアント側ファイル内のファイル エントリが反復処理されます。 ファイルのタイムスタンプが同期のタイムスタンプより後である場合は、そのファイルが開発スペースに同期されます。 同期が完了すると、クライアント側とコントローラー側の両方のファイルで同期のタイムスタンプが更新されます。

クライアント側ファイルが存在しない場合は、すべてのプロジェクト ファイルが同期されます。 この動作では、クライアント側ファイルを削除することによって完全同期を適用できます。

## <a name="how-running-your-code-is-configured"></a>コード実行の構成方法

Azure Dev Spaces では、`azds.yaml` ファイルを使用してサービスをインストールおよび構成します。 コントローラーでは、`azds.yaml` ファイル内の `install` プロパティを使用して、Helm チャートをインストールし、Kubernetes オブジェクトを作成します。

```yaml
...
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
...
```

既定では、Helm チャートは `prep` コマンドによって生成されます。 また、*install.chart* プロパティは Helm チャートのディレクトリに設定されます。 Helm チャートを別の場所で使用する場合は、その場所を使用するようにこのプロパティを更新できます。

Helm チャートのインストール時には、Helm チャート内の値をオーバーライドする方法が Azure Dev Spaces から提供されます。 Helm チャートの既定値は `charts/APP_NAME/values.yaml` に含まれています。

*install.values* プロパティを使用すると、Helm チャートで置き換える値を定義する 1 つ以上のファイルを表示できます。 たとえば、開発スペースでアプリケーションを実行する場合にホスト名またはデータベース構成が必要な場合は、このオーバーライド機能を使用できます。 また、 *?* を ファイル名の末尾に追加して、オプションとして設定することもできます。

*install.set* プロパティを使用すると、Helm チャートで置き換える 1 つ以上の値を構成できます。 *install.set* で構成された値によって、*install.values* に表示されているファイルで構成された値がオーバーライドされます。 *install.set* に属するプロパティは、Helm チャート内の値に依存し、生成される Helm チャートによって異なる場合があります。

上記の例では、*install.set.replicaCount* プロパティは、開発スペースで実行するアプリケーションのインスタンス数をコントローラーに通知します。 シナリオによっては、この値を増やすことができますが、これはアプリケーションのポッドへのデバッガーのアタッチに影響を及ぼします。 詳細については、[トラブルシューティングの記事][troubleshooting]を参照してください。

生成される Helm チャートでは、コンテナー イメージが *{{ .Values.image.repository }}:{{ .Values.image.tag }}* に設定されます。 `azds.yaml` ファイルでは、*install.set.image.tag* プロパティが既定で *$(tag)* として定義されます。これは、 *{{ .Values.image.tag }}* の値として使用されます。 この方法で *install.set.image.tag* プロパティを設定すると、Azure Dev Spaces の実行時に独自の方法でアプリケーションのコンテナー イメージにタグ付けできます。 この特定のケースでは、イメージに *\<value from image.repository>:$(tag)* というタグが付けられます。 Dev Spaces が AKS クラスター内のコンテナーを認識して特定できるようにするには、 *$(tag)* 変数を *install.set.image.tag* の値として使用する必要があります。

上記の例では、`azds.yaml` によって *install.set.ingress.hosts* が定義されます。 *install.set.ingress.hosts* プロパティでは、パブリック エンドポイントのホスト名の形式が定義されます。 また、このプロパティでは *$(spacePrefix)* 、 *$(rootSpacePrefix)* 、および *$(hostSuffix)* を使用します。これらはコントローラーから提供される値です。

*$(spacePrefix)* は子開発スペースの名前であり、*SPACENAME.s* という形式になります。 *$(RootSpacePrefix)* は親スペースの名前です。 たとえば、*azureuser* が *default* の子スペースである場合、 *$(rootSpacePrefix)* の値は *default*、 *$(spacePrefix)* の値は *azureuser.s* になります。 スペースが子スペースでない場合、 *$(spacePrefix)* は空白になります。 たとえば、*default* スペースに親スペースがない場合、 *$(rootSpacePrefix)* の値は *default*、 *$(spacePrefix)* の値は空白になります。 *$(hostSuffix)* は、AKS クラスターで実行する Azure Dev Spaces イングレス コントローラーを指す DNS サフィックスです。 この DNS サフィックスは、Azure Dev Spaces コントローラーが AKS クラスターに追加されたときに作成されたワイルドカードの DNS エントリ (たとえば、 *\*.RANDOM_VALUE.eus.azds.io*) に対応しています。

上記の `azds.yaml` ファイルでは、アプリケーションのホスト名を変更するように *install.set.ingress.hosts* を更新することも可能でした。 たとえば、アプリケーションのホスト名を *$(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)* から *$(spacePrefix)$(rootSpacePrefix)web$(hostSuffix)* に簡略化する場合などです。

アプリケーションのコンテナーをビルドするために、コントローラーでは、`azds.yaml` 構成ファイルの以下のセクションを使用します。

```yaml
build:
  context: .
  dockerfile: Dockerfile
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
...
```

コントローラーでは、Dockerfile を使用してアプリケーションをビルドおよび実行します。

*build.context* プロパティでは、Dockerfile が存在するディレクトリを表示します。 *build.dockerfile* プロパティでは、運用バージョンのアプリケーションをビルドするための Dockerfile の名前を定義します。 *configurations.develop.build.dockerfile* プロパティでは、開発バージョンのアプリケーション用の Dockerfile の名前を構成します。

開発と運用に異なる Dockerfile を使用すると、開発時に特定の項目を有効にする一方で、運用環境ではそれらの項目を無効にすることができます。 たとえば、デバッグや詳細なログを開発時には有効にして、運用環境では無効にすることができます。 また、Dockerfile の名前が異なる場合や、Dockerfile が別の場所にある場合にこれらのプロパティを更新することもできます。

開発時に迅速に反復処理を行えるようにするために、Azure Dev Spaces ではローカル プロジェクトから変更を同期して、アプリケーションを増分更新します。 `azds.yaml` 構成ファイルの以下のセクションを使用して、同期と更新を構成します。

```yaml
...
configurations:
  develop:
    ...
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
...
```

変更を同期するファイルとディレクトリは *configurations.develop.container.sync* プロパティで示されます。 これらのディレクトリは、`up` コマンドの実行時および変更が検出されたときに最初に同期されます。 開発スペースと同期させる追加のディレクトリまたは別のディレクトリがある場合は、このプロパティを変更できます。

*configurations.develop.container.iterate.buildCommands* プロパティでは、開発シナリオでアプリケーションをビルドする方法を指定します。 *configurations.develop.container.command* プロパティでは、開発シナリオでアプリケーションを実行するためのコマンドを提供します。 開発時に使用するビルドまたは実行時の追加のフラグやパラメーターがある場合は、これらのプロパティのどちらかの更新が必要になることがあります。

*configurations.develop.container.iterate.processesToKill* では、アプリケーションを停止するために中止するプロセスを表示します。 開発時にアプリケーションの再起動の動作を変更する場合は、このプロパティの更新が必要になることがあります。 たとえば、アプリケーションのビルドや起動の方法を変更するように *configurations.develop.container.iterate.buildCommands* プロパティまたは *configurations.develop.container.command* プロパティを更新した場合は、停止するプロセスの変更が必要になることがあります。

`azds prep` コマンドを使用してコードを準備する場合は、`--enable-ingress` フラグを追加することができます。 `--enable-ingress` フラグを追加すると、アプリケーションのパブリックにアクセス可能な URL が作成されます。 このフラグを省略した場合は、クラスター内から、または localhost トンネルを使用してアプリケーションにアクセスする必要があります。 `azds prep` コマンドの実行後、`charts/APPNAME/values.yaml` で *ingress.enabled* プロパティを変更して、この設定を変更することができます。

```yaml
ingress:
  enabled: true
```

## <a name="next-steps"></a>次のステップ

Azure Dev Spaces におけるネットワークおよび要求のルーティング方法について詳しくは、「[Azure Dev Spaces でルーティングがどのように機能するか][how-it-works-routing]」を参照してください。

Kubernetes で短時間で繰り返しと開発を行う方法については、「[Bridge to Kubernetes のしくみ][how-it-works-bridge-to-kubernetes]」と「[Azure Dev Spaces でのコードのリモート デバッグのしくみ][how-it-works-remote-debugging]」を参照してください。


[azds-yaml-section]: #how-running-your-code-is-configured
[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[sync-section]: #file-synchronization
[troubleshooting]: troubleshooting.md