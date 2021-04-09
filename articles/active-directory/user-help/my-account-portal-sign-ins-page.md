---
title: '[自分のサインイン] ページから最近のサインイン アクティビティを表示および検索する - Azure Active Directory | Microsoft Docs'
description: マイ アカウント ポータルの [自分のサインイン] ページで、最近のサインインアクティビティを表示および検索する方法について詳しく説明します。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/19/2021
ms.author: curtand
ms.openlocfilehash: 1062c8dfe416af2d39063bf71f39d52e151e28d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100096074"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-page"></a>[自分のサインイン] ページから最近のサインイン アクティビティを表示および検索する

**マイ アカウント** ポータルの **[自分のサインイン]** ページで、すべての最近の職場または学校アカウントのサインインアクティビティを表示できます。 サインイン履歴を確認すると、次の点を確認して、異常なアクティビティを確認することができます。

- だれかがパスワードを推測しようとしているような痕跡。
- 普段の利用場所とは異なるところから攻撃者が自分のアカウントへのサインインに成功したこと。
- 攻撃者がアクセスしようとしたアプリ。

>[!Note]
> 個人用 Microsoft アカウントでのサインイン時にエラーが発生した場合でも、組織のドメイン名 (contoso.com など) または組織の **テナント ID** (管理者から入手) を使用して、次のいずれかの URL でサインインできます。
>
>   - https://myaccount.microsoft.com?tenantId=*your_domain_name*
>   - https://myaccount.microsoft.com?tenant=*your_tenant_ID*

## <a name="view-your-recent-sign-in-activity"></a>最近のサインイン アクティビティを表示する

1. 職場または学校アカウントにサインインした後、、 https://myaccount.microsoft.com/ ページに移動します。

2. 左側のナビゲーション ウィンドウから **[自分のサインイン]** を選択するか、 **[自分のサインイン]** ブロックから **[最近のアクティビティを確認する]** リンクを選択します。

    ![[最近のアクティビティ] リンクが強調表示されている [マイ アカウント] ページ](media/my-account-portal/my-account-portal-sign-ins.png)

3. サインイン項目を展開し、それぞれに覚えがあることを確認します。 覚えがないサインイン項目を見つけた場合は、お使いのアカウントにセキュリティ侵害が発生した場合に備えて、お使いのパスワードを変更してください。

    ![サインインの詳細が展開されている [最近のアクティビティ] ページ](media/my-account-portal-sign-ins-page/recent-activity.png)

### <a name="if-you-see-a-successful-sign-in"></a>サインインの成功が表示される場合

場合によっては、通常のサインイン アクティビティを確認しているときに、覚えがない場所、ブラウザー、またはオペレーティング システムからのサインインに成功していることに気付く場合があります。 覚えがないサインインは、攻撃者がそのアカウントにアクセスしたことを意味する可能性があります。 許可していないアクティビティに気付いた場合は、すぐにパスワードを変更し、[[セキュリティ情報]](https://mysignins.microsoft.com/security-info) に移動して、セキュリティ設定を更新することをお勧めします。

何かが不適切であることを判断する前に、偽陽性 (項目が疑わしくとも問題ないこと) が表示されていないことを確認してください。 たとえば、Microsoft はユーザーの IP アドレスに基づいて、おおよその場所とマップを判断します。 モバイル ネットワークは、遠く離れた場所にトラフィックをルーティングすることがあるため、特に特定するのは困難です。 ワシントン州でモバイル デバイスを使用してサインインした場合でも、位置情報にはカリフォルニア州からのサインインと表示されることがあります。 場所だけでなく、詳細を確認することを強くお勧めします。 オペレーティング システム、ブラウザー、およびアプリもすべて納得できるものであることを確認してください。

### <a name="if-you-see-an-unsuccessful-sign-in"></a>サインインの失敗が表示される場合

サインインの失敗が表示される場合は、資格情報を誤って入力した可能性があります。 攻撃者がパスワードを推測しようとしている可能性もあります。 このリスクに対応するには、パスワードを変更する必要はありませんが、Azure AD Multi-Factor Authentication (MFA) に登録することをお勧めします。 多要素認証を使用すると、ハッカーがパスワードを推測したとしても、それだけではアカウントにアクセスできません。

![サインインの失敗のタイル](media/my-account-portal-sign-ins-page/unsuccessful.png)

失敗したサインインが表示され、 **[セッション アクティビティ]** の下に「`Additional verification failed, invalid code`」という記載が表示された場合、これは、プライマリ認証資格情報は成功した一方で、多要素認証が失敗したことを意味します。 この状態は、攻撃者がパスワードを正しく推測したが、多要素認証の要求に応えることができなかったことを意味する可能性があります。 この場合でもパスワードを変更することをお勧めします。攻撃者がそれを既に取得していて、[[セキュリティ情報]](https://mysignins.microsoft.com/security-info) ページに移動してセキュリティ設定を更新してしまう可能性があるためです。

## <a name="search-for-specific-sign-in-activity"></a>特定のサインイン アクティビティを検索する

利用可能な情報によって、最近のサインイン アクティビティを検索することができます。 たとえば、オペレーティング システム、場所、アプリなどによって、最近のサインイン アクティビティを検索できます。

1. **[最近のアクティビティを確認する]** ページで、**検索** バーに検索する情報を入力します。 たとえば、「`Unsuccessful`」と入力すると、マイ アカウント アプリによって収集されたすべてのサインインの失敗アクティビティが検索されます。

2. 検索を開始するには、**検索** ボタンを選択します。

    ![検索バー、検索ボタン、結果が強調表示された [最近のアクティビティ] ページ](media/my-account-portal-sign-ins-page/sign-in-search.png)

### <a name="confirm-unusual-activity"></a>通常とは異なるアクティビティを確認する

通常とは異なるアクティビティのフラグが付いているサインインは、 **[自分のサインイン]** ページのそのアクティビティのタイルで確認できます。

![通常とは異なるサインインを試行したかどうかを確認するためのタイル](media/my-account-portal-sign-ins-page/this-wasnt-me.png)

## <a name="next-steps"></a>次のステップ

最近のサインイン アクティビティを表示した後に、次の操作を実行できます。

- [セキュリティ情報](./security-info-setup-signin.md)を表示または管理する。

- 接続されている[デバイス](my-account-portal-devices-page.md)を表示または管理する。

- [組織](my-account-portal-organizations-page.md)を表示または管理する。

- 組織での[プライバシー関連データの使用](my-account-portal-privacy-page.md)方法を表示します。

- [マイ アカウント ポータルの設定](my-account-portal-settings.md)を変更する