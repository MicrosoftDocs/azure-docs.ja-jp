---
title: "Azure Active Directory Domain Services: トラブルシューティング ガイド | Microsoft Docs"
description: "Azure AD ドメイン サービスのトラブルシューティング ガイド"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: d6695b0c40f56093e8701dfe6394143268114453
ms.lasthandoff: 03/18/2017


---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Azure AD Domain Services - トラブルシューティング ガイド
この記事では、Azure Active Directory (AD) ドメイン サービスの設定や管理の際に生じる可能性のある問題をトラブルシューティングするためのヒントを提供します。

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Azure AD ディレクトリの Azure AD Domain Services を有効にすることはできません
このセクションでは、ディレクトリの Azure AD Domain Services を有効にしようとしたときに失敗するエラーや "無効" と表示されるエラーのトラブルシューティングについて説明します。

表示されたエラー メッセージに対応するトラブルシューティング手順を選択してください。

| **エラー メッセージ** | **解決策** |
| --- |:--- |
| "*名前 contoso100.com はこのネットワーク上で既に使用されています。使用されていない名前を指定してください。*" |[仮想ネットワーク内のドメイン名の競合](active-directory-ds-troubleshooting.md#domain-name-conflict) |
| *Domain Services could not be enabled in this Azure AD tenant. (この Azure AD テナントでは Domain Services を有効にできませんでした。)The service does not have adequate permissions to the application called 'Azure AD Domain Services Sync'. (アプリケーション "Azure AD Domain Services Sync" への適切なアクセス許可がサービスにありません。)Delete the application called 'Azure AD Domain Services Sync' and then try to enable Domain Services for your Azure AD tenant. (アプリケーション "Azure AD Domain Services Sync" を削除してから、Azure AD テナントの Domain Services を有効にしてください。)*) |[Azure AD Domain Services Sync アプリケーションへの適切な権限が Domain Services サービスにない](active-directory-ds-troubleshooting.md#inadequate-permissions) |
| *Domain Services could not be enabled in this Azure AD tenant. (この Azure AD テナントでは Domain Services を有効にできませんでした。)The Domain Services application in your Azure AD tenant does not have the required permissions to enable Domain Services. (Domain Services を有効にするために必要なアクセス許可が Azure AD テナントの Domain Services アプリケーションにありません。)Delete the application with the application identifier d87dcbc6-a371-462e-88e3-28ad15ec4e64 and then try to enable Domain Services for your Azure AD tenant. (アプリケーション識別子が d87dcbc6-a371-462e-88e3-28ad15ec4e64 のアプリケーションを削除してから、Azure AD テナントの Domain Services を有効にしてください。)* |[テナント内で Domain Services アプリケーションが適切に構成されていない](active-directory-ds-troubleshooting.md#invalid-configuration) |
| *Domain Services could not be enabled in this Azure AD tenant. (この Azure AD テナントでは Domain Services を有効にできませんでした。)The Microsoft Azure AD application is disabled in your Azure AD tenant. (Azure AD テナントで Microsoft Azure AD アプリケーションが無効になっています。)Enable the application with the application identifier 00000002-0000-0000-c000-000000000000 and then try to enable Domain Services for your Azure AD tenant. (アプリケーション識別子が 00000002-0000-0000-c000-000000000000 のアプリケーションを有効にしてから、Azure AD テナントの Domain Services を有効にしてください。)* |[Azure AD テナントで Microsoft Graph アプリケーションが無効になっている](active-directory-ds-troubleshooting.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>ドメイン名の競合
**エラー メッセージ:**

"*名前 contoso100.com はこのネットワーク上で既に使用されています。使用されていない名前を指定してください。*"

**解決策:**

その仮想ネットワークで使用できるドメインと同じ名前のドメインがないことを確認します。 たとえば、選択した仮想ネットワークで既に利用可能な "contoso.com" という名前のドメインがあると仮定します。 その後、その仮想ネットワークでこれと同じドメイン名 (つまり "contoso.com") で、Azure AD Domain Services の管理対象ドメインを有効にしようとします。 Azure AD Domain Services を有効にしようとすると、エラーが発生します。

このエラーの原因は、仮想ネットワークのドメイン名で名前が競合していることにあります。 この場合、Azure AD ドメイン サービスの管理対象ドメインを設定するには、別の名前を使用する必要があります。 または、既存のドメインのプロビジョニングを解除してから、Azure AD ドメイン サービスの有効化に進みます。

### <a name="inadequate-permissions"></a>不適切なアクセス許可
**エラー メッセージ:**

*Domain Services could not be enabled in this Azure AD tenant. (この Azure AD テナントでは Domain Services を有効にできませんでした。)The service does not have adequate permissions to the application called 'Azure AD Domain Services Sync'. (アプリケーション "Azure AD Domain Services Sync" への適切なアクセス許可がサービスにありません。)Delete the application called 'Azure AD Domain Services Sync' and then try to enable Domain Services for your Azure AD tenant. (アプリケーション "Azure AD Domain Services Sync" を削除してから、Azure AD テナントの Domain Services を有効にしてください。)*)

**解決策:**

Azure AD ディレクトリに "Azure AD Domain Services Sync" という名前のアプリケーションがあるかどうかを確認します。 このアプリケーションが存在する場合は、アプリケーションを削除してから、Azure AD Domain Services を再度有効にします。

アプリケーションの有無を確認し、存在した場合に削除する手順は、次のとおりです。

1. **Azure クラシック ポータル** ([https://manage.windowsazure.com](https://manage.windowsazure.com)) に移動します。
2. 左ウィンドウで、 **[Active Directory]** を選択します。
3. Azure AD Domain Services を有効にする Azure AD テナント (ディレクトリ) を選択します。
4. **[アプリケーション]** タブに移動します。
5. ドロップダウンで **[自分の会社が所有するアプリケーション]** のオプションを選択します。
6. **[Azure AD ドメイン サービス同期]**というアプリケーションを探します。 アプリケーションが存在する場合は、削除に進みます。
7. アプリケーションが削除されたら、もう一度 Azure AD ドメイン サービスの有効化を試します。

### <a name="invalid-configuration"></a>構成が無効です
**エラー メッセージ:**

*Domain Services could not be enabled in this Azure AD tenant. (この Azure AD テナントでは Domain Services を有効にできませんでした。)The Domain Services application in your Azure AD tenant does not have the required permissions to enable Domain Services. (Domain Services を有効にするために必要なアクセス許可が Azure AD テナントの Domain Services アプリケーションにありません。)Delete the application with the application identifier d87dcbc6-a371-462e-88e3-28ad15ec4e64 and then try to enable Domain Services for your Azure AD tenant. (アプリケーション識別子が d87dcbc6-a371-462e-88e3-28ad15ec4e64 のアプリケーションを削除してから、Azure AD テナントの Domain Services を有効にしてください。)*

**解決策:**

"AzureActiveDirectoryDomainControllerServices" という名前のアプリケーション (アプリケーション識別子 d87dcbc6-a371-462e-88e3-28ad15ec4e64) が Azure AD ディレクトリにあるかどうかを確認します。 このアプリケーションが存在する場合は、削除して Azure AD Domain Services を再度有効にする必要があります。

アプリケーションを見つけて削除するには、次の PowerShell スクリプトを使用します。

> [!NOTE]
> このスクリプトでは、**Azure AD PowerShell Version 2** のコマンドレットを使用しています。 使用可能なすべてのコマンドレットの一覧とモジュールのダウンロードについては、[Azure AD PowerShell のリファレンス ドキュメント](https://msdn.microsoft.com/library/azure/mt757189.aspx)を参照してください。
>
>

```
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
<br>

### <a name="microsoft-graph-disabled"></a>Microsoft Graph が無効
**エラー メッセージ:**

Domain Services could not be enabled in this Azure AD tenant. (この Azure AD テナントでは Domain Services を有効にできませんでした。) The Microsoft Azure AD application is disabled in your Azure AD tenant. (Azure AD テナントで Microsoft Azure AD アプリケーションが無効になっています。) Enable the application with the application identifier 00000002-0000-0000-c000-000000000000 and then try to enable Domain Services for your Azure AD tenant. (アプリケーション識別子が 00000002-0000-0000-c000-000000000000 のアプリケーションを有効にしてから、Azure AD テナントの Domain Services を有効にしてください。)

**解決策:**

識別子が 00000002-0000-0000-c000-000000000000 のアプリケーションが無効になっているかどうかを確認します。 これは Microsoft Azure AD アプリケーションで、Azure AD テナントへのアクセスを Graph API に提供します。 Azure AD Domain Services は、Azure AD テナントを管理対象ドメインと同期するためにこのアプリケーションを有効にする必要があります。

このエラーを解決するには、このアプリケーションを有効にしてから、Azure AD テナントの Domain Services を有効にします。

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>ユーザーが Azure AD ドメイン サービスの管理対象ドメインにサインインできない
Azure AD テナントの 1 人または複数のユーザーが、新しく作成された管理対象ドメインにサインインすることができない場合、次のトラブルシューティングの手順を実行します。

* **UPN 形式でのサインイン:** SAMAccountName 形式 (CONTOSO\joeuser) ではなく UPN 形式 ('joeuser@contoso.com' など) でサインインを試みます。 UPN プレフィックスが極端に長いか、管理対象ドメイン上の別のユーザーと同じであるユーザーに対して SAMAccountName を自動的に生成できます。 UPN 形式は、Azure AD テナント内で一意であることが保証されています。

> [!NOTE]
> UPN 形式を使用して、Azure AD Domain Services の管理対象ドメインにサインインすることをお勧めします。
>
>

* ファースト ステップ ガイドで説明されている手順に従って [パスワード同期が有効になっている](active-directory-ds-getting-started-password-sync.md) ことを確認します。
* **外部アカウント:** 影響を受けているユーザーのアカウントが Azure AD テナントの外部アカウントでないことを確認します。 外部アカウントには、Microsoft アカウント ('joe@live.com' など) や外部の Azure AD ディレクトリのユーザー アカウントが含まれます。 Azure AD ドメイン サービスにはこのようなユーザー アカウントの資格情報がないため、これらのユーザーは管理対象ドメインにサインインできません。
* **同期されるアカウント:** 影響を受けているユーザー アカウントがオンプレミスのディレクトリから同期されている場合は、次について確認します。

  * [Azure AD Connect の最新の推奨リリース](https://www.microsoft.com/en-us/download/details.aspx?id=47594)をデプロイまたは更新している。
  * [完全同期を実行](active-directory-ds-getting-started-password-sync.md)するように Azure AD Connect を構成している。
  * ディレクトリのサイズによっては、ユーザー アカウントと資格情報のハッシュを Azure AD ドメイン サービスで使用できるまで時間がかかる可能性があります。 十分な時間 (ディレクトリのサイズに応じて数時間から 1 ～ 2 日間) を待機してから認証を再試行してください。
  * 上記の手順を確認しても問題が解決しない場合は、Microsoft Azure AD 同期サービスを再起動してみてください。 同期コンピューターから、コマンド プロンプトを起動し、次のコマンドを実行します。

    1. net stop 'Microsoft Azure AD Sync'
    2. net start 'Microsoft Azure AD Sync'
* **クラウド専用のアカウント**: 影響を受けているユーザー アカウントがクラウド専用のユーザー アカウントの場合は、Azure AD ドメイン サービスを有効にした後でユーザーが自分のパスワードを変更していることを確認します。 この手順によって、Azure AD ドメイン サービスに必要な資格情報ハッシュが生成されます。

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Azure AD テナントから削除されたユーザーが管理対象ドメインから削除されない
Azure AD では、ユーザー オブジェクトが誤って削除されないように保護されています。 Azure AD テナントからユーザー アカウントを削除すると、対応するユーザー オブジェクトはごみ箱に移動されます。 この削除操作が管理対象ドメインに同期されると、対応するユーザー アカウントは無効としてマークされます。 この機能により、後でユーザー アカウントを回復する (削除を取り消す) ことができます。

ユーザー アカウントは、Azure AD ディレクトリで同じ UPN でユーザー アカウントを再作成した場合でも、管理対象ドメイン内で無効な状態のままです。 管理対象ドメインからユーザー アカウントを削除するには、Azure AD テナントからユーザーを強制的に削除する必要があります。

管理対象ドメインからユーザー アカウントを完全に削除するには、Azure AD テナントからユーザーを完全に削除します。 こちらの[MSDN の記事](https://msdn.microsoft.com/library/azure/dn194132.aspx)で説明されているように、-RemoveFromRecycleBin オプションを指定した Remove-MsolUser PowerShell コマンドレットを使用します。

## <a name="contact-us"></a>お問い合わせ
[フィードバックの共有およびサポートについては](active-directory-ds-contact-us.md)、Azure Active Directory Domain Services 製品チームにお問い合わせください。

