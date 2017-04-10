---
title: "Azure Active Directory B2C: セルフサービスによるパスワードのリセット | Microsoft Docs"
description: "Azure Active Directory B2C でコンシューマー向けにセルフサービスによるパスワードのリセットをセットアップする方法を示すトピック"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: curtand
ms.assetid: c87ed86e-1520-42b1-8c31-46cd44ed5310
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 4b13c040a15bef2f04d2cd2126e2270d061898bd
ms.openlocfilehash: 0508868e3b00c5771cc26038a3dd71fde6625a84
ms.lasthandoff: 12/29/2016


---
# <a name="azure-active-directory-b2c-set-up-self-service-password-reset-for-your-consumers"></a>Azure Active Directory B2C: コンシューマー向けにセルフサービスによるパスワードのリセットをセットアップする
セルフサービスのパスワード リセット機能を使用すると、コンシューマー (ローカル アカウントにサインアップしたユーザー) はパスワードを自分でリセットできます。 これによりサポート スタッフの負担を大幅に軽減でき、アプリケーションを定期的に使用するコンシューマーが何百万人もいるような場合に特に効果的です。 現在、回復の手段としては検証済みの電子メール アドレスのみがサポートされています。 将来的には、他の回復方法 (検証済みの電話番号、セキュリティの質問など) が追加される予定です。

> [!NOTE]
> この記事は、サインイン ポリシーのコンテキストで使用されるセルフ サービスのパスワード リセットに適用されます。 アプリケーションから呼び出される完全にカスタマイズ可能なパスワードのリセット ポリシーが必要な場合は、 [この記事](active-directory-b2c-reference-policies.md#create-a-password-reset-policy)を参照してください。
> 
> 

既定のディレクトリでは、セルフサービスによるパスワードのリセットは無効になっています。 この機能を有効にするには次のようにします。

1. サブスクリプション管理者として [Azure クラシック ポータル](https://manage.windowsazure.com/) にサインインします。 これは、ディレクトリを作成するときに使用したものと同じ職場/学校アカウント、または同じ Microsoft アカウントです。
2. 左側のナビゲーション バーで Active Directory 拡張機能に移動します。
3. **[ディレクトリ]** タブで、作成したディレクトリをクリックします。
4. [ **構成** ] タブをクリックします。
5. 下にスクロールして **[ユーザー パスワードのリセット ポリシー]** セクションに移動し、**[パスワードのリセットが有効になっているユーザー]** オプションを **[はい]** に変更します。 **[連絡用電子メール アドレス]** オプションがオンになっていることに注意してください (そのままにします)。
   
    ![セルフサービスのパスワード リセット](./media/active-directory-b2c-reference-sspr/sspr.png)
6. ページの下部にある **[保存]** をクリックします。 以上で終わりです。

テストするには、任意のサインイン ポリシー (ID プロバイダーがローカル アカウントのもの) で [今すぐ実行] 機能を使用します。 ローカル アカウントのサインイン ページ (電子メール アドレスとパスワードまたはユーザー名とパスワードを入力する場所) で、 **[アカウントにアクセスできない場合]** をクリックしてコンシューマー エクスペリエンスを確認します。

> [!NOTE]
> [セルフサービスによるパスワードのリセット] ページは、 [会社のブランド機能](../active-directory/active-directory-add-company-branding.md)を使用してカスタマイズできます。
> 
> 


