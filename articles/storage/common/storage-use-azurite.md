---
title: Azure Storage の開発とテストに Azurite オープンソース エミュレーターを使用する (プレビュー)
description: Azurite オープンソース エミュレーター (プレビュー) では、Azure Storage アプリケーションをテストするための無料のローカル環境が提供されます。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: e611afd6f10154636eb2e0dd08437b4f7468d6b3
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309525"
---
# <a name="use-the-azurite-open-source-emulator-for-azure-storage-development-and-testing-preview"></a>Azure Storage の開発とテストに Azurite オープンソース エミュレーターを使用する (プレビュー)

Azurite バージョン 3.2 オープンソース エミュレーター (プレビュー) では、Azure Blob Storage アプリケーションおよび Azure Queue storage アプリケーションをテストするための無料のローカル環境が提供されます。 ローカルでのアプリケーションの動作に満足できたら、クラウドでの Azure Storage アカウントの使用に切り替えることができます。 エミュレーターは、Windows、Linux、および MacOS でのクロスプラットフォーム サポートを提供します。 Azurite v3 は、Azure Blob サービスによって実装された API をサポートします。

Azurite は今後のストレージ エミュレーター プラットフォームです。 Azurite は [Azure ストレージ エミュレーター](storage-use-emulator.md)よりも優先されます。 Azurite は、最新バージョンの Azure Storage API をサポートするために引き続き更新されます。

Azurite をローカル システムにインストールして実行するには、次のようないくつかの方法があります。

  1. [Azurite Visual Studio Code の拡張機能をインストールして実行する](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [NPM を使用して Azurite をインストールして実行する](#install-and-run-azurite-by-using-npm)
  1. [Azurite Docker イメージをインストールして実行する](#install-and-run-the-azurite-docker-image)
  1. [GitHub リポジトリから Azurite を複製、ビルド、および実行する](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Azurite Visual Studio Code の拡張機能をインストールして実行する

Visual Studio Code で、 **[EXTENSIONS]** ウィンドウを選択し、 **[EXTENSIONS:MARKETPLACE]** で *Azurite* を検索します。

![Visual Studio Code の拡張機能のマーケットプレース](media/storage-use-azurite/azurite-vs-code-extension.png)

または、ブラウザーで [VS Code 拡張機能のマーケット](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite)に移動します。 **[Install]\(インストール\)** を選択して Visual Studio Code を開き、Azurite 拡張機能ページに直接アクセスします。

Azurite をすばやく開始または終了するには、VS Code ステータスバーで **[Azurite Blob Service]** または **[Azurite Queue Service]** をクリックするか、VS Code コマンド パレットで次のコマンドを発行します。 コマンド パレットを開くには、VS Code で **F1** キーを押します。

拡張機能では、次の Visual Studio Code コマンドがサポートされています。

   * **Azurite:Start** - すべての Azurite サービスを開始します
   * **Azurite:Close** - すべての Azurite サービスを閉じます
   * **Azurite:Clean** - すべての Azurite サービス永続性データをリセットします
   * **Azurite:Start Blob Service** - Blob Service を開始します
   * **Azurite:Close Blob Service** - Blob Service を閉じます
   * **Azurite:Clean Blob Service** - Blob Service をクリーンアップします
   * **Azurite:Start Queue Service** - Queue service を開始します
   * **Azurite:Close Queue Service** - Queue service を閉じます
   * **Azurite:Clean Queue Service** - Queue service をクリーンアップします

Visual Studio Code 内で Azurite を構成するには、拡張機能ウィンドウを選択します。 **Azurite** の **[管理]** (歯車) アイコンを選択します。 **[Configure Extension Settings]\(拡張機能の設定の構成\)** を選択します。

![Azurite の拡張機能の設定の構成](media/storage-use-azurite/azurite-configure-extension-settings.png)

次の設定がサポートされています。

   * **Azurite:Blob Host** - Blob service のリッスン エンドポイント。 既定の設定は 127.0.0.1 です。
   * **Azurite:Blob Port** - Blob service のリスニング ポート。 既定のポートは 10000 です。
   * **Azurite:Debug** - Azurite チャネルにデバッグ ログを出力します。 既定値は **false** です。
   * **Azurite:Location** - ワークスペースの場所のパス。 既定値は Visual Studio Code の作業フォルダーです。
   * **Azurite:Queue Host** - Queue サービスのリッスン エンドポイント。 既定の設定は 127.0.0.1 です。
   * **Azurite:Queue Port** - Queue サービスのリッスン ポート。 既定のポートは 10001 です。
   * **Azurite:Silent** - サイレント モードではアクセス ログが無効になります。 既定値は **false** です。

## <a name="install-and-run-azurite-by-using-npm"></a>NPM を使用して Azurite をインストールして実行する

このインストール方法では、[Node.js バージョン8.0 以降](https://nodejs.org)がインストールされている必要があります。 **npm** は、Node.js のすべてのインストールに含まれるパッケージ管理ツールです。 Node.js をインストールした後、次の **npm** コマンドを実行して Azurite をインストールします。

```console
npm install -g azurite
```

Azurite をインストールしたら、「[Azurite をコマンドラインから実行する](#run-azurite-from-a-command-line)」を参照してください。

## <a name="install-and-run-the-azurite-docker-image"></a>Azurite Docker イメージをインストールして実行する

[DockerHub](https://hub.docker.com/) を使用して、次のコマンドを使用することによって[最新の Azurite イメージ](https://hub.docker.com/_/microsoft-azure-storage-azurite)をプルします。

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Azurite Docker イメージを実行する**:

次のコマンドは Azurite Docker を実行します。 `-p 10000:10000` パラメーターにより、ホスト マシンのポート 10000 から Docker インスタンスに要求がリダイレクトされます。

```console
docker run -p 10000:10000 -p 10001:10001 mcr.microsoft.com/azure-storage/azurite
```

**ワークスペースの場所を指定する**:

次の例で、`-v c:/azurite:/data` パラメーターでは Azurite の永続的なデータの場所として *c:/azurite* が指定されています。 Docker コマンドを実行する前に、*c:/azurite* ディレクトリを作成しておく必要があります。

```console
docker run -p 10000:10000 -p 10001:10001 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Blob Service だけを実行する**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

**すべての Azurite パラメーターを設定する**:

この例では、すべてのコマンドライン パラメーターを設定する方法を示します。 以下のすべてのパラメーターは、1 つのコマンドラインで指定する必要があります。

```console
docker run -p 8888:8888
           -p 9999:9999
           -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite
           -l /workspace
           -d /workspace/debug.log
           --blobPort 8888
           --blobHost 0.0.0.0
           --queuePort 9999
           --queueHost 0.0.0.0
```

起動時に Azurite を構成する方法の詳細については、「[コマンド ライン オプション](#command-line-options)」を参照してください。

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>GitHub リポジトリから Azurite を複製、ビルド、および実行する

このインストール方法では、[Git](https://git-scm.com/) がインストールされている必要があります。 次のコンソール コマンドを使用して、Azurite プロジェクト用の [GitHub リポジトリ](https://github.com/azure/azurite)を複製します。

```console
git clone https://github.com/Azure/Azurite.git
```

ソースコードを複製した後、複製されたリポジトリのルートから次のコマンドを実行して、Azurite をビルドおよびインストールします。

```console
npm install
npm run build
npm install -g
```

Azurite をインストールおよびビルドしたら、「[Azurite をコマンドラインから実行する](#run-azurite-from-a-command-line)」を参照してください。

## <a name="run-azurite-from-a-command-line"></a>Azurite をコマンドラインから実行する

> [!NOTE]
> Visual Studio Code 拡張機能のみをインストールした場合、Azurite をコマンドラインから実行することはできません。 代わりに、VS Code コマンド パレットを使用してください。 詳細については、「[Azurite Visual Studio Code の拡張機能をインストールして実行する](#install-and-run-the-azurite-visual-studio-code-extension)」を参照してください。

コマンドラインを使用してすぐに作業を開始するには、**c:\azurite** という名前のディレクトリを作成し、次のコマンドを発行して Azurite を起動します。

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

このコマンドは、すべてのデータを特定のディレクトリ **c:\azurite** に格納するように Azurite に指示します。 **--location** オプションを省略した場合は、現在の作業ディレクトリが使用されます。

## <a name="command-line-options"></a>コマンド ライン オプション

このセクションでは、Azurite を起動するときに使用できるコマンドライン スイッチについて詳しく説明します。 すべてのコマンドライン スイッチは省略可能です。

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>] 
    [-d | --debug <log file path>] [-l | --location <workspace path>]
    [--queueHost <IP address>] [--queuePort <port address>]
    [-s | --silent] [-h | --help]
```

**-d** は **--debug** のショートカット、 **-l** スイッチは **--location** のショートカット、 **-s** は **--silent** のショートカット、 **-h** は **--help** のショートカットです。

### <a name="blob-listening-host"></a>BLOB リスニング ホスト

**省略可能**: 既定では、Azurite はローカル サーバーとして 127.0.0.1 をリッスンします。 要件に合わせてアドレスを設定するには、 **--blobHost** スイッチを使用します。

ローカル コンピューターの要求のみを受け入れる:

```console
azurite --blobHost 127.0.0.1
```

リモート要求を許可する:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> リモート要求を許可すると、システムが外部からの攻撃に対して脆弱になる可能性があります。

### <a name="blob-listening-port-configuration"></a>BLOB リスニング ポートの構成

**省略可能**: 既定では、Azurite は Blob service をポート 10000 でリッスンします。 必要なリスニング ポートを指定するには、 **--blobPort** スイッチを使用します。

> [!NOTE]
> カスタマイズされたポートを使用した後、Azure Storage ツールまたは SDK で、接続文字列または対応する構成を更新する必要があります。

Blob service のリスニング ポートをカスタマイズする:

```console
azurite --blobPort 8888
```

使用可能なポートをシステムに自動選択させる:

```console
azurite --blobPort 0
```

使用中のポートは Azurite の起動時に表示されます。

### <a name="queue-listening-host"></a>キュー リスニング ホスト

**省略可能**: 既定では、Azurite はローカル サーバーとして 127.0.0.1 をリッスンします。 要件に合わせてアドレスを設定するには、 **--queueHost** スイッチを使用します。

ローカル コンピューターの要求のみを受け入れる:

```console
azurite --queueHost 127.0.0.1
```

リモート要求を許可する:

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> リモート要求を許可すると、システムが外部からの攻撃に対して脆弱になる可能性があります。

### <a name="queue-listening-port-configuration"></a>キュー リスニング ポートの構成

**省略可能**: Azurite の既定では、ポート 10001 で Queue サービスがリッスンされます。 必要なリスニング ポートを指定するには、 **--queuePort** スイッチを使用します。

> [!NOTE]
> カスタマイズされたポートを使用した後、Azure Storage ツールまたは SDK で、接続文字列または対応する構成を更新する必要があります。

Queue サービスのリスニング ポートをカスタマイズします。

```console
azurite --queuePort 8888
```

使用可能なポートをシステムに自動選択させる:

```console
azurite --queuePort 0
```

使用中のポートは Azurite の起動時に表示されます。

### <a name="workspace-path"></a>ワークスペース パス

**省略可能**: Azurite は、実行中にデータをローカル ディスクに格納します。 ワークスペースの場所としてパスを指定するには、 **--location** スイッチを使用します。 既定では、現在のプロセスの作業ディレクトリが使用されます。

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>アクセス ログ

**省略可能**: 既定では、アクセス ログはコンソール ウィンドウに表示されます。 **--silent** スイッチを使用すると、アクセス ログの表示が無効になります。

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>デバッグ ログ

**省略可能**: デバッグ ログにはすべての要求と例外スタック トレースに関する詳細情報が含まれます。 **--debug** スイッチに有効なローカル ファイル パスを指定すると、デバッグ ログが有効になります。

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

## <a name="authorization-for-tools-and-sdks"></a>ツールと SDK の認証

任意の認証方法を使用して、Azure Storage の SDK またはツール ([Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) など) から Azurite に接続します。 認証が必要です。 Azurite は、共有キーと Shared Access Signature (SAS) を使用した認証をサポートしています。 Azurite ではパブリック コンテナーへの匿名アクセスもサポートされています。

### <a name="well-known-storage-account-and-key"></a>既知のストレージ アカウントとキー

Azurite では次のアカウント名とキーを使用できます。 これは従来の Azure Storage エミュレーターで使用されるものと同じ既知のアカウントとキーです。

* アカウント名: `devstoreaccount1`
* アカウント キー: `Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> SharedKey 認証に加えて、Azurite ではアカウントとサービスの SAS 認証がサポートされています。 コンテナーがパブリック アクセスを許可するように設定されている場合は匿名アクセスも使用できます。

### <a name="connection-string"></a>接続文字列

アプリケーションから Azurite に接続する最も簡単な方法は、ショートカット *UseDevelopmentStorage=true* を参照するアプリケーションの構成ファイル内で接続文字列を構成することです。 たとえば、*app.config* ファイル内の接続文字列は次のようになります。

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

詳細については、「[Azure Storage の接続文字列を構成する](storage-configure-connection-string.md)」を参照してください。

### <a name="storage-explorer"></a>ストレージ エクスプローラー

Azure Storage Explorer では、 **[アカウントの追加]** アイコンをクリックし、 **[Attach to a local emulator]\(ローカル エミュレーターにアタッチ\)** を選択して **[接続]** をクリックすることによって Azurite に接続します。

## <a name="differences-between-azurite-and-azure-storage"></a>Azurite と Azure Storage の違い

Azurite のローカル インスタンスとクラウドの Azure Storage アカウントには機能上の違いがあります。

### <a name="endpoint-and-connection-url"></a>エンドポイントおよび接続 URL

Azurite のサービス エンドポイントは、Azure Storage アカウントのエンドポイントとは異なります。 ローカル コンピューターはドメイン名の解決を行わず、Azurite エンドポイントをローカル アドレスにする必要があります。

Azure Storage アカウントのリソースをアドレス指定する場合、アカウント名は URI ホスト名の一部になります。 アドレス指定されるリソースは、URI パスの一部です。

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

以下の URI は、Azure Storage アカウント内の BLOB の有効なアドレスです。

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

ローカル コンピューターがドメイン名の解決を実行しないため、アカウント名は、ホスト名ではなく URI パスの一部になります。 Azurite のリソースには、次の URI 形式を使用します。

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Azurite の BLOB にアクセスするには次のアドレスを使用できます。

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>スケーリングとパフォーマンス

Azurite はスケーラブルなストレージ サービスではなく、多数の同時クライアントはサポートされません。 パフォーマンスの保証はありません。 Azurite は開発とテストを目的としています。

### <a name="error-handling"></a>エラー処理

Azurite は Azure Storage エラー処理ロジックに合わせて調整されていますが、違いがあります。 たとえば、エラー メッセージは異なる場合がありますが、エラー状態コードは調整されています。

### <a name="ra-grs"></a>RA-GRS

Azurite は、読み取りアクセス geo 冗長レプリケーション (RA-GRS) をサポートしています。 ストレージ リソースの場合、2 次拠点にアクセスするにはアカウント名に **-secondary** を付加します。 たとえば、Azurite で読み取り専用の 2 次拠点を使用して BLOB にアクセスするには、次のアドレスを使用します。

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite はオープンソースです

Azurite への参加や提案をお待ちしています。 近日公開される機能やバグ修正のために追跡しているマイルストーンと作業項目については、Azurite の [GitHub プロジェクト](https://github.com/Azure/Azurite/projects) ページまたは [GitHub の課題](https://github.com/Azure/Azurite/issues)を参照してください。 GitHub では詳細な作業項目も追跡されています。

## <a name="next-steps"></a>次の手順

* 「[Azure ストレージ エミュレーターを使用した開発とテスト](storage-use-emulator.md)」には、Azurite によって置き換えられつつある従来の Azure ストレージ エミュレーターについて記載されています。
* 「[Azure Storage の接続文字列を構成する](storage-configure-connection-string.md)」では、有効な Azure Storage の接続文字列をアセンブルする方法が説明されています。
