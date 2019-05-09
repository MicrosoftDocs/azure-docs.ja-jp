---
title: OPC Vault モジュールをゼロからデプロイする方法 - Azure |Microsoft Docs
description: OPC Vault をゼロからデプロイする方法。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: dedba42d7e4b8d603d97522cf0173f41efd20b3a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59496898"
---
# <a name="deploy-opc-vault-from-scratch"></a>OPC Vault をゼロからデプロイする

OPC Vault は、OPC UA サーバーとクライアント アプリケーションに使用される証明書のライフサイクルの構成、登録、管理をクラウドで行うマイクロサービスです。 この記事では、OPC Vault をゼロからデプロイする方法を示します。

## <a name="configuration-and-environment-variables"></a>構成と環境変数

サービスの構成は、ASP.NET Core の構成アダプターを使用して appsettings.ini に格納されます。 INI 形式では、コメントを付けた読みやすい形式で値を格納できます。
アプリケーションは、資格情報やネットワークの詳細など、環境変数の挿入もサポートしています。 (このセクションは、もともと「TODO: 構成と環境変数」というタイトルです。)

リポジトリ内の構成ファイルは、少なくとも 1 回作成する必要があるいくつかの環境変数を参照します。 OS や IDE に応じて、環境変数を管理する方法はいくつかあります。

- Windows ユーザーの場合、env-vars-setup.cmd スクリプトを準備し、1 回だけ実行する必要があります。 実行すると、複数のターミナル セッションおよび再起動にわたって設定が保持されます。

- Linux および OSX 環境の場合、env-vars-setup スクリプトは、新しいコンソールを開くたびに実行する必要があります。 OS やターミナルによっては、グローバルに値を存続させる方法があります。詳細については、以下のページが役立ちます。

  - https://stackoverflow.com/questions/13046624/how-to-permanently-export-a-variable-in-linux
  
  - https://stackoverflow.com/questions/135688/setting-environment-variables-in-os-x
  
  - https://help.ubuntu.com/community/EnvironmentVariables
  

- Visual Studio:環境変数は、Visual Studio の [プロジェクト プロパティ] からも設定できます。左側のウィンドウで [構成プロパティ]、[環境] の順に選択し、複数の変数を追加できるセクションにアクセスします。

- IntelliJ Rider:IntelliJ IDEA (https://www.jetbrains.com/help/idea/run-debug-configuration-application.html) と同様に、各実行構成で環境変数を設定できます

## <a name="run-and-debug-with-visual-studio"></a>Visual Studio で実行およびデバッグする

Visual Studio では、IDE の外部で何も構成しなくても、コマンド プロンプトを使用せずにすばやくアプリケーションを開くことができます。

Visual Studio 2017 を使用した手順:

1. `iot-opc-gds-service.sln` ファイルを使用しているソリューションを開きます。

1. ソリューションが読み込まれたら、`WebService` プロジェクトを右クリックして選択し、`Debug` セクションに移動します。

1. 同じセクションで、必要な環境変数を定義します。

1. **F5** キーまたは **[実行]** アイコンを押します。 Visual Studio によってブラウザーが開かれ、JSON 形式でサービスの状態が表示されます。

## <a name="run-and-debug-with-intellij-rider"></a>IntelliJ Rider で実行およびデバッグする

1. `iot-opc-gds-service.sln` ファイルを使用しているソリューションを開きます。

1. ソリューションが読み込まれたら、`Run > Edit Configurations` に移動し、新しい `.NET Project` 構成を作成します。

1. 構成内で、WebService プロジェクトを選択します。

1. 設定を保存し、作成された構成を IDE ツールバーから実行します。

1. IntelliJ の実行ウィンドウにサービス ブートストラップのメッセージが表示され、Web サービスが実行されている URL などの詳細に加えてサービス ログが表示されます。

## <a name="build-and-run-from-the-command-line"></a>コマンド ラインからビルドして実行する

スクリプトのフォルダーには、頻繁に行う作業のスクリプトがいくつか含まれています。

- `build`:すべてのプロジェクトをコンパイルし、テストを実行します。

- `compile`:すべてのプロジェクトをコンパイルします。

- `run`:プロジェクトをコンパイルしてサービスを実行します。この方法で Web サービスを実行するには、Windows で昇格された特権が要求されます。

スクリプトは、環境変数の設定を調べます。 OS でグローバルに環境変数を設定することも、スクリプト フォルダーで "env-vars-setup" スクリプトを使用することもできます。

### <a name="sandbox"></a>サンドボックス

スクリプトは、.NET Core および Docker を使用する開発環境を構成済みであることを前提としています。 .NET Core をインストールせずに Docker のみをインストールし、コマンド ライン パラメーター `--in-sandbox` (または短い形式 `-s`) を使用できます。以下に例を示します。

- `build --in-sandbox`:Docker コンテナーの内側でビルド タスクを実行します (短い形式 `build -s`)。

- `compile --in-sandbox`:Docker コンテナーの内側でコンパイル タスクを実行します (短い形式 `compile -s`)。

- `run --in-sandbox`:Docker コンテナーの内側でサービスを開始します (短い形式 `run -s`)。

サンド ボックスに使用される Docker イメージは、Docker Hub の[ここ](https://hub.docker.com/r/azureiotpcs/code-builder-dotnet)でホストされています。

## <a name="package-the-application-to-a-docker-image"></a>アプリケーションを Docker イメージにパッケージ化する

`scripts` フォルダーには、サービスを Docker イメージにパッケージ化するのに必要なファイルがある Docker のサブフォルダーが含まれています。

- `Dockerfile`:Docker イメージの仕様。
- `build`:Docker コンテナーをビルドし、ローカル レジストリにイメージを格納します。
- `run`:ローカル レジストリに格納されているイメージから Docker コンテナーを実行します。
- `content`:エントリ ポイント スクリプトを含めて、イメージにコピーされたファイルが含まれるフォルダー。

## <a name="azure-iot-hub-setup"></a>Azure IoT Hub のセットアップ

マイクロサービスを使用するには、開発および統合テスト用に Azure IoT Hub を設定します。

プロジェクトには、このセットアップに役立つ Bash スクリプトがいくつか含まれています。

- 新しい IoT Hub を作成します: `./scripts/iothub/create-hub.sh`

- 既存のハブを一覧表示します: `./scripts/iothub/list-hubs.sh`

- IoT Hub の詳細 (キーなど) を表示します: `./scripts/iothub/show-hub.sh`

Azure サブスクリプションが複数あった場合:

- サブスクリプションの一覧を表示します: `./scripts/iothub/list-subscriptions.sh`

- 現在のサブスクリプションを変更します: `./scripts/iothub/select-subscription.sh`

## <a name="development-setup"></a>開発のセットアップ

### <a name="net-setup"></a>.NET のセットアップ

プロジェクト ワークフローは [.NET Core](https://dotnet.github.io) 1.x を介して管理されます。これは、お使いの環境にインストールする必要があります。そうすることで、すべてのスクリプトを実行でき、IDE が確実に、予期したとおり機能します。

Microsoft では、このプロジェクトの [Java バージョン](https://github.com/Azure/iot-opc-gds-service-dotnet)と、その他の Azure IoT PCS コンポーネントも提供しています。

### <a name="ide"></a>IDE

以下に、Azure IoT PCS で作業するために使用できる IDE の一部を示します。

- [Visual Studio](https://www.visualstudio.com)
- [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac)
- [IntelliJ Rider](https://www.jetbrains.com/rider)
- [Visual Studio Code](https://code.visualstudio.com)

### <a name="git-setup"></a>Git のセットアップ

プロジェクトには、コードの変更を受け入れる前のいくつかのチェックを自動化する Git フックが含まれています。 手動でテストを実行することも、CI プラットフォームにテストを実行させることもできます。 ここでは次の Git フックを使用して、コードの変更を GitHub に送信する前にすべてのテストを自動的に実行し、開発ワークフローをスピードアップします。

いずれかの時点でフックを削除する場合は、`.git/hooks` の下にインストールされているファイルを削除するだけです。 `--no-verify` オプションを使用して事前コミット フックをバイパスすることもできます。

#### <a name="pre-commit-hook-with-sandbox"></a>サンドボックスを使用する事前コミット フック

含まれているフックをセットアップするには、Windows/Linux/MacOS のコンソールを開き、次を実行します。

```
cd PROJECT-FOLDER
cd scripts/git
setup --with-sandbox
```

この構成では、ファイルをチェックインするときに Git が、アプリケーションがすべてのテストに合格することを確認するので、すべての開発要件を指定して構成されている Docker コンテナーの内側でビルドとテストを実行します。

#### <a name="pre-commit-hook-without-sandbox"></a>サンドボックスを使用しない事前コミット フック

> [!NOTE] 
> サンドボックスを使用しないフックでは、システム パスに [.NET Core](https://dotnet.github.io) が含まれている必要があります。

含まれているフックをセットアップするには、Windows/Linux/MacOS のコンソールを開き、次を実行します。

```
cd PROJECT-FOLDER
cd scripts/git
setup --no-sandbox
```
この構成では、ファイルをチェックインするときに Git が、アプリケーションがすべてのテストに合格することを確認するので、OS にインストールされているツールを使用して、ワークステーションでビルドとテストを実行します。

プロジェクトのコード スタイルに関するガイダンス:

- 妥当な場合、コードのレビューとコマンドライン エディターに役立つように、行の長さが最大 80 文字に制限されます。

- コード ブロックは 4 つのスペースでインデントします。 タブ文字は避ける必要があります。

- テキスト ファイルでは Unix の行末形式 (LF) を使用します。

- 依存関係の挿入は [Autofac](https://autofac.org) で管理されます。

- Web サービスの API フィールドは、メタデータを除いてキャメルケースにされます。

## <a name="next-steps"></a>次の手順

ここでは、OPC Vault をゼロからデプロイする方法を学習しました。推奨される次の手順は次のとおりです。

> [!div class="nextstepaction"]
> [OPC Twin をゼロからデプロイする](howto-opc-twin-deploy-modules.md)