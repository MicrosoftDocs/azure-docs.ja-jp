---
title: "Azure AD アプリケーション プロキシを使用して、発行されたアプリのカスタム ホーム ページを設定する | Microsoft Docs"
description: "Azure AD アプリケーション プロキシ コネクタの基本について説明します"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 5818efb315a0452beea03cde1adc657a9520dcac
ms.openlocfilehash: 1fe3f3a697618bec5d314c6ebf161da37efc1346
ms.lasthandoff: 03/01/2017


---

# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシを使用して、発行されたアプリのカスタム ホーム ページを設定する

この記事では、ユーザーが Azure Active Directory (Azure AD) アクセス パネルと Office 365 アプリ起動ツールからアプリにアクセスした場合に、ユーザーをカスタム ホーム ページに移動させるようにアプリを構成する方法について説明します。

>[!NOTE]
>アプリケーション プロキシ機能は、Azure Active Directory の Premium または Basic エディションにアップグレードした場合にのみ利用できます。 詳細については、「 [Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。
>

アプリ ユーザーをアプリ内の特定のページに移動させたい場合などに、Azure AD PowerShell モジュールを使用すると、ホーム ページのカスタム URL (*https://expenseApp-contoso.msappproxy.net/login/login.aspx* など) を定義できます。

>[!NOTE]
>発行されたアプリにユーザーがアクセスできるようにすると、[Azure AD アクセス パネル](active-directory-saas-access-panel-introduction.md)と [Office 365 アプリ起動ツール](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher)にアプリが表示されます。
>

ユーザーがアプリを起動すると、既定では、発行されたアプリのルート ドメイン URL に移動します。 通常、ランディング ページがホーム ページの URL として設定されます。 たとえば、バックエンド アプリ http://ExpenseApp の URL が *https://expenseApp-contoso.msappproxy.net* として発行されているとします。 既定では、ホーム ページの URL は *https://expenseApp-contoso.msappproxy.net* に設定されます。

## <a name="determine-the-home-page-url"></a>ホーム ページの URL を決定する

ホーム ページの URL を設定する前に、次の点に注意してください。

* 指定するパスがルート ドメイン URL のサブドメインのパスであることを確認してください。

 たとえば、発行したアプリにルート ドメイン URL (https://intranet-contoso.msappproxy.net/ など) からアクセスできる場合、構成するホーム ページの URL は https://intranet-contoso.msappproxy.net/ で始まる必要があります。

* ルート ドメイン URL が https://apps.contoso.com/app1/ である場合、構成するホーム ページの URL は https://apps.contoso.com/app1/ で始まる必要があります。

* 発行されたアプリに変更を加えると、ホーム ページの URL の値がリセットされる可能性があります。 したがって、アプリを更新する場合は、ホーム ページの URL を再確認し、必要であれば更新してください。

次のセクションでは、発行されたアプリ向けのホーム ページのカスタム URL を設定する方法を説明します。 

## <a name="install-the-azure-ad-powershell-module"></a>Azure AD PowerShell モジュールをインストールする

PowerShell を使用してホーム ページのカスタム URL を定義する前に、Azure AD PowerShell モジュールの非標準パッケージをインストールします。 このパッケージは、Graph API エンドポイントを使用する [PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzureAD/1.1.23.0)からダウンロードできます。 

PowerShell を使用してパッケージをインストールするには、次の手順を実行します。

1. 標準の PowerShell ウィンドウを開き、次のコマンドを実行します。

    ```
     Install-Module -Name AzureAD -RequiredVersion 1.1.23.0
    ```
    コマンドを非管理者として実行している場合は、**-scope currentuser** オプションを使用します。
2. インストール中に **Y** を選択して、Nuget.org から&2; つのパッケージをインストールします。 両方のパッケージが必要です。 

## <a name="set-a-custom-home-page-url-by-using-the-azure-ad-powershell-module"></a>Azure AD PowerShell モジュールを使用してホーム ページのカスタム URL を設定する

Azure AD PowerShell モジュールのインストールが完了したので、ホーム ページの URL を設定する準備ができました。 これを行うには、次の&2; つのセクションの手順に従ってください。

### <a name="step-1-find-the-objectid-of-the-app"></a>手順 1: アプリの ObjectID を取得する

アプリの ObjectID を取得し、ホーム ページでアプリを検索します。

1. PowerShell を開き、次のコマンドを使用して Azure AD モジュールをインポートします。

    ```
    Import-Module AzureAD
    ```

2. 次のコマンドレットを使用して Azure AD モジュールにサインインし、画面の指示に従います。 必ずテナント管理者としてサインインしてください。

    ```
    Connect-AzureAD
    ```
3. 次のコマンドレットを使用して、*sharepoint-iddemo* を含むホーム ページに基づいたアプリを検索します。 アプリを編集するには、次の値をアプリの有効な値に置き換えます。

    ```
    Get-AzureADApplications | where { $_.Homepage -like “*sharepoint-iddemo*” } | fl DisplayName, Homepage, ObjectID
    ```
4. 次のような結果が表示されます。 「手順 2: ホーム ページの URL を更新する」で使用できるように GUID (ObjectID) をコピーします。

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

### <a name="step-2-update-the-home-page-url"></a>手順 2: ホーム ページの URL を更新する

「手順 1: アプリの ObjectID を取得する」で使用したのと同じ PowerShell モジュールで、次の手順を実行します。

1. 正しいアプリであることを確認して、*8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4* を、前の手順でコピーした GUID (ObjectID) に置き換えます。

    ```
    Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

 これでアプリの確認が完了し、次の手順に従ってホーム ページを更新する準備ができました。

2. 修正する変更箇所を保持しておくために、空のアプリケーション オブジェクトを作成します。  

 >[!NOTE]
 >これは、アップデート対象の値を保持するための変数であるため、実際には何も作成されません。

    ```
    $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
    ```

3. ホーム ページの URL を目的の値に設定します。 この値は、発行済みアプリのサブドメイン パスである必要があります。 たとえば、ホーム ページ URL を *https://sharepoint-iddemo.msappproxy.net/* から *https://sharepoint-iddemo.msappproxy.net/hybrid/* に変更すると、アプリ ユーザーはカスタム ホーム ページに直接移動します。

    ```
    $appnew.Homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
    ```
4. 「手順 1: アプリの ObjectID を取得する」でコピーした GUID (ObjectID) を使用して、更新を行います。

    ```
    Set-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 - Application $appnew
    ```
5. 変更が成功したことを確認するには、アプリを再起動します。

    ```
    Get-AzureADApplication -AppObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>アプリを変更すると、ホーム ページの URL がリセットされる可能性があります。 このような場合は、「手順 2: ホーム ページの URL を更新する」の処理を繰り返します。

## <a name="next-steps"></a>次のステップ

[Azure AD アプリケーション プロキシによる SharePoint へのリモート アクセスの有効化](application-proxy-enable-remote-access-sharepoint.md)<br>
[Azure Portal でアプリケーション プロキシを有効にする](https://github.com/Microsoft/azure-docs-pr/blob/master/articles/active-directory/active-directory-application-proxy-enable.md)

