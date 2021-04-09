---
title: Microsoft 365 証明書利用者信頼の署名ハッシュ アルゴリズムを変更する - Azure
description: このページでは、Microsoft 365 でフェデレーション信頼の SHA アルゴリズムを変更するガイドラインについて説明します。
keywords: SHA1、SHA256、M365、フェデレーション、aadconnect、adfs、ad fs、sha の変更、フェデレーション信頼、証明書利用者信頼
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bf9347d4d14e6583febd4ffaf0447e912133b80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89660918"
---
# <a name="change-signature-hash-algorithm-for-microsoft-365-relying-party-trust"></a>Microsoft 365 証明書利用者信頼の署名ハッシュ アルゴリズムを変更する
## <a name="overview"></a>概要
Active Directory フェデレーション サービス (AD FS) から Microsoft Azure Active Directory に対して発行されるトークンには、改ざんを防ぐために署名が施されます。 この署名には、SHA1 または SHA256 を使用できます。 Azure Active Directory は SHA256 アルゴリズムで署名されたトークンをサポートするようになり、最上位レベルのセキュリティ確保のためトークン署名アルゴリズムを SHA256 に設定することを推奨します。 この記事では、トークン署名アルゴリズムをより安全性の高い SHA 256 レベルに設定するために必要なステップについて説明します。

>[!NOTE]
>SHA1 よりセキュリティ保護されているという理由から、トークンに署名するためのアルゴリズムとしては SHA256 の使用をお勧めしますが、SHA1 は引き続きサポートされるオプションのままです。

## <a name="change-the-token-signing-algorithm"></a>トークン署名アルゴリズムの変更
以下に示したいずれかの手順で署名アルゴリズムが設定されると、Microsoft 365 の証明書利用者信頼用のトークンに対し、AD FS が SHA256 で署名するようになります。 特別な構成変更は必要ありません。変更したとしても、Microsoft 365 をはじめとする Azure AD アプリケーションへのアクセス権には一切影響しません。

### <a name="ad-fs-management-console"></a>AD FS 管理コンソール
1. プライマリ AD FS サーバーで AD FS 管理コンソールを開きます。
2. AD FS ノードを展開し、 **[証明書利用者信頼]** をクリックします。
3. Microsoft 365/Azure 証明書利用者信頼を右クリックし、 **[プロパティ]** を選択します。
4. **[詳細設定]** タブを選択し、セキュア ハッシュ アルゴリズムとして SHA 256 を選択します。
5. **[OK]** をクリックします。

![SHA256 署名アルゴリズム - MMC](./media/how-to-connect-fed-sha256-guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>AD FS PowerShell コマンドレット
1. 任意の AD FS サーバーから管理者特権で PowerShell を開きます。
2. **Set-AdfsRelyingPartyTrust** コマンドレットを使用してセキュア ハッシュ アルゴリズムを設定します。
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'https://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>追加情報
* [Azure AD Connect を使用して Microsoft 365 の信頼を修復する](how-to-connect-fed-management.md#repairthetrust)

