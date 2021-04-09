---
title: ローカルでの Azure Storage の開発に Azurite エミュレーターを使用する
description: Azurite オープンソース エミュレーターでは、Azure Storage アプリケーションをテストするための無料のローカル環境が提供されます。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/15/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: b5051a5e29f9e34c43963a9a264ee47e2ad7b04a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92490783"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development"></a>ローカルでの Azure Storage の開発に Azurite エミュレーターを使用する

Azurite オープンソース エミュレーターには、Azure Blob およびキュー ストレージ アプリケーションをテストするための無料のローカル環境が用意されています。 ローカルでのアプリケーションの動作に満足できたら、クラウドでの Azure Storage アカウントの使用に切り替えることができます。 エミュレーターは、Windows、Linux、および macOS でのクロスプラットフォーム サポートを提供します。

Azurite は今後のストレージ エミュレーター プラットフォームです。 Azurite は [Azure Storage Emulator](storage-use-emulator.md)よりも優先されます。 Azurite は、最新バージョンの Azure Storage API をサポートするために引き続き更新されます。

Azurite をローカル システムにインストールして実行するには、次のようないくつかの方法があります。

  1. [Azurite Visual Studio Code の拡張機能をインストールして実行する](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [NPM を使用して Azurite をインストールして実行する](#install-and-run-azurite-by-using-npm)
  1. [Azurite Docker イメージをインストールして実行する](#install-and-run-the-azurite-docker-image)
  1. [GitHub リポジトリから Azurite を複製、ビルド、および実行する](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Azurite Visual Studio Code の拡張機能をインストールして実行する

Visual Studio Code で、 **[EXTENSIONS]** ウィンドウを選択し、 **[EXTENSIONS:MARKETPLACE]** で *Azurite* を検索します。

![Visual Studio Code の拡張機能のマーケットプレース](media/storage-use-azurite/azurite-vs-code-extension.png)

また、ブラウザーで [Visual Studio Code 拡張機能のマーケット](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite)に移動することもできます。 **[Install]\(インストール\)** を選択して Visual Studio Code を開き、Azurite 拡張機能ページに直接アクセスします。

拡張機能では、次の Visual Studio Code コマンドがサポートされています。 コマンド パレットを開くには、Visual Studio Code で F1 キーを押します。 

   - **Azurite:Clean** - すべての Azurite サービス永続性データをリセットします
   - **Azurite:Clean Blob Service** - Blob Service をクリーンアップします
   - **Azurite:Clean Queue Service** - Queue service をクリーンアップします
   - **Azurite:Close** - すべての Azurite サービスを閉じます
   - **Azurite:Close Blob Service** - Blob Service を閉じます
   - **Azurite:Close Queue Service** - Queue service を閉じます
   - **Azurite:Start** - すべての Azurite サービスを開始します
   - **Azurite:Start Blob Service** - Blob Service を開始します
   - **Azurite:Start Queue Service** - Queue service を開始します

Visual Studio Code 内で Azurite を構成するには、拡張機能ウィンドウを選択します。 **Azurite** の **[管理]** (歯車) アイコンを選択します。 **[拡張機能の設定]** を選択します。

![Azurite の拡張機能設定の構成](media/storage-use-azurite/azurite-configure-extension-settings.png)

次の設定がサポートされています。

   - **Azurite:Blob Host** - Blob service のリッスン エンドポイント。 既定の設定は 127.0.0.1 です。
   - **Azurite:Blob Port** - Blob service のリスニング ポート。 既定のポートは 10000 です。
   - **Azurite:Cert** - HTTPS モードを有効にするための、ローカルに信頼されている PEM または PFX 証明書ファイルへのパス。
   - **Azurite:Debug** - Azurite チャネルにデバッグ ログを出力します。 既定値は **false** です。
   - **Azurite:Key** - ローカルに信頼されている PEM キーファイルへのパス。**Azurite:Cert** が PEM ファイルを指す場合は必須です。
   - **Azurite:Location** - ワークスペースの場所のパス。 既定値は Visual Studio Code の作業フォルダーです。
   - **Azurite:Loose** - ルーズ モード (サポートされていないヘッダーとパラメーターを無視します) を有効にします。
   - **Azurite:OAuth** - 省略可能な OAuth レベル。
   - **Azurite:Pwd** - PFX ファイルのパスワード。 **Azurite:Cert** が PFX ファイルを指す場合は必須です。
   - **Azurite:Queue Host** - Queue サービスのリッスン エンドポイント。 既定の設定は 127.0.0.1 です。
   - **Azurite:Queue Port** - Queue サービスのリッスン ポート。 既定のポートは 10001 です。
   - **Azurite:Silent** - サイレント モードではアクセス ログが無効になります。 既定値は **false** です。
   - **Azurite:API バージョンの確認をスキップします** - 要求 API バージョン チェックをスキップします。 既定値は **false** です。

## <a name="install-and-run-azurite-by-using-npm"></a>NPM を使用して Azurite をインストールして実行する

このインストール方法では、[Node.js バージョン8.0 以降](https://nodejs.org)がインストールされている必要があります。 ノード パッケージ マネージャー (npm) は、Node.js のすべてのインストールに含まれるパッケージ管理ツールです。 Node.js をインストールした後、次の `npm` コマンドを実行して Azurite をインストールします。

```console
npm install -g azurite
```

Azurite をインストールしたら、「[Azurite をコマンド ラインから実行する](#run-azurite-from-a-command-line)」を参照してください。

## <a name="install-and-run-the-azurite-docker-image"></a>Azurite Docker イメージをインストールして実行する

[DockerHub](https://hub.docker.com/) を使用して、次のコマンドを使用することによって[最新の Azurite イメージ](https://hub.docker.com/_/microsoft-azure-storage-azurite)をプルします。

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Azurite Docker イメージを実行する**:

次のコマンドは Azurite Docker を実行します。 `-p 10000:10000` パラメーターにより、ホスト マシンのポート 10000 から Docker インスタンスに要求がリダイレクトされます。

```console
docker run -p 10000:10000 -p 10001:10001 \
    mcr.microsoft.com/azure-storage/azurite
```

**ワークスペースの場所を指定する**:

次の例で、`-v c:/azurite:/data` パラメーターでは Azurite の永続的なデータの場所として *c:/azurite* が指定されています。 Docker コマンドを実行する前に、*c:/azurite* ディレクトリを作成しておく必要があります。

```console
docker run -p 10000:10000 -p 10001:10001 \
    -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Blob Service だけを実行する**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite \
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
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

Azurite をインストールおよびビルドしたら、「[Azurite をコマンド ラインから実行する](#run-azurite-from-a-command-line)」を参照してください。

## <a name="run-azurite-from-a-command-line"></a>Azurite をコマンド ラインから実行する

> [!NOTE]
> Visual Studio Code 拡張機能のみをインストールした場合、Azurite をコマンドラインから実行することはできません。 代わりに、Visual Studio Code コマンド パレットを使用します。 詳細については、「[Azurite Visual Studio Code の拡張機能をインストールして実行する](#install-and-run-the-azurite-visual-studio-code-extension)」を参照してください。

コマンド ラインを使用してすぐに作業を開始するには、*c:\azurite* という名前のディレクトリを作成し、次のコマンドを発行して Azurite を起動します。

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

このコマンドは、すべてのデータを特定のディレクトリ *c:\azurite* に格納するように Azurite に指示します。 `--location` オプションを省略した場合は、現在の作業ディレクトリが使用されます。

## <a name="command-line-options"></a>コマンド ライン オプション

このセクションでは、Azurite を起動するときに使用できるコマンド ライン スイッチについて詳しく説明します。

### <a name="help"></a>Help

**オプション** - `-h` または `--help` スイッチを使用してコマンド ラインのヘルプを取得します。

```console
azurite -h
azurite --help
```

### <a name="blob-listening-host"></a>BLOB リスニング ホスト

**オプション** - 既定では、Azurite はローカル サーバーとして 127.0.0.1 をリッスンします。 要件に合わせてアドレスを設定するには、`--blobHost` スイッチを使用します。

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

**オプション** - 既定では、Azurite は Blob service をポート 10000 でリッスンします。 必要なリスニング ポートを指定するには、`--blobPort` スイッチを使用します。

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

**オプション** - 既定では、Azurite はローカル サーバーとして 127.0.0.1 をリッスンします。 要件に合わせてアドレスを設定するには、`--queueHost` スイッチを使用します。

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

**オプション** - 既定では、Azurite は Queue サービスをポート 10001 でリッスンします。 必要なリスニング ポートを指定するには、`--queuePort` スイッチを使用します。

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

**オプション** - Azurite は、実行中にデータをローカル ディスクに格納します。 ワークスペースの場所としてパスを指定するには、`-l` または `--location` スイッチを使用します。 既定では、現在のプロセスの作業ディレクトリが使用されます。 小文字の "l" であることに注意してください。

```console
azurite -l c:\azurite
azurite --location c:\azurite
```

### <a name="access-log"></a>アクセス ログ

**オプション** - 既定では、アクセス ログはコンソール ウィンドウに表示されます。 `-s` または `--silent` スイッチを使用すると、アクセス ログの表示が無効になります。

```console
azurite -s
azurite --silent
```
### <a name="debug-log"></a>デバッグ ログ

**オプション** - デバッグ ログにはすべての要求と例外スタック トレースに関する詳細情報が含まれます。 `-d` または `--debug` スイッチに有効なローカル ファイル パスを指定すると、デバッグ ログが有効になります。

```console
azurite -d path/debug.log
azurite --debug path/debug.log
```

### <a name="loose-mode"></a>ルーズ モード

**オプション** - Azurite では、既定で、サポートされていない要求ヘッダーとパラメーターをブロックするために厳格モードが適用されます。 `-L` または `--loose` スイッチを使用すると、厳格モードが無効になります。 大文字の "L" であることに注意してください。

```console
azurite -L
azurite --loose
```
### <a name="version"></a>Version

**オプション** - `-v` または `--version` スイッチを使用して、インストールされている Azurite のバージョン番号を表示します。

```console
azurite -v
azurite --version
```

### <a name="certificate-configuration-https"></a>証明書の構成 (HTTPS)

**オプション** - 既定では、Azurite は HTTP プロトコルを使用します。 HTTPS モードを有効にするには、Privacy Enhanced Mail (.pem) または [Personal Information Exchange (.pfx)](/windows-hardware/drivers/install/personal-information-exchange---pfx--files) 証明書ファイルへのパスを `--cert` スイッチに指定します。

PEM ファイルに `--cert` が指定されている場合、対応する `--key` スイッチを指定する必要があります。

```console
azurite --cert path/server.pem --key path/key.pem
```

PFX ファイルに `--cert` が指定されている場合、対応する `--pwd` スイッチを指定する必要があります。

```console
azurite --cert path/server.pfx --pwd pfxpassword
```

PEM および PFX ファイルの作成の詳細については、「[HTTPS セットアップ](https://github.com/Azure/Azurite/blob/master/README.md#https-setup)」を参照してください。

### <a name="oauth-configuration"></a>OAuth 構成

**オプション** - `--oauth` スイッチを使用して、Azurite の OAuth 認証を有効にします。

```console
azurite --oauth basic --cert path/server.pem --key path/key.pem
```

> [!NOTE]
> OAuth には HTTPS エンドポイントが必須です。 `--oauth` スイッチと共に `--cert` スイッチを指定することで、HTTPS を確実に有効にしてください。

Azurite は、`--oauth` スイッチに `basic` パラメーターを指定することによって基本認証をサポートします。 Azurite は、受信ベアラー トークンの検証や、発行者、対象ユーザー、有効期限の確認などの基本認証を行います。 Azurite は、トークンの署名およびアクセス許可を確認しません。

### <a name="skip-api-version-check"></a>API のバージョン チェックをスキップする

**省略可能** -起動時に、Azurite では、要求された API バージョンが有効であることが確認されます。 次のコマンドを使用すると、API のバージョン チェックがスキップされます。

```console
azurite --skipApiVersionCheck
```


## <a name="authorization-for-tools-and-sdks"></a>ツールと SDK の認証

任意の認証方法を使用して、Azure Storage の SDK またはツール ([Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) など) から Azurite に接続します。 認証が必要です。 Azurite は、OAuth、共有キー、および Shared Access Signature (SAS) を使用した認証をサポートしています。 Azurite ではパブリック コンテナーへの匿名アクセスもサポートされています。

Azure SDK を使用している場合は、`--oauth basic and --cert --key/--pwd` オプションを使用して Azurite を開始します。

### <a name="well-known-storage-account-and-key"></a>既知のストレージ アカウントとキー

Azurite は、従来の Azure Storage Emulator で使用されるものと同じ既知のアカウントとキーを受け入れます。

- アカウント名: `devstoreaccount1`
- アカウント キー: `Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

### <a name="custom-storage-accounts-and-keys"></a>カスタム ストレージ アカウントとキー

Azurite では、`AZURITE_ACCOUNTS` 環境変数を次の形式で設定することにより、カスタム ストレージ アカウント名とキーがサポートされます: `account1:key1[:key2];account2:key1[:key2];...`。

たとえば、1 つのキーを持つカスタム ストレージ アカウントを使用する場合は、次のように指定します。

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

```bash
export AZURITE_ACCOUNTS="account1:key1"
```

それぞれ 2 つのキーを持つ複数のストレージ アカウントを使用する場合は、次のように指定します。

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

```bash
export AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

Azurite では、既定で、1 分ごとにカスタム アカウント名とキーが環境変数から更新されます。 この機能により、アカウント キーを動的にローテーションしたり、Azurite を再起動することなく新しいストレージ アカウントを追加したりできます。

> [!NOTE]
> カスタム ストレージ アカウントを設定すると、既定の `devstoreaccount1` ストレージ アカウントは無効になります。

### <a name="connection-strings"></a>Connection strings

アプリケーションから Azurite に接続する最も簡単な方法は、ショートカット *UseDevelopmentStorage=true* を参照するアプリケーションの構成ファイル内で接続文字列を構成することです。 たとえば、*app.config* ファイル内の接続文字列は次のようになります。

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="http-connection-strings"></a>HTTP 接続文字列

次の接続文字列は、[Azure SDK](https://aka.ms/azsdk) またはツール (Azure CLI 2.0 や Storage Explorer など) に渡すことができます。

完全な接続文字列は次のとおりです。

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

Blob service にのみ接続する場合、接続文字列は次のようになります。

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;`

Queue サービスにのみ接続する場合、接続文字列は次のようになります。

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

#### <a name="https-connection-strings"></a>HTTPS 接続文字列

完全な HTTPS 接続文字列は次のとおりです。

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

Blob service のみを使用する場合、HTTPS 接続文字列は次のようになります。

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;`

Queue サービスのみを使用する場合、HTTPS 接続文字列は次のようになります。

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

`dotnet dev-certs` を使用して自己署名証明書を生成した場合は、次の接続文字列を使用します。

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://localhost:10000/devstoreaccount1;QueueEndpoint=https://localhost:10001/devstoreaccount1;`

[カスタム ストレージ アカウントとキー](#custom-storage-accounts-and-keys)を使用する場合は、接続文字列を更新します。

詳細については、「[Azure Storage の接続文字列を構成する](storage-configure-connection-string.md)」を参照してください。

### <a name="azure-sdks"></a>Azure SDK

[Azure SDK](https://aka.ms/azsdk) で Azurite を使用するには、 OAuth と HTTPS のオプションを使用します。

```console
azurite --oauth basic --cert certname.pem --key certname-key.pem
```

#### <a name="azure-blob-storage"></a>Azure Blob Storage

続いて、BlobContainerClient、BlobServiceClient、または BlobClient をインスタンス化できます。

```csharp
// With container URL and DefaultAzureCredential
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new BlobContainerClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "container-name"
  );

// With account name and key
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

#### <a name="azure-queue-storage"></a>Azure Queue Storage

QueueClient または QueueServiceClient をインスタンス化することもできます。

```csharp
// With queue URL and DefaultAzureCredential
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new QueueClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "queue-name"
  );

// With account name and key
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

Storage Explorer を使用すると、Azurite に格納されているデータを表示できます。

#### <a name="connect-to-azurite-using-http"></a>HTTP を使用して Azurite に接続する

Storage Explorer で、次の手順に従って Azurite に接続します。

 1. **[アカウントの管理]** アイコンを選択します
 1. **[アカウントの追加]** を選択します
 1. **[ローカル エミュレーターにアタッチする]** を選択します
 1. **[次へ]** を選択します
 1. **[表示名]** フィールドを編集して、任意の名前にします
 1. もう一度 **[次へ]** を選択します
 1. **[接続]** を選択します

#### <a name="connect-to-azurite-using-https"></a>HTTPS を使用して Azurite に接続する

既定では、Storage Explorer は、自己署名証明書を使用する HTTPS エンドポイントを開きません。 HTTPS で Azurite を実行している場合は、自己署名証明書を使用している可能性があります。 Storage Explorer で、 **[編集]**  ->  **[SSL 証明書]**  ->  **[証明書のインポート]** ダイアログを使用して SSL 証明書をインポートします。

##### <a name="import-certificate-to-storage-explorer"></a>証明書を Storage Explorer にインポートする

1. ローカル コンピューターで証明書を検索します。
1. Storage Explorer で、 **[編集]**  ->  **[SSL 証明書]**  ->  **[証明書のインポート]** の順に移動して、証明書をインポートします。

証明書をインポートしないと、次のエラーが表示されます。

`unable to verify the first certificate` または `self signed certificate in chain`

##### <a name="add-azurite-via-https-connection-string"></a>HTTPS 接続文字列を使用して Azurite を追加する

Storage Explorer に Azurite HTTPS を追加するには、次の手順に従います。

1. **[エクスプローラーの切り替え]** を選択します
1. **[Local & Attached]\(ローカルで接続済み\)** を選択します
1. **[ストレージ アカウント]** を右クリックし、 **[Connect to Azure Storage]\(Azure Storage に接続する\)** を選択します
1. **[Use a connection string]\(接続文字列を使用する\)** を選択します
1. **[次へ]** を選択します。
1. **[表示名]** フィールドに値を入力します
1. このドキュメントの前のセクションに示した [HTTPS 接続文字列](#https-connection-strings)を入力します
1. **[次へ]** を選択します
1. **[接続]** を選択します

## <a name="workspace-structure"></a>ワークスペースの構造

Azurite を初期化するときに、次のファイルとフォルダーがワークスペースの場所に作成されることがあります。

- `__blobstorage__` - Azurite Blob service の永続化されたバイナリ データを格納しているディレクトリ
- `__queuestorage__` - Azurite Queue サービスの永続化されたバイナリ データを格納しているディレクトリ
- `__azurite_db_blob__.json` - Azurite Blob service のメタデータ ファイル
- `__azurite_db_blob_extent__.json` - Azurite Blob service のエクステント メタデータ ファイル
- `__azurite_db_queue__.json` - Azurite Queue サービスのメタデータ ファイル
- `__azurite_db_queue_extent__.json` - Azurite Queue サービスのエクステント メタデータ ファイル

Azurite をクリーンアップするには、上記のファイルとフォルダーを削除し、エミュレーターを再起動します。

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

Azurite は、多数の接続されたクライアントをサポートしていません。 パフォーマンスの保証はありません。 Azurite は開発とテストを目的としています。

### <a name="error-handling"></a>エラー処理

Azurite は Azure Storage エラー処理ロジックに合わせて調整されていますが、違いがあります。 たとえば、エラー メッセージは異なる場合がありますが、エラー状態コードは調整されています。

### <a name="ra-grs"></a>RA-GRS

Azurite は、読み取りアクセス geo 冗長レプリケーション (RA-GRS) をサポートしています。 ストレージ リソースの場合、2 次拠点にアクセスするにはアカウント名に `-secondary` を付加します。 たとえば、Azurite で読み取り専用の 2 次拠点を使用して BLOB にアクセスするには、次のアドレスを使用します。

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

### <a name="table-support"></a>テーブル サポート

Azurite でのテーブルのサポートは現在開発中であり、協力を歓迎しています。 最新の進行状況については、[Azurite V3 テーブル](https://github.com/Azure/Azurite/wiki/Azurite-V3-Table) プロジェクトを確認してください。

持続的関数のサポートにはテーブルが必要です。

## <a name="azurite-is-open-source"></a>Azurite はオープンソースです

Azurite への参加や提案をお待ちしています。 近日公開される機能やバグ修正のために追跡しているマイルストーンと作業項目については、Azurite の [GitHub プロジェクト](https://github.com/Azure/Azurite/projects) ページまたは [GitHub の課題](https://github.com/Azure/Azurite/issues)を参照してください。 GitHub では詳細な作業項目も追跡されています。

## <a name="next-steps"></a>次のステップ

- 「[開発とテストに Azure Storage Emulator を使用する](storage-use-emulator.md)」には、Azurite によって置き換えられつつある従来の Azure Storage Emulator についての記述があります。
- 「[Azure Storage の接続文字列を構成する](storage-configure-connection-string.md)」では、有効な Azure Storage の接続文字列をアセンブルする方法が説明されています。
