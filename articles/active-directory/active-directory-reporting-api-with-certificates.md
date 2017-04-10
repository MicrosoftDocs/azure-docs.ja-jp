---
title: "Azure AD Reporting API と証明書を使ってデータを取得する | Microsoft Docs"
description: "Azure AD Reporting API と証明書の資格情報を使い、ユーザーの介入なしでディレクトリからデータを取得する方法について説明します。"
services: active-directory
documentationcenter: 
author: ramical
writer: v-lorisc
manager: kannar
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/24/2017
ms.author: ramical
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: c1345dcda6e52267a8037ffd7207e6bc3b0d3b31
ms.lasthandoff: 03/28/2017


---
# <a name="get-data-using-the-azure-ad-reporting-api-with-certificates"></a>Azure AD Reporting API と証明書を使ってデータを取得する
この記事では、Azure AD Reporting API と証明書の資格情報を使い、ユーザーの介入なしでディレクトリからデータを取得する方法について説明します。 

## <a name="use-the-azure-ad-reporting-api"></a>Azure AD Reporting API の使用 
Azure AD Reporting API を使用するには、次の手順を実行する必要があります。
 *    必須コンポーネントをインストールする
 *    目的のアプリで証明書を設定する
 *    アクセス トークンを取得する
 *    アクセス トークンを使用して Graph API を呼び出す

ソース コードについては、[Report API モジュールの活用](https://github.com/AzureAD/azure-activedirectory-powershell/tree/gh-pages/Modules/AzureADUtils)に関するページを参照してください。 

### <a name="install-prerequisites"></a>必須コンポーネントをインストールする
Azure AD PowerShell V2 と AzureADUtils モジュールをインストールする必要があります。

1. [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md) についての手順に従い、Azure AD Powershell V2 をダウンロードしてインストールします。
2. [AzureAD/azure-activedirectory-powershell](https://github.com/AzureAD/azure-activedirectory-powershell/blob/gh-pages/Modules/AzureADUtils/AzureADUtils.psm1) から Azure AD Utils モジュールをダウンロードします。 
  このモジュールには、いくつかのユーティリティ コマンドレットが用意されています。その例を次に示します。
   * Nuget を使用して最新バージョンの ADAL を入手
   * ADAL を使用してユーザー キー、アプリケーション キー、証明書からアクセス トークンを取得
   * Graph API でページ単位の結果を処理

**Azure AD Utils モジュールをインストールするには:**

1. ユーティリティ モジュールの保存先ディレクトリ (c:\azureAD など) を作成し、GitHub からモジュールをダウンロードします。
2. PowerShell セッションを開いて、先ほど作成したディレクトリに移動します。 
3. 対象のモジュールをインポートし、Install-AzureADUtilsModule コマンドレットを使用して PowerShell モジュールのパスにインストールします。 

このセッションは次のような画面になります。

  ![Windows PowerShell](./media/active-directory-report-api-with-certificates/windows-powershell.png)

### <a name="set-the-certificate-in-your-app"></a>目的のアプリで証明書を設定する
1. 目的のアプリが既にある場合は、そのオブジェクト ID を Azure Portal から取得します。 

  ![Azure Portal](./media/active-directory-report-api-with-certificates/azure-portal.png)

2. PowerShell セッションを開き、Connect-AzureAD コマンドレットを使って Azure AD に接続します。

  ![Azure Portal](./media/active-directory-report-api-with-certificates/connect-azuaread-cmdlet.png)

3. AzureADUtils の New-AzureADApplicationCertificateCredential コマンドレットを使って証明書の資格情報をそこに追加します。 

>[!Note]
>先ほど取得したアプリケーションのオブジェクト ID のほか、証明書オブジェクト (Cert: ドライブを使用して取得) を指定する必要があります。
>


  ![Azure Portal](./media/active-directory-report-api-with-certificates/add-certificate-credential.png)
  
### <a name="get-an-access-token"></a>アクセス トークンを取得する

アクセス トークンを取得するには、AzureADUtils の Get-AzureADGraphAPIAccessTokenFromCert コマンドレットを使います。 

>[!NOTE]
>前のセクションで使用したオブジェクト ID ではなくアプリケーション ID を使う必要があります。
>

 ![Azure Portal](./media/active-directory-report-api-with-certificates/application-id.png)

### <a name="use-the-access-token-to-call-the-graph-api"></a>アクセス トークンを使用して Graph API を呼び出す

これでスクリプトを作成する準備は整いました。 以下に示したのは、AzureADUtils の Invoke-AzureADGraphAPIQuery コマンドレットを使用する例です。 複数のページにわたる結果を処理し、それらの結果を PowerShell パイプラインに送っています。 

 ![Azure Portal](./media/active-directory-report-api-with-certificates/script-completed.png)

ここから CSV をエクスポートして SIEM システムに保存することができます。 また、スケジュールされたタスクにスクリプトをラップすれば、ソース コードにアプリケーション キーを保存せずに Azure AD データをテナントから定期的に取得することができます。 

## <a name="next-steps"></a>次のステップ
[Azure ID 管理の基礎](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals-identity)<br>




