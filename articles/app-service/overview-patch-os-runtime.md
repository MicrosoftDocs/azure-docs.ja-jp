---
title: OS とランタイムのパッチ適用周期 - Azure App Service | Microsoft Docs
description: Azure App Service で OS とランタイムが更新される方法と、更新プログラムのお知らせを取得する方法について説明します。
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 576627c96b19dd3563ab21a5d478b779e4a3ed64
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53730554"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>Azure App Service での OS とランタイムのパッチ適用

この記事では、[App Service](overview.md) で OS またはソフトウェアに関する特定のバージョン情報を取得する方法について説明します。 

App Service はサービスとしてのプラットフォームです。これは、OS およびアプリケーション スタックが Azure によって自動的に管理されることを意味します。ユーザーが管理するのはアプリケーションとそのデータのみです。 [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/) では、OS およびアプリケーション スタックをより詳細に制御できます。 このことを念頭に置いても、App Service ユーザーが次のような情報を把握することは役に立ちます。

-   OS の更新プログラムが適用される方法と時期
-   App Service で重大な脆弱性 (ゼロデイなど) に対してパッチが適用される方法
-   アプリを実行している OS およびランタイムのバージョン

セキュリティ上の理由から、セキュリティ情報の特定の詳細は公開されません。 しかし、この記事は、プロセスの透明性を最大限に高めて不安を緩和することを目的としています。この記事では、セキュリティ関連のお知らせまたはランタイムの更新について最新情報を確認する方法について説明します。

## <a name="how-and-when-are-os-updates-applied"></a>OS の更新プログラムが適用される方法と時期

Azure では、2 つのレベルで OS のパッチ適用が管理されます。1 つが物理サーバーで、もう 1 つが App Service リソースを実行するゲスト仮想マシン (VM) です。 どちらも、月例[パッチ火曜日](https://technet.microsoft.com/security/bulletins.aspx)スケジュールに従って毎月更新されます。 これらの更新プログラムは、Azure サービスの高可用性 SLA を保証する方法で、自動的に適用されます。 

更新プログラムが適用される方法の詳細については、「[Demystifying the magic behind App Service OS updates (App Service OS 更新プログラムの背後にあるマジックをわかりやすく説明する)](https://blogs.msdn.microsoft.com/appserviceteam/2018/01/18/demystifying-the-magic-behind-app-service-os-updates/)」を参照してください。

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Azure が重大な脆弱性に対処する方法

[ゼロデイ脆弱性](https://wikipedia.org/wiki/Zero-day_(computing))など、深刻な脆弱性に対して迅速にパッチを適用する必要がある場合、ケースバイケースで優先度の高い更新プログラムが処理されます。

[Azure セキュリティ ブログ](https://azure.microsoft.com/blog/topics/security/)を確認して、Azure のセキュリティに関する重大なお知らせを常にチェックしてください。 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>サポートされる言語ランタイムの更新、追加、非推奨の時期

サポートされる言語ランタイムの新しい安定バージョン (メジャー、マイナー、またはパッチ) は、App Service インスタンスに定期的に追加されます。 一部の更新プログラムでは既存のインストールが上書きされますが、その他の更新プログラムは既存のバージョンとサイド バイ サイドでインストールされます。 上書きインストールでは、アプリは更新されたランタイムで自動的に実行されます。 サイドバイサイド インストールでは、新しいバージョンのランタイムを利用するにはアプリを手動で移行する必要があります。 詳細については、いずれかのサブセクションを参照してください。

ランタイムの更新と廃止は以下で発表されます。

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> こちらの情報は、App Service アプリに組み込まれている言語ランタイムに適用されます。 たとえば、自分で App Service にアップロードするカスタム ランタイムは、手動でアップグレードするまで変更されません。
>
>

### <a name="new-patch-updates"></a>新しいパッチ更新プログラム

.NET、PHP、Java SDK または Tomcat/Jetty バージョンに対するパッチ更新プログラムは、既存のインストールが新しいバージョンに上書きされることで、自動的に適用されます。 Node.js パッチ更新プログラムは、(次のセクションのメジャー バージョンとマイナー バージョンと同様に) 既存のバージョンとサイド バイ サイドでインストールされます。 新しい Python パッチ バージョンは、[サイト拡張機能](https://www.siteextensions.net/packages?q=Tags%3A%22python%22)を通じて手動でインストールできます。これは、組み込みの Python インストールとサイド バイ サイドでインストールされます。

### <a name="new-major-and-minor-versions"></a>新しいメジャー バージョンとマイナー バージョン

新しいメジャー バージョンまたはマイナー バージョンが追加された場合、それは既存のバージョンとサイド バイ サイドでインストールされます。 アプリを新しいバージョンに手動でアップグレードできます。 構成ファイル (`web.config` や `package.json` など) でランタイム バージョンを構成した場合、同じ方法でアップグレードする必要があります。 App Service 設定を使用してランタイム バージョンを構成した場合、[Azure Portal](https://portal.azure.com) でそれを変更できます。または、次の例に示すように、[Cloud Shell](../cloud-shell/overview.md) で [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) コマンドを実行して変更することができます。

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>非推奨のバージョン  

以前のバージョンが非推奨となる場合、それに応じてランタイム バージョンのアップグレードを計画できるように、削除日が発表されます。 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>インスタンスの OS とランタイムの更新状態を照会する方法  

OS の重要な情報へのアクセスはロックダウンされていますが (「[Azure App Service におけるオペレーティング システムの機能](operating-system-functionality.md)」を参照)、[Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) コンソールでは、App Service インスタンスの OS バージョンとランタイム バージョンを照会できます。 

次の表では、アプリを実行している Windows と言語ランタイムのバージョンを見つける方法を示します。

| 情報 | 参照先 | 
|-|-|
| Windows のバージョン | `https://<appname>.scm.azurewebsites.net/Env.cshtml` を参照します (システム情報の下) |
| .NET バージョン | `https://<appname>.scm.azurewebsites.net/DebugConsole` において、コマンド プロンプトで次のコマンドを実行します。 <br>`powershell -command "gci 'Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Net Framework Setup\NDP\CDF'"` |
| .NET Core バージョン | `https://<appname>.scm.azurewebsites.net/DebugConsole` において、コマンド プロンプトで次のコマンドを実行します。 <br> `dotnet --version` |
| PHP バージョン | `https://<appname>.scm.azurewebsites.net/DebugConsole` において、コマンド プロンプトで次のコマンドを実行します。 <br> `php --version` |
| 既定の Node.js バージョン | [Cloud Shell](../cloud-shell/overview.md) で次のコマンドを実行します。 <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Python バージョン | `https://<appname>.scm.azurewebsites.net/DebugConsole` において、コマンド プロンプトで次のコマンドを実行します。 <br> `python --version` |  

> [!NOTE]  
> レジストリの場所 `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages` にアクセスします。ここには ["KB" パッチ](https://docs.microsoft.com/security-updates/SecurityBulletins/securitybulletins)に関する情報が格納され、ロックダウンされています。
>
>

## <a name="more-resources"></a>その他のリソース

[トラスト センター:セキュリティ](https://www.microsoft.com/en-us/trustcenter/security)  
[Azure App Service 上の 64 ビット ASP.NET Core](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
