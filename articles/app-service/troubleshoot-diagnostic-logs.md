---
title: アプリの診断ログの有効化 - Azure App Service
description: 診断ログを有効にしてインストルメンテーションをアプリケーションに追加する方法と、Azure によってログ記録された情報にアクセスする方法を説明します。
services: app-service
author: cephalin
manager: gwallace
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/17/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: b0fab51e002ecb431bf68f58984290889296b2a9
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097556"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Azure App Service でのアプリの診断ログの有効化
## <a name="overview"></a>概要
Azure では、組み込みの診断機能により、 [App Service アプリ](overview.md)のデバッグを容易に行うことができます。 この記事では、診断ログを有効にしてインストルメンテーションをアプリケーションに追加する方法と、Azure によってログに記録された情報にアクセスする方法について説明します。

この記事では、[Azure portal](https://portal.azure.com) と Azure CLI を使用して診断ログを操作します。 Visual Studio で診断ログを使用する方法の詳細については、「 [Visual Studio での Azure のトラブルシューティング](troubleshoot-dotnet-visual-studio.md)」を参照してください。

|種類|プラットフォーム|Location|説明|
|-|-|-|-|
| アプリケーションのログ記録 | Windows、Linux | App Service ファイル システムおよび Azure Storage BLOB | アプリケーションのコードによって生成されたメッセージがログに記録されます。 メッセージは、選択した Web フレームワークによって、またはお使いの言語の標準ログ パターンを使用してアプリケーションのコードから直接、生成できます。 各メッセージには、次のいずれかのカテゴリが割り当てられます: **重大**、**エラー**、**警告**、**情報**、**デバッグ**、**トレース**。 アプリケーションのログ記録を有効にするときに、重大度レベルを設定することにより、ログ記録の詳細さを指定できます。|
| Web サーバーのログ記録| Windows | App Service ファイル システムまたは Azure Storage BLOB| [W3C 拡張ログ ファイル形式](/windows/desktop/Http/w3c-logging)の生 HTTP 要求データ。 各ログ メッセージには、HTTP メソッド、リソース URI、クライアント IP、クライアント ポート、ユーザー エージェント、応答コードなどのデータが含まれます。 |
| 詳細なエラー ログ記録 | Windows | App Service ファイル システム | クライアントのブラウザーに送信された *.htm* エラー ページのコピー。 セキュリティ上の理由から、詳細なエラー ページを運用環境のクライアントに送信することはできませんが、App Service では、HTTP コード 400 以上のアプリケーション エラーが発生するたびにエラー ページを保存できます。 ページには、サーバーによってエラー コードが返される理由を特定するのに役立つ情報が記録されている場合があります。 |
| 失敗した要求トレース | Windows | App Service ファイル システム | 要求の処理に使用された IIS コンポーネントのトレースや各コンポーネントにかかった時間など、失敗した要求の詳細なトレース情報。 これは、サイトのパフォーマンスを向上させたり、特定の HTTP エラーを分離したりする場合に役立ちます。 失敗した要求ごとに 1 つのフォルダーが生成され、それには、XML ログ ファイルと、ログ ファイルを表示するための XSL スタイルシートが含まれます。 |
| デプロイ ログ | Windows、Linux | App Service ファイル システム | アプリにコンテンツを発行するときのログ。 デプロイ ログの記録は自動的に行われ、デプロイ ログの構成可能な設定はありません。 デプロイが失敗した理由を判断するのに役立ちます。 たとえば、[カスタム デプロイ スクリプト](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)を使用している場合は、デプロイ ログを使用して、スクリプトでエラーが発生する理由を特定できることがあります。 |

> [!NOTE]
> App Service では、アプリケーションのトラブルシューティングに役立つ専用の対話型診断ツールが提供されています。 詳細については、「[Azure App Service 診断の概要](overview-diagnostics.md)」を参照してください。
>
> さらに、[Azure Monitor](../azure-monitor/app/azure-web-apps.md) などの他の Azure サービスを使用して、アプリのログ記録と監視の機能を向上させることができます。
>

## <a name="enable-application-logging-windows"></a>アプリケーションのログ記録を有効にする (Windows)

[Azure portal](https://portal.azure.com) で Windows アプリのアプリケーション ログ記録を有効にするには、アプリに移動し、 **[App Service ログ]** を選択します。

**[アプリケーション ログ (ファイル システム)]** と **[アプリケーション ログ (Blob)]** の一方または両方で、 **[オン]** を選択します。 

**ファイル システム** オプションは、一時的なデバッグ用であり、12 時間で自動的にオフになります。 **Blob** オプションは、長期的なログ記録用であり、ログを書き込む BLOB ストレージ コンテナーが必要です。  **Blob** オプションには、ログ メッセージの生成元 VM インスタンスの ID (`InstanceId`)、スレッド ID (`Tid`)、さらに細かいタイムスタンプ ([`EventTickCount`](https://docs.microsoft.com/dotnet/api/system.datetime.ticks)) など、ログ メッセージの追加情報も含まれます。

> [!NOTE]
> 現在、Blob Storage には .NET アプリケーションのログのみ書き込むことができます。 Java、PHP、Node.js、Python のアプリケーション ログは、App Service ファイル システムにのみ保存できます (外部ストレージにログを書き込むためのコードの変更は必要ありません)。
>
> また、[ストレージ アカウントのアクセス キーを再生成する](../storage/common/storage-create-storage-account.md)場合、更新されたキーを使用するように、該当するログ記録の構成を再設定する必要があります。 これを行うには、次の手順を実行します。
>
> 1. **[構成]** タブで、該当するログ機能を **[オフ]** に設定します。 設定を保存します。
> 2. ストレージ アカウントの BLOB へのログを再び有効にします。 設定を保存します。
>
>

ログに記録する詳細さの**レベル**を選択します。 次の表に、各レベルに含まれるログのカテゴリを示します。

| Level | 含まれるカテゴリ |
|-|-|
|**Disabled** | なし |
|**Error** | エラー、クリティカル |
|**Warning** | 警告、エラー、クリティカル|
|**情報** | 情報、警告、エラー、クリティカル|
|**Verbose** | トレース、デバッグ、情報、警告、エラー、クリティカル (すべてのカテゴリ) |

終わったら、 **[保存]** を選択します。

## <a name="enable-application-logging-linuxcontainer"></a>アプリケーションのログ記録を有効にする (Linux/コンテナー)

[Azure portal](https://portal.azure.com) で Linux アプリまたはカスタム コンテナー アプリのアプリケーション ログ記録を有効にするには、アプリに移動し、 **[App Service ログ]** を選択します。

**[アプリケーション ログ記録]** で **[ファイル システム]** を選択します。

**[クォータ (MB)]** で、アプリケーション ログのディスク クォータを指定します。 **[リテンション期間 (日)]** で、ログを保持する日数を設定します。

終わったら、 **[保存]** を選択します。

## <a name="enable-web-server-logging"></a>Web サーバーのログ記録を有効にする

[Azure portal](https://portal.azure.com) で Windows アプリの Web サーバー ログ記録を有効にするには、アプリに移動し、 **[App Service ログ]** を選択します。

**[Web サーバーのログ記録]** で、Blob Storage にログを格納する場合は **[ストレージ]** を選択し、App Service ファイル システムにログを格納する場合は **[ファイル システム]** を選択します。 

**[リテンション期間 (日)]** で、ログを保持する日数を設定します。

> [!NOTE]
> [ストレージ アカウントのアクセス キーを再生成する](../storage/common/storage-create-storage-account.md)場合は、該当するログ構成を更新後のキーを使用するように設定し直す必要があります。 これを行うには、次の手順を実行します。
>
> 1. **[構成]** タブで、該当するログ機能を **[オフ]** に設定します。 設定を保存します。
> 2. ストレージ アカウントの BLOB へのログを再び有効にします。 設定を保存します。
>
>

終わったら、 **[保存]** を選択します。

## <a name="log-detailed-errors"></a>詳細なエラーのログを記録する

[Azure portal](https://portal.azure.com) で Windows アプリのエラー ページまたは失敗した要求のトレースを保存するには、アプリに移動し、 **[App Service ログ]** を選択します。

**[Detailed Error Logging]\(詳細なエラー ログ記録\)** または **[失敗した要求のトレース]** で、 **[オン]** を選択し、 **[保存]** を選択します。

どちらの種類のログも、App Service ファイル システムに格納されます。 最大 50 件のエラー (ファイル/フォルダー) が保持されます。 HTML ファイルの数が 50 を超えた場合、古い順に 26 エラーが自動的に削除されます。

## <a name="add-log-messages-in-code"></a>コードでログ メッセージを追加する

アプリケーションのコードでは、通常のログ記録機能を使用して、ログ メッセージをアプリケーション ログに送信します。 例:

- ASP.NET アプリケーションは、 [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) クラスを使用して、情報をアプリケーション診断ログに記録できます。 例:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- ASP.NET Core では、既定で、[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) ログ プロバイダーが使用されます。 詳しくは、[Azure 内での ASP.NET Core のログ記録](https://docs.microsoft.com/aspnet/core/fundamentals/logging/)に関するページをご覧ください。

## <a name="stream-logs"></a>ログのストリーミング

ログをリアルタイムでストリーミングする前に、目的のログの種類を有効にします。 */LogFiles* ディレクトリ (d:/home/logfiles) に格納されており、末尾が .txt、.log、.htm のいずれかになっているファイルに書き込まれた情報が、App Service によってストリーミングされます。

> [!NOTE]
> 一部の種類のログ バッファーはログ ファイルに書き込まれるため、ストリーミング中に無効な順序エラーが発生する可能性があります。 たとえば、ユーザーがページにアクセスしたときに発生するアプリケーション ログ エントリは、ページ要求の該当する HTTP ログ エントリより前のストリームに表示されることがあります。
>

### <a name="in-azure-portal"></a>Azure Portal

[Azure portal](https://portal.azure.com) でログをストリーミングするには、アプリに移動し、 **[ログ ストリーム]** を選択します。 

### <a name="in-cloud-shell"></a>Cloud Shell の場合

[Cloud Shell](../cloud-shell/overview.md) でログをライブ ストリーミングするには、次のコマンドを使用します。

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

特定のイベント (エラーなど) をフィルター処理するには、 **-Filter** パラメーターを使用します。 例:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --filter Error
```
特定のログの種類 (HTTP など) をフィルター処理するには、 **--Path** パラメーターを使用します。 例:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --path http
```

### <a name="in-local-terminal"></a>ローカル ターミナルの場合

ローカル コンソールでログをストリーミングするには、[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) をインストールし、[アカウントにサインイン](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)します。 サインインした後、[Cloud Shell の手順](#in-cloud-shell)に従います

## <a name="access-log-files"></a>アクセス ログ ファイル

ログの種類に Azure Storage BLOB オプションを構成する場合は、Azure Storage で使用できるクライアント ツールが必要です。 詳しくは、「[Azure Storage クライアント ツール](../storage/common/storage-explorers.md)」をご覧ください。

App Service ファイル システムに格納されているログの場合に最も簡単な方法は、次の場所にある ZIP ファイルをブラウザーでダウンロードすることです。

- Linux/コンテナー アプリ: `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Windows アプリ: `https://<app-name>.scm.azurewebsites.net/api/dump`

Linux/コンテナー アプリの場合、ZIP ファイルには、Docker ホストと Docker コンテナー両方のコンソール出力ログが含まれます。 スケールアウトされたアプリの場合、ZIP ファイルには、各インスタンスの 1 セットのログが含まれます。 App Service ファイル システムでは、これらのログ ファイルは */home/LogFiles* ディレクトリの内容です。

Windows アプリの場合、ZIP ファイルには、App Service ファイル システムの *D:\Home\LogFiles* ディレクトリの内容が含まれています。 その構造を次に示します。

| ログのタイプ | Directory | 説明 |
|-|-|-|
| **アプリケーション ログ** |*/LogFiles/Application/* | 1 つ以上のテキスト ファイルが含まれます。 ログ メッセージの形式は、使用するログ プロバイダーによって異なります。 |
| **失敗した要求のトレース** | */LogFiles/W3SVC#########/* | XML ファイルと XSL ファイルが含まれます。 書式設定された XML ファイルをブラウザーで表示できます。 |
| **詳細なエラー ログ** | */LogFiles/DetailedErrors/* | HTM エラー ファイルが含まれます。 ブラウザーで HTM ファイルを表示できます。<br/>失敗した要求のトレースを表示するもう 1 つの方法は、ポータルでアプリのページに移動します。 左側のメニューから、 **[問題の診断と解決]** を選択し、 **[Failed Request Tracing Logs]\(失敗した要求トレースのログ\)** を検索し、アイコンをクリックして目的のトレースを参照して表示します。 |
| **Web サーバー ログ** | */LogFiles/http/RawLogs/* | [W3C 拡張ログ ファイル形式](/windows/desktop/Http/w3c-logging)を使用して書式設定されたテキスト ファイルが含まれます。 この情報は、テキスト エディターまたは [Log Parser](https://go.microsoft.com/fwlink/?LinkId=246619) などのユーティリティを使用して読むことができます。<br/>App Service では、`s-ip`、`s-computername`、または `cs-version` フィールドはサポートされていません。 |
| **デプロイ ログ** | */LogFiles/Git/* および */deployments/* | 内部デプロイ プロセスによって生成されたログだけでなく、Git デプロイのログも含まれます。 |

## <a name="nextsteps"></a> 次のステップ
* [Azure App Service を監視する方法](web-sites-monitor.md)
* [Visual Studio での Azure App Service のトラブルシューティング](troubleshoot-dotnet-visual-studio.md)
* [HDInsight でのアプリ ログの分析](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
