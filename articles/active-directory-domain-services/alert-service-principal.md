---
title: Azure AD Domain Services でサービス プリンシパル アラートを解決する | Microsoft Docs
description: Azure Active Directory Domain Services のサービス プリンシパル構成アラートをトラブルシューティングする方法について説明します
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 15dba02d3db45d663b8ba0aa7eb93bbc3f388532
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645876"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>既知の問題:Azure Active Directory Domain Services のサービス プリンシパル アラート

[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)は、Azure プラットフォームが Azure Active Directory Domain Services (Azure AD DS) マネージド ドメインを管理、更新、および維持するために使用するアプリケーションです。 サービス プリンシパルが削除されると、マネージド ドメインの機能に影響します。

この記事は、サービス プリンシパルに関連した構成アラートのトラブルシューティングと解決に役立ちます。

## <a name="alert-aadds102-service-principal-not-found"></a>アラート AADDS102: Service principal not found (サービス プリンシパルが見つかりません)

### <a name="alert-message"></a>アラート メッセージ

"*Azure AD Domain Services が正常に機能するために必要なサービス プリンシパルが Azure AD ディレクトリから削除されています。この構成は、マネージド ドメインに対する監視、管理、修正のプログラム適用、および同期を行うための Microsoft の能力に影響します。* "

必要なサービス プリンシパルが削除された場合、Azure プラットフォームは自動化された管理タスクを実行できません。 マネージド ドメインで、更新プログラムの適用およびバックアップの実行が正しく行われない可能性があります。

### <a name="check-for-missing-service-principals"></a>不足しているサービス プリンシパルを確認する

不足していて再作成が必要なサービス プリンシパルを確認するには、次の手順を行います。

1. Azure portal で、左側のナビゲーション メニューの **[Azure Active Directory]** を選択します。
1. **[エンタープライズ アプリケーション]** を選択します。 **[アプリケーションの種類]** ドロップダウン メニューの *[すべてのアプリケーション]* を選択し、 **[適用]** を選択します。
1. 次のアプリケーション ID をそれぞれ検索します。 既存のアプリケーションが見つからない場合は、*解決策* の手順に従ってサービス プリンシパルを作成するか、名前空間を再登録します。

    | アプリケーション ID | 解決方法 |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [不足しているサービス プリンシパルを再作成する](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [Microsoft.AAD 名前空間を再登録する](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [Microsoft.AAD 名前空間を再登録する](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Microsoft.AAD 名前空間を再登録する](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>不足しているサービス プリンシパルを再作成する

アプリケーション ID *2565bd9d-da50-47d4-8b85-4c97f669dc36* が Azure AD ディレクトリにない場合は、Azure AD PowerShell を使用して次の手順を実行します。 詳細については、[Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) に関するページを参照してください。

1. 必要に応じて、次のように Azure AD PowerShell モジュールをインストールし、インポートします。

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. 次に、[New-AzureADServicePrincipal][New-AzureAdServicePrincipal] コマンドレットを使用してサービス プリンシパルを作成します。

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

マネージド ドメインの正常性が 2 時間以内に自動的に更新され、アラートが削除されます。

### <a name="re-register-the-microsoft-aad-namespace"></a>Microsoft.AAD 名前空間を再登録する

アプリケーション ID *443155a6-77f3-45e3-882b-22b3a8d431fb*、*abba844e-bc0e-44b0-947a-dc74e5d09022*、または *d87dcbc6-a371-462e-88e3-28ad15ec4e64* が Azure AD ディレクトリにない場合は、次の手順を実行して *Microsoft.AAD* リソース プロバイダーを再登録します。

1. Azure portal で、**サブスクリプション** を検索して選択します。
1. マネージド ドメインに関連付けられているサブスクリプションを選択します。
1. 左側のナビゲーションから、 **[リソース プロバイダー]** を選択します。
1. *Microsoft.AAD* を検索し、 **[再登録]** を選択します。

マネージド ドメインの正常性が 2 時間以内に自動的に更新され、アラートが削除されます。

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>アラート AADDS105: Password synchronization application is out of date (パスワード同期アプリケーションの期限が切れています)

### <a name="alert-message"></a>アラート メッセージ

*The service principal with the application ID "d87dcbc6-a371-462e-88e3-28ad15ec4e64" was deleted and then recreated. (アプリケーション ID "d87dcbc6-a371-462e-88e3-28ad15ec4e64" のサービス プリンシパルは削除されましたが、その後再作成されました。)The recreation leaves behind inconsistent permissions on Azure AD Domain Services resources needed to service your managed domain. (再作成により、マネージド ドメインのサービスに必要な Azure AD Domain Services リソースに整合性のないアクセス許可が残っています。)Synchronization of passwords on your managed domain could be affected. (マネージド ドメインでのパスワードの同期が影響を受ける可能性があります。)*

Azure AD DS は、Azure AD からユーザー アカウントと資格情報を自動的に同期します。 このプロセスで使用されている Azure AD アプリケーションに問題がある場合、Azure AD DS と Azure AD 間の資格情報の同期は失敗します。

### <a name="resolution"></a>解決方法

資格情報の同期に使用する Azure AD アプリケーションを再作成するには、Azure AD PowerShell を使用して次の手順を実行します。 詳細については、[Azure AD PowerShell のインストール](/powershell/azure/active-directory/install-adv2)に関するページを参照してください。

1. 必要に応じて、次のように Azure AD PowerShell モジュールをインストールし、インポートします。

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. それから、次の PowerShell コマンドレットを使用して古いアプリケーションとオブジェクトを削除します。

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $spObject
    ```

両方のアプリケーションを削除した後、Azure プラットフォームによってそれらが自動的に再作成され、パスワード同期の再開が試行されます。 マネージド ドメインの正常性が 2 時間以内に自動的に更新され、アラートが削除されます。

## <a name="next-steps"></a>次のステップ

まだ問題が解決しない場合は、さらなるトラブルシューティングの支援を求めて、[Azure サポート リクエストを開いて][azure-support]ください。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
