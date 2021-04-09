---
title: カスタム コンテナーを構成する
description: Azure App Service でカスタム コンテナーを構成する方法について説明します。 この記事では、最も一般的な構成タスクを紹介しています。
ms.topic: article
ms.date: 02/23/2021
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 8083c3c0c88d904ccb3ec75ae69a699867bd0f25
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704873"
---
# <a name="configure-a-custom-container-for-azure-app-service"></a>Azure App Service のカスタム コンテナーを構成する

この記事では、Azure App Service で実行されるようにカスタム コンテナーを構成する方法を示します。

::: zone pivot="container-windows"

このガイドでは、App Service の Windows アプリのコンテナー化の主要概念および手順について説明します。 Azure App Service を使用したことがない場合は、最初に[カスタム コンテナー クイック スタート](quickstart-custom-container.md)と[チュートリアル](tutorial-custom-container.md)に従ってください。

::: zone-end

::: zone pivot="container-linux"

このガイドでは、App Service の Linux アプリのコンテナー化の主要概念および手順について説明します。 Azure App Service を使用したことがない場合は、最初に[カスタム コンテナー クイック スタート](quickstart-custom-container.md)と[チュートリアル](tutorial-custom-container.md)に従ってください。 [複数コンテナー アプリのクイック スタート](quickstart-multi-container.md)と[チュートリアル](tutorial-multi-container-app.md)もあります。

::: zone-end

::: zone pivot="container-windows"

## <a name="supported-parent-images"></a>サポートされている親イメージ

カスタム Windows イメージの場合は、必要なフレームワークに合った適切な[親イメージ (ベース イメージ)](https://docs.docker.com/develop/develop-images/baseimages/)を選択する必要があります。

- .NET Framework のアプリをデプロイするには、Windows Server Core [長期サービス チャネル (LTSC)](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) リリースに基づく親イメージを使用します。 
- .NET Core のアプリをデプロイするには、Windows Server Nano [半期サービス チャネル (SAC)](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) リリースに基づく親イメージを使用します。 

アプリの起動中は、親イメージのダウンロードに多少の時間がかかります。 ただし、Azure App Service にあらかじめキャッシュされている次のいずれかの親イメージを使用することで、起動時間を短縮することができます。

- [mcr.microsoft.com/windows/servercore](https://hub.docker.com/_/microsoft-windows-servercore):2004
- [mcr.microsoft.com/windows/servercore](https://hub.docker.com/_/microsoft-windows-servercore):ltsc2019
- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.8-windowsservercore-2004
- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.8-windowsservercore-ltsc2019
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-2004
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-1909
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-1903
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-1809
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-2004
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-1909
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-1903
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-1809

::: zone-end

## <a name="change-the-docker-image-of-a-custom-container"></a>カスタム コンテナーの Docker イメージを変更する

既存のカスタム コンテナー アプリの現在の Docker イメージを新しいイメージに変更するには、次のコマンドを使用します。

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>プライベート レジストリからイメージを使用する

Azure Container Registry などのプライベート レジストリから イメージを使用するには、次のコマンドを実行します。

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

*\<username>* および *\<password>* には、プライベート レジストリ アカウントのログイン資格情報を指定します。

## <a name="i-dont-see-the-updated-container"></a>更新されたコンテナーが表示されない

新しいコンテナーを指すように Docker コンテナー設定を変更した場合、アプリが新しいコンテナーからの HTTP 要求を処理するまでに数分かかることがあります。 新しいコンテナーがプルされ、開始されている間、App Service は古いコンテナーからの要求を引き続き処理します。 新しいコンテナーが開始され、要求を受信する準備が整った場合にのみ、App Service がそれに対する要求の送信を開始します。

## <a name="how-container-images-are-stored"></a>コンテナー イメージの格納方法

App Service でカスタム Docker イメージを初めて実行する場合、App Service は `docker pull` を行い、すべてのイメージ レイヤーをプルします。 これらのレイヤーは、オンプレミスの Docker を使用しているかのようにディスクに格納されます。 アプリが再起動するたびに、App Service は `docker pull` を実行しますが、変更されたレイヤーのみをプルします。 変更がなかった場合、App Service はローカル ディスク上の既存のレイヤーを使用します。

価格レベルのスケールアップやスケールダウンなど、何らかの理由でアプリがコンピューティング インスタンスを変更した場合、App Service はすべてのレイヤーを再度プルする必要があります。 さらにインスタンスを追加するためにスケールアウトする場合も同様です。 また、スケーリング操作を行わなくてもアプリ インスタンスが変更されることがまれにあります。

## <a name="configure-port-number"></a>ポート番号を構成する

既定では、App Service はカスタム コンテナーがポート 80 でリッスンしていることを前提としています。 コンテナーが別のポートをリッスンしている場合は、App Service アプリで `WEBSITES_PORT` アプリ設定を指定します。 これは、[Cloud Shell](https://shell.azure.com) を使用して設定できます。 Bash では次のとおりです。

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

PowerShell では次のとおりです。

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_PORT"="8000"}
```

現在、App Service では、コンテナーが HTTP 要求に対して 1 つのポートのみを公開することが許可されています。 

## <a name="configure-environment-variables"></a>環境変数を構成する

カスタム コンテナーには、外部で指定する必要がある環境変数を使用できます。 これらは、[Cloud Shell](https://shell.azure.com) 経由で渡すことができます。 Bash では次のとおりです。

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings DB_HOST="myownserver.mysql.database.azure.com"
```

PowerShell では次のとおりです。

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"DB_HOST"="myownserver.mysql.database.azure.com"}
```

アプリを実行すると、App Service アプリ設定が環境変数としてプロセスに自動的に挿入されます。 コンテナー環境変数は、URL `https://<app-name>.scm.azurewebsites.net/Env)` を使用して確認できます。

::: zone pivot="container-windows"
IIS または .NET Framework (4.0 以降) ベースのコンテナーの場合、これらは、App Service によって .NET アプリ設定と接続文字列として `System.ConfigurationManager` に挿入されます。 他のすべての言語またはフレームワークでは、これらは、次のいずれかの対応するプレフィックス付きの環境変数としてプロセスに提供されます。

- `APPSETTING_`
- `SQLCONTR_`
- `MYSQLCONTR_`
- `SQLAZURECOSTR_`
- `POSTGRESQLCONTR_`
- `CUSTOMCONNSTR_`

::: zone-end

::: zone pivot="container-linux"

この方法は単一のコンテナー アプリまたは複数のコンテナー アプリの両方で利用できます。ただし、環境変数は *docker compose.yml* ファイルで指定されます。

::: zone-end

## <a name="use-persistent-shared-storage"></a>永続的な共有ストレージを使用する

::: zone pivot="container-windows"

アプリのファイル システムの *C:\home* ディレクトリを使用して、再起動間でファイルを永続化し、インスタンス間でそれらを共有することができます。 アプリの `C:\home` は、コンテナー アプリが永続的ストレージにアクセスできるようにするために指定されます。

永続的ストレージが無効になっている場合、`C:\home` ディレクトリへの書き込みは保持されません。 [Docker ホスト ログとコンテナー ログ](#access-diagnostic-logs)は、コンテナーに接続されていない既定の永続的な共有ストレージに保存されます。 永続的ストレージが有効になっている場合、`C:\home` ディレクトリへのすべての書き込みは保持され、スケールアウトされたアプリのすべてのインスタンスからアクセスでき、`C:\home\LogFiles` でログにアクセスできます。

::: zone-end

::: zone pivot="container-linux"

アプリのファイル システムの */home* ディレクトリを使用して、再起動間でファイルを永続化し、インスタンス間でそれらを共有することができます。 アプリの `/home` は、コンテナー アプリが永続的ストレージにアクセスできるようにするために指定されます。

永続的ストレージが無効になると、`/home` ディレクトリへの書き込みは、アプリの再起動後、または複数のインスタンス間で保持されません。 唯一の例外は `/home/LogFiles` ディレクトリであり、これは Docker およびコンテナーのログを格納するために使用されます。 永続的ストレージが有効になると、`/home` ディレクトリへのすべての書き込みは存続し、スケールアウトされたアプリのすべてのインスタンスからアクセスできます。

::: zone-end

既定では、永続的ストレージは無効になっており、この設定はアプリケーション設定では公開されていません。 これを有効にするには、[Cloud Shell](https://shell.azure.com) を使用して `WEBSITES_ENABLE_APP_SERVICE_STORAGE` アプリ設定を指定します。 Bash では次のとおりです。

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

PowerShell では次のとおりです。

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=true}
```

> [!NOTE]
> [独自の永続的ストレージを構成](configure-connect-to-azure-storage.md)することもできます。

## <a name="detect-https-session"></a>HTTPS セッションの検出

App Service は、フロントエンドで TLS/SSL を終了します。 つまり、TLS/SSL 要求がアプリに到達することはありません。 アプリに TLS/SSL のサポートを実装する必要はないため、実装すべきではありません。 

フロントエンドは Azure データ センター内に配置されます。 アプリで TLS/SSL を使用する場合、インターネット経由のトラフィックは常に安全に暗号化されます。

::: zone pivot="container-windows"

## <a name="customize-aspnet-machine-key-injection"></a>ASP.NET マシン キーの挿入をカスタマイズする

 コンテナーの開始時、自動的に生成されたキーが ASP.NET 暗号ルーチンのマシン キーとしてコンテナーに挿入されます。 [コンテナー内のこれらのキーを見つける](#connect-to-the-container)には、環境変数 `MACHINEKEY_Decryption`、`MACHINEKEY_DecryptionKey`、`MACHINEKEY_ValidationKey`、`MACHINEKEY_Validation` を探し ます。 

各再起動時の新しいキーによって ASP.NET フォーム認証とビュー状態がリセットされることがあります (それらにアプリが依存している場合)。 キーが自動的に再生成されないようにするには、[それらを App Service アプリ設定として手動で設定します](#configure-environment-variables)。 

## <a name="connect-to-the-container"></a>コンテナーに接続する

診断タスクのために Windows コンテナーに直接接続するには、`https://<app-name>.scm.azurewebsites.net/DebugConsole` に移動します。 しくみは次のとおりです。

- デバッグ コンソールでは、PowerShell セッションの開始、レジストリ キーの検査、コンテナー ファイル システム全体での移動など、対話型のコマンドを実行できます。
- その上にあるグラフィカル ブラウザー ([共有ストレージ](#use-persistent-shared-storage)内のファイルのみを表示) とは別に機能します。
- スケールアウトされたアプリでは、デバッグ コンソールはコンテナー インスタンスのいずれかに接続されています。 上部のメニューの **[インスタンス]** ドロップダウンから別のインスタンスを選択できます。
- コンソール内からコンテナーに対して行った変更は、Docker イメージの一部ではないため、アプリの再起動時には存続 "*しません*" (共有ストレージ内の変更は除きます)。 レジストリ設定やソフトウェアのインストールなどの変更を保持するには、Dockerfile の一部にします。

## <a name="access-diagnostic-logs"></a>診断ログにアクセスする

App Service は、Docker ホストによるアクションと、コンテナー内からのアクティビティをログします。 Docker ホストからのログ (プラットフォーム ログ) は既定で送られますが、コンテナー内からのアプリケーション ログや Web サーバー ログは手動で有効にする必要があります。 詳細については、「[アプリケーションのログ記録を有効にする](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer)」と「[Web サーバーのログ記録を有効にする](troubleshoot-diagnostic-logs.md#enable-web-server-logging)」を参照してください。 

Docker ログにアクセスするには、いくつかの方法があります。

- [Azure Portal](#in-azure-portal)
- [Kudu コンソールから](#from-the-kudu-console)
- [Kudu API を使用する](#with-the-kudu-api)
- [ログを Azure Monitor に送信する](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)

### <a name="in-azure-portal"></a>Azure Portal

Docker ログは、ポータル内のご自分のアプリの **[コンテナーの設定]** ページに表示されます。 ログは切り捨てられますが、 **[ダウンロード]** をクリックしてすべてのログをダウンロードすることができます。 

### <a name="from-the-kudu-console"></a>Kudu コンソールから

`https://<app-name>.scm.azurewebsites.net/DebugConsole` に移動し、**LogFiles** フォルダーをクリックして、個々のログ ファイルを表示します。 **LogFiles** ディレクトリ全体をダウンロードするには、ディレクトリ名の左側にある **ダウンロード** アイコンをクリックします。 このフォルダーには、FTP クライアントを使用してアクセスすることもできます。

コンソール ターミナルでは、永続的な共有ストレージが有効になっていないため、既定では `C:\home\LogFiles` フォルダーにアクセスできません。 コンソール ターミナルでこの動作を有効にするには、[永続的な共有ストレージを有効にします](#use-persistent-shared-storage)。

FTP クライアントを使用して現在使用中の Docker ログをダウンロードしようとすると、ファイル ロックが原因でエラーが発生することがあります。

### <a name="with-the-kudu-api"></a>Kudu API を使用する

`https://<app-name>.scm.azurewebsites.net/api/logs/docker` に直接移動して、Docker ログのメタデータを表示します。 複数のログ ファイルが表示される場合があります。また、`href` プロパティを使用すると、ログ ファイルを直接ダウンロードできます。 

すべてのログを 1 つの ZIP ファイルにまとめてダウンロードするには、`https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip` にアクセスします。

## <a name="customize-container-memory"></a>コンテナー メモリをカスタマイズする

既定では Azure App Service にデプロイされるすべての Windows コンテナーは、1 GB の RAM に制限されます。 この値を変更するには、[Cloud Shell](https://shell.azure.com) を使用して `WEBSITE_MEMORY_LIMIT_MB` アプリ設定を指定します。 Bash では次のとおりです。

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_MEMORY_LIMIT_MB=2000
```

PowerShell では次のとおりです。

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_MEMORY_LIMIT_MB"=2000}
```

この値は MB 単位で定義されており、ホストの合計物理メモリ以下である必要があります。 たとえば、8 GB の RAM を備えた App Service プランでは、すべてのアプリの累積合計 `WEBSITE_MEMORY_LIMIT_MB` が 8 GB を超えないようにする必要があります。 各価格レベルで使用できるメモリ量に関する情報については、「[App Service の価格](https://azure.microsoft.com/pricing/details/app-service/windows/)」の **Premium コンテナー (Windows) プラン** に関するセクションを参照してください。

## <a name="customize-the-number-of-compute-cores"></a>コンピューティング コアの数をカスタマイズする

既定では、Windows コンテナーは、選択した価格レベルで使用できるすべてのコアで実行されます。 たとえば、ステージング スロットで使用されるコアの数を減らすことができます。 コンテナーによって使用されるコアの数を減らすには、`WEBSITE_CPU_CORES_LIMIT` アプリ設定を、希望するコア数に設定します。 これは、[Cloud Shell](https://shell.azure.com) を使用して設定できます。 Bash では次のとおりです。

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --slot staging --settings WEBSITE_CPU_CORES_LIMIT=1
```

PowerShell では次のとおりです。

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_CPU_CORES_LIMIT"=1}
```

> [!NOTE]
> アプリ設定を更新すると、自動再起動がトリガーされ、ダウンタイムが最小限に抑えられます。 運用アプリの場合は、それをステージング スロットに入れ替え、ステージング スロットでアプリ設定を変更してから、運用環境に戻すことを検討してください。

Kudu コンソール (`https://<app-name>.scm.azurewebsites.net`) に移動して、PowerShell を使用して次のコマンドを入力し、調整された数を確認します。 各コマンドは数値を出力します。

```PowerShell
Get-ComputerInfo | ft CsNumberOfLogicalProcessors # Total number of enabled logical processors. Disabled processors are excluded.
Get-ComputerInfo | ft CsNumberOfProcessors # Number of physical processors.
```

プロセッサは、マルチコアまたはハイパースレッディングのプロセッサにすることができます。 各価格レベルで使用できるコア数に関する情報については、「[App Service の価格](https://azure.microsoft.com/pricing/details/app-service/windows/)」の **Premium コンテナー (Windows) プラン** に関するセクションを参照してください。

## <a name="customize-health-ping-behavior"></a>正常性 ping の動作をカスタマイズする

App Service では、コンテナーが開始して HTTP ping に応答すると、コンテナーが正常に開始したと見なされます。 正常性 ping 要求には、ヘッダー `User-Agent= "App Service Hyper-V Container Availability Check"` が含まれています。 コンテナーが起動したが、一定の時間が経過しても ping に応答しない場合、App Service は、コンテナーが起動しなかったことを示すイベントを Docker ログに記録します。 

アプリケーションがリソースを大量に消費する場合、コンテナーは時間内に HTTP ping に応答しない可能性があります。 HTTP ping が失敗したときのアクションを制御するには、`CONTAINER_AVAILABILITY_CHECK_MODE` アプリ設定を指定します。 これは、[Cloud Shell](https://shell.azure.com) を使用して設定できます。 Bash では次のとおりです。

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings CONTAINER_AVAILABILITY_CHECK_MODE="ReportOnly"
```

PowerShell では次のとおりです。

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"CONTAINER_AVAILABILITY_CHECK_MODE"="ReportOnly"}
```

指定可能な値を次の表に示します。

| [値] | 説明 |
| - | - |
| **修復** | 3 回連続して可用性チェックを実行した後にコンテナーを再起動する |
| **ReportOnly** | 既定値。 コンテナーを再起動しないが、3 回連続して可用性チェックを実行した後、そのコンテナーについて Docker ログに報告します。 |
| "**オフ**" | 可用性を確認しません。 |

## <a name="support-for-group-managed-service-accounts"></a>グループの管理されたサービス アカウントのサポート

グループの管理されたサービス アカウント (gMSA) は現在、App Service の Windows コンテナーではサポートされていません。

::: zone-end

::: zone pivot="container-linux"

## <a name="enable-ssh"></a>SSH を有効にする

SSH では、コンテナーとクライアント間の通信をセキュリティで保護できます。 カスタム コンテナーで SSH をサポートするために、Docker イメージ自体に追加する必要があります。

> [!TIP]
> App Service 内のすべての組み込み Linux コンテナーは、そのイメージ リポジトリ内に SSH 命令を追加しました。 [Node.js 10.14 リポジトリ](https://github.com/Azure-App-Service/node/blob/master/10.14)で次のとおり実行し、それをそこで有効にする方法を表示できます。 Node.js 組み込みイメージでの構成は少し異なりますが、原則は同じです。

- 次の例のように、 [sshd_config ファイル](https://man.openbsd.org/sshd_config)をリポジトリに追加します。

    ```
    Port            2222
    ListenAddress       0.0.0.0
    LoginGraceTime      180
    X11Forwarding       yes
    Ciphers aes128-cbc,3des-cbc,aes256-cbc,aes128-ctr,aes192-ctr,aes256-ctr
    MACs hmac-sha1,hmac-sha1-96
    StrictModes         yes
    SyslogFacility      DAEMON
    PasswordAuthentication  yes
    PermitEmptyPasswords    no
    PermitRootLogin     yes
    Subsystem sftp internal-sftp
    ```

    > [!NOTE]
    > このファイルは OpenSSH を構成するものです。ファイル内には、次の項目を含める必要があります。
    > - `Port` は 2222 に設定されている必要があります。
    > - `Ciphers` には、`aes128-cbc,3des-cbc,aes256-cbc` の項目を少なくとも 1 つ含める必要があります。
    > - `MACs` には、`hmac-sha1,hmac-sha1-96` の項目を少なくとも 1 つ含める必要があります。

- Dockerfile で、次のコマンドを追加します。

    ```Dockerfile
    # Install OpenSSH and set the password for root to "Docker!". In this example, "apk add" is the install instruction for an Alpine Linux-based image.
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 

    # Copy the sshd_config file to the /etc/ssh/ directory
    COPY sshd_config /etc/ssh/

    # Open port 2222 for SSH access
    EXPOSE 80 2222
    ```

    この構成は、コンテナーへの外部接続を許可しません。 コンテナーのポート 2222 は、プライベート仮想ネットワークのブリッジ ネットワーク内でのみアクセスでき、インターネット上の攻撃者からはアクセスできません。

- コンテナーのスタートアップ スクリプトで、SSH サーバーを起動します。

    ```bash
    /usr/sbin/sshd
    ```

## <a name="access-diagnostic-logs"></a>診断ログにアクセスする

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="configure-multi-container-apps"></a>複数コンテナー アプリを構成する

- [Docker Compose で永続的ストレージを使用する](#use-persistent-storage-in-docker-compose)
- [プレビューの制限事項](#preview-limitations)
- [Docker Compose のオプション](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Docker Compose で永続的なストレージを使用する

WordPress のような複数コンテナー アプリでは、永続的ストレージが適切に機能する必要があります。 有効にするには、Docker Compose 構成が、コンテナー *外* の保存場所を指す必要があります。 コンテナー内部の保存場所では、アプリの再起動後に変更内容が保持されません。

[Cloud Shell](https://shell.azure.com) で [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) コマンドを使用して、`WEBSITES_ENABLE_APP_SERVICE_STORAGE` アプリ設定を指定することで、永続的ストレージを有効にします。

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

*docker-compose.yml* ファイルで、`volumes` オプションを `${WEBAPP_STORAGE_HOME}` にマップします。 

`WEBAPP_STORAGE_HOME` は、アプリの永続的なストレージにマップされる App Service の環境変数です。 次に例を示します。

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>プレビューの制限事項

複数コンテナーは現在プレビュー段階です。 次の App Service プラットフォーム機能はサポートされません。

- 認証/認可
- マネージド ID
- CORS

### <a name="docker-compose-options"></a>Docker Compose のオプション

以下の一覧は、Docker Compose 構成オプションでサポートされているものとサポートされていないものを示しています。

#### <a name="supported-options"></a>サポートされているオプション

- command
- entrypoint
- 環境
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>サポートされていないオプション

- build (禁止)
- depends_on (無視)
- networks (無視)
- secrets (無視)
- ports (80 および 8080 以外) (無視)

> [!NOTE]
> パブリック プレビューでは、ここで明示的に示されていないその他のオプションが無視されます。

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル: カスタム コンテナーを使用してカスタム ソフトウェアを Azure App Service に移行する](tutorial-custom-container.md)

::: zone pivot="container-linux"

> [!div class="nextstepaction"]
> [チュートリアル:マルチコンテナーの WordPress アプリ](tutorial-multi-container-app.md)

::: zone-end

または、その他のリソースを参照してください:

[Windows/Linux コンテナーで証明書を読み込む](configure-ssl-certificate-in-code.md#load-certificate-in-linuxwindows-containers)
