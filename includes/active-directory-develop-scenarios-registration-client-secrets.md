---
title: インクルード ファイル
description: 機密クライアント シナリオのランディング ページのインクルード ファイル (デーモン、Web アプリ、Web API)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a3acdbb93dd20f0b89e4f99d64f5f7a30ce40623
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102797"
---
## <a name="register-secrets-or-certificates"></a>シークレットまたは証明書の登録

機密クライアント アプリケーションについては、シークレットまたは証明書を登録する必要があります。 [Azure portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) の対話型エクスペリエンスまたはコマンドライン ツール (PowerShell など) を使用して、アプリケーション シークレットを登録できます。

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>アプリケーション登録ポータルを使用したクライアント シークレットの登録

クライアント資格情報の管理は、アプリケーションの **[証明書とシークレット]** ページで行います。

![[証明書とシークレット] ページ](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- Azure portal のアプリの登録で **[新しいクライアント シークレット]** を選択して、"*クライアント シークレット*" を作成します。 クライアント シークレットを作成する場合は、 **[証明書とシークレット]** ウィンドウから離れて移動する前に、シークレットの文字列を記録する "_必要があります_"。 シークレットの文字列が再び表示されることはありません。
- アプリケーションの登録時に、**[証明書のアップロード]** ボタンを使用して証明書をアップロードします。 Azure AD では、アプリケーションで直接登録されている証明書のみがサポートされ、証明書チェーンには従いません。

詳細については、「[クイック スタート:Web API にアクセスするようにクライアント アプリケーションを構成する」で、資格情報のアプリケーションへの追加](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)に関するセクションを参照してください。

### <a name="register-client-secrets-by-using-powershell"></a>PowerShell を使用したクライアント シークレットの登録

または、コマンド ライン ツールを使用して、Azure AD でアプリケーションを登録できます。 [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) のサンプルでは、Azure AD アプリケーションでアプリケーション シークレットまたは証明書を登録する方法を示しています。

- アプリケーション シークレットの登録方法の詳細については、[AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190) に関するページを参照してください。
- アプリケーションでの証明書の登録方法の詳細については、[AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178) に関するページを参照してください。
