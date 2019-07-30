---
title: Azure AD コネクタ アカウント パスワードの変更 | Microsoft Docs
description: このトピックでは、Azure AD コネクタ アカウントを復元する方法を説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ea151ee79fccd66f1d9422744d8f57829677ec0
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204534"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Azure AD コネクタ アカウント パスワードの変更
Azure AD コネクタ アカウントは、無料のサービスであると想定されています。 その資格情報をリセットする必要がある場合、このトピックが役立ちます。 たとえば、グローバル管理者が PowerShell を使用してアカウントのパスワードを誤ってリセットしてしまった場合などです。

## <a name="reset-the-credentials"></a>資格情報をリセットする
Azure AD コネクタのアカウントが認証の問題のために Azure AD に接続できない場合、パスワードをリセットすることができます。

1. Azure AD Connect 同期サーバーにサインインし、PowerShell を起動します。
2. `Add-ADSyncAADServiceAccount`を実行します。  
   ![PowerShell コマンドレット addadsyncaadserviceaccount](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Azure AD グローバル管理者の資格情報を指定します。

このコマンドレットは、Azure AD と同期エンジンの両方で、サービス アカウントのパスワードをリセットして更新します。

## <a name="known-issues-these-steps-can-solve"></a>上記の手順で解決できる既知の問題
このセクションでは、ユーザーから報告された、Azure AD コネクタ アカウントの資格情報をリセットすることで修正されたエラーの一覧を示します。

---
イベント 6900  
The server encountered an unexpected error while processing a password change notification (パスワード変更通知の処理中に、サーバーで予期しないエラーが発生しました):  
AADSTS70002: Error validating credentials. (資格情報の検証中にエラーが発生しました。) AADSTS50054: 認証に古いパスワードが使用されました。

---
イベント 659  
Error while retrieving password policy sync configuration. (パスワード ポリシーの同期構成を取得しているときにエラーが発生しました。) Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Error validating credentials. (資格情報の検証中にエラーが発生しました。) AADSTS50054: 認証に古いパスワードが使用されました。

## <a name="next-steps"></a>次の手順
**概要トピック**

* [Azure AD Connect 同期:同期を理解してカスタマイズする](how-to-connect-sync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)

