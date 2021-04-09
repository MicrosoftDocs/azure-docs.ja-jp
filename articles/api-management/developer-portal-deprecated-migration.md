---
title: レガシ開発者ポータルから新しい開発者ポータルに移行する
titleSuffix: Azure API Management
description: API Management で、レガシ開発者ポータルから新しい開発者ポータルに移行する方法について説明します。
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: f5105c685de4b3ccdffe69eec8ee8eeb32976c1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92325931"
---
# <a name="migrate-to-the-new-developer-portal"></a>新しい開発者ポータルへの移行

この記事では、API Management で非推奨のレガシ ポータルから新しい開発者ポータルに移行するために必要な手順について説明します。

> [!IMPORTANT]
> レガシ開発者ポータルは非推奨となり、セキュリティ更新プログラムのみを受け取るようになりました。 2023 年 10 月に廃止され、すべての API Management サービスから削除されるまで、通常どおりに引き続き使用できます。

![API Management 開発者ポータル](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="improvements-in-new-developer-portal"></a>新しい開発者ポータルの機能強化

新しい開発者ポータルは、非推奨のポータルの多くの制限に対処しています。 [コンテンツを編集するためのビジュアル ドラッグ アンド ドロップ エディター](api-management-howto-developer-portal-customize.md)と、デザイナーが Web サイトのスタイルを設定するための専用パネルを備えています。 ページ、カスタマイズ、および構成は、API Management サービスに Azure Resource Manager リソースとして保存されます。これにより、[ポータルのデプロイを自動化](api-management-howto-developer-portal.md#automate)することができます。 最後に、ポータルのコードベースはオープンソースであるため、[カスタム機能を使用して拡張する](api-management-howto-developer-portal.md#managed-vs-self-hosted)ことができます。

## <a name="how-to-migrate-to-new-developer-portal"></a>新しい開発者ポータルへの移行方法

新しい開発者ポータルは、非推奨のポータルとは互換性がなく、自動移行はできません。 コンテンツ (ページ、テキスト、メディア ファイル) を手動で再作成し、新しいポータルの外観をカスタマイズする必要があります。 正確な手順は、ご使用のポータルのカスタマイズと複雑さによって異なります。 ガイダンスについては、[開発者ポータルのチュートリアル](api-management-howto-developer-portal-customize.md)を参照してください。 API、製品、ユーザー、ID プロバイダーのリストなどの残りの構成は、両方のポータル間で自動的に共有されます。

> [!IMPORTANT]
> 以前に新しい開発者ポータルを起動したことがあるが、変更を加えていない場合は、[既定のコンテンツをリセット](api-management-howto-developer-portal.md#preview-to-ga)して、最新バージョンに更新してください。

非推奨のポータルから移行する場合は、次の変更に注意してください。

- カスタム ドメインを使用して開発者ポータルを公開する場合は、新しい開発者ポータルに[ドメインを割り当てます](configure-custom-domain.md)。 Azure portal のドロップダウンから **[開発者ポータル]** オプションを使用します。
- API に [CORS ポリシーを適用](api-management-howto-developer-portal.md#cors)して、対話型のテスト コンソールを有効にします。
- ポータルのスタイルを設定するためにカスタム CSS を挿入する場合は、[組み込みのデザイン パネルを使用してスタイル設定をレプリケートする](api-management-howto-developer-portal-customize.md)必要があります。 CSS インジェクションは新しいポータルでは許可されていません。
- カスタム JavaScript を挿入できるのは、[新しいポータルのセルフホステッド バージョン](api-management-howto-developer-portal.md#managed-vs-self-hosted)だけです。
- API Management が仮想ネットワーク内にあり、Application Gateway を介してインターネットに公開されている場合、詳細な構成手順については、[このドキュメントの記事](api-management-howto-integrate-internal-vnet-appgateway.md)を参照してください。 以下を実行する必要があります。

    - API Management の管理エンドポイントへの接続を有効にします。
    - 新しいポータル エンドポイントへの接続を有効にします。
    - 選択した Web Application Firewall 規則を無効にします。

- 明示的に定義された非推奨のポータル URL を含めるように既定の電子メール通知テンプレートを変更した場合は、ポータルの URL パラメーターを使用するか、新しいポータルの URL を指すようにそれらを変更します。 代わりに組み込みポータルの URL パラメーターがテンプレートで使用されている場合、変更は必要ありません。
- "*イシュー*" と "*アプリケーション*" は、新しい開発者ポータルではサポートされていません。
- ID プロバイダーとしての Facebook、Microsoft、Twitter、および Google との直接統合は、新しい開発者ポータルではサポートされていません。 Azure AD B2C を経由してこれらのプロバイダーと統合できます。
- 委任を使用する場合は、アプリケーションの戻り先 URL を変更し、"*SSO URL の生成*" エンドポイントの代わりに、"[*共有アクセス トークンの取得*" API エンドポイント](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken)を使用します。
- Azure AD を ID プロバイダーとして使用する場合は、次のことを行います。

    - アプリケーションの戻り先 URL を、新しい開発者ポータル ドメインを指すように変更します。
    - アプリケーションの戻り先 URL のサフィックスを `/signin-aad` から `/signin` に変更します。

- Azure AD B2C を ID プロバイダーとして使用する場合は、次のことを行います。

    - アプリケーションの戻り先 URL を、新しい開発者ポータル ドメインを指すように変更します。
    - アプリケーションの戻り先 URL のサフィックスを `/signin-aad` から `/signin` に変更します。
    - アプリケーション要求に、"*名*"、"*姓*"、"*ユーザーのオブジェクト ID*" を含めます。

- 対話型テスト コンソールで OAuth 2.0 を使用する場合は、アプリケーションの戻り先 URL を変更して、新しい開発者ポータル ドメインを指定し、次のようにサフィックスを変更します。

    - 承認コードの付与フローの場合は、`/docs/services/[serverName]/console/oauth2/authorizationcode/callback` から `/signin-oauth/code/callback/[serverName]`。
    - 暗黙的な許可フローの場合は、`/docs/services/[serverName]/console/oauth2/implicit/callback` から `/signin-oauth/implicit/callback`。
- 対話型テスト コンソールで OpenID Connect を使用する場合は、アプリケーションの戻り先 URL を変更して、新しい開発者ポータル ドメインをポイントし、次のようにサフィックスを変更します。

    - 承認コードの付与フローの場合は、`/docs/services/[serverName]/console/openidconnect/authorizationcode/callback` から `/signin-oauth/code/callback/[serverName]`。
    - 暗黙的な許可フローの場合は、`/docs/services/[serverName]/console/openidconnect/implicit/callback` から `/signin-oauth/implicit/callback`。

## <a name="next-steps"></a>次の手順

開発者ポータルの詳細については、次を参照してください。

- [Azure API Management 開発者ポータルの概要](api-management-howto-developer-portal.md)
- [開発者ポータルへのアクセスとそのカスタマイズ](api-management-howto-developer-portal-customize.md)