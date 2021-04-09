---
title: グループの名前付けポリシーのクイック スタート - Azure Active Directory | Microsoft Docs
description: Azure Active Directory で新しいユーザーを追加する方法または既存のユーザーを削除する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6013d14d7639bf4f7fe318c9a2da96e4d8acbcb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96546286"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>クイック スタート: Azure Active Directory におけるグループの名前付けポリシー

このクイックスタートでは、Azure Active Directory (Azure AD) 組織に存在するグループの並べ替えと検索がしやすいよう、ユーザーによって作成される Microsoft 365 グループの名前付けポリシーを組織に設定します。 名前付けポリシーの使用例を次に示します。

* グループ、メンバーシップ、地理的地域の機能やグループ作成者の職務を伝える。
* アドレス帳でグループを分類しやすいようにする。
* グループ名やグループ エイリアスで特定の単語の使用を禁止する。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="configure-the-group-naming-policy-in-the-azure-portal"></a>Azure portal でグループの名前付けポリシーを構成する

1. ユーザー管理者アカウントを使用して [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
1. **[グループ]** を選択し、 **[名前付けポリシー]** を選択して [名前付けポリシー] ページを開きます。

    ![管理センターで [名前付けポリシー] ページを開く](./media/groups-quickstart-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>プレフィックス/サフィックス名前付けポリシーを表示または編集する

1. **[名前付けポリシー]** ページで、 **[グループの名前付けポリシー]** を選択します。
1. 名前付けポリシーの一部として強制する属性または文字列を選択することで、現在のプレフィックスまたはサフィックス名前付けポリシーを個別に表示または編集できます。
1. プレフィックスまたはサフィックスを一覧から削除するには、プレフィックスまたはサフィックスを選択して **[削除]** を選択します。 複数の項目を同時に削除できます。
1. **[保存]** を選択して、ポリシーへの変更を有効にします。

### <a name="view-or-edit-the-custom-blocked-words"></a>カスタム禁止単語を表示または編集する

1. **[名前付けポリシー]** ページで、 **[ブロックされている単語]** を選択します。

    ![名前付けポリシーのブロックされている単語の一覧の編集とアップロード](./media/groups-quickstart-naming-policy/blockedwords.png)

1. **[ダウンロード]** を選択して、現在のカスタムのブロックされている単語の一覧を表示または編集します。
1. ファイル アイコンを選択して、カスタムのブロックされている単語の新しい一覧をアップロードします。
1. **[保存]** を選択して、ポリシーへの変更を有効にします。

これで終了です。 名前付けポリシーが設定され、カスタム禁止単語が追加されました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

### <a name="remove-the-naming-policy-using-azure-portal"></a>Azure portal を使用して名前付けポリシーを削除する

1. **[名前付けポリシー]** ページで、**[ポリシーの削除]** を選択します。
1. 削除を確定すると、すべてのプレフィックス/サフィックス名前付けポリシーとカスタムのブロックされている単語を含め、名前付けポリシーが削除されます。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure portal から Azure AD 組織の名前付けポリシーを設定する方法について説明しました。

次の記事に進み、名前付けポリシー用の PowerShell コマンドレット、技術的な制約、カスタム禁止単語の一覧の追加、Microsoft 365 の各アプリ間のエンド ユーザー エクスペリエンスなどの詳細を確認してください。
> [!div class="nextstepaction"]
> [名前付けポリシーの PowerShell](groups-naming-policy.md)