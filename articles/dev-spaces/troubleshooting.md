---
title: トラブルシューティング
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/11/2018
ms.topic: conceptual
description: Azure のコンテナーとマイクロサービスを使用した迅速な Kubernetes 開発
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s '
ms.openlocfilehash: 5dd77d85e06a821d8dd359174bb5de6bca8b4d61
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669778"
---
# <a name="troubleshooting-guide"></a>トラブルシューティング ガイド

このガイドでは、Azure Dev Spaces 使用時の一般的な問題についての情報を示します。

Azure Dev Spaces の使用時に問題が発生した場合は、[Azure Dev Spaces GitHub リポジトリに問題](https://github.com/Azure/dev-spaces/issues)を作成します。

## <a name="enabling-detailed-logging"></a>詳細なログ記録の有効化

問題のトラブルシューティングをより効果的に行うには、レビュー用のより詳細なログを作成することが役に立つ可能性があります。

Visual Studio 拡張機能の場合、`MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED`環境変数を 1 に設定します。 環境変数を有効にするために Visual Studio を再起動してください。 有効になると、詳細なログが `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` ディレクトリに書き込まれます。

CLI では、`--verbose` スイッチを使用してコマンド実行中に詳細な情報を出力できます。 `%TEMP%\Azure Dev Spaces`でより詳しいログを参照することもできます。 Mac では、ターミナルウィンドウから`echo $TMPDIR`を実行すると、TEMP ディレクトリが見つかります。 Linux コンピューターでは、TEMP ディレクトリは通常`/tmp`です。

## <a name="debugging-services-with-multiple-instances"></a>複数のインスタンスでサービスのデバッグ

現時点では、Azure Dev Spaces は、単一のインスタンス (ポッド) のデバッグで最も効果的に機能します。 azds.yaml ファイルには、Kubernetes がサービスに対して実行するポッドの数を示す設定である *replicaCount* が含まれています。 replicaCount を変更して特定のサービスに対して複数のポッドを実行するようにアプリケーションを設定すると、デバッガは (アルファベット順にリストされている場合) 最初のポッドに接続されます。 元のポッドがリサイクルされた場合、デバッガは別のポッドに接続されます。これにより、予期しない動作が発生する可能性があります。

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>エラー "Azure Dev Spaces コントローラーを作成できませんでした"

コントローラーの作成で問題が発生した場合に、このようなエラーが表示されることがあります。 これが一時的なエラーの場合は、コントローラーを削除して再作成することで修正されます。

### <a name="try"></a>次の操作を試してください。

コントローラーを削除するには、Azure Dev Spaces の CLI を使用します。 Visual Studio や Cloud Shell でこれを行うことはできません。 AZDS CLI をインストールするには、まず、Azure CLI をインストールして、以下のコマンドを実行します。

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

次に、以下のコマンドを実行してコントローラーを削除します。

```cmd
azds remove -g <resource group name> -n <cluster name>
```

コントローラーの再作成は、CLI や Visual Studio から行うことができます。 初めての場合と同じように、チュートリアルの手順に従ってください。


## <a name="error-service-cannot-be-started"></a>エラー "サービスを開始できません。"

サービス コードを起動できない場合、このエラーが表示されることがあります。 通常、原因はユーザー コードです。 詳しい診断情報を取得するには、コマンドや設定に次の変更を行います。

### <a name="try"></a>次の操作を試してください。

コマンド ラインで次の操作を行います。

_azds.exe_ を使用した場合、--verbose コマンド ライン オプションを使用し、--output コマンド ライン オプションを使用して出力形式を指定します。
 
```cmd
azds up --verbose --output json
```

Visual Studio で次の操作を行います。

1. **[ツール] > [オプション]** を開き、**[プロジェクトとソリューション]** で、**[ビルドおよび実行]** を選択します。
2. **[MSBuild プロジェクト ビルドの出力の詳細]** の設定を **[詳細]** または **[診断]** に変更します。

    ![ツール オプション ダイアログのスクリーンショット](media/common/VerbositySetting.PNG)
    
### <a name="multi-stage-dockerfiles"></a>マルチステージの Dockerfile:
マルチステージの Dockerfile を使用すると、"*Service cannot be started (サービスを開始できません)*" エラーが発生します。 このような状況では、詳細出力に次のテキストが含まれます。

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

このエラーは、AKS ノードでマルチステージ ビルドをサポートしていない旧バージョンの Docker が実行されているために発生します。 マルチステージ ビルドを回避するには、Dockerfile を書き直します。

### <a name="rerunning-a-service-after-controller-re-creation"></a>コントローラーの再作成後のサービスの再実行
このクラスターに関連付けられた Azure Dev Spaces を削除してから再作成した後、サービスを再実行しようとすると、"*Service cannot be started (サービスを開始できません)*" エラーが発生します。 このような状況では、詳細出力に次のテキストが含まれます。

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

このエラーは、Dev Spaces コントローラーを削除しても、そのコントローラーによって以前にインストールされたサービスが削除されないという理由で発生します。 コントローラーを再作成してから、新しいコントローラーを使用してサービスを実行しようとしても、古いサービスがまだ残っているため失敗します。

この問題に対処するには、`kubectl delete` コマンドを使用して古いサービスをクラスターから手動で削除した後、Dev Spaces を再実行して新しいサービスをインストールします。

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Dev Spaces サービスに関連付けられたパブリック URL で DNS の名前解決が失敗します。

`azds prep` コマンドで `--public` スイッチを指定するか、Visual Studio で `Publicly Accessible` チェック ボックスをオンにすることで、サービスのパブリック URL エンドポイントを構成できます。 Dev Spaces でサービスを実行すると、パブリック DNS 名が自動的に登録されます。 この DNS 名が登録されていない場合は、パブリック URL に接続するときに、Web ブラウザーに "*Page cannot be displayed (ページを表示できません)*" または "*Site cannot be reached (サイトに到達できません)*" エラーが表示されます。

### <a name="try"></a>次の操作を試してください。

次のコマンドを使用して、お使いの Dev Spaces サービスに関連付けられたすべての URL をリストアップします。

```cmd
azds list-uris
```

URL が*保留中*の状態にある場合、これはまだ Dev Spaces が DNS の登録が完了するのを待っている状態にあることを示します。 登録が完了するまでに数分かかることがあります。 また、Dev Spaces は各サービスの localhost トンネルを開き、これを DNS の登録を待っている間に使用できます。

URL の "*保留中*" 状態が 5 分を超える場合、パブリック エンドポイントを作成する外部 DNS ポッド、またはパブリック エンドポイントを取得する nginx イングレス コントローラー ポッドに問題がある可能性があります。 次のコマンドを使用して、これらのポッドを削除できます。 削除されたポッドは、AKS によって自動的に再作成されます。

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>エラー "必要なツールと構成が見つからない"

このエラーは、VS Code を起動するときに発生する場合があります。"[[Azure Dev Spaces] Required tools and configurations to build and debug '[project name]' are missing.]\([Azure Dev Spaces] '[プロジェクト名]' をビルドして出バックするために必要なツールと構成がありません。\)"
エラーは、VS Code に示されるように、azds.exe が PATH 環境変数に無いことを意味します。

### <a name="try"></a>次の操作を試してください。

PATH 環境変数が正しく設定されているコマンド プロンプトから VS Code を起動します。

## <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>エラー "Required tools to build and debug 'projectname' are out of date." ('projectname' をビルドおよびデバッグするために必要なツールの期限が切れています。)

このエラーは、Azure Dev Spaces 用の VS Code 拡張機能のバージョンは新しいが、Azure Dev Spaces CLI のバージョンが古い場合に、Visual Studio Code に表示されます。

### <a name="try"></a>試す

最新バージョンの Azure Dev Spaces CLI をダウンロードしてインストールします:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>エラー 'azds' が、内部または外部コマンド、操作可能プログラム、またはバッチ ファイルとして認識されません
 
azds.exe が正しくインストールされていないか、構成されていない場合に、このエラーが表示されることがあります。

### <a name="try"></a>次の操作を試してください。

1. %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI (プレビュー) で azds.exe を探してください。 見つかった場合は、その場所を PATH 環境変数に追加します。
2. azds.exe がインストールされていない場合は、次のコマンドを実行します。

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>警告 "Dockerfile could not be generated due to unsupported language"
Azure Dev Spaces では、C# と Node.js がネイティブでサポートされます。 これらのいずれかの言語で記述されたコードが含まれているディレクトリで *azds prep* を実行すると、Azure Dev Spaces によって適切な Dockerfile が自動的に作成されます。

その他の言語で記述されたコードを Azure Dev Spaces で使用できますが、*azds up* の初回の実行前に、Dockerfile を手動で作成しておく必要があります。

### <a name="try"></a>次の操作を試してください。
Azure Dev Spaces でネイティブにサポートされる言語でアプリケーションが記述されていない場合、コードが実行されるコンテナー イメージをビルドするために適切な Dockerfile を設定する必要があります。 Docker では、[Dockerfile の記述に関するベスト プラクティスの一覧](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)と [Dockerfile リファレンス](https://docs.docker.com/engine/reference/builder/)を提供しています。これはご自身のニーズに合った Dockerfile を記述するために役立ちます。

適切な Dockerfile を用意できたら、*azds up* の実行に進んで、Azure Dev Spaces でアプリケーションを実行できます。

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>エラー 'upstream connect error or disconnect/reset before headers'
このエラーは、サービスへのアクセスを試みたときに発生する場合があります。 たとえば、ブラウザーでサービスの URL にアクセスしたときです。 

### <a name="reason"></a>理由 
コンテナーのポートが使用できません。 この問題は、以下の理由で発生する可能性があります。 
* コンテナーがまだビルドとデプロイの処理中です。 `azds up` を実行またはデバッガーを起動した後、コンテナーが正常にデプロイされる前にコンテナーにアクセスしようとした場合、この問題が発生する可能性があります。
* _Dockerfile_、Helm チャート、およびポートを開くサーバー コード間でポート構成が整合していません。

### <a name="try"></a>次の操作を試してください。
1. コンテナーがビルド/デプロイ処理中の場合、2 ～ 3 秒待ってからサービスへのアクセスを再試行できます。 
1. ポート 構成を確認します。 次のすべてのアセットに指定されたポート番号が**同一**である必要があります。
    * **Dockerfile:**`EXPOSE` 命令によって指定されます。
    * **[Helm チャート](https://docs.helm.sh):** サービスの `externalPort` および `internalPort` 値 (多くの場合、`values.yml` ファイルにある) で指定されます。
    * Node.js などのアプリケーション コードで開かれているポートがあります: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>構成ファイルが見つかりません
`azds up` を実行すると次のエラーが表示されます: `Config file not found: .../azds.yaml`

### <a name="reason"></a>理由
実行するコードのルート ディレクトリから `azds up` を実行する必要があります。また、Azure Dev Spaces で実行するようにコード フォルダーを初期化する必要があります。

### <a name="try"></a>次の操作を試してください。
1. サービス コードが含まれているルート フォルダーにカレント ディレクトリを変更します。 
1. _azds.yaml_ ファイルがコード フォルダーにない場合、`azds prep` を実行して Docker、Kubernetes、および Azure Dev Spaces アセットを生成します。

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>エラー:'The pipe program 'azds' exited unexpectedly with code 126.'
VS Code デバッガーを起動すると、このエラーが発生する場合があります。

### <a name="try"></a>次の操作を試してください。
1. VS Code を閉じて、もう一度開きます。
2. もう一度 F5 キーを押します。

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>デバッグ エラー 'Failed to find debugger extension for type:coreclr'
VS Code デバッガーを実行すると次のエラーが報告されます: `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>理由
開発マシンに C＃ 用の VS コード拡張機能がインストールされていません。 C# 拡張機能には、.NET Core (CoreCLR) 用のデバッグ サポートが含まれています。

### <a name="try"></a>次の操作を試してください。
[C# 用 VS Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)をインストールします。

## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>デバッグ エラー 'Configured debug type 'coreclr' is not supported'
VS Code デバッガーを実行すると次のエラーが報告されます: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>理由
Azure Dev Spaces 用の VS Code 拡張機能が開発用コンピューターにインストールされていません。

### <a name="try"></a>次の操作を試してください。
[Azure Dev Spaces 用の VS Code 拡張機能](get-started-netcore.md)をインストールします。

## <a name="debugging-error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>デバッグ エラー "Invalid 'cwd' value '/src'. The system cannot find the file specified." ('cwd' の値 '/src' が無効です。指定されたファイルがシステムに見つかりません。) または "launch: program '/src/[path to project binary]' does not exist" (launch: program '/src/[プロジェクト バイナリのパス]' が存在しません)
VS Code デバッガーを実行すると、エラー `Invalid 'cwd' value '/src'. The system cannot find the file specified.` と `launch: program '/src/[path to project executable]' does not exist` のどちらかまたは両方が報告されます

### <a name="reason"></a>理由
既定では、VS Code 拡張機能はコンテナー上のプロジェクト用の作業ディレクトリとして `src` を使用します。 `Dockerfile` を更新して別の作業ディレクトリを指定した場合、このエラーが表示されることがあります。

### <a name="try"></a>次の操作を試してください。
プロジェクト フォルダーの `.vscode` サブディレクトリ下の `launch.json` ファイルを更新します。 プロジェクトの `Dockerfile` で定義されている `WORKDIR` と同じディレクトリを指すように、`configurations->cwd` ディレクティブを変更します。 `configurations->program` ディレクティブも更新することが必要な場合もあります。

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>型または名前空間名 'MyLibrary' が見つかりませんでした

### <a name="reason"></a>理由 
ビルド コンテキストは既定でプロジェクト/サービス レベルのため、使用しているライブラリ プロジェクトを見つけることができません。

### <a name="try"></a>次の操作を試してください。
必要な手順:
1. _azds.yaml_ ファイルを変更して、ビルド コンテキストをソリューション レベルに設定します。
2. _Dockerfile_ および _Dockerfile.develop_ ファイルを変更して、新しいビルド コンテキストからの相対位置でプロジェクト (_.csproj_) ファイルを正しく参照します。
3. _.dockerignore_ ファイルを .sln ファイルと同じ場所に置き、必要に応じて変更します。

https://github.com/sgreenmsft/buildcontextsample に例があります

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>'Microsoft.DevSpaces/register/action' 承認エラー
Azure Dev Spaces を管理するには、お使いの Azure サブスクリプション内に "*所有者*" または "*共同作成者*" アクセス権が必要です。 このエラーは、Dev Spaces を管理しようとしたが、関連付けられている Azure サブスクリプションに "*所有者*" アクセス権も "*共同作成者*" アクセス権もない場合に表示される可能性があります。
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>理由
選択した Azure サブスクリプションが `Microsoft.DevSpaces` 名前空間を登録していません。

### <a name="try"></a>次の操作を試してください。
Azure サブスクリプションの所有者または共同作成者のアクセス権を持つユーザーが、次の Azure CLI コマンドを実行して `Microsoft.DevSpaces` 名前空間を手動で登録できます。

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="dev-spaces-times-out-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>AKS 仮想ノードの "*Waiting for container image build... (コンテナーイメージがビルドされるのを待っています...)*" 手順で Dev Spaces がタイムアウトする

### <a name="reason"></a>理由
このタイムアウトは、Dev Spaces を使用して、[AKS 仮想ノード](https://docs.microsoft.com/azure/aks/virtual-nodes-portal)上で実行するように構成されているサービスを実行しようとした場合に発生します。 Dev Spaces では、現在、仮想ノード上でのサービスのビルドもデバッグもサポートされていません。

`azds up` を `--verbose` スイッチを指定して実行するか、Visual Studio で詳細ログを有効にした場合、次の詳細が表示されます。

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

上のコマンドは、サービスのポッドが、仮想ノードである *virtual-node-aci-linux* に割り当てられたことを示しています。

### <a name="try"></a>次の操作を試してください。
サービス用の Helm チャートで、仮想ノード上でのサービスの実行を許可する *nodeSelector* 値と *tolerations* 値を削除して、チャートを更新します。 通常、これらの値は、チャートの `values.yaml` ファイルに定義されます。

Dev Spaces を介してビルドまたはデバッグするサービスを VM ノード上で実行したい場合は、仮想ノード機能が有効な AKS クラスターを引き続き使用できます。 これは既定の構成です。

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Dev Spaces 起動時の "Error: could not find a ready tiller pod" (エラー: 準備のできた Tiller ポッドが見つかりませんでした)

### <a name="reason"></a>理由
このエラーは、クラスターで実行されている Tiller ポッドに Helm クライアントが通信できなくなった場合に発生します。

### <a name="try"></a>次の操作を試してください。
通常は、クラスター内のエージェント ノードを再起動すると、この問題が解決します。

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Azure Dev Spaces プロキシは開発空間で実行されているその他のポッドと干渉することがあります

### <a name="reason"></a>理由
AKS クラスター内の名前空間で Dev Spaces を有効にすると、_mindaro-proxy_ と呼ばれる追加のコンテナーが、その名前空間内で実行される各ポッドにインストールされます。 このコンテナーは、ポッド内のサービスへの呼び出しをインターセプトしますが、これは Dev Spaces のチーム開発機能に不可欠です。ただし、これらのポッド内で実行中の特定のサービスに干渉する可能性があります。 Azure Cache for Redis を実行するポッドに干渉し、マスター/スレーブ通信での接続エラーと障害の原因になることがわかっています。

### <a name="try"></a>次の操作を試してください。
影響を受けるポッドを、Dev Spaces が有効になって "_いない_" クラスター内の名前空間に移動できます。 Dev Spaces が有効な名前空間内で、残りのアプリケーションの実行を続けることができます。 Dev Spaces は、Dev Spaces に非対応の名前空間の内部に _mindaro-proxy_ コンテナーをインストールしません。

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev Spaces が既存の Dockerfile を使用してコンテナーをビルドしていないと思われる

### <a name="reason"></a>理由
プロジェクト内の特定の _Dockerfile_ を指示するように Azure Dev Spaces を構成できます。 Azure Dev Spaces で、コンテナーをビルドするために想定した _Dockerfile_ が使用されていないと思われる場合は、どの Dockerfile ファイルを使用するかを Azure Dev Spaces に明示的に通知する必要があります。 

### <a name="try"></a>次の操作を試してください。
Azure Dev Spaces によってプロジェクト内に生成された _azds.yaml_ ファイルを開きます。 *configurations->develop->build->dockerfile* ディレクティブを使用して、使用する Dockerfile を指示します。

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

## <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Node.js アプリケーションにデバッグを接続するとエラー "Internal watch failed: watch ENOSPC" が発生する

### <a name="reason"></a>理由

デバッガーで接続しようとしている Node.js アプリケーションでポッドを実行しているノードが *fs.inotify.max_user_watches* 値を超えました。 場合によっては、[*fs.inotify.max_user_watches* の既定値が小さすぎて、デバッガーのポッドへの直接の接続を処理できない可能性があります](https://github.com/Azure/AKS/issues/772)。

### <a name="try"></a>試す
この問題の一時的な対処法として、クラスター内の各ノード上で *fs.inotify.max_user_watches* の値を増やし、そのノードを再起動して変更を有効にしてください。