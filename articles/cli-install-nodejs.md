---
title: "Azure CLI 1.0 のインストール | Microsoft Docs"
description: "Mac、Linux、および Windows に Azure CLI 1.0 をインストールして Azure サービスの利用を開始する"
editor: 
manager: timlt
documentationcenter: 
author: squillace
services: virtual-machines-linux,virtual-network,storage,azure-resource-manager
tags: azure-resource-manager,azure-service-management
ms.assetid: bdb776c8-7a76-4f3a-887c-236b4fffee10
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: command-line-interface
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: de3f8cb4a603b3aab7cef21b06f9615ce7d892cb
ms.lasthandoff: 03/21/2017


---
# <a name="install-the-azure-cli-10"></a>Azure CLI 1.0 のインストール
> [!div class="op_single_selector"]
> * [PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs)
> * [Azure CLI 1.0](cli-install-nodejs.md)
> * [Azure CLI 2.0](/cli/azure/install-azure-cli)

> [!IMPORTANT]
> このトピックでは、Azure CLI 1.0 をインストールする方法について説明します。これは nodeJs に構築され、すべてのクラシック デプロイメント API 呼び出しと、多数の Resource Manager デプロイ アクティビティに対応します。 新しいまたは将来的な CLI デプロイメントおよび管理の場合は、[Azure CLI 2.0](/cli/azure/overview) を使用する必要があります。

Azure コマンド ライン インターフェイス (Azure CLI 1.0) を簡単にインストールして、コマンド ライン シェルからオープン ソースのコマンドを使って Microsoft Azure 上のリソースを作成したり管理したりすることができます。 お使いのコンピューターにこれらのクロスプラットフォーム ツールをインストールするオプションは複数あります。

* **npm パッケージ** - npm (JavaScript 用のパッケージ マネージャー) を実行して、Linux ディストリビューションまたは OS に最新の Azure CLI 1.0 パッケージをインストールします。 お使いのコンピューターに node.js と npm が必要です。
* **インストーラー** - Mac または Windows に簡単インストールするにはインストーラーをダウンロードします。
* **Docker コンテナー** - すぐに実行できる Docker コンテナーで最新の CLI の使用を開始します。 お使いのコンピューター上に Docker ホストが必要です。

その他のオプションと背景については、 [GitHub](https://github.com/azure/azure-xplat-cli)のプロジェクト リポジトリを参照してください。

Azure CLI 1.0 をインストールした後、[Azure サブスクリプションに接続](xplat-cli-connect.md)し、コマンド ライン インターフェイス (Bash、ターミナル、コマンド プロンプトなど) から **azure** コマンドを実行して、Azure リソースを操作します。

## <a name="option-1-install-an-npm-package"></a>オプション 1: npm パッケージのインストール
CLI を npm パッケージからインストールするには、[最新の Node.js と npm](https://nodejs.org/en/download/package-manager/) をダウンロードし、インストールしていることを確認してください。 次に、**npm install** を実行して、azure-cli パッケージをインストールします。

```bash
npm install -g azure-cli
```

Linux ディストリビューションの場合、**npm** コマンドを正常に実行するには、次のように **sudo** の使用が必要になる場合があります。

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> Node.js と npm を Linux ディストリビューションまたは OS にインストールまたは更新する必要がある場合は、最新の Node.js LTS バージョン (4.x) をインストールすることをお勧めします。 以前のバージョンを使用すると、インストール エラーが発生する場合があります。

必要に応じて、npm パッケージの最新の Linux [tar ファイルを][linux-installer]ローカルにダウンロードします。 その後、ダウンロードした npm パッケージを次のようにインストールします (Linux ディストリビューションでは **sudo**を使用しなければならないことがあります)。

```bash
npm install -g <path to downloaded tar file>
```

## <a name="option-2-use-an-installer"></a>オプション 2: インストーラーの使用
Mac または Windows コンピューターを使用する場合、次の CLI インストーラーをダウンロードに使用できます。

* [Mac OS X インストーラー][mac-installer]
* [Windows MSI][windows-installer]

> [!TIP]
> Windows では、 [Web プラットフォーム インストーラー](https://go.microsoft.com/?linkid=9828653) をダウンロードして CLI をインストールすることもできます。 このインストーラーを使用すると、CLI をインストールした後で、その他の Azure SDK とコマンド ライン ツールをインストールすることもできます。

## <a name="option-3-use-a-docker-container"></a>オプション 3: Docker コンテナーの使用
お使いのコンピューターを [Docker](https://docs.docker.com/engine/understanding-docker/) ホストとして設定すると、Docker コンテナーで最新の Azure CLI 1.0 を実行できるようになります。 次のコマンドを実行します (Linux ディストリビューションの場合、**sudo** の使用が必要になる場合があります)。

```bash
docker run -it microsoft/azure-cli
```

## <a name="run-azure-cli-10-commands"></a>Azure CLI 1.0 コマンドの実行
Azure CLI 1.0 をインストールした後、コマンド ライン ユーザー インターフェイス (Bash、ターミナル、コマンド プロンプトなど) から **azure** コマンドを実行します。 たとえば、ヘルプ コマンドを実行するには、次のように入力します。

```azurecli
azure help
```

> [!NOTE]
> 一部の Linux ディストリビューションでは、`/usr/bin/env: ‘node’: No such file or directory` のようなエラーが表示されることがあります。 このエラーは、/usr/bin/nodejs に最近インストールされた Node.js が原因で発生します。 このエラーを修正するには、次のコマンドを実行して /usr/bin/node へのシンボリック リンクを作成します。

```bash
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

インストールした Azure CLI 1.0 のバージョンを確認するには、次のように入力します。

```azurecli
azure --version
```

これで準備が整いました。 すべての CLI コマンドにアクセスして独自のリソースを操作するには、 [Azure CLI から Azure サブスクリプションに接続](xplat-cli-connect.md)します。

> [!NOTE]
> Azure CLI を初めて使用する場合、Microsoft が使用状況についての情報を収集することを許可するかどうかをたずねるメッセージが表示されます。 参加は任意です。 参加した後でも、 `azure telemetry --disable`を実行するといつでも停止できます。 参加を有効にするには、任意のタイミングで `azure telemetry --enable`を実行します。

## <a name="update-the-cli"></a>CLI の更新
マイクロソフトは、Azure CLI の更新バージョンを頻繁にリリースしています。 ご使用のオペレーティング システム用のインストーラーを使用するか、最新の Docker コンテナーを実行して、CLI を再インストールします。 または、最新の Node.js と npm がインストールされている場合は、次のコマンドを入力して更新します (Linux ディストリビューションでは、 **sudo**の使用が必要になる場合があります)。

```bash
npm update -g azure-cli
```

## <a name="enable-tab-completion"></a>タブ補完の有効化
Mac と Linux では、CLI コマンドのタブ補完がサポートされます。

zsh で有効化する場合は、次のコマンドを実行します。

```bash
echo '. <(azure --completion)' >> .zshrc
```

bash で有効化する場合は、次のコマンドを実行します。

```bash
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## <a name="next-steps"></a>次のステップ
* [CLI から Azure サブスクリプションへの接続](xplat-cli-connect.md) を行い、Azure リソースを作成および管理します。
* Azure CLI の詳細、ソース コードのダウンロード、問題のレポート、プロジェクトへの協力については、 [GitHub リポジトリの Azure CLI](https://github.com/azure/azure-xplat-cli)のページを参照してください。
* Azure CLI または Azure の使用に関してご不明な点がある場合は、 [Azure のフォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting)をご覧ください。


[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: http://aka.ms/webpi-azure-cli
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: /cli/azure/get-started-with-az-cli2
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md

