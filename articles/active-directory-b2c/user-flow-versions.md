---
title: Azure Active Directory B2C のユーザー フロー バージョン | Microsoft Docs
description: Azure Active Directory B2C で使用できるユーザー フローのバージョンについて説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1c05ff1bf1956943230bf523584025787495d57f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102517856"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C のユーザー フロー バージョン

Azure Active Directory B2C (Azure AD B2C) のユーザー フローは、ユーザー ID エクスペリエンスを完全に記述する共通の[ポリシー](user-flow-overview.md)を設定するために役立ちます。 このようなエクスペリエンスには、サインアップ、サインイン、パスワード リセット、プロファイル編集が含まれます。 次の表は、Azure AD B2C で使用できるユーザー フローを示しています。

> [!IMPORTANT]
> ユーザー フローのバージョンを参照する方法が変更されました。 これまでは、V1 (実稼働可能) バージョンと V1.1 および V2 (プレビュー) バージョンが提供されていました。 今後は、ユーザー フローが 2 つのバージョンに統合されます。
>
>- **推奨** ユーザー フローは、ユーザー フローの新しいプレビュー バージョンです。 これらは徹底的にテストされ、レガシ **V2** と **v1.1** バージョンのすべての機能が組み合わさっています。 今後は、この新しい推奨ユーザー フローが保守および更新されます。 これらの新しい推奨ユーザー フローに移動すると、新機能がリリースされる度にアクセスできるようになります。
>- **標準** ユーザー フロー (以前の **V1**) は、一般提供されており、実稼働可能なユーザー フローです。 ユーザー フローがミッション クリティカルで、非常に安定したバージョンに依存している場合、これらのバージョンは保守および更新されないことを理解した上で、標準ユーザー フローを使用し続けることができます。
>
>すべてのレガシ プレビュー ユーザー フロー (v1.1 および V2) は、**2021 年 8 月 1 日** までに非推奨となる予定です。 可能な限り、最新の機能と更新プログラムをいつでも利用できるように、できるだけ早く [新しい **推奨** バージョンに切り替える](#how-to-switch-to-a-new-recommended-user-flow)ことを強くお勧めします。 *これらの変更は、Azure パブリック クラウドにのみ適用されます。その他の環境では、[レガシ ユーザー フローのバージョンの管理](user-flow-versions-legacy.md)が引き続き使用されます。*

## <a name="recommended-user-flows"></a>推奨ユーザー フロー

推奨ユーザー フローは、レガシ V2 および v1.1 の機能に新機能を組み合わせたプレビュー バージョンです。 今後は、推奨ユーザー フローが保守および更新されます。

| ユーザー フロー | 説明 |
| --------- | ----------- |
| パスワードのリセット (プレビュー) | ユーザーが電子メールを確認してから新しいパスワードを選択できるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](multi-factor-authentication.md)</li><li>トークンの互換性の設定</li><li>[年齢制限](age-gating.md)</li><li>[パスワードの複雑さの要件](password-complexity.md)</li></ul> |
| プロファイルの編集 (プレビュー) | ユーザーがユーザー属性を構成することを許可します。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li></ul> |
| サインイン (プレビュー) | ユーザーが自分のアカウントにサインインできるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](multi-factor-authentication.md)</li><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>[年齢制限](age-gating.md)</li><li>サインイン ページのカスタマイズ</li></ul> |
| サインアップ (プレビュー) | ユーザーがアカウントを作成できるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](multi-factor-authentication.md)</li><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>[年齢制限](age-gating.md)</li><li>[パスワードの複雑さの要件](password-complexity.md)</li></ul> |
| サインアップとサインイン (プレビュー) | ユーザーがアカウントを作成したり、アカウントにサインインしたりすることができるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](multi-factor-authentication.md)</li><li>[年齢制限](age-gating.md)</li><li>[パスワードの複雑さの要件](password-complexity.md)</li></ul> |

## <a name="standard-user-flows"></a>標準ユーザー フロー

標準ユーザー フロー (以前は V1 と呼ばれていました) は、一般提供されており、実稼働可能なユーザー フローです。 標準ユーザー フローは今後更新されません。

| ユーザー フロー | 説明 |
| --------- | ----------- | ----------- |
| パスワードのリセット | ユーザーが電子メールを確認してから新しいパスワードを選択できるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](multi-factor-authentication.md)</li><li>トークンの互換性の設定</li><li>[パスワードの複雑さの要件](password-complexity.md)</li></ul> |
| プロファイル編集 | ユーザーがユーザー属性を構成することを許可します。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li></ul> |
| サインイン | ユーザーが自分のアカウントにサインインできるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](multi-factor-authentication.md)</li><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>サインインのブロック</li><li>パスワードの強制的なリセット</li><li>サインインしたままにする (KMSI)</ul><br>このユーザー フローではユーザー インターフェイスをカスタマイズできません。 |
| サインアップ | ユーザーがアカウントを作成できるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](multi-factor-authentication.md)</li><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>[パスワードの複雑さの要件](password-complexity.md)</li></ul> |
| サインアップとサインイン | ユーザーがアカウントを作成したり、アカウントにサインインしたりすることができるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](multi-factor-authentication.md)</li><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>[パスワードの複雑さの要件](password-complexity.md)</li></ul>|


## <a name="how-to-switch-to-a-new-recommended-user-flow"></a>新しい推奨ユーザー フローに切り替える方法

レガシ バージョンのユーザー フローから新しい **推奨** プレビュー バージョンに切り替えるには、次の手順を実行します。

1. 「[チュートリアル:Azure Active Directory B2C 内にユーザー フローを作成する](tutorial-create-user-flows.md)」の手順に従って、新しいユーザー フロー ポリシーを作成します。 ユーザー フローの作成時に、**推奨** バージョンを選択します。

3. レガシ ポリシーで構成したものと同じ設定を使用して、新しいユーザー フローを構成します。

4. アプリケーションのサインイン URL を、新しく作成したポリシーに更新します。

5. ユーザー フローをテストし、動作していることを確認したら、次の手順に従ってレガシ ユーザー フローを削除します。
   1. Azure AD B2C テナントの概要メニューで、 **[ユーザー フロー]** を選択します。
   2. 削除するユーザー フローを見つけます。
   3. 最後の列で、コンテキスト メニュー ( **...** ) を選択し、 **[削除]** を選択します。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="can-i-still-create-legacy-v2-and-v11-user-flows"></a>レガシ V2 と v1.1 ユーザー フローは今後も作成できますか?

レガシ V2 と v1.1 のバージョンに基づいて新しいユーザー フローを作成することはできませんが、現在使用しているレガシ V2 と v1.1 ユーザー フローは引き続き、読み取り、更新、および削除できます。

### <a name="is-there-any-reason-to-continue-using-legacy-v2-and-v11-user-flows"></a>レガシ V2 と v1.1 ユーザー フローを引き続き使用する理由はありますか?

特にはありません。 新しい **推奨** プレビュー バージョンには、レガシ V2 と v1.1 バージョンと同じ機能が含まれています。 削除された内容はなく、実際には追加機能が含まれています。

### <a name="if-i-dont-switch-from-legacy-v2-and-v11-policies-how-will-it-impact-my-application"></a>レガシ V2 と V1.1 ポリシーから切り替えないと、アプリケーションにどのような影響がありますか?

レガシ V2 または V1.1 ユーザー フローを使用している場合、アプリケーションはこのバージョン管理の変更の影響を受けません。 ただし、今後新しい機能やポリシーの変更にアクセスできるようにするには、新しい **推奨** バージョンに切り替える必要があります。

### <a name="will-microsoft-still-support-my-legacy-v2-or-v11-user-flow-policy"></a>Microsoft では、現在使用しているレガシ V2 または v1.1 のユーザー フロー ポリシーは引き続きサポートされますか?

ユーザー フローのレガシ V2 と 1.1 バージョンは、引き続き完全にサポートされます。
