---
title: 新しい開発者ポータルへのアクセスとそのカスタマイズ - Azure API Management | Microsoft Docs
description: API Management で新しい開発者ポータルを使用する方法について説明します。
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/12/2019
ms.author: apimpm
ms.openlocfilehash: da75ca43a2576e3214d4b67f9eb61c7bad3bd5cc
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073510"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>Azure API Management での新しい開発者ポータルへのアクセスとそのカスタマイズ

この記事では、新しい Azure API Management 開発者ポータルにアクセスする方法を示します。 コンテンツの追加と編集、Web サイトの外観のカスタマイズなど、ビジュアル エディターのエクスペリエンスについても説明します。

![新しい API Management 開発者ポータル](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a> 前提条件

- 次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)。
- Azure API Management インスタンスをインポートして発行します。 詳細については、[インポートと発行](import-and-publish.md)に関するページを参照してください。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> 新しい開発者ポータルは現在プレビュー段階です。

## <a name="managed-vs-self-hosted"></a>マネージド バージョンとセルフホステッド バージョン

開発者ポータルは 2 つの方法で構築できます。

- **マネージド バージョン** - API Management インスタンスに組み込まれたポータルを編集およびカスタマイズします。URL `<your-api-management-instance-name>.developer.azure-api.net` からアクセスできます。
- **セルフホステッド バージョン** - API Management インスタンスの外部にポータルをデプロイし、自らホスティングします。 このアプローチを使用すると、ポータルのコードベースを編集したり、提供されているコア機能を拡張したりできます。 詳細および手順については、[ポータルのソース コードを含む GitHub リポジトリ][1]を参照してください。

## <a name="managed-access"></a>マネージド バージョンのポータルにアクセスする

マネージド バージョンのポータルにアクセスするには、次の手順に従います。

1. Azure portal で API Management サービス インスタンスに移動します。
1. 上部ナビゲーション バーにある **[New developer portal (preview)] \(新しい開発者ポータル (プレビュー)\)** ボタンをクリックします。 ブラウザーの新しいタブで、管理バージョンのポータルが開きます。 ポータルに初めてアクセスする場合、既定のコンテンツが自動的にプロビジョニングされます。

## <a name="managed-tutorial"></a>マネージド バージョンのポータルを編集およびカスタマイズする

次のビデオでは、ポータルのコンテンツを編集する方法、Web サイトの外観をカスタマイズする方法、変更を公開する方法を示します。

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="faq"></a>よく寄せられる質問

このセクションでは、新しい開発者ポータルに関してよく寄せられる一般的な質問に回答します。 セルフホステッド バージョンに関する質問については、[GitHub リポジトリの wiki セクション](https://github.com/Azure/api-management-developer-portal/wiki)を参照してください。

### <a name="how-can-i-migrate-content-from-the-old-developer-portal-to-the-new-one"></a>以前の開発者ポータルから新しい開発者ポータルにコンテンツを移行するにはどうすればよいですか。

実行できません。 ポータルに互換性がありません。

### <a name="when-will-the-portal-become-generally-available"></a>ポータルはいつ一般公開されますか?

ポータルは現在プレビュー段階であり、2019 年内に一般公開される予定です。 プレビュー バージョンは運用目的で使用しないでください。

### <a name="will-the-old-portal-be-deprecated"></a>古いポータルは非推奨となりますか?

はい。新しいものが一般公開されると、非推奨となります。 懸念事項がある場合は、[専用の GitHub の問題](https://github.com/Azure/api-management-developer-portal/issues/121)に提起してください。

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>新しいポータルには古いポータルのすべての機能がありますか。

一般公開の目標は、以前のポータルとのシナリオベースの機能パリティを提供することです。 それまでは、プレビュー バージョンでは一部の機能が実装されていない可能性があります。

例外は、古いポータルの "*アプリケーション*" と "*問題*" であり、新しいポータルでは使用できません。 以前のポータルで "*問題*" を使用していて、新しいポータルでも必要な場合は、[専用の GitHub の問題](https://github.com/Azure/api-management-developer-portal/issues/122)にコメントを投稿してください。

### <a name="ive-found-bugs-andor-id-like-to-request-a-feature"></a>バグを見つけました。または機能のリクエストがあります。

これでセットアップは終了です。 [GitHub リポジトリの [Issues]\(問題\) セクション](https://github.com/Azure/api-management-developer-portal/issues)で、フィードバックの提供、機能のリクエストの送信、バグ報告の提出を行うことができます。 ここでは、`community` ラベルでマークされている問題についてフィードバックもぜひお寄せください。

### <a name="i-want-to-move-the-content-of-the-new-portal-between-environments-how-can-i-do-that-and-do-i-need-to-go-with-the-self-hosted-version"></a>新しいポータルのコンテンツを環境間で移動する必要があります。 どのようにすればよいですか。またセルフホステッド バージョンを使用する必要がありますか。

この動作はポータルのバージョン管理型とセルフホステッド型の両方で実行できます。 新しい開発者ポータルでは、API Management サービスの管理 API を使用したコンテンツの抽出がサポートされています。 API については、[GitHub リポジトリの wiki セクション](https://github.com/Azure/api-management-developer-portal/wiki/)に記載されています。 また、[スクリプト](https://github.com/Azure/api-management-developer-portal/blob/master/scripts/migrate.bat)も作成しました。これは作業の開始に役立つ場合があります。

このプロセスと API Management DevOps リソース キットとの整合に引き続き取り組んでいます。

### <a name="how-can-i-select-a-layout-when-creating-a-new-page"></a>新しい "*ページ*" を作成するときに "*レイアウト*" を選択する方法はありますか。

"*レイアウト*" は、URL テンプレートを "*ページ*" の URL に一致させることによってページに適用されます。 たとえば、URL テンプレートが `/wiki/*` の "*レイアウト*" は、`/wiki/getting-started` や `/wiki/styles` などの `/wiki/` セグメントを持つすべての "*ページ*" に適用されます。

### <a name="why-doesnt-the-interactive-developer-console-work"></a>対話型の開発者コンソールが動作しないのはなぜですか。

CORS に関連している可能性があります。 対話型コンソールは、ブラウザーからクライアント側の API 要求を行います。 API に [CORS ポリシー](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS)を追加して、CORS の問題を解決することができます。 すべてのパラメーターを手動で指定したり (たとえば、配信元 https://contoso.com) 、またはワイルドカード値 `*` を使用したりできます。

## <a name="next-steps"></a>次の手順

新しい開発者ポータルの詳細を確認します:

- [ソース コードがある GitHub リポジトリ][1]
- [ポータルの自己ホストの手順][2]
- [プロジェクトのパブリック ロードマップ][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects