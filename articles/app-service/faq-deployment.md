---
title: デプロイに関する FAQ - Azure App Service | Microsoft Docs
description: Azure App Service の Web Apps 機能のデプロイに関するよくあるご質問への回答を掲載しています。
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 163a6940e50d1f8beacc23855fd1e6f9daad0085
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88080475"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Azure の Web Apps のデプロイに関するよくあるご質問

この記事では、[Azure App Service の Web Apps 機能](https://azure.microsoft.com/services/app-service/web/)のデプロイの問題に関するよくあるご質問 (FAQ) への回答を掲載しています。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>私は App Service の Web Apps を使い始めたばかりです。 コードを発行するにはどうすればよいですか。

Web アプリのコードを発行する方法はいくつかあります。

*   Visual Studio を使用してデプロイする。 Visual Studio ソリューションをお持ちの場合、Web アプリケーション プロジェクトを右クリックして **[発行]** を選択します。
*   FTP クライアントを使用してデプロイする。 Azure Portal で、コードをデプロイする Web アプリの発行プロファイルをダウンロードします。 次に、同じ発行プロファイルの FTP 資格情報を使用して、\site\wwwroot にファイルをアップロードします。

詳細については、[App Service へのアプリのデプロイ](deploy-local-git.md)に関するページをご覧ください。

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this-error"></a>Visual Studio からデプロイしようとするとエラー メッセージが表示されます。 このエラーを解決するにはどうすればよいですか?

次のメッセージが表示される場合、古いバージョンの SDK を使用している可能性があります。「リソース グループ 'リソース グループ名' のリソース 'リソース名' の配置の際にエラーが発生しました: 。MissingRegistrationForLocation: サブスクリプションが場所 '米国中部' のリソースの種類 'コンポーネント' に登録されていません。 この場所にアクセスできるようにするには、このプロバイダーに再登録してください。" 

このエラーを解決するには、[最新の SDK](https://azure.microsoft.com/downloads/) にアップグレードしてください。 このメッセージが表示され、かつ最新の SDK を使用している場合は、サポート リクエストを送信してください。

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Visual Studio から App Service に ASP.NET アプリケーションをデプロイするにはどうすればよいですか。
<a id="deployasp"></a>

[5 分で初めての ASP.NET Web アプリを Azure に作成](quickstart-dotnetcore.md)のチュートリアルで、Visual Studio を使用して ASP.NET Web アプリケーションを App Service の Web アプリにデプロイする方法を説明しています。

## <a name="what-are-the-different-types-of-deployment-credentials"></a>デプロイ資格情報の種類にはどのようなものがありますか。

App Service では、ローカル Git デプロイと FTP/S デプロイ用の 2 種類の資格情報をサポートしています。 デプロイ資格情報を構成する方法の詳細については、[App Service のデプロイ資格情報の構成](deploy-configure-credentials.md)に関するページをご覧ください。

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>App Service の Web アプリのファイルまたはディレクトリ構造はどのようなものですか。

App Service アプリのファイル構造については、[Azure でのファイル構造](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure)に関するページをご覧ください。

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>ファイルを FTP しようとすると「FTP エラー 550 - ディスクに十分な領域がありません」というエラーが発生しますが、解決するにはどうすればよいですか。

このメッセージが表示される場合、Web アプリのサービス プランのディスク クォータに達している可能性があります。 ディスク領域の必要性に応じて、上位のサービス レベルにスケール アップする必要がある場合があります。 料金プランとリソース制限の詳細については、「[App Service の価格](https://azure.microsoft.com/pricing/details/app-service/)」をご覧ください。

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>App Service の Web アプリの継続的デプロイを設定するにはどうすればよいですか。

継続的デプロイは、Azure DevOps、OneDrive、GitHub、Bitbucket、Dropbox、およびその他の Git リポジトリなど、複数のリソースから設定できます。 これらのオプションは、ポータルで確認できます。 [App Service への継続的デプロイ](deploy-continuous-deployment.md)に関するチュートリアルで継続的デプロイの設定方法を説明しているので、お役立てください。

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>GitHub や Bitbucket からの継続的デプロイに関する問題をトラブルシューティングするにはどうすればよいですか。

GitHub や Bitbucket からの継続的デプロイに関する問題の調査については、「[Investigating continuous deployment (継続的デプロイの調査)](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)」をご覧ください。

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this-issue"></a>自分のサイトに FTP できず、コードを発行できません。 この問題を解決するにはどうすればよいですか?

FTP の問題を解決するには、次のことを行います。

1. 正しいホスト名と資格情報を入力していることを確認します。 さまざまな種類の資格情報とその使用方法の詳細については、「[Deployment credentials (デプロイ資格情報)](https://github.com/projectkudu/kudu/wiki/Deployment-credentials)」をご覧ください。
2. FTP ポートがファイアウォールによってブロックされていないことを確認します。 ポートには次の設定がされている必要があります。
    * FTP コントロール接続ポート: 21
    * FTP データ接続ポート: 989、10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>App Service にコードを発行するにはどうすればよいですか。

Azure クイック スタートは、お好きなデプロイ スタックと方法を使用してアプリをデプロイできるように設計されています。 クイック スタートを使うには、Azure portal で App Service に移動し、**[展開]** の **[クイック スタート]** を選択します。

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>App Service へのデプロイ後にアプリが再起動することがありますが、これはなぜですか。

アプリのデプロイによって再起動が発生する状況については、「[Deployment vs. runtime issues (デプロイとランタイムの問題)](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts")」をご覧ください。 記事で説明しているとおり、App Service は wwwroot フォルダーにファイルをデプロイします。 アプリを直接再起動することはありません。

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>Azure DevOps のコードを App Service に統合するにはどうすればよいですか。

Azure DevOps で継続的デプロイを使用するには、2 つの方法があります。

*   Git プロジェクトを使用する。 デプロイ センターを使用して、App Service 経由で接続します。
*   Team Foundation バージョン管理 (TFVC) プロジェクトを使用する。 App Service のビルド エージェントを使用してデプロイします。

どちらの方法でも、コードの継続的デプロイは既存の開発者ワークフローとチェックイン プロシージャに依存します。 詳細と例については、次の記事をご覧ください。 

*   [アプリの継続的デプロイを Azure Web サイトに実装する](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Set up an Azure DevOps organization so it can deploy to a web app](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)\(Web アプリにデプロイできるよう Azure DevOps 組織をセットアップする\)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>FTP または FTPS を使用してアプリを App Service にデプロイするにはどうすればよいですか。

FTP または FTPS を使用して Web アプリを App Service にデプロイする方法については、[FTP/S を使用した App Service へのアプリのデプロイ](deploy-ftp.md)に関するページをご覧ください。
