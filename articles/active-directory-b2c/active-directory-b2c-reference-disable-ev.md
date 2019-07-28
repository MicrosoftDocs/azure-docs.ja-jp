---
title: Azure Active Directory B2C でコンシューマーのサインアップ時の電子メール検証を無効にする | Microsoft Docs
description: Azure Active Directory B2C でコンシューマーのサインアップ時の電子メール検証を無効にする方法を示すトピック。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: b2c
ms.openlocfilehash: 8b50b59b6a1f99787b842923cd6ec77ee6500f0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66509531"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でコンシューマーのサインアップ時のメール検証を無効にする 
有効である場合、Azure Active Directory (Azure AD) B2C は、コンシューマーが電子メール アドレスを指定してローカル アカウントを作成することによってアプリケーションにサインアップできるようにします。 Azure AD B2C は、電子メール アドレスが有効であることを確認するために、コンシューマーのサインアップ中にその検証を要求します。 また、悪意のある自動化されたプロセスがアプリケーション用の偽のアカウントを生成できないようにします。

一部のアプリケーション開発者は、サインアップ プロセス中の電子メールの検証はスキップして、代わりに後でコンシューマーに電子メール アドレスを検証させようとします。 それをサポートするために、電子メールの検証を無効にするように Azure AD B2C を構成することができます。 そうすることで、サインアップ プロセスがよりスムーズになり、開発者は電子メール アドレスを検証したコンシューマーと検証していないコンシューマーを柔軟に区別することができます。

既定では、サインアップ ユーザー フローで電子メールの検証が有効になっています。 この機能を無効にするには、次のようにします。

1. **[User Flows]** をクリックします。
2. ユーザー フロー (例: "B2C_1_SiUp") をクリックして開きます。 
3. **[ページ レイアウト]** をクリックします。
4. **[ローカル アカウントのサインアップ ページ]** をクリックします。
5. **[ユーザー属性]** セクションの **[名前]** 列の **[メール アドレス]** をクリックします。
6. **[確認が必要]** で **[いいえ]** を選択します。
7. ブレードの上部にある **[保存]** をクリックします。 以上で終わりです。

> [!NOTE]
> サインアップ プロセスでの電子メールの検証を無効にすると、スパムにつながる場合があります。 既定値を無効にする場合は、独自の検証システムを追加することをお勧めします。
> 
>
