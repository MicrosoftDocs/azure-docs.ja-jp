---
title: Azure Active Directory Domain Services のトラブルシューティング | Microsoft Docs'
description: Azure Active Directory Domain Services の作成または管理時に発生する一般的なエラーのトラブルシューティング方法について説明します
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 89b04f86d41f8e4828580f70a9aec8acea3e0053
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618452"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services の一般的なエラーとトラブルシューティングの手順

アプリケーションの ID と認証の中心部分として、Azure Active Directory Domain Services (Azure AD DS) では問題が発生することがあります。 問題が発生した場合のために、動作の再開に役立つ、いくつかの一般的なエラー メッセージと、関連するトラブルシューティングの手順があります。 また、追加のトラブルシューティング支援を求めて、いつでも [Azure サポート リクエストを開く][azure-support]ことができます。

この記事では、Azure AD DS での一般的な問題のトラブルシューティング手順を示します。

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Azure AD ディレクトリの Azure AD Domain Services を有効にすることはできません

Azure AD DS の有効化で問題が発生した場合は、以下の一般的なエラーと、その解決手順を確認してください。

| **サンプルのエラー メッセージ** | **解決策** |
| --- |:--- |
| *aaddscontoso.com という名前 はこのネットワーク上で既に使用されています。使用されていない名前を指定してください。* " |[仮想ネットワーク内のドメイン名の競合](troubleshoot.md#domain-name-conflict) |
| *Domain Services could not be enabled in this Azure AD tenant. (この Azure AD テナントでは Domain Services を有効にできませんでした。)The service does not have adequate permissions to the application called 'Azure AD Domain Services Sync'. (アプリケーション "Azure AD Domain Services Sync" への適切なアクセス許可がサービスにありません。)Delete the application called 'Azure AD Domain Services Sync' and then try to enable Domain Services for your Azure AD tenant. (アプリケーション "Azure AD Domain Services Sync" を削除してから、Azure AD テナントの Domain Services を有効にしてください。)* ) |[Azure AD Domain Services Sync アプリケーションに対する適切な権限が Domain Services にない](troubleshoot.md#inadequate-permissions) |
| *Domain Services could not be enabled in this Azure AD tenant. (この Azure AD テナントでは Domain Services を有効にできませんでした。)The Domain Services application in your Azure AD tenant does not have the required permissions to enable Domain Services. (Domain Services を有効にするために必要なアクセス許可が Azure AD テナントの Domain Services アプリケーションにありません。)Delete the application with the application identifier d87dcbc6-a371-462e-88e3-28ad15ec4e64 and then try to enable Domain Services for your Azure AD tenant. (アプリケーション識別子が d87dcbc6-a371-462e-88e3-28ad15ec4e64 のアプリケーションを削除してから、Azure AD テナントの Domain Services を有効にしてください。)* |[Azure AD テナント内で Domain Services アプリケーションが適切に構成されていない](troubleshoot.md#invalid-configuration) |
| *Domain Services could not be enabled in this Azure AD tenant. (この Azure AD テナントでは Domain Services を有効にできませんでした。)The Microsoft Azure AD application is disabled in your Azure AD tenant. (Azure AD テナントで Microsoft Azure AD アプリケーションが無効になっています。)Enable the application with the application identifier 00000002-0000-0000-c000-000000000000 and then try to enable Domain Services for your Azure AD tenant. (アプリケーション識別子が 00000002-0000-0000-c000-000000000000 のアプリケーションを有効にしてから、Azure AD テナントの Domain Services を有効にしてください。)* |[Azure AD テナントで Microsoft Graph アプリケーションが無効になっている](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>ドメイン名の競合

**エラー メッセージ**

*aaddscontoso.com という名前 はこのネットワーク上で既に使用されています。使用されていない名前を指定してください。* "

**解決策**

同じ、またはピアリングされた仮想ネットワーク上に同じドメイン名の既存の AD DS 環境が存在しないことを確認します。 たとえば、Azure VM 上で実行されている *aaddscontoso.com* という名前の AD DS ドメインがあるとします。 その仮想ネットワーク上で *aaddscontoso.com* という同じ名前を持つ Azure AD DS のマネージド ドメインを有効にしようとすると、要求した操作は失敗します。

このエラーは、仮想ネットワーク上のドメイン名での名前の競合が原因です。 DNS 参照では、既存の AD DS 環境が、要求されたドメイン名に対して応答するかどうかが確認されます。 このエラーを解決するには、別の名前を使用してマネージド ドメインを設定するか、既存の AD DS ドメインのプロビジョニングを解除してから、Azure AD DS の有効化を再試行してください。

### <a name="inadequate-permissions"></a>不適切なアクセス許可

**エラー メッセージ**

*Domain Services could not be enabled in this Azure AD tenant. (この Azure AD テナントでは Domain Services を有効にできませんでした。)The service does not have adequate permissions to the application called 'Azure AD Domain Services Sync'. (アプリケーション "Azure AD Domain Services Sync" への適切なアクセス許可がサービスにありません。)Delete the application called 'Azure AD Domain Services Sync' and then try to enable Domain Services for your Azure AD tenant. (アプリケーション "Azure AD Domain Services Sync" を削除してから、Azure AD テナントの Domain Services を有効にしてください。)* )

**解決策**

Azure AD ディレクトリに *Azure AD Domain Services Sync* という名前のアプリケーションがあるかどうかを確認します。 このアプリケーションが存在する場合は、アプリケーションを削除してから、Azure AD DS の有効化を再試行します。 既存のアプリケーションを確認し、必要に応じて削除するには、次の手順を実行します。

1. Azure portal で、左側のナビゲーション メニューの **[Azure Active Directory]** を選択します。
1. **[エンタープライズ アプリケーション]** を選択します。 **[アプリケーションの種類]** ドロップダウン メニューの *[すべてのアプリケーション]* を選択し、 **[適用]** を選択します。
1. 検索ボックスに「*Azure AD Domain Services Sync*」と入力します。アプリケーションが存在する場合は、それを選択し、 **[削除]** を選択します。
1. アプリケーションの削除が完了したら、Azure AD DS の有効化を再試行します。

### <a name="invalid-configuration"></a>構成が無効です

**エラー メッセージ**

*Domain Services could not be enabled in this Azure AD tenant. (この Azure AD テナントでは Domain Services を有効にできませんでした。)The Domain Services application in your Azure AD tenant does not have the required permissions to enable Domain Services. (Domain Services を有効にするために必要なアクセス許可が Azure AD テナントの Domain Services アプリケーションにありません。)Delete the application with the application identifier d87dcbc6-a371-462e-88e3-28ad15ec4e64 and then try to enable Domain Services for your Azure AD tenant. (アプリケーション識別子が d87dcbc6-a371-462e-88e3-28ad15ec4e64 のアプリケーションを削除してから、Azure AD テナントの Domain Services を有効にしてください。)*

**解決策**

*AzureActiveDirectoryDomainControllerServices* という名前のアプリケーション (アプリケーション識別子 *d87dcbc6-a371-462e-88e3-28ad15ec4e64*) が Azure AD ディレクトリに既にあるかどうかを確認します。 このアプリケーションが存在する場合は、アプリケーションを削除してから、Azure AD DS の有効化を再試行します。

次の PowerShell スクリプトを使用して、既存のアプリケーション インスタンスを検索し、必要に応じて削除します。

```powershell
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```

### <a name="microsoft-graph-disabled"></a>Microsoft Graph が無効

**エラー メッセージ**

*Domain Services could not be enabled in this Azure AD tenant. (この Azure AD テナントでは Domain Services を有効にできませんでした。)The Microsoft Azure AD application is disabled in your Azure AD tenant. (Azure AD テナントで Microsoft Azure AD アプリケーションが無効になっています。)Enable the application with the application identifier 00000002-0000-0000-c000-000000000000 and then try to enable Domain Services for your Azure AD tenant. (アプリケーション識別子が 00000002-0000-0000-c000-000000000000 のアプリケーションを有効にしてから、Azure AD テナントの Domain Services を有効にしてください。)*

**解決策**

識別子が *00000002-0000-0000-c000-000000000000* のアプリケーションが無効になっているかどうかを確認します。 これは Microsoft Azure AD アプリケーションで、Azure AD テナントへのアクセスを Graph API に提供します。 Azure AD テナントを同期するには、このアプリケーションを有効にする必要があります。

このアプリケーションの状態を確認し、必要に応じて有効にするには、次の手順を実行します。

1. Azure portal で、左側のナビゲーション メニューの **[Azure Active Directory]** を選択します。
1. **[エンタープライズ アプリケーション]** を選択します。 **[アプリケーションの種類]** ドロップダウン メニューの *[すべてのアプリケーション]* を選択し、 **[適用]** を選択します。
1. 検索ボックスに「*00000002-0000-0000-c000-00000000000*」と入力します。 アプリケーションを選択し、 **[プロパティ]** を選択します。
1. **[ユーザーのサインインが有効になっていますか?]** が *[いいえ]* に設定される場合は、その値を *[はい]* に設定し、 **[保存]** を選択します。
1. アプリケーションが有効になったら、Azure AD DS の有効化を再試行します。

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>ユーザーが Azure AD Domain Services のマネージド ドメインにサインインできない

Azure AD テナント内の 1 人以上のユーザーがマネージド ドメインにサインインできない場合は、次のトラブルシューティング手順を実行します。

* **資格情報の形式** - UPN 形式を使用して資格情報を指定してみます (例: `dee@aaddscontoso.onmicrosoft.com`)。 UPN 形式は、Azure AD DS で資格情報を指定するための推奨される方法です。 この UPN が Azure AD で正しく構成されていることを確認してください。

    同じ UPN プレフィックスを使用する複数のユーザーがテナント内に存在する場合や、使用する UPN プレフィックスが長すぎる場合は、お使いのアカウントの *SAMAccountName* (*AADDSCONTOSO\driley* など) が自動生成されることがあります。 そのため、お使いのアカウントの *SAMAccountName* 形式が想定した形式やオンプレミス ドメインで使用する形式と異なる可能性があります。

* **パスワード同期** - [クラウド専用ユーザー][cloud-only-passwords]または [Azure AD Connect を使用するハイブリッド環境][hybrid-phs]のパスワード同期が有効になっていることを確認します。
    * **ハイブリッド同期済みアカウント:** 影響を受けているユーザー アカウントがオンプレミスのディレクトリから同期されている場合は、以下の領域を確認します。
    
      * [Azure AD Connect の最新の推奨リリース](https://www.microsoft.com/download/details.aspx?id=47594)をデプロイまたは更新している。
      * [完全同期を実行する][hybrid-phs]ように Azure AD Connect を構成している。
      * ディレクトリのサイズによっては、ユーザー アカウントと資格情報ハッシュがマネージド ドメインで使用できるようになるまでに時間がかかる場合があります。 十分に時間を空けてから、マネージド ドメインに対して認証を試行してください。
      * 前の手順を確認しても問題が解決しない場合は、"*Microsoft Azure AD Sync サービス*" を再起動してみてください。 Azure AD Connect サーバーから、コマンド プロンプトを開き、次のコマンドを実行します。
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **クラウド専用アカウント**: 影響を受けているユーザー アカウントがクラウド専用のユーザー アカウントである場合は、[Azure AD DS を有効にした後でユーザーが自分のパスワードを変更した][cloud-only-passwords]ことを確認します。 このパスワードのリセットによって、生成されるマネージド ドメインに必要な資格情報ハッシュが生成されます。

* **ユーザー アカウントがアクティブであることを確認する**:既定では、マネージド ドメインで 2 分以内に無効なパスワードの試行が 5 回行われると、ユーザーのアカウントは、30 分間ロックアウトされます。 アカウントがロックアウトされている間、ユーザーはサインインできません。30 分後、ユーザー アカウントは、自動的にロック解除されます。
  * マネージド ドメインでの無効なパスワードの試行によっては、Azure AD のユーザー アカウントはロックアウトされません。 このユーザー アカウントは、マネージド ドメイン内だけでロックアウトされます。 [マネージド VM][management-vm] を使用して、Azure AD ではなく "*Active Directory 管理コンソール (ADAC)* " で、ユーザー アカウントの状態を確認します。
  * 既定のロックアウトのしきい値と期間を変更するために[細かい設定が可能なパスワード ポリシーを構成する][password-policy]こともできます。

* **外部アカウント:** 影響を受けているユーザー アカウントが Azure AD テナントの外部アカウントでないことを確認します。 外部アカウントの例として、`dee@live.com` のような Microsoft アカウントや、外部の Azure AD ディレクトリのユーザー アカウントなどがあります。 Azure AD DS には外部ユーザー アカウントの資格情報が格納されないため、それらのアカウントはマネージド ドメインにサインインできません。

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>マネージド ドメインに 1 つまたは複数のアラートがある

マネージド ドメイン上にアクティブなアラートがある場合は、認証プロセスが正常に機能しなくなる可能性があります。

アクティブなアラートがあるかどうかを調べるには、[マネージド ドメインの正常性状態を確認][check-health]します。 アラートが表示されている場合は、[それらをトラブルシューティングして解決][troubleshoot-alerts]します。

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Azure AD テナントから削除されたユーザーがマネージド ドメインから削除されない

Azure AD では、ユーザー オブジェクトが誤って削除されないように保護されています。 Azure AD テナントからユーザー アカウントを削除すると、対応するユーザー オブジェクトはごみ箱に移動されます。 この削除操作がマネージド ドメインに同期されると、対応するユーザー アカウントは無効としてマークされます。 この機能は、ユーザー アカウントの復旧 (削除取り消し) に役立ちます。

このユーザー アカウントは、Azure AD ディレクトリ内に同じ UPN のユーザー アカウントを作成し直した場合でも、マネージド ドメイン内に無効な状態のまま残ります。 マネージド ドメインからこのユーザー アカウントを削除するには、Azure AD テナントから強制的に削除する必要があります。

マネージド ドメインからユーザー アカウントを完全に削除するには、`-RemoveFromRecycleBin` パラメーターを指定した [Remove-MsolUser][Remove-MsolUser] PowerShell コマンドレットを使用して、Azure AD テナントからユーザーを完全に削除します。

## <a name="next-steps"></a>次のステップ

まだ問題が解決しない場合は、さらなるトラブルシューティングの支援を求めて、[Azure サポート リクエストを開いて][azure-support]ください。

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
