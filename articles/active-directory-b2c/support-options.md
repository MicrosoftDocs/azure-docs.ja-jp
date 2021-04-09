---
title: Azure Active Directory B2C のサポート | Microsoft Docs
description: Azure Active Directory B2C のサポート要求を提出する方法。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5195241003b1ce4ea505002e2cc3c10410e6cde1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "78183722"
---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Azure Active Directory B2C: ファイル サポート要求
Azure portal で Azure Active Directory B2C (Azure AD B2C) のサポート要求を提出するには、次の手順に従います。

1. B2C テナントを、Azure サブスクリプションが関連付けられている別のテナントに切り替えます。 通常、移動先のテナントは、従業員のテナント、または Azure サブスクリプションにサインアップしたときに作成された既定のテナントです。 詳細については、「[Azure サブスクリプションが Azure AD に関連付けられる方法](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)」を参照してください。

    ![テナント選択が強調表示された Azure portal](./media/support-options/support-switch-dir.png)

1. テナントを切り替えた後、 **[ヘルプとサポート]** をクリックします。

    ![Azure portal 内で強調表示された [ヘルプとサポート] タイル](./media/support-options/support-support.png)

1. **[新しいサポート要求]** をクリックします。

    ![Azure portal 内で強調表示された [新しいサポート要求] タイル](./media/support-options/support-new.png)

1. **[基本]** ブレードで、以下の説明に従ってから、**[次へ]** をクリックします。

    * **[問題の種類]** は **[技術]** です。
    * 適切な **サブスクリプション** を選択します。
    * **[サービス]** は **[Active Directory]** です。
    * 適切な **サポート プラン** を選択します。 サポート プランがない場合は、 [ここ](https://azure.microsoft.com/support/plans/)からサインアップできます。

     ![Azure portal 内で強調表示された [基本] ページの [次へ] ボタン](./media/support-options/support-basics.png)

1. **[問題]** ブレードで、以下の説明に従ってから、**[次へ]** をクリックします。

    * 適切な **重要度** レベルを選択します。
    * **[問題の種類]** は **[B2C]** です。
    * 適切な **カテゴリ** を選択します。
    * **[詳細]** フィールドに問題の説明を入力します。 B2C テナントの名前、問題の説明、エラー メッセージ、関連付け ID (ある場合) などの詳細情報を入力します。
    * **[期間]** フィールドに問題が発生した日時 (タイム ゾーンを含む) を入力します。
    * **[ファイルのアップロード]** で、問題の解決に役立つと思われるすべてのスクリーンショットやファイルをアップロードします。

     ![Azure portal 内で強調表示された [問題] ページの [次へ] ボタン](./media/support-options/support-problem.png)

1. **[連絡先情報]** ブレードで、自分の連絡先情報を追加します。 **Create** をクリックしてください。

    ![ポータルの強調表示された [連絡先情報] ページの [作成] ボタン](./media/support-options/support-contact.png)

1. サポート要求を送信した後は、スタート画面の **[ヘルプとサポート]** をクリックした後 **[サポート要求の管理]** をクリックして、監視できます。

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>既知の問題点: B2C テナントのコンテキストでのサポート要求の提出

上で説明した手順 2 を実行せず、自分の B2C テナントのコンテキストでサポート要求を作成しようとすると、次のエラーが表示されます。

> [!IMPORTANT]
> B2C テナントで新しい Azure サブスクリプションにサインアップしないでください。

![Azure portal に表示された "サブスクリプションがありません" エラー](./media/support-options/support-no-sub.png)
