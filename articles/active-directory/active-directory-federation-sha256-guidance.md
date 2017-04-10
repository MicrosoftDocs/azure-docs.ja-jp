---
title: "Office 365 証明書利用者信頼の署名ハッシュ アルゴリズムを変更する | Microsoft Docs"
description: "このページでは、Office 365 でフェデレーション信頼の SHA アルゴリズムを変更するガイドラインについて説明します。"
keywords: "SHA1、SHA256、O365、フェデレーション、aadconnect、adfs、ad fs、sha の変更、フェデレーション信頼、証明書利用者信頼"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: samueld
editor: 
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: anandy
translationtype: Human Translation
ms.sourcegitcommit: e1b909f419c8c04a9332a29669148321ab3dbd2d
ms.openlocfilehash: 2afd8e04ac325f1c9f2dee8aed867b0d0a6b558d
ms.lasthandoff: 02/09/2017


---
# <a name="change-signature-hash-algorithm-for-office-365-relying-party-trust"></a>Office 365 証明書利用者信頼の署名ハッシュ アルゴリズムを変更する
## <a name="overview"></a>概要
Active Directory フェデレーション サービス (AD FS) から Microsoft Azure Active Directory に対して発行されるトークンには、改ざんを防ぐために署名が施されます。 この署名には、SHA1 または SHA256 を使用できます。 Azure Active Directory は SHA256 アルゴリズムで署名されたトークンをサポートするようになり、最上位レベルのセキュリティ確保のためトークン署名アルゴリズムを SHA256 に設定することを推奨します。 この記事では、トークン署名アルゴリズムをより安全性の高い SHA&256; レベルに設定するために必要なステップについて説明します。

## <a name="change-the-token-signing-algorithm"></a>トークン署名アルゴリズムの変更
以下に示したいずれかの手順で署名アルゴリズムが設定されると、Office 365 の証明書利用者信頼用のトークンに対し、AD FS が SHA256 で署名するようになります。 特別な構成変更は必要ありません。変更したとしても、Office 365 をはじめとする Azure AD アプリケーションへのアクセス権には一切影響しません。

### <a name="ad-fs-management-console"></a>AD FS 管理コンソール
1. プライマリ AD FS サーバーで AD FS 管理コンソールを開きます。
2. AD FS ノードを展開し、 **[証明書利用者信頼]**をクリックします。
3. Office 365/Azure 証明書利用者信頼を右クリックし、 **[プロパティ]**を選択します。
4. **[詳細設定]** タブを選択し、セキュア ハッシュ アルゴリズムとして SHA&256; を選択します。
5. **[OK]**をクリックします。

![SHA256 署名アルゴリズム - MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>AD FS PowerShell コマンドレット
1. 任意の AD FS サーバーから管理者特権で PowerShell を開きます。
2. **Set-AdfsRelyingPartyTrust** コマンドレットを使用してセキュア ハッシュ アルゴリズムを設定します。
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>追加情報
* [Azure AD Connect を使用して Office 365 信頼を修復する](connect/active-directory-aadconnect-federation-management.md#repairthetrust)


