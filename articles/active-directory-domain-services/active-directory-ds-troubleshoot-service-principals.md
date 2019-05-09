---
title: Azure Active Directory Domain Services:サービス プリンシパル構成のトラブルシューティング | Microsoft Docs
description: Azure AD Domain Services 向けのサービス プリンシパル構成のトラブルシューティング
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ergreenl
ms.openlocfilehash: 5bed5dd19f6b736aca7ed2d551c8be0e6f03a810
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58094543"
---
# <a name="troubleshoot-invalid-service-principal-configuration-for-your-managed-domain"></a>マネージド ドメインの無効なサービス プリンシパル構成のトラブルシューティング

この記事は、サービス プリンシパル関連の構成エラーのトラブルシューティングと解決に役立ちます。次のような警告メッセージは、このようなエラーにより表示されます。

## <a name="alert-aadds102-service-principal-not-found"></a>アラート AADDS102: Service Principal not found (サービス プリンシパルが見つかりません)

**アラート メッセージ:**"*Azure AD Domain Services が正常に機能するために必要なサービス プリンシパルが Azure AD ディレクトリから削除されています。この構成は、マネージド ドメインに対する監視、管理、修正のプログラム適用、および同期を行うための Microsoft の能力に影響します。*"

[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)は、Microsoft がマネージド ドメインを管理、更新、および維持するために使用するアプリケーションです。 それらが削除されると、ドメインにサービスを提供する Microsoft の機能が中断します。


## <a name="check-for-missing-service-principals"></a>不足しているサービス プリンシパルを確認する
次の手順を使用して、どのサービス プリンシパルを再作成する必要があるかを判断してください。

1. Azure Portal で [[Enterprise Applications - すべてのアプリケーション]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) に移動します。
2. **[表示]** ドロップダウンで **[すべてのアプリケーション]** を選択し、**[適用]** をクリックします。
3. 次の表を使用して、各アプリケーション ID を検索ボックスに貼り付け、Enter キーを押して検索します。 検索結果が空の場合は、"解決策" 列の手順に従ってそのサービス プリンシパルを再作成する必要があります。

| アプリケーション ID | 解決策 |
| :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [不足しているサービス プリンシパルを PowerShell を使用して再作成する](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Microsoft.AAD 名前空間に再登録する](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Microsoft.AAD 名前空間に再登録する](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Microsoft.AAD 名前空間に再登録する](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |

## <a name="recreate-a-missing-service-principal-with-powershell"></a>不足しているサービス プリンシパルを PowerShell を使用して再作成する
Azure AD ディレクトリで ID ```2565bd9d-da50-47d4-8b85-4c97f669dc36``` のサービス プリンシパルが不足している場合は、次の手順に従います。

**解決策:** この手順を完了するには Azure AD PowerShell が必要です。 Azure AD PowerShell のインストールについては、[こちらの記事](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.)を参照してください。

この問題に対処するには、PowerShell ウィンドウで次のコマンドを入力します。
1. Azure AD PowerShell モジュールをインストールし、インポートします。

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. 次の PowerShell コマンドを実行して、Azure AD Domain Services に必要なサービス プリンシパルがディレクトリに不足しているかどうかをチェックします。

    ```powershell
    Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
    ```

3. 次の PowerShell コマンドを入力して、サービス プリンシパルを作成します。

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

4. 不足しているサービス プリンシパルを作成した後、2 時間待機してからマネージド ドメインの正常性をチェックします。


## <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Azure Portal を使用して、Microsoft AAD 名前空間に再登録する
Azure AD ディレクトリに ID が ```443155a6-77f3-45e3-882b-22b3a8d431fb```、```abba844e-bc0e-44b0-947a-dc74e5d09022```、または ```d87dcbc6-a371-462e-88e3-28ad15ec4e64``` のサービス プリンシパルがない場合は、次の手順に従います。

**解決策:** 次の手順を使用して、ディレクトリに Domain Services を復元します。

1. Azure Portal の [[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ページに移動します。
2. テーブルからマネージド ドメインに関連付けられているサブスクリプションを選択します。
3. 左側のナビゲーションから、**[リソース プロバイダー]** を選択します。
4. テーブルで [Microsoft.AAD] を見つけ、**[再登録]** をクリックします。
5. アラートが解決されたことを確認するには、2 時間後にマネージド ドメインの正常性ページを表示します。


## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>アラート AADDS105: Password synchronization application is out of date (パスワード同期アプリケーションの期限が切れています)

**アラート メッセージ:** アプリケーション ID "d87dcbc6-a371-462e-88e3-28ad15ec4e64" のサービス プリンシパルは削除されましたが、その後再作成されました。 The recreation leaves behind inconsistent permissions on Azure AD Domain Services resources needed to service your managed domain. (再作成により、マネージド ドメインのサービスに必要な Azure AD Domain Services リソースに整合性のないアクセス許可が残っています。) Synchronization of passwords on your managed domain could be affected. (マネージド ドメインでのパスワードの同期が影響を受ける可能性があります。)


**解決策:** この手順を完了するには Azure AD PowerShell が必要です。 Azure AD PowerShell のインストールについては、[こちらの記事](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.)を参照してください。

この問題に対処するには、PowerShell ウィンドウで次のコマンドを入力します。
1. Azure AD PowerShell モジュールをインストールし、インポートします。

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```
2. 次の PowerShell コマンドを使用して古いアプリケーションとオブジェクトを削除します。

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```
3. 両方を削除したら、システムが自らを修正し、パスワード同期に必要なアプリケーションを再作成します。 アラートが修正されたことを確認するには、2 時間待ってからドメインの正常性を確認します。


## <a name="contact-us"></a>お問い合わせ
[フィードバックの共有およびサポートについては](active-directory-ds-contact-us.md)、Azure Active Directory Domain Services 製品チームにお問い合わせください。
