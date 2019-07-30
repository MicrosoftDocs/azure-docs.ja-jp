---
title: リダイレクト URI および応答 URL に関する制約と制限 - Microsoft ID プラットフォーム
description: リダイレクト URI および応答 URL に関する制約と制限
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: article
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5e557d74ff0cb959b11e99391c47e91a90d17ef
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325299"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>リダイレクト URI および応答 URL に関する制約と制限

リダイレクト URI、または応答 URL は、アプリが正常に承認され、認証コードまたはアクセス トークンが付与されたとき、認証サーバーがユーザーを送信する場所です。 このコードまたはトークンはリダイレクト URI または応答トークンに含まれます。そのため、アプリ登録プロセスの一環として正しい場所を登録することが重要です。

## <a name="maximum-number-of-redirect-uris"></a>リダイレクト URI の最大数

次の表では、アプリの登録時に追加できるリダイレクト URI の最大数を示しています。 

| サインイン中のアカウント | リダイレクト URI の最大数 | 説明 |
|--------------------------|---------------------------------|-------------|
| 組織の Azure Active Directory (Azure AD) テナントにある Microsoft の職場または学校アカウント | 256 | アプリケーション マニフェストの `signInAudience` フィールドは *AzureADMyOrg* か *AzureADMultipleOrgs* に設定されています |
| Microsoft の個人用アカウント、職場用アカウント、学校用アカウント | 100 | アプリケーション マニフェストの `signInAudience` フィールドは *AzureADandPersonalMicrosoftAccount* に設定されています |

## <a name="maximum-uri-length"></a>URI の最大長

アプリ登録に追加するリダイレクト URI ごとに最大 256 文字を使用できます。

## <a name="restrictions-using-a-wildcard-in-uris"></a>URI にワイルドカードを使用する制限

`https://*.contoso.com` のようなワイルドカード URI は便利ですが、避けてください。 リダイレクト URI にワイルドカードを使用すると、セキュリティ面で影響が出ます。 OAuth 2.0 仕様 ([セクション 3.1.2 of RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)) によると、リダイレクト エンドポイント URI は絶対 URI にする必要があります。 

Microsoft の個人用アカウント、職場用アカウント、学校用アカウントにサインインするように構成されているアプリの場合、Azure AD アプリケーション モデルではワイルドカード URI がサポートされません。 ただし、組織の Azure AD テナントで本日、職場または学校アカウントにサインインするように構成されているアプリの場合、ワイルドカード URI が許可されます。 
 
> [!NOTE]
> 新しい[アプリの登録](https://go.microsoft.com/fwlink/?linkid=2083908)エクスペリエンスでは、開発者は UI にワイルドカード URI を追加できません。 職場または学校アカウントにサインインするアプリに対してワイルドカード URI を追加することは、アプリ マニフェスト エディターでのみサポートされます。 今後、新しいアプリではリダイレクト URI でワイルドカードを使用できません。 ただし、リダイレクト URI にワイルドカードを含む以前のアプリは引き続き動作します。

シナリオ上、許可される最大制限を超えるリダイレクト URI が必要な場合、ワイルドカード リダイレクト URI を追加する代わりに、次のいずれかの手法を検討してください。

### <a name="use-a-state-parameter"></a>状態パラメーターを使用する

たくさんのサブドメインがあるとき、認証に成功したユーザーを最初のページにリダイレクトする必要がある場合、状態パラメーターの使用が便利なことがあります。 

このアプローチでは:

1. 認証エンドポイントから受け取ったセキュリティ トークンを処理する "共有" リダイレクト URI をアプリケーション別に作成します。
1. アプリケーションでは、状態パラメーターでアプリケーション固有のパラメーター (ユーザーがセッションを始めたサブドメイン URL やブランド情報のようなもの) を送信できます。 状態パラメーターの使用時、CSRF 対策をしてください。仕様は[セクション 10.12 of RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12) にあります。 
1. アプリケーション固有のパラメーターには、ユーザーにとって適切なエクスペリエンスをアプリケーションから与えるために、つまり、アプリケーションの適切な状態を構築するために必要な情報がすべて含まれます。 Azure AD 認証エンドポイントにより状態パラメーターから HTML が取り除かれます。そのため、このパラメーターでは HTML コンテンツを渡さないようにしてください。
1. Azure AD から "共有" リダイレクト URI に応答を送信するとき、状態パラメーターがアプリケーションに返されます。
1. その後、ユーザーをさらに送信する宛先となる URL を決定する目的で、状態パラメーターの値をアプリケーションで利用できます。 CSRF 対策の有効性を確認してください。

> [!NOTE]
> この手法では、セキュリティを侵害されたクライアントが状態パラメーターで送信された追加パラメーターを変更し、ユーザーを別の URL にリダイレクトすることを許します。これは RFC 6819 に説明がある[オープン リダイレクターの脅威](https://tools.ietf.org/html/rfc6819#section-4.2.4)です。 そのため、クライアントは状態を暗号化し、他の手段で検証することでこれらのパラメーターを保護する必要があります。他の手段とは、たとえば、リダイレクト URI に含まれているドメイン名の妥当性をトークンに照らして検証します。

### <a name="add-redirect-uris-to-service-principals"></a>リダイレクト URI をサービス プリンシパルに追加する

もう 1 つ手法は、Azure AD テナントでアプリ登録を表す[サービス プリンシパル](app-objects-and-service-principals.md#application-and-service-principal-relationship)にリダイレクト URI を追加することです。 状態パラメーターを使用できないときや、シナリオ上、サポートする新しいテナントごとにアプリ登録に新しいリダイレクト URI を追加する必要があるとき、この手法を利用できます。 

## <a name="next-steps"></a>次の手順

- [アプリケーション マニフェスト](reference-app-manifest.md)について学習する
