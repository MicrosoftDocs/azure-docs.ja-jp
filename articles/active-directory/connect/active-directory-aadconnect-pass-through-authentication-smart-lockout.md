---
title: "Azure AD Connect: パススルー認証 - スマート ロックアウト | Microsoft Docs"
description: "この記事では、Azure Active Directory (Azure AD) パススルー認証がクラウドのブルート フォース パスワード攻撃からオンプレミスのアカウントを保護する方法について説明します。"
services: active-directory
keywords: "Azure AD Connect パススルー認証, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 9633e79929329470c2def2b1d06d95994ab66e38
ms.openlocfilehash: c84b2406e6373701c83c509342129bd6d7d4034b
ms.contentlocale: ja-jp
ms.lasthandoff: 08/04/2017

---

# <a name="azure-active-directory-pass-through-authentication-smart-lockout"></a>Azure Active Directory パススルー認証: スマート ロックアウト

## <a name="overview"></a>概要

Azure AD は、ブルート フォース パスワード攻撃から保護し、正規のユーザーが Office 365 および SaaS アプリケーションからロックアウトされることを防ぎます。 **スマート ロックアウト**と呼ばれるこの機能は、サインイン方法としてパススルー認証を使っている場合にサポートされます。 スマート ロックアウトは、既定ですべてのテナントに対して有効になり、常にユーザー アカウントを保護しています。有効にする必要はありません。

スマート ロックアウトは、失敗したサインインの試行の追跡を維持することによって動作し、特定の**ロックアウトしきい値**の後で、**ロックアウト期間**を開始します。 ロックアウト期間中に攻撃者がサインインしようとしても拒否されます。 攻撃が継続して発生する場合、ロックアウト期間終了後にサインインの試行が失敗すると、ロックアウト期間がさらに長くなります。

>[!NOTE]
>既定のロックアウトしきい値は試行失敗 10 個であり、既定のロックアウト期間は 60 秒です。

また、スマート ロックアウトは正規のユーザーによるサインインと攻撃者によるサインインを区別し、ほとんどの場合は攻撃者のみをロックアウトします。 この機能は、攻撃者の悪意によって正規のユーザーがロックアウトされるのを防ぎます。 正規のユーザーと攻撃者を区別するには、過去のサインイン動作、ユーザーのデバイスとブラウザー、その他のシグナルが使われます。 アルゴリズムは常に改善されています。

パススルー認証はパスワード検証要求をオンプレミスの Active Directory (AD) に転送するため、攻撃者がユーザーの AD アカウントをロックアウトするのを防ぐ必要があります。 独自の AD アカウント ロックアウト ポリシーがあるので (具体的には、[**アカウントのロックアウトのしきい値**](https://technet.microsoft.com/library/hh994574(v=ws.11).aspx)と[**ロックアウト カウンターのリセット ポリシー**](https://technet.microsoft.com/library/hh994568(v=ws.11).aspx))、攻撃者がオンプレミスの AD に到達する前にクラウド内で排除されるよう、Azure AD のロックアウトしきい値とロックアウト期間の値を適切に構成する必要があります。

>[!NOTE]
>スマート ロックアウト機能は無料で、すべてのユーザーに対して既定で_オン_になっています。 ただし、Graph API を使って Azure AD のロックアウトしきい値とロックアウト期間の値を変更するには、テナントが Azure AD Premium P2 のライセンスを少なくとも 1 つは所有している必要があります。 パススルー認証を使ったスマート ロックアウト機能を利用するために "_ユーザーごと_" の Azure AD Premium P2 ライセンスは必要ありません。

ユーザーのオンプレミスの AD アカウントを適切に保護するには、以下のようにする必要があります。

1.  Azure AD のロックアウトしきい値が、AD のアカウント ロックアウトしきい値より "_小さく_" なるようにします。 AD のアカウント ロックアウトしきい値が Azure AD のロックアウトしきい値の少なくとも 2 ～ 3 倍になるように、値を設定することをお勧めします。
2.  Azure AD のロックアウト期間 (秒単位) が、AD のロックアウト カウンターのリセット (分単位) より "_長く_" なるようにします。

## <a name="verify-your-ad-account-lockout-policies"></a>AD アカウント ロックアウト ポリシーを確認する

AD アカウント ロックアウト ポリシーを確認するには、次のようにします。

1.  グループ ポリシー管理ツールを開きます。
2.  既定のドメイン ポリシーなど、すべてのユーザーに適用されるグループ ポリシーを編集します。
3.  [コンピューターの構成]、[ポリシー]、[Windows の設定]、[セキュリティの設定]、[アカウント ポリシー]、[アカウント ロックアウトのポリシー] の順に移動します。
4.  [アカウントのロックアウトのしきい値] と [ロックアウト カウンターのリセット] の値を確認します。

![AD アカウント ロックアウト ポリシー](./media/active-directory-aadconnect-pass-through-authentication/pta5.png)

## <a name="use-the-graph-api-to-manage-your-tenants-smart-lockout-values"></a>テナントのスマート ロックアウトの値を管理するには、Graph API を使います。

>[!IMPORTANT]
>Graph API を使って Azure AD のロックアウトしきい値とロックアウト期間の値を変更するのは、Azure AD Premium P2 の機能です。 また、テナントのグローバル管理者が行う必要があります。

[Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)を使って、Azure AD のスマート ロックアウトの値を読み取り、設定し、更新できます。 ただし、これらの操作はプログラムでも実行できます。

### <a name="read-smart-lockout-values"></a>スマート ロックアウトの値を読み取る

テナントのスマート ロックアウトの値を読み取るには、次の手順のようにします。

1. テナントのグローバル管理者として Graph Explorer にサインインします。 メッセージが表示されたら、要求されたアクセス許可のアクセスを許可します。
2. [Modify permissions]\(アクセス許可の変更\) をクリックし、"Directory.ReadWrite.All" を選びます。
3. Graph API の要求で、バージョンを "BETA" に、要求の種類を "GET" に、URL を `https://graph.microsoft.com/beta/<your-tenant-domain>/settings` に設定します。
4. [Run Query]\(クエリの実行\) をクリックして、テナントのスマート ロックアウトの値を確認します。 それまでにテナントの値を設定していない場合、空のセットが表示されます。

### <a name="set-smart-lockout-values"></a>スマート ロックアウトの値を設定する

テナントのスマート ロックアウトの値を設定するには、次の手順のようにします (初回のみ)。

1. テナントのグローバル管理者として Graph Explorer にサインインします。 メッセージが表示されたら、要求されたアクセス許可のアクセスを許可します。
2. [Modify permissions]\(アクセス許可の変更\) をクリックし、"Directory.ReadWrite.All" を選びます。
3. Graph API の要求で、バージョンを "BETA" に、要求の種類を "POST" に、URL を `https://graph.microsoft.com/beta/<your-tenant-domain>/settings` に設定します。
4. 次の JSON 要求をコピーし、"Request Body" に貼り付けます。 必要に応じてスマート ロックアウトの値を変更し、`templateId` にはランダムな GUID を使います。
5. [Run Query]\(クエリの実行\) をクリックして、テナントのスマート ロックアウトの値を設定します。

```
{
  "templateId": "5cf42378-d67d-4f36-ba46-e8b86229381d",
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "300"
    },
    {
      "name": "LockoutThreshold",
      "value": "5"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

>[!NOTE]
>これらの値を使っていない場合は、**BannedPasswordList** を空 ("")、**EnableBannedPasswordCheck** を "false" のままにします。

[こちらの手順](#read-smart-lockout-values)を使って、テナントのスマート ロックアウトの値が正しく設定されていることを確認します。

### <a name="update-smart-lockout-values"></a>スマート ロックアウトの値を更新する

テナントのスマート ロックアウトの値を更新するには、次の手順のようにします (前に設定してある場合)。

1. テナントのグローバル管理者として Graph Explorer にサインインします。 メッセージが表示されたら、要求されたアクセス許可のアクセスを許可します。
2. [Modify permissions]\(アクセス許可の変更\) をクリックし、"Directory.ReadWrite.All" を選びます。
3. [テナントのスマート ロックアウトの値を読み取るには、こちらの手順のようにします](#read-smart-lockout-values)。 項目 "displayName" と "PasswordRuleSettings" の `id` の値 (GUID) をコピーします。
4. Graph API の要求で、バージョンを "BETA" に、要求の種類を "PATCH" に、URL を `https://graph.microsoft.com/beta/<your-tenant-domain>/settings/<id>` に設定します。`<id>` には、手順 3 の GUID を使います。
5. 次の JSON 要求をコピーし、"Request Body" に貼り付けます。 スマート ロックアウトの値を適切に変更します。
6. [Run Query]\(クエリの実行\) をクリックして、テナントのスマート ロックアウトの値を更新します。

```
{
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "30"
    },
    {
      "name": "LockoutThreshold",
      "value": "4"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

[こちらの手順](#read-smart-lockout-values)を使って、テナントのスマート ロックアウトの値が正しく更新されていることを確認します。

## <a name="next-steps"></a>次のステップ
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 新しい機能の要求を提出します。

