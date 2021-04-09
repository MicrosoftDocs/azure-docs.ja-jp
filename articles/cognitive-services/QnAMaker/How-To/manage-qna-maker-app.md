---
title: QnA Maker アプリの管理 - QnA Maker
description: QnA Maker では、複数のユーザーがナレッジ ベースを共同利用できます。 QnA Maker には、アクティブ ラーニングを使用してナレッジ ベースの質を向上させる機能があります。 既存の質問の削除や変更は行わずに、レビュー、承認、却下できます。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 49bfca118e53bbe3e4287b2ce25e5baffa717175
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102217331"
---
# <a name="manage-qna-maker-app"></a>QnA Maker アプリの管理

QnA Maker では共同作成者の役割に基づいて共同作成者のアクセスを制限できるため、さまざまな作成者やコンテンツ エディターと共同作業できます。
[QnA Maker コラボレーター認証の概念](../Concepts/role-based-access-control.md)に関する詳細を参照してください。

## <a name="add-azure-role-based-access-control-azure-rbac"></a>Azure ロールベースのアクセス制御を追加する (Azure RBAC)

QnA Maker を使用すると、複数の担当者が同じ QnA Maker リソース内のすべてのナレッジベースで共同作業を行うことができます。 この機能は、[Azure ロール ベースのアクセス制御 (Azure RBAC)](../../../role-based-access-control/role-assignments-portal.md) で提供されています。

## <a name="access-at-the-qna-maker-resource-level"></a>QnA Maker リソース レベルでのアクセス

1 つの QnA Maker サービスにある特定のナレッジ ベースの共有はできません。 より詳細なアクセス制御が必要な場合は、さまざまな QnA Maker リソースにわたってナレッジ ベースを配布することを検討した上で、各リソースにロールを追加してください。

## <a name="add-a-role-to-a-resource"></a>リソースにロールを追加する

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>QnA Maker リソースにユーザー アカウントを追加する

次の手順では、コラボレーター ロールを使用しますが、これらの手順を使用して、いずれかのロールを追加することができます。

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
> [ナレッジ ベースの作成](./manage-knowledge-bases.md)
