---
title: Azure AD 条件付きアクセスのカスタム コントロール
description: Azure Active Directory の条件付きアクセスのカスタム コントロールのしくみについて説明します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7585c91e42b2d3591532756c1ead9ea60b7035e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94837585"
---
# <a name="custom-controls-preview"></a>カスタム コントロール (プレビュー)

カスタム コントロールは、Azure Active Directory のプレビュー機能です。 カスタム コントロールを使用すると、Azure Active Directory の外部で認証要件を満たすために、ユーザーが互換性のあるサービスにリダイレクトされます。 このコントロールを満たすために、ユーザーのブラウザーは外部サービスにリダイレクトされ、すべての必要な認証を実行してから、元の Azure Active Directory にリダイレクトされます。 Azure Active Directory は応答を検証し、ユーザーが正常に認証または検証された場合、そのユーザーは条件付きアクセス フロー内にとどまります。

> [!NOTE]
> カスタム コントロール機能に関して計画されている変更の詳細については、2020 年 2 月の[新着情報のアーカイブ](../fundamentals/whats-new-archive.md#upcoming-changes-to-custom-controls)に関する記事をご覧ください。

## <a name="creating-custom-controls"></a>カスタム コントロールの作成

カスタム コントロールを使用できるのは、一部の承認された認証プロバイダーだけです。 カスタム コントロールを作成するには、まず利用するプロバイダーに連絡する必要があります。 Microsoft 以外の各プロバイダーには、サインアップ、サブスクライブ、またはサービスの一部になることや、条件付きアクセスとの統合の意思表示について、独自のプロセスと要件があります。 その時点で、プロバイダーからは JSON 形式のデータ ブロックが提供されます。 このデータにより、プロバイダーと条件付きアクセスがユーザーのテナント用に連携して動作できるようになり、新しいコントロールが作成され、ユーザーがプロバイダーの検証を正常に実行した場合は条件付きアクセスからどのように伝えられるかが定義されます。

その JSON データをコピーし、それを関連するテキスト ボックスに貼り付けます。 自分が行う変更を明示的に理解していない限り、JSON を変更しないでください。 少しでも変更すると、プロバイダーと Microsoft の間の接続が中断され、アカウントからロック アウトされる可能性があります。

カスタム コントロールを作成するオプションは、 **[条件付きアクセス]** ページの **[管理]** セクションにあります。

![条件付きアクセスのカスタム コントロール インターフェイス](./media/controls/custom-controls-conditional-access.png)

**[New custom control] \(新しいカスタム コントロール)** をクリックし、コントロールの JSON データ用のテキスト ボックスを含むブレードを開きます。  

![新しいカスタム コントロール](./media/controls/new-custom-controls-conditional-access.png)

## <a name="deleting-custom-controls"></a>カスタム コントロールの削除

カスタム コントロールを削除するには、まず、それがどの条件付きアクセス ポリシーでも使用されていないことを確認する必要があります。 完了したら、次のことを行います。

1. [Custom controls] \(カスタム コントロール) 一覧に移動します。
1. […] をクリックします。  
1. **[削除]** を選択します。

## <a name="editing-custom-controls"></a>カスタム コントロールの編集

カスタム コントロールを編集するには、現在のコントロールを削除し、更新された情報で新しいコントロールを作成する必要があります。

## <a name="known-limitations"></a>既知の制限事項

カスタム コントロールは、Privileged Identity Manager (PIM) でロールを昇格させる目的で Azure AD Multi-Factor Authentication、Azure AD のセルフサービス パスワード リセット (SSPR)、多要素認証要求の要件を満たすことを必要とする ID 保護の自動化では使用できず、Intune デバイス登録の一環として使用できず、また、Azure AD にデバイスを登録するときに使用できません。

## <a name="next-steps"></a>次のステップ

- [Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)

- [レポート専用モード](concept-conditional-access-report-only.md)

- [Simulate sign in behavior using the Conditional Access What If tool](troubleshoot-conditional-access-what-if.md) (条件付きアクセスの What If ツールを使用したサインイン動作のシミュレート)
