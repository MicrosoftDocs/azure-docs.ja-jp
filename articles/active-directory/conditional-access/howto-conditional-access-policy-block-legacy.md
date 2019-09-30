---
title: 条件付きアクセス - レガシ認証をブロックする - Azure Active Directory
description: カスタムの条件付きアクセス ポリシーを作成して、レガシ認証プロトコルをブロックします
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: d802e48231a03fbf3fee9439894371188f31d5e1
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576006"
---
# <a name="conditional-access-block-legacy-authentication"></a>条件付きアクセス:レガシ認証をブロックする

レガシ認証プロトコルに関連したリスクが増大しているため、Microsoft では組織に対して、これらのプロトコルを使用した認証要求をブロックし、先進認証を必須にすること推奨しています。

## <a name="create-a-conditional-access-policy"></a>条件付きアクセス ポリシーを作成する

次の手順では、割り当てられた管理者ロールに対して、多要素認証の実行を必須にする条件付きアクセス ポリシーを作成します。

1.  **Azure portal**  にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[条件付きアクセス]** の順に移動します。
1. **[新しいポリシー]** を選択します。
1. ポリシーに名前を付けます。 ポリシーの名前に対する意味のある標準を組織で作成することをお勧めします。
1. **[割り当て]** で、 **[ユーザーとグループ]** を選択します。
   1. **[Include]\(含める\)** で、 **[すべてのユーザー]** を選択します。
   1. **[Exclude]\(除外\)** で、 **[ユーザーとグループ]** を選択し、今後もレガシ認証を使用できる必要があるアカウントをすべて選択します。 
   1. **[完了]** を選択します。
1. **[Cloud apps or actions]\(クラウド アプリまたはアクション\)**  >  **[Include]\(含める\)** で、 **[すべてのクラウド アプリ]** を選択します。
   1. 特定のアプリケーションをポリシーから除外する必要がある場合は、 **[除外されたクラウド アプリの選択]** で **[除外]** タブから選択して **[選択]** を選びます。
   1. **[完了]** を選択します。
1. **[条件]**  >  **[Client apps (preview)]\(クライアント アプリ (プレビュー)\)** で、 **[Configure]\(構成する\)** を **[はい]** に設定します。
   1. **[モバイル アプリとデスクトップ クライアント]**  >  **[Other clients]\(その他のクライアント\)** チェック ボックスのみをオンにします。
   2. **[完了]** を選択します。
1. **アクセス制御** > **許可** で、**アクセスのブロック** を選択します。
   1. **[選択]** を選択します。
1. 設定を確認し、 **[Enable policy]\(ポリシーの有効化\)** を **[オン]** に設定します。
1. **[作成]** を選択して、ポリシーを作成および有効化します。

## <a name="next-steps"></a>次の手順

[Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)

[Simulate sign in behavior using the Conditional Access What If tool](troubleshoot-conditional-access-what-if.md) (条件付きアクセスの What If ツールを使用したサインイン動作のシミュレート)
