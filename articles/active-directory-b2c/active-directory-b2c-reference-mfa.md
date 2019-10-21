---
title: Azure Active Directory B2C の Multi-Factor Authentication | Microsoft Docs
description: Azure Active Directory B2C によってセキュリティ保護されているコンシューマー向けアプリケーションで Multi-factor Authentication を有効にする方法。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0b872fcfbc2ead2cebdd32ff718b582c13af314e
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065579"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Azure Active Directory B2C の多要素認証 | Microsoft Docs

Azure Active Directory B2C (Azure AD B2C) は [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) と直接統合されているため、ご自分のアプリケーションのサインアップおよびサインイン エクスペリエンスに第 2 のセキュリティ層を簡単に追加できます。 コードを 1 行も記述する必要なく多要素認証を有効にできます。 サインアップおよびサインイン ユーザー フローを既に作成していても、多要素認証を有効にできます。

この機能を利用すると、次のようなシナリオをアプリケーションで処理するのに役立ちます。

- 多要素認証が、あるアプリケーションのアクセスには必要ないが、別のアプリケーションのアクセスには必要な場合。 たとえば、顧客が自動車保険アプリケーションにサインインするにはソーシャルまたはローカル アカウントを使用できますが、同じディレクトリに登録されている住宅保険アプリケーションにアクセスするには事前に電話番号を確認する必要があるような場合です。
- 通常のアプリケーションへのアクセスには多要素認証は必要ないが、アプリケーション内の機密性が高い部分へのアクセスにはそれが必要である場合。 たとえば、顧客が銀行取引アプリケーションにサインインし、口座の残高を確認するにはソーシャルまたはローカル アカウントを使用できますが、ネット送金を行うには事前に電話番号の確認が必要になるような場合です。

## <a name="set-multi-factor-authentication"></a>多要素認証の設定

ユーザー フローを作成する場合、多要素認を有効にするオプションがあります。

![多要素認証の設定](./media/active-directory-b2c-reference-mfa/add-policy.png)

**[多要素認証]** を **[有効]** に設定します。

エクスペリエンスを検証するには、 **[ユーザー フローを実行します]** を使用します。 次のシナリオで確認してみましょう。

多要素認証の手順が実行される前に、顧客のアカウントがテナントに作成されます。 この手順の過程で、顧客は自分の電話番号を入力し、確認することを求められます。 検証が成功した場合、後で使用できるように電話番号がアカウントに関連付けられます。 顧客がキャンセルまたは中止した場合であっても、多要素認証が有効になっている場合、次にサインインするときに顧客は電話番号の確認を再度求められる場合があります。

## <a name="add-multi-factor-authentication"></a>多要素認証の追加

以前作成したユーザー フローで多要素認証を有効にすることもできます。

多要素認証を有効にするには

1. ユーザー フローを開き、 **[プロパティ]** を選択します。
2. **[多要素認証]** の横にある **[有効]** を選択します。
3. ページの上部にある **[保存]** をクリックします。


