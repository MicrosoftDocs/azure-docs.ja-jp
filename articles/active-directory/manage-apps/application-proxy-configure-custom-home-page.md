---
title: Azure AD アプリケーション プロキシを使用して、発行されたアプリのカスタム ホーム ページを設定する | Microsoft Docs
description: Azure AD アプリケーション プロキシ コネクタの基本について説明します
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51596e4db8999de5089748e40f9b24bd46c84e56
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807832"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシを使用して、発行されたアプリのカスタム ホーム ページを設定する

この記事では、ユーザーをカスタム ホーム ページに移動させるようにアプリを構成する方法について説明します。 アプリケーション プロキシを使用してアプリを発行するときに内部 URL を設定しますが、それが、最初にユーザーに対して表示すべきページでない場合があります。 ユーザーがアプリにアクセスしたときに適切なページが表示されるように、カスタム ホーム ページを設定します。 Azure Active Directory アクセス パネルからアプリにアクセスするか、Office 365 アプリ起動ツールからアクセスするかにかかわらず、ユーザーには、設定したカスタム ホーム ページが表示されます。

ユーザーはアプリを起動すると、既定で、発行されたアプリのルート ドメイン URL に案内されます。 通常、ランディング ページがホーム ページの URL として設定されます。 アプリ ユーザーに、アプリ内の特定のページを最初に訪問させたい場合は、Azure AD PowerShell モジュールを使用してカスタム ホーム ページの URL を定義します。

ご自分の会社がカスタム ホーム ページを設定する理由を説明する 1 つのシナリオを次に示します。

- あなたの企業ネットワーク内では、ユーザーが `https://ExpenseApp/login/login.aspx` に移動して、お使いのアプリにサインインしてアクセスします。
- フォルダー構造の最上位に、アプリケーション プロキシがアクセスする必要がある他のアセット (イメージなど) があるため、内部 URL として `https://ExpenseApp` を使用してアプリを発行します。
- 既定の外部 URL は `https://ExpenseApp-contoso.msappproxy.net` ですが、外部ユーザーはここからサインイン ページには行けません。
- 代わりに `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` をホーム ページ URL として設定します。それにより、外部ユーザーに対しては最初にサインインページが表示されます。

> [!NOTE]
> 発行されたアプリにユーザーがアクセスできるようにすると、[Azure AD アクセス パネル](../user-help/my-apps-portal-end-user-access.md)と [Office 365 アプリ起動ツール](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/)にアプリが表示されます。

## <a name="before-you-start"></a>開始する前に

ホーム ページの URL を設定する前に、次の要件にご注意ください。

- 指定するパスは、ルート ドメイン URL のサブドメインのパスでなければなりません。

  たとえば、ルート ドメイン URL が `https://apps.contoso.com/app1/` の場合、構成するホーム ページの URL は `https://apps.contoso.com/app1/` で始まる必要があります。

- 発行されたアプリに変更を加えると、ホーム ページの URL の値がリセットされる可能性があります。 後でアプリを更新する場合は、ホーム ページの URL を再確認し、必要であれば更新してください。

ホーム ページの URL は、Azure portal または PowerShell を使用して設定することができます。

## <a name="change-the-home-page-in-the-azure-portal"></a>Azure Portal でホーム ページを変更する

Azure AD ポータルからご自分のアプリのホーム ページ URL を変更するには、次の手順に従ってください。

1. [Azure Portal](https://portal.azure.com/) に管理者としてサインインします。
1. **[Azure Active Directory]** を選択し、 **[アプリの登録]** を選択します。 登録済みのアプリの一覧が表示されます。
1. 一覧からアプリを選択します。 登録済みのアプリの詳細を示すページが表示されます。
1. **[管理]** 下にある **[ブランド]** を選択します。
1. **[ホーム ページ URL]** をご自分の新しいパスで更新します。

   ![[ホーム ページ URL] フィールドが表示された登録済みアプリのブランド ページ](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. **[保存]** を選択します。

## <a name="change-the-home-page-with-powershell"></a>PowerShell でホーム ページを変更する

PowerShell を使用してアプリのホーム ページを構成するには、次を実行する必要があります。

1. Azure AD PowerShell モジュールをインストールします。
1. アプリの ObjectId 値を見つけます。
1. PowerShell のコマンドを使用してアプリのホーム ページの URL を更新します。

### <a name="install-the-azure-ad-powershell-module"></a>Azure AD PowerShell モジュールをインストールする

PowerShell を使用してカスタム ホーム ページの URL を定義する前に、Azure AD PowerShell モジュールをインストールします。 このパッケージは、Graph API エンドポイントを使用する [PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16)からダウンロードできます。

このパッケージをインストールするには、次の手順を実行します。

1. 標準の PowerShell ウィンドウを開き、次のコマンドを実行します。

   ```powershell
   Install-Module -Name AzureAD
   ```

    コマンドを非管理者として実行している場合は、`-scope currentuser` オプションを使用します。

1. インストール中に **Y** を選択して、Nuget.org から 2 つのパッケージをインストールします。両方のパッケージが必要です。

### <a name="find-the-objectid-of-the-app"></a>アプリの ObjectId を見つける

アプリの ObjectId は、その表示名またはホーム ページでアプリを検索して取得します。

1. 同じ PowerShell ウィンドウで、Azure AD モジュールをインポートします。

   ```powershell
   Import-Module AzureAD
   ```

1. Azure AD モジュールにテナント管理者としてサインインします。

   ```powershell
   Connect-AzureAD
   ```

1. アプリを見つけます。 この例では、PowerShell を使用して、`SharePoint` の表示名でアプリを検索して ObjectId を検出しています。

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   次のような結果が表示されます。 次のセクションで使用するために ObjectId GUID をコピーします。

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   または、単にすべてのアプリの一覧をプルし、特定の表示名やホーム ページでアプリの一覧を検索し、該当のアプリが検出されたらアプリの ObjectId をコピーすることもできます。

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>ホーム ページの URL を更新する

ホーム ページの URL を作成し、その値でアプリを更新します。 同じ PowerShell ウィンドウを引き続き使用します。または、新しい PowerShell ウィンドウを使用する場合は、`Connect-AzureAD` を使用してもう一度 Azure AD モジュールにサインインしてください。 その後、次の手順に従います。

1. 前のセクションでコピーした ObjectId の値を保持する変数を作成します。 (この SharePoint の例で使用された ObjectId の値は、ご自分のアプリの ObjectId の値に置き換えてください。)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. 次のコマンドを実行して、正しいアプリを持っていることを確認します。 この出力は、前のセクション (「[アプリの Objectid を見つける](#find-the-objectid-of-the-app)」) で確認した出力と同じものになるはずです。

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. 修正する変更箇所を保持しておくために、空のアプリケーション オブジェクトを作成します。

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. ホーム ページの URL を目的の値に設定します。 この値は、発行済みアプリのサブドメイン パスである必要があります。 たとえば、ホーム ページの URL を `https://sharepoint-iddemo.msappproxy.net/` から `https://sharepoint-iddemo.msappproxy.net/hybrid/` に変更すると、アプリ ユーザーはカスタム ホーム ページに直接移動します。

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. ホーム ページの更新を行います。

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. 変更が成功したことを確認するには、手順 2 からの次のコマンドをもう一度実行します。

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   この例では、出力は次のように表示されるはずです。

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. アプリを再起動して、想定どおりにホーム ページが最初の画面として表示されることを確認します。

> [!NOTE]
> アプリを変更すると、ホーム ページの URL がリセットされる可能性があります。 ホーム ページの URL がリセットされた場合は、このセクションの手順を繰り返して設定し直します。

## <a name="next-steps"></a>次の手順

- [Azure AD アプリケーション プロキシによる SharePoint へのリモート アクセスの有効化](application-proxy-integrate-with-sharepoint-server.md)
- [チュートリアル:Azure Active Directory のアプリケーション プロキシを使用してリモート アクセスするためのオンプレミス アプリケーションを追加する](application-proxy-add-on-premises-application.md)