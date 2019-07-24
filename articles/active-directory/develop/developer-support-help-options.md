---
title: Azure AD アプリ開発者向けのサポート オプションとヘルプ オプション | Microsoft Docs
description: Microsoft ID (Azure Active Directory および Microsoft アカウント) と連携するアプリケーションを作成するときの開発関連の質問や問題について、ヘルプとサポートを入手する方法について確認します
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2019
ms.author: celested
ms.reviewer: jmprieur, dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0de9b3e3535253724d068f806b219736f6889477
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59501597"
---
# <a name="support-and-help-options-for-developers"></a>開発者向けのサポート オプションとヘルプ オプション

Azure Active Directory (Azure AD)、Microsoft ID、または Microsoft Graph API との連携を開始する場合や、アプリケーションに新しい機能を実装するときに、コミュニティからヘルプを入手し、開発者として使用できるサポートのオプションを把握しておく必要があります。 この記事は、これらのオプションを理解するうえで役立ちます。次のようなオプションがあります。

> [!div class="checklist"]
> * 問題に対する回答がコミュニティから得られないか、または実装を試みている機能に関して既存のドキュメントが既に存在するかを検索する方法
> * 一部のケースにおいて、マイクロソフトのサポート ツールを使用して固有の問題をデバッグするだけでかまわない
> * 必要な回答が見つけられない場合に、*Stack Overflow* 上で質問する
> * 認証ライブラリで問題が見つかった場合は *GitHub* の問題として提起する
> * 最後に、サポート要求をオープンしてサポート担当者と話をする

## <a name="search"></a>Search

開発関連の質問がある場合、ドキュメント内、[GitHub のサンプル](https://github.com/azure-samples)、または [Stack Overflow](https://www.stackoverflow.com) の質問への回答の中に、回答を見つけられることがあります。

### <a name="scoped-search"></a>範囲指定の検索

より迅速に結果を得るには、お好みの検索エンジンで次のクエリを使って、検索範囲を Stack Overflow、ドキュメント、およびコード サンプルに設定できます。

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

ここで、*{Your Search Terms}* は検索するキーワードに該当します。

## <a name="use-the-development-support-tools"></a>開発サポート ツールを使用する

| ツール  | 説明  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | ID またはアクセス トークンを貼り付けて、要求の名前と値をデコードする。 |
| [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Microsoft Graph API に対して要求を行って応答を確認できるツール。 |

## <a name="post-a-question-to-stack-overflow"></a>Stack Overflow に質問を投稿する

Stack Overflow は、開発関連の質問を投稿するのに適したチャネルです。 ここでは、開発者コミュニティのメンバーや Microsoft チームのメンバーから、問題の解決方法について直接意見を聞くことができます。

検索によって質問に対する回答が見つけられない場合は、Stack Overflow に新しい質問を提出します。 質問するときは、コミュニティ ID を補助してより早く質問に回答してもらえるように、次のうちいずれか 1 つのタグを使用します。

|コンポーネント/領域  | Tags |
|---------|---------|
| ADAL ライブラリ | [[adal]](https://stackoverflow.com/questions/tagged/adal) |
| MSAL ライブラリ     | [[msal]](https://stackoverflow.com/questions/tagged/msal) |
| OWIN ミドルウェア  | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Microsoft Graph API](https://developer.microsoft.com/graph/) | [[microsoft-graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| その他認証や承認のトピックに関連する区分 | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

Stack Overflow の次の投稿には、質問方法やソース コードの追加方法に関するヒントが示されています。 これらのガイドラインに従うと、質問に対するコミュニティ メンバーからの評価や回答がより早く返ってくる可能性が高まります。

* [良い質問をする方法](https://stackoverflow.com/help/how-to-ask)
* [最小限の例、完全な例、実証可能な例を作成する方法](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>GitHub の issue を作成する

ライブラリに関するバグや問題が見つかった場合は、GitHub リポジトリに問題を報告してください。 ライブラリはオープンソースであるため、プル要求を送信することもできます。

ライブラリの一覧と GitHub リポジトリを確認するには、次の記事を参照してください。

* [ADAL](active-directory-authentication-libraries.md) ライブラリおよび GitHub リポジトリ
* [MSAL](reference-v2-libraries.md) ライブラリおよび GitHub リポジトリ

## <a name="open-a-support-request"></a>サポート要求をオープンする

サポート要求をオープンして、サポート担当者と話をすることができます。 Azure をご利用のお客様には複数のサポート オプションが用意されています。 プランの比較については、[こちらのページ](https://azure.microsoft.com/support/plans/)をご覧ください。 また、Azure をご利用のお客様には Developer サポートも用意されています。 Developer サポート プランを購入する方法については、[こちらのページ](https://azure.microsoft.com/support/plans/developer/)をご覧ください。

* Azure サポート プランをお持ちの場合は、[こちらからサポート要求をオープン](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)します。

* Azure をご利用でない場合、マイクロソフトの[商用サポート](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial)からサポート要求をオープンすることもできます。

サポートを受けたり質問したりするために、[仮想エージェント](https://support.microsoft.com/contactus/?ws=support)をお試しいただくこともできます。

### <a name="free-chat-support-for-a-limited-time"></a>無料のチャット サポート (時間制限あり)

マイクロソフトのパートナーであれば、所定の時間、無料のチャット サポートを利用することもできます。 マイクロソフトのパートナーでない場合は、[こちら](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx)から無料で登録し、他のメリットも享受できます。

登録後、[こちら](https://aka.ms/devchat)からチャット要求を開始できます。
