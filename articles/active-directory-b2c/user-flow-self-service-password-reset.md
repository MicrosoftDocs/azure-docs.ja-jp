---
title: Azure Active Directory B2C でのセルフサービス パスワード リセット | Microsoft Docs
description: Azure Active Directory B2C で顧客向けにセルフサービス パスワード リセットをセットアップする方法を示します
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0c9edaf3356ea4c1a521a89f2ec60a4b6ba1a5ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87481497"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>顧客向けにセルフサービス パスワード リセットをセットアップする

セルフサービス パスワード リセット機能を使用すると、ローカル アカウントにサインアップした顧客はパスワードを自分でリセットできます。 これによりサポート スタッフの負担を大幅に軽減でき、アプリケーションを定期的に使用する顧客が何百万人もいるような場合に特に効果的です。 現在、回復の手段としては、検証済みの電子メール アドレスの使用のみがサポートされています。

> [!NOTE]
> この記事は、ID プロバイダーとして **ローカル アカウント サインイン** を使用する標準 **サインイン** ユーザー フローのコンテキスト内で使用されるセルフサービス パスワード リセットに適用されます。 ご利用のアプリから呼び出される完全にカスタマイズ可能なパスワードのリセット ユーザー フローが必要な場合は、[こちらの記事](user-flow-overview.md)を参照してください。
>
>

既定のディレクトリでは、セルフサービス パスワード リセット は無効になっています。 この機能を有効にするには次のようにします。

1. サブスクリプション管理者として [Azure Portal](https://portal.azure.com/) にサインインします。 これは、ディレクトリを作成するときに使用したものと同じ職場/学校アカウント、または同じ Microsoft アカウントです。
2. (左側にあるナビゲーション バーで) **Azure Active Directory** を開きます。
3. オプション ブレードで下にスクロールし、 **[パスワードのリセット]** を選択します。
4. **[セルフ サービスによるパスワードのリセットが有効]** を **[すべて]** に設定します。
5. ページの上部にある **[保存]** をクリックします。 以上で終わりです。

テストするには、任意のサインイン ユーザー フロー (ID プロバイダーがローカル アカウントのもの) で [今すぐ実行] 機能を使用します。 ローカル アカウントのサインイン ページ (電子メール アドレスとパスワード、またはユーザー名とパスワードを入力する場所) で、 **[アカウントにアクセスできない場合]** をクリックしてカスタマー エクスペリエンスを確認します。

> [!NOTE]
> [セルフサービス パスワード リセット ] ページは、 [会社のブランド機能](../active-directory/fundamentals/customize-branding.md)を使用してカスタマイズできます。
>
>

