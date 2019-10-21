---
title: Azure Active Directory B2C で顧客のサインアップ時のメール検証を無効にする
description: Azure Active Directory B2C で顧客のサインアップ時のメール検証を無効にする方法について説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9a7adc04991dd4a472bdaf1aa47aacaf6cdeb190
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256923"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Azure Active Directory B2C で顧客のサインアップ時のメール検証を無効にする

既定で、Azure Active Directory B2C (Azure AD B2C) では、顧客のローカル アカウント (メール アドレスまたはユーザー名を使用してサインアップしたユーザーのアカウント) のメール アドレスが検証されます。 Azure AD B2C では、メール アドレスが有効であることを確認するために、サインアップ時に顧客が検証することを必須にしています。 また、悪意のあるアクターが自動化されたプロセスを使用してアプリケーションに不正なアカウントを生成することを防ぐことができます。

アプリケーション開発者によっては、サインアップ プロセス時にはメールの検証をスキップし、代わりに後で顧客にメール アドレスを検証させることを好みます。 それをサポートするために、電子メールの検証を無効にするように Azure AD B2C を構成することができます。 そうすることで、サインアップ プロセスがよりスムーズになり、開発者はメール アドレスを検証した顧客と検証していない顧客を柔軟に区別することができます。

次の手順に従って、メールの検証を無効にします。

1. [Azure ポータル](https://portal.azure.com)
1. 上部のメニューにある **[ディレクトリとサブスクリプション]** フィルターを使用して、お使いの Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ユーザー フロー]** を選択します。
1. メールの検証を無効にするユーザー フローを選択します。 たとえば、*B2C_1_signinsignup* です。
1. **[Page layouts]\(ページ レイアウト\)** を選択します。
1. **[ローカル アカウント サインアップ ページ]** を選択します。
1. **[ユーザー属性]** で **[メール アドレス]** を選択します。
1. **[確認が必要]** ドロップダウンで **[いいえ]** を選択します。
1. **[保存]** を選択します。 このユーザー フローでは、メールの検証が無効になりました。

> [!WARNING]
> サインアップ プロセスでの電子メールの検証を無効にすると、スパムにつながる場合があります。 既定の Azure AD B2C で提供されるメールの検証を無効にする場合は、代替の検証システムを実装することをお勧めします。
