---
title: FTP/S を使用したコンテンツのデプロイ
description: FTP または FTPS を使用して Azure App Service にアプリをデプロイする方法について説明します。 暗号化されていない FTP を無効にして Web サイトのセキュリティを強化します。
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 02/26/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: c7427a1f8f528fdf405b22c4e91941ea7a915ffa
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045804"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>FTP/S を使用した Azure App Service へのアプリのデプロイ

この記事では、FTP または FTPS を使用して、Web アプリ、モバイル アプリ バックエンド、または API アプリを [Azure App Service](./overview.md) にデプロイする方法について説明します。

アプリの FTP/S エンドポイントは既にアクティブです。 FTP/S デプロイを有効にするための構成は必要ありません。

> [!NOTE]
> Azure portal の **[開発センター (クラシック)]** ページ (以前のデプロイ エクスペリエンス) は、2021 年 3 月に非推奨となる予定です。 この変更はアプリの既存のデプロイ設定には影響せず、 **[デプロイ センター]** ページで引き続きアプリのデプロイを管理できます。

## <a name="get-deployment-credentials"></a>デプロイ資格情報を取得する

1. 「[Azure App Service のデプロイ資格情報の構成](deploy-configure-credentials.md)」の指示に従って、アプリケーション スコープの資格情報をコピーするか、ユーザー スコープの資格情報を設定します。 いずれかの資格情報を使用して、アプリの FTP/S エンドポイントに接続できます。

1. 選択した資格情報のスコープに応じて、次の形式で FTP ユーザー名を作成します。

    | アプリケーション スコープ | ユーザー スコープ |
    | - | - |
    |`<app-name>\$<app-name>`|`<app-name>\<deployment-user>`|

    ---

    App Service では、FTP/S エンドポイントはアプリ間で共有されます。 ユーザー スコープの資格情報は特定のリソースにリンクされていないため、前述のように、ユーザースコープのユーザー名の前にアプリ名を付加する必要があります。

## <a name="get-ftps-endpoint"></a>FTP/S エンドポイントを取得する
    
# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

デプロイ資格情報をコピーしたアプリの同じ管理ページ ( **[デプロイ センター]**  >  **[FTP 資格情報]** ) で、**FTPS エンドポイント** をコピーします。

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

[az webapp deployment list-publishing-profiles](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) コマンドを実行します。 次の例では、[JMES パス](https://jmespath.org/) を使用して、出力から FTP/S エンドポイントを抽出します。

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app-name> --resource-group <group-name> --query "[?ends_with(profileName, 'FTP')].{profileName: profileName, publishUrl: publishUrl}"
```

各アプリには、2 つの FTP/S エンドポイントがあります。1 つは読み取り/書き込みで、もう 1 つは読み取り専用 (`profileName` に `ReadOnly` が含まれます) で、データ復旧シナリオ用です。 FTP を使用してファイルをデプロイするには、読み取り/書き込みエンドポイントの URL をコピーします。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

[Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) コマンドを実行します。 次の例では、XML 出力から FTP/S エンドポイントを抽出します。

```azurepowershell-interactive
$xml = [xml](Get-AzWebAppPublishingProfile -Name <app-name> -ResourceGroupName <group-name> -OutputFile null)
$xml.SelectNodes("//publishProfile[@publishMethod=`"FTP`"]/@publishUrl").value
```

-----

## <a name="deploy-files-to-azure"></a>ファイルを Azure にデプロイする

1. FTP クライアント ([Visual Studio](https://www.visualstudio.com/vs/community/)、[Cyberduck](https://cyberduck.io/)、[WinSCP](https://winscp.net/index.php) など) から、収集した接続情報を使用してアプリに接続します。
2. ファイルとそれぞれのディレクトリ構造を、Azure の [ **/site/wwwroot** ディレクトリ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) (WebJobs の場合は **/site/wwwroot/App_Data/Jobs/** ディレクトリ) にコピーします。
3. アプリの URL を参照して、アプリが正しく動作していることを確認します。 

> [!NOTE] 
> [Git ベースのデプロイ](deploy-local-git.md)や[Zip デプロイ](deploy-zip.md)とは異なり、FTP デプロイでは次のようなビルド オートメーションはサポートされていません。 
>
> - 依存関係の復元 (NuGet、NPM、PIP、Composer の自動化など)
> - .NET バイナリのコンパイル
> - web.config の生成 ([Node.js の例はこちら](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> ローカル コンピューターでこれらの必要なファイルを手動生成した後、アプリと共にデプロイします。
>

## <a name="enforce-ftps"></a>FTPS を強制する

セキュリティを強化するには、FTP over TLS/SSL のみを許可する必要があります。 FTP デプロイを使用していない場合は、FTP と FTPS の両方を無効にすることもできます。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. [Azure portal](https://portal.azure.com) のご使用のアプリのリソース ページで、左側のナビゲーションの **[構成]**  >  **[全般設定]** を選択します。

2. 暗号化されていない FTP を無効にするには、 **[FTP state]\(FTP の状態\)** で **[FTPS のみ]** を選択します。 FTP と FTPS の両方を完全に無効にするには、 **[無効にする]** を選択します。 完了したら、 **[保存]** をクリックします。 **[FTPS のみ]** を使用する場合は、Web アプリの **[TLS/SSL 設定]** ブレードに移動して TLS 1.2 以降を適用する必要があります。 TLS 1.0 と 1.1 は、 **[FTPS のみ]** ではサポートされません。

    ![FTP/S の無効化](./media/app-service-deploy-ftp/disable-ftp.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

`--ftps-state` 引数を指定して [az webapp config set](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) コマンドを実行します。

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <group-name> --ftps-state FtpsOnly
```

`--ftps-state` に指定できる値は、`AllAllowed` (FTP および FTPS が有効)、`Disabled` (FTP および FTPS が無効)、`FtpsOnly` (FTPS のみ) です。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

`-FtpsState` パラメーターを指定して [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) コマンドを実行します。

```azurepowershell-interactive
Set-AzWebApp -Name <app-name> -ResourceGroupName <group-name> -FtpsState FtpsOnly
```

`--ftps-state` に指定できる値は、`AllAllowed` (FTP および FTPS が有効)、`Disabled` (FTP および FTPS が無効)、`FtpsOnly` (FTPS のみ) です。

-----

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>FTP デプロイのトラブルシューティング

- [FTP デプロイをトラブルシューティングするには、どうすればよいですか。](#how-can-i-troubleshoot-ftp-deployment)
- [FTP 接続してコードを発行できません。問題を解決するには、どうすればよいですか。](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [パッシブ モードを使用して Azure App Service で FTP に接続するには、どうすればよいですか。](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

#### <a name="how-can-i-troubleshoot-ftp-deployment"></a>FTP デプロイをトラブルシューティングするには、どうすればよいですか。

FTP デプロイのトラブルシューティングを行うための最初の手順は、デプロイの問題を、ランタイム アプリケーションの問題から切り離すことです。

デプロイの問題が発生すると、通常、アプリにファイルがデプロイされなかったり、誤ったファイルがデプロイされたりします。 FTP のデプロイを調べるか、代替デプロイ パス (ソース コントロールなど) を選択することで、トラブルシューティングを行うことができます。

ランタイム アプリケーションの問題が発生した場合は、通常、お使いのアプリに正しいファイル セットがデプロイされますが、アプリが適切に動作しません。 実行時のコードの動作に注目して特定のエラーのパスを調べることで、トラブルシューティングを行うことができます。

デプロイの問題かランタイムの問題かを判断するには、「[Deployment vs. runtime issues (デプロイの問題とランタイムの問題)](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues)」を参照してください。

#### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>FTP 接続してコードを発行できません。 問題を解決するには、どうすればよいですか。
入力した[ホスト名](#get-ftps-endpoint)と[資格情報](#get-deployment-credentials)が正しいことを確認してください。 また、使用しているマシン上の次の FTP ポートが、ファイアウォールによってブロックされていないことも確認します。

- FTP コントロール接続ポート: 21、990
- FTP データ接続ポート: 989、10001-10300
 
#### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>パッシブ モードを使用して Azure App Service で FTP に接続するには、どうすればよいですか。
Azure App Service では、アクティブ モードとパッシブ モードの両方を使用した接続がサポートされます。 お使いのデプロイ マシンは、通常、(オペレーティング システム内の、またはホーム ネットワークまたはビジネス ネットワークの一部として) ファイアウォールの内側にあるため、パッシブ モードをお勧めします。 [WinSCP ドキュメントに記載された使用例](https://winscp.net/docs/ui_login_connection)をご覧ください。 

## <a name="more-resources"></a>その他のリソース

* [Azure App Service へのローカル Git デプロイ](deploy-local-git.md)
* [Azure App Service のデプロイ資格情報](deploy-configure-credentials.md)
* [サンプル: Web アプリを作成し、FTP を使用してファイルをデプロイする (Azure CLI)](./scripts/cli-deploy-ftp.md)。
* [サンプル: FTP を使用して Web アプリにファイルをアップロードする (PowerShell)](./scripts/powershell-deploy-ftp.md)。
