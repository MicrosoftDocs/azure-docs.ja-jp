---
title: "Microsoft Forefront から Azure AD アプリケーション プロキシへの切り替え | Microsoft Docs"
description: "Microsoft Forefront の TMG および UAG ソリューションから Azure Active Directory アプリケーション プロキシに移行する方法に関する基本事項について説明します。"
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
ms.date: 01/27/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: c887adaae811972efd7fcea86eaa67a899f3f4c2
ms.openlocfilehash: 542dd7df7e0b887298522f29cb597f1df73709cb
ms.lasthandoff: 02/27/2017


---
# <a name="transition-to-azure-ad-application-proxies-from-microsoft-forefront"></a>Microsoft Forefront から Azure AD アプリケーション プロキシへの切り替え

この記事では、Microsoft Forefront の Threat Management Gateway (TMG) および United Access Gateway (UAG) ソリューションから、Azure Active Directory (Azure AD) アプリケーション プロキシ (Web アプリケーション プロキシと Azure AD アプリケーション プロキシ) に移行する方法について説明します。

> [!NOTE]
> Azure AD アプリケーション プロキシは、Azure AD の Premium または Basic エディションにアップグレードしている場合にのみ利用できる機能です。 詳細については、「 [Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。


Forefront の TMG および UAG から新しいアプリケーション プロキシへの切り替えの詳細については、[Microsoft から関連するホワイト ペーパーをダウンロード](http://download.microsoft.com/download/3/E/3/3E335D93-6DB8-4834-90A8-B86105419F05/Microsoft%20TMG%20and%20UAG%20EOL%20and%20transitioning%20to%20WAP%20and%20AADAP.docx)してください。

## <a name="functionality-details-for-the-conversion"></a>変換に関する機能の詳細

|**TMG/UAG の機能**|**Web アプリケーション プロキシ/Azure AD アプリケーション プロキシ**|
|:-----|:-----|
|ブラウザー アプリケーションの選択的な HTTP の公開|Windows Server 2012 R2 の Web アプリケーション プロキシで利用可能。 現在は Azure AD アプリケーション プロキシで利用可能。|
|Active Directory フェデレーション サービス (AD FS) 統合|Windows Server 2012 R2 の Web アプリケーション プロキシで利用可能。 現在は Azure AD アプリケーション プロキシで利用可能。|
|さまざまなプロトコルでの公開 (Citrix、Lync、RDG など)|Windows Server 2012 R2 の Web アプリケーション プロキシで利用可能。 現在は Azure AD アプリケーション プロキシで利用可能。|
|ActiveSync (HTTP Basic) および RDG の事前認証|Web アプリケーション プロキシおよび Azure AD アプリケーション プロキシでは現在利用できません。|
|ポータル|Web アプリケーション プロキシで Intune または System Center を使用。 Azure AD アプリケーション プロキシで Azure AD アクセス パネルまたは Office 365 アプリ起動ツールを使用。|
|エンドポイントの正常性の検出|Intune または System Center を使用。|
|SSL トンネリング|Windows SSL または VPN 機能を使用。|
|レイヤー 2/3 ファイアウォール|Windows Server の機能を使用。|
|Web アプリケーション ファイアウォール|現在、Microsoft のソリューションはありません。|
|セキュリティ保護された Web ゲートウェイ (転送プロキシ)|現在、Microsoft のソリューションはありません。|


## <a name="next-steps"></a>次のステップ

[ブログ: Web アプリケーション プロキシ](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy)<br>
[ブログ: Azure AD アプリケーション プロキシ](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap)

