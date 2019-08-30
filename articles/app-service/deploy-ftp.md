---
title: FTP/S を使用してコンテンツをデプロイする - Azure App Service | Microsoft Docs
description: FTP または FTPS を使用して Azure App Service にアプリをデプロイする方法について説明します。
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/30/2018
ms.author: cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: f239fb4464d2e7f67b91a2d18c21b089ffd52b27
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070500"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>FTP/S を使用した Azure App Service へのアプリのデプロイ

この記事では、FTP または FTPS を使用して、Web アプリ、モバイル アプリ バックエンド、または API アプリを [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) にデプロイする方法について説明します。

アプリの FTP/S エンドポイントは既にアクティブです。 FTP/S デプロイを有効にするための構成は必要ありません。

## <a name="open-ftp-dashboard"></a>FTP ダッシュボードを開く

[Azure Portal](https://portal.azure.com) でアプリの[リソース ページ](../azure-resource-manager/manage-resources-portal.md#manage-resources)を開きます。

FTP ダッシュボードを開くには、 **[デプロイ センター]**  >  **[FTP]**  >  **[ダッシュボード]** の順にクリックします。

![FTP ダッシュボードを開く](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>FTP の接続情報を取得する

FTP ダッシュボードで、 **[コピー]** をクリックして、FTPS エンドポイントとアプリの資格情報をコピーします。

![FTP 情報のコピー](./media/app-service-deploy-ftp/ftp-dashboard.png)

アプリごとに一意であるため、**アプリの資格情報**を使用してデプロイすることをお勧めします。 ただし、 **[ユーザーの資格情報]** をクリックした場合は、サブスクリプション内のすべての App Service アプリへの FTP/S のログインで使用できるユーザー レベルの資格情報を設定できます。

> [!NOTE]
> ユーザーレベルの資格情報を使用した FTP または FTPS エンドポイントの認証には、次の形式のユーザー名が必要です。 
>
>`<app-name>\<user-name>`
>
> ユーザーレベルの資格情報は、特定のリソースではなく、ユーザーにリンクされているため、適切なアプリのエンドポイントに対してサインイン アクションを実行するには、ユーザー名はこの形式でなければなりません。
>

## <a name="deploy-files-to-azure"></a>ファイルを Azure にデプロイする

1. FTP クライアント ([Visual Studio](https://www.visualstudio.com/vs/community/)、[Cyberduck](https://cyberduck.io/)、[WinSCP](https://winscp.net/index.php) など) から、収集した接続情報を使用してアプリに接続します。
2. ファイルとそれぞれのディレクトリ構造を、Azure の [ **/site/wwwroot** ディレクトリ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (WebJobs の場合は **/site/wwwroot/App_Data/Jobs/** ディレクトリ) にコピーします。
3. アプリの URL を参照して、アプリが正しく動作していることを確認します。 

> [!NOTE] 
> [git ベース デプロイ](deploy-local-git.md)とは異なり、FTP デプロイでは、次のデプロイ自動化はサポートされません。 
>
> - 依存関係の復元 (NuGet、NPM、PIP、Composer の自動化など)
> - .NET バイナリのコンパイル
> - web.config の生成 ([Node.js の例はこちら](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> ローカル コンピューターでこれらの必要なファイルを手動生成した後、アプリと共にデプロイします。
>

## <a name="enforce-ftps"></a>FTPS を強制する

セキュリティを強化するには、FTP over SSL のみを許可する必要があります。 FTP デプロイを使用していない場合は、FTP と FTPS の両方を無効にすることもできます。

[Azure Portal](https://portal.azure.com) 上のアプリのリソース ページで、左側のナビゲーションの **[アプリの設定]** を選択します。

暗号化されていない FTP を無効にするには、 **[FTPS only]\(FTPS のみ\)** を選択します。 FTP および FTPS の両方を完全に無効にするには、 **[無効にする]** を選択します。 完了したら、 **[保存]** をクリックします。 **[FTPS のみ]** を使用する場合は、Web アプリの **[SSL 設定]** ブレードに移動して TLS 1.2 以降を強制的に使用する必要があります。 TLS 1.0 と 1.1 は、 **[FTPS のみ]** ではサポートされません。

![FTP/S の無効化](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>スクリプトで自動化する

[Azure CLI](/cli/azure) を使用した FTP の展開については、[Web アプリの作成と FTP を使用したファイルの展開 (Azure CLI)](./scripts/cli-deploy-ftp.md) に関する記事を参照してください。

[Azure PowerShell](/cli/azure)を使用した FTP のデプロイについては、「[FTP を使用して Web アプリにファイルをアップロードする (PowerShell)](./scripts/powershell-deploy-ftp.md)」を参照してください。

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>FTP デプロイのトラブルシューティング

- [FTP デプロイをトラブルシューティングするには、どうすればよいですか。](#how-can-i-troubleshoot-ftp-deployment)
- [FTP 接続してコードを発行できません。問題を解決するには、どうすればよいですか。](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [パッシブ モードを使用して Azure App Service で FTP に接続するには、どうすればよいですか。](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>FTP デプロイをトラブルシューティングするには、どうすればよいですか。

FTP デプロイのトラブルシューティングを行うための最初の手順は、デプロイの問題を、ランタイム アプリケーションの問題から切り離すことです。

デプロイの問題が発生すると、通常、アプリにファイルがデプロイされなかったり、誤ったファイルがデプロイされたりします。 FTP のデプロイを調べるか、代替デプロイ パス (ソース コントロールなど) を選択することで、トラブルシューティングを行うことができます。

ランタイム アプリケーションの問題が発生した場合は、通常、お使いのアプリに正しいファイル セットがデプロイされますが、アプリが適切に動作しません。 実行時のコードの動作に注目して特定のエラーのパスを調べることで、トラブルシューティングを行うことができます。

デプロイの問題かランタイムの問題かを判断するには、「[Deployment vs. runtime issues (デプロイの問題とランタイムの問題)](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues)」を参照してください。

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>FTP 接続してコードを発行できません。 問題を解決するには、どうすればよいですか。
入力したホスト名と[資格情報](#open-ftp-dashboard)が正しいかどうかを確認してください。 また、使用しているマシン上の次の FTP ポートが、ファイアウォールによってブロックされていないことも確認します。

- FTP コントロール接続ポート: 21
- FTP データ接続ポート: 989、10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>パッシブ モードを使用して Azure App Service で FTP に接続するには、どうすればよいですか。
Azure App Service では、アクティブ モードとパッシブ モードの両方を使用した接続がサポートされます。 お使いのデプロイ マシンは、通常、(オペレーティング システム内の、またはホーム ネットワークまたはビジネス ネットワークの一部として) ファイアウォールの内側にあるため、パッシブ モードをお勧めします。 [WinSCP ドキュメントに記載された使用例](https://winscp.net/docs/ui_login_connection)をご覧ください。 

## <a name="next-steps"></a>次の手順

高度なデプロイ シナリオの詳細については、[Git を使用した Azure へのデプロイ](deploy-local-git.md)に関するページをご覧ください。 Azure への Git ベース デプロイでは、バージョン管理、パッケージの復元、MSBuild などが可能です。

## <a name="more-resources"></a>その他のリソース

* [Azure App Service のデプロイ資格情報](deploy-configure-credentials.md)
