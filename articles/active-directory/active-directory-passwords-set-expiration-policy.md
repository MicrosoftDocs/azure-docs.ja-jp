---
title: "Azure Active Directory でパスワード有効期限ポリシーを設定する | Microsoft Docs"
description: "個別または一括で Azure Active Directory のパスワードの有効期限ポリシーを確認する方法、およびパスワードの有効期限を変更する方法を説明します"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 6887250c-15d4-4b59-a161-f0380c0f0acb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: ab55d08c78f6c333819dc31936b24ae6797870bb
ms.lasthandoff: 03/28/2017


---
# <a name="set-password-expiration-policies-in-azure-active-directory"></a>Azure Active Directory でパスワード有効期限ポリシーを設定する
> [!IMPORTANT]
> **サインインに問題がありますか?** その場合は、[自分のパスワードを変更してリセットする方法をここから参照してください](active-directory-passwords-update-your-own-password.md#reset-your-password)にお進みください。
>
>

Microsoft クラウド サービスのグローバル管理者は、Windows PowerShell 用 Microsoft Azure Active Directory モジュールを使用して、ユーザーのパスワードの有効期限が切れないように設定できます。 また、Windows PowerShell コマンドレットを使用すると、期限が切れない構成を削除したり、期限が切れないように設定されているユーザー パスワードを確認したりすることもできます。 この記事は、ID およびディレクトリ サービスを Microsoft Azure Active Directory に依存する、Microsoft Intune や Office 365 などのクラウド サービスのヘルプです。

> [!NOTE]
> 有効期限が切れないように構成できるのは、ディレクトリ同期によって同期されていないユーザー アカウントのパスワードだけです。 ディレクトリ同期の詳細については、 [ディレクトリ同期のロードマップ](https://msdn.microsoft.com/library/azure/hh967642.aspx)に関するページのトピック一覧を参照してください。
>
>

Windows PowerShell コマンドレットを使用するには、最初に Windows PowerShell をインストールする必要があります。

## <a name="what-do-you-want-to-do"></a>どの操作を行いますか。
* [パスワードの有効期限ポリシーを確認する方法](#how-to-check-expiration-policy-for-a-password)
* [パスワードを期限付きに設定する](#set-a-password-to-expire)
* [パスワードを無期限に設定する](#set-a-password-to-never-expire)

## <a name="how-to-check-expiration-policy-for-a-password"></a>パスワードの有効期限ポリシーを確認する方法
1. 会社の管理者の資格情報を使用して Windows PowerShell に接続します。
2. 次のいずれかを実行します。

   * 特定のユーザーについてパスワードの有効期限が切れないかどうかを確認するには、確認するユーザーのユーザー プリンシパル名 (UPN) (例: aprilr@contoso.onmicrosoft.com) またはユーザー ID を使用して、次のコマンドレットを実行します。`Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * すべてのユーザーについて「パスワードを無期限にする」設定を表示するには、次のコマンドレットを実行します。 `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

## <a name="set-a-password-to-expire"></a>パスワードを期限付きに設定する
1. 会社の管理者の資格情報を使用して Windows PowerShell に接続します。
2. 次のいずれかを実行します。

   * 特定のユーザーのパスワードを期限付きに設定するには、ユーザーのユーザー プリンシパル名 (UPN) またはユーザー ID を使用して、次のコマンドレットを実行します。 `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * 組織内のすべてのユーザーのパスワードを期限付きに設定するには、次のコマンドレットを使用します。 `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

## <a name="set-a-password-to-never-expire"></a>パスワードを無期限に設定する
1. 会社の管理者の資格情報を使用して Windows PowerShell に接続します。
2. 次のいずれかを実行します。

   * 特定のユーザーのパスワードを無期限に設定するには、ユーザーのユーザー プリンシパル名 (UPN) またはユーザー ID を使用して、次のコマンドレットを実行します。 `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * 組織内のすべてのユーザーのパスワードを無期限に設定するには、次のコマンドレットを実行します。 `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

## <a name="next-steps"></a>次のステップ
* **サインインに問題がありますか?** その場合は、[自分のパスワードを変更してリセットする方法をここから参照してください](active-directory-passwords-update-your-own-password.md#reset-your-password)にお進みください。

