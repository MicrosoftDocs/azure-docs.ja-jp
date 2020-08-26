---
title: ナレッジ ベースの共同利用 - QnA Maker
description: QnA Maker では、複数のユーザーがナレッジ ベースを共同利用できます。 この機能は、Azure ロール ベースのアクセス制御 (Azure RBAC) で提供されています。
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 0d59ca83e6067c083e4bfebdb7afaaf6669dda8a
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926849"
---
# <a name="collaboration-with-authors-and-editors"></a>作成者や編集者との共同作業

コラボレーションは、QnA Maker リソース レベルで提供され、コラボレーターのロールに基づいて、コラボレーターのアクセスの制限を可能にします。 QnA Maker コラボレーター認証の[概念](../Concepts/role-based-access-control.md)に関する詳細を参照してください。

## <a name="add-azure-role-based-access-control-azure-rbac-to-your-qna-maker-resource"></a>QnA Maker リソースへ Azure ロールベースのアクセス制御 (Azure RBAC) を追加する

QnA Maker を使用すると、複数の担当者が同じ QnA Maker リソース内のすべてのナレッジベースで共同作業を行うことができます。 この機能は、[Azure ロール ベースのアクセス制御 (Azure RBAC)](../../../active-directory/role-based-access-control-configure.md) で提供されています。

## <a name="access-at-the-qna-maker-resource-level"></a>QnA Maker リソース レベルでのアクセス

1 つの QnA Maker サービスにある特定のナレッジ ベースの共有はできません。 より詳細なアクセス制御が必要な場合は、さまざまな QnA Maker リソースにわたってナレッジ ベースを配布することを検討した上で、各リソースにロールを追加してください。

## <a name="add-role-to-resource"></a>リソースへのロールの追加

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>QnA Maker リソースにユーザー アカウントを追加する

次の手順では、コラボレーター ロールを使用しますが、これらの手順を使用して、いずれかの[ロール](../reference-role-based-access-control.md)を追加することができます。

1. [Azure](https://portal.azure.com/) portal にサインインして、使用する QnA Maker リソースに移動します。

    ![QnA Maker のリソース リスト](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. **[アクセス制御 (IAM)]** タブを開きます。

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. **[追加]** を選択します。

    ![QnA Maker IAM の [追加]](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. 次の一覧からロールを選択します。

    |Role|
    |--|
    |所有者|
    |Contributor|
    |Cognitive Services QnA Maker 閲覧者|
    |Cognitive Services QnA Maker エディター|
    |Cognitive Services ユーザー|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker IAM のロールの追加。":::

1. ユーザーのメール アドレスを入力し、 **[保存]** を押します。

    ![QnA Maker IAM の電子メールの追加](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>QnA Maker ナレッジ ベースを表示する

QnA Maker サービスを共有する相手が [QnA Maker ポータル](https://qnamaker.ai)にログインすると、自身のロールに基づいて、そのサービスのすべてのナレッジ ベースを表示できます。

ナレッジ ベースを選択すると、その QnA Maker リソースでの現在のロールがナレッジベース名の横に表示されます。

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="編集モードのナレッジ ベースのスクリーンショット。Web ページの左上隅にあるナレッジ ベース名の横に、かっこで囲まれたロール名が表示されています。":::

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ナレッジ ベースのテスト](./test-knowledge-base.md)

コラボレーションに関する詳細については、こちらを参照してください。
* [Azure](../../../active-directory/role-based-access-control-configure.md) ロールベースのアクセス制御
* QnA Maker ロールベースのアクセス制御の[概念](../Concepts/role-based-access-control.md)
