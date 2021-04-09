---
title: 他の人と共同作業を行う - QnA Maker
description: Azure ロールベースのアクセス制御を使用して、他の作成者や編集者と共同作業を行う方法について説明します。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: 512ae470d3c9068b9d284095a18013fc84dbf36e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103232547"
---
# <a name="collaborate-with-other-authors-and-editors"></a>他の作成者や編集者との共同作業

QnA Maker リソースに設定された Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、他の作成者や編集者と共同作業を行います。

## <a name="access-is-provided-on-the-qna-maker-resource"></a>アクセスは QnA Maker リソースで提供される

すべてのアクセス許可は、QnA Maker リソースに設定されたアクセス許可によって制御されます。 これらのアクセス許可は、読み取り、書き込み、公開、フル アクセスに合わせて調整されます。 QnA Maker リソースの [RBAC アクセスを更新](../how-to/manage-qna-maker-app.md)することで、複数のユーザー間のコラボレーションを許可できます。

この Azure RBAC の特徴は次のとおりです。
* Azure Active Directory (AAD) は、所有者および共同作成者のキーベースの認証と完全に下位互換性があります。 お客様は、要求でキーベースの認証または Azure RBAC ベースの認証を使用できます。
* 制御はナレッジ ベース レベルではなくリソース レベルであるため、リソース内のすべてのナレッジ ベースに作成者と編集者をすばやく追加できます。

> [!NOTE]
> 必ずリソース用のカスタム サブドメインを追加してください。 [カスタム サブドメイン](../../cognitive-services-custom-subdomains.md)は既定で存在しているはずですが、存在しない場合は追加してください。

## <a name="access-is-provided-by-a-defined-role"></a>アクセスは定義済みのロールによって提供される

[!INCLUDE [Azure RBAC permissions table](../includes/role-based-access-control.md)]

## <a name="authentication-flow"></a>Authentication flow

次の図は、QnA Maker ポータルにサインインし、オーサリング API を使用するためのフローを、作成者の観点から示しています。

> [!div class="mx-imgBorder"]
> ![次の図は、QnA Maker ポータルにサインインし、オーサリング API を使用するためのフローを、作成者の観点から示しています。](../media/qnamaker-how-to-collaborate-knowledge-base/rbac-flow-from-portal-to-service.png)

|手順|説明|
|--|--|
|1|ポータルで QnA Maker リソースのトークンが取得されます。|
|2|ポータルで、キーの代わりにトークンを渡して適切な QnA Maker オーサリング API (APIM) が呼び出されます。|
|3|QnA Maker API によってトークンが検証されます。|
|4 |QnA Maker API によって QnAMaker サービスが呼び出されます。|

[オーサリング API](../index.yml) を呼び出す場合は、認証を設定する方法の詳細を確認してください。

## <a name="authenticate-by-qna-maker-portal"></a>QnA Maker ポータルによる認証

QnA Maker ポータルを使用して作成および共同作業を行う場合、コラボレーター用のリソースに適切なロールを追加すると、QnA Maker ポータルによってすべてのアクセス許可が管理されます。

## <a name="authenticate-by-qna-maker-apis-and-sdks"></a>QnA Maker API および SDK による認証

REST または SDK を介して API を使用して作成および共同作業を行う場合は、認証を管理するために[サービス プリンシパルを作成](../../authentication.md#assign-a-role-to-a-service-principal)する必要があります。

## <a name="next-step"></a>次のステップ

* 言語およびクライアント アプリケーション用のナレッジ ベースを設計する
