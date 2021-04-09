---
title: ユーザー プライバシーと Azure AD シームレス シングル サインオン | Microsoft Docs
description: この記事では、Azure Active Directory (Azure AD) シームレス SSO および GDPR コンプライアンスについて説明します。
services: active-directory
keywords: Azure AD Connect とは, GDPR, Azure AD に必要なコンポーネント, SSO, シングル サインオン
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2aa1a30c548ef60cd9b596031f4115297dd20844
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89278600"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>ユーザー プライバシーと Azure AD シームレス シングル サインオン

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>概要


Azure AD シームレス SSO では、個人データを含めることができる次の種類のログが作成されます。 

- Azure AD Connect トレース ログ ファイル。

次の 2 つの方法でシームレス SSO のユーザー プライバシーを強化します。

1.  要請を受けた際、個人のデータを抽出し、その個人のデータを環境から削除する
2.  すべてのデータが 48 時間しか保持されないようにする。

実装や保守がより簡単なので、2 番目の方法を強くお勧めします。 ログの種類ごとの以下の手順を確認してください。

### <a name="delete-azure-ad-connect-trace-log-files"></a>Azure AD Connect トレース ログ ファイルを削除する

**%ProgramData%\AADConnect** フォルダーの内容を確認して、Azure AD Connect のインストールまたはアップグレード、あるいはシームレス SSO の構成の変更から 48 時間以内のこのフォルダーのトレース ログ コンテンツ (**trace-\*.log** ファイル) を削除します。このアクションによって GDPR が適用されるデータが作成されるためです。

>[!IMPORTANT]
>このフォルダー内にある **PersistedState.xml** ファイルは削除しないでください。このファイルは Azure AD Connect の以前のインストールの状態を保持するために使用され、さらには、アップグレードのインストールが完了された場合にも使用されるためです。 このファイルが個人に関するデータを含むことはないため、絶対に削除しないでください。

これらのトレース ログ ファイルの確認と削除には Windows エクスプ ローラーを使用することもできますし、次のような PowerShell スクリプトを使用して、必要なアクションを実行することもできます。

```powershell
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

拡張子が ".PS1" のファイルにスクリプトを保存します。 必要に応じて、このスクリプトを実行してください。

関連する Azure AD Connect の GDPR 要件の詳細については、[こちらの記事](reference-connect-user-privacy.md)をご覧ください。

### <a name="note-about-domain-controller-logs"></a>ドメイン コントローラー ログに関する注意事項

監査ログが有効になっている場合、この製品では、お使いのドメイン コント ローラーのセキュリティ ログを生成できます。 監査ポリシーの構成に関する詳細については、[こちらの記事](/previous-versions/tn-archive/dd277403(v=technet.10))をご覧ください。

## <a name="next-steps"></a>次のステップ

* [セキュリティ センターで Microsoft のプライバシー ポリシーを確認する](https://www.microsoft.com/trustcenter)
  - [**トラブルシューティング**](tshoot-connect-sso.md) - この機能に関する一般的な問題を解決する方法を確認します。
  - [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 新しい機能の要求を提出します。