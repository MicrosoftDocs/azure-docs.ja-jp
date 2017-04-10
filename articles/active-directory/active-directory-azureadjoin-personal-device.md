---
title: "個人のデバイスを組織に参加させる | Microsoft Docs"
description: "ユーザーが個人用の Windows 10 デバイスを企業ネットワークに登録する方法と、BYOD シナリオのデプロイメント手順について説明します。"
services: active-directory
documentationcenter: 
author: femila
manager: femila
editor: 
tags: azure-classic-portal
ms.assetid: 9f3d38f5-1cfd-43d4-97da-4fed1255a1ff
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: bf9f0ae5876af6f9e92f86f0d49315ccb5ff06cf
ms.openlocfilehash: 5f25bee0d6cb4c6985a63073e19929f8d69de139


---
# <a name="join-a-personal-device-to-your-organization"></a>個人のデバイスを組織に参加させる
## <a name="to-join-a-windows-10-device-to-your-organization"></a>Windows 10 デバイスを組織に参加させるには
1. **[スタート]** メニューの **[設定]** を選択します。
2. **[アカウント]** を選択し、**[自分のアカウント]** をクリックします。
3. **[職場または学校のアカウントの追加]**をクリックし、組織のアカウントを入力します。
4. 組織のサインイン ページでユーザー名とパスワードを入力し、 **[OK]**をクリックします。
5. Multi-Factor Authentication で必要な情報の入力を求められます (これは IT 管理者によって構成されています)。
6. Azure Active Directory (Azure AD) によって、このデバイスをモバイル デバイス管理に登録する必要があるかどうかがチェックされます。
7. Windows によって、デバイスが Azure AD の組織のディレクトリに登録され、必要に応じてモバイル デバイス管理に登録されます。
8. 管理対象ユーザーの場合は、自動サインインによりデスクトップに移動します。
9. フェデレーション ユーザーの場合は、資格情報を入力するための Windows サインイン画面が表示されます。

## <a name="additional-information"></a>追加情報
* [エンタープライズ向け Windows 10: デバイスを仕事に使用する方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 参加を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-user-upgrade.md)
* [Microsoft Passport 経由でのパスワードを使用しない ID の認証](active-directory-azureadjoin-passport.md)
* [Azure AD 参加の使用シナリオについて](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 エクスペリエンスのためのドメイン参加済みデバイスの Azure AD への接続](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD Join の設定](active-directory-azureadjoin-setup.md)




<!--HONumber=Dec16_HO5-->


