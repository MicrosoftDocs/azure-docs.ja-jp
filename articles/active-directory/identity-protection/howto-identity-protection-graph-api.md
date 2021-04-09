---
title: Microsoft Graph PowerShell SDK と Azure Active Directory Identity Protection
description: Microsoft Graph に対してクエリを実行して、Azure Active Directory からリスク検出とその関連情報を取得する方法を説明します
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 01/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2db8cfe652c0fca4b68b00d846e345c1b60cd05d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880238"
---
# <a name="azure-active-directory-identity-protection-and-the-microsoft-graph-powershell-sdk"></a>Azure Active Directory Identity Protection と Microsoft Graph PowerShell SDK

Microsoft Graph は、Microsoft の統合 API エンドポイントであり、[Azure Active Directory Identity Protection](./overview-identity-protection.md) API のホームです。 この記事では、PowerShell を使用して危険なユーザーの詳細を取得するために、[Microsoft Graph PowerShell SDK](/graph/powershell/get-started) を使用する方法について説明します。 Microsoft Graph API に対して直接クエリを実行する組織は、[チュートリアル: Microsoft Graph API を使用したリスクの特定と修復](/graph/tutorial-riskdetection-api)に関する記事を使用して作業を開始できます。


## <a name="connect-to-microsoft-graph"></a>Microsoft Graph に接続する

Microsoft Graph を介して Identity Protection のデータにアクセスするには、次の 4 つのステップがあります。

- [証明書を作成します](#create-a-certificate)
- [新しいアプリ登録を作成する](#create-a-new-app-registration)
- [API のアクセス許可を構成する](#configure-api-permissions)
- [有効な資格情報名を構成する](#configure-a-valid-credential)

### <a name="create-a-certificate"></a>証明書を作成する

運用環境では、実稼働の証明機関からの証明書を使用しますが、このサンプルでは自己署名証明書を使用します。 次の PowerShell コマンドを使用して、証明書を作成してエクスポートします。

```powershell
$cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"
```

### <a name="create-a-new-app-registration"></a>新しいアプリ登録の作成

1. Azure portal で、 **[Azure Active Directory]**  >  **[アプリの登録]** に移動します。
1. **[新規登録]** を選択します。
1. **[作成]** ページで、以下の手順を実行します。
   1. **[名前]** ボックスにアプリケーションの名前 (例:Azure AD リスク検出 API)。
   1. **[サポートされているアカウントの種類]** で、その API を使用するアカウントの種類を選択します。
   1. **[登録]** を選択します。
1. **[アプリケーション (クライアント) ID]** と **[ディレクトリ (テナント) ID]** をメモします。これらの項目は後で必要になります。

### <a name="configure-api-permissions"></a>API のアクセス許可を構成する

この例では、アプリケーションのアクセス許可を構成して、このサンプルを自動で使用できるようにします。 ログオンするユーザーにアクセス許可を付与する場合は、代わりに委任されたアクセス許可を選択します。 各種のアクセス許可の詳細については、記事「[Microsoft ID プラットフォームでのアクセス許可と同意](../develop/v2-permissions-and-consent.md#permission-types)」を参照してください。

1. 作成した **アプリケーション** から、 **[API のアクセス許可]** を選択します。
1. **[構成されたアクセス許可]** ページで、上部ツール バーの **[アクセス許可の追加]** をクリックします。
1. **[API アクセスの追加]** ページで、 **[API を選択します]** をクリックします。
1. **[API を選択します]** ページで、 **[Microsoft Graph]** を選んで、 **[選択]** をクリックします。
1. **[API アクセス許可の要求]** ページで、以下を実行します。 
   1. **[アプリケーションのアクセス許可]** を選択します。
   1. [`IdentityRiskEvent.Read.All`] と [`IdentityRiskyUser.Read.All`] の横にあるチェックボックスをオンにします。
   1. **[アクセス許可の追加]** を選択します.
1. **[Grant admin consent for domain]\(ドメインに管理者の同意を与えます\)** を選択します。 

### <a name="configure-a-valid-credential"></a>有効な資格情報を構成する

1. 作成した **アプリケーション** から、 **[証明書とシークレット]** を選択します。
1. **[証明書]** で **[証明書のアップロード]** を選択します。
   1. 開いたウィンドウで、前にエクスポートした証明書を選択します。
   1. **[追加]** を選択します。
1. 証明書の **[拇印]** をメモします。次の手順でこの情報が必要になります。

## <a name="list-risky-users-using-powershell"></a>PowerShell を使用して危険なユーザーを一覧表示する

Microsoft Graph に対してクエリを実行できるようにするために、PowerShell ウィンドウで `Install-Module Microsoft.Graph` コマンドを使用して `Microsoft.Graph` モジュールをインストールする必要があります。

次の変数を変更して、前の手順で生成された情報を含めます。次に、PowerShell を使用して、それらを全体として実行して危険なユーザーの詳細を取得します。

```powershell
$ClientID       = "<your client ID here>"        # Application (client) ID gathered when creating the app registration
$tenantdomain   = "<your tenant domain here>"    # Directory (tenant) ID gathered when creating the app registration
$Thumbprint     = "<your client secret here>"    # Certificate thumbprint gathered when configuring your credential

Select-MgProfile -Name "beta"
  
Connect-MgGraph -ClientId $ClientID -TenantId $tenantdomain -CertificateThumbprint $Thumbprint

Get-MgRiskyUser -All
```

## <a name="next-steps"></a>次のステップ

- [Microsoft Graph PowerShell SDK の概要](/graph/powershell/get-started)に関する記事
- [チュートリアル: Microsoft Graph API を使用したリスクの特定と修復](/graph/tutorial-riskdetection-api)に関する記事
- [Microsoft Graph の概要](https://developer.microsoft.com/graph/docs)
- [ユーザーなしでアクセスを取得する](/graph/auth-v2-service)
- [Azure AD Identity Protection Service Root](/graph/api/resources/identityprotectionroot)
- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
