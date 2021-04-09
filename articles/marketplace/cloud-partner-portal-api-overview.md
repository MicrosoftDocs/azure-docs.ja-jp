---
title: Cloud パートナー ポータルの API リファレンス - Microsoft コマーシャル マーケットプレース
description: マーケットプレース API 操作の説明、使用のための前提条件、一覧。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 57d3d1c6bb14db3eb2ca499069934a628d2f7fea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92425787"
---
# <a name="cloud-partner-portal-api-reference"></a>Cloud パートナー ポータルの API リファレンス

> [!NOTE]
> Cloud パートナー ポータル API はパートナー センターと統合されており、引き続き機能します。 切り替えにより、小さな変更が加えられました。 このドキュメントに記載されている [CPP API の変更点](#changes-to-cpp-apis-after-the-migration-to-partner-center)を調べて、パートナー センターへの切り替え後にコードが機能し続けることを確認してください。 CPP API は、パートナー センターへの切り替え前に既に統合されている既存の製品に対してのみ使用してください。新しい製品では、パートナー センター申請 API を使用する必要があります。

Cloud パートナー ポータルの REST API を使用すると、ワークロード、プラン、発行元プロファイルのプログラムによる取得と操作が可能になります。 API は、処理時に正しいアクセス許可を実施するために、Azure ロールベースのアクセス制御 (Azure RBAC) を使用します。

このリファレンスでは、Cloud パートナー ポータル REST API の技術的な詳細について説明します。 このドキュメントのペイロードのサンプルは参考にすぎず、新しい機能が追加されると変更される可能性があります。

## <a name="prerequisites-and-considerations"></a>前提条件と考慮事項

API を使用する前に、次の点を確認してください。

- アカウントにサービス プリンシパルを追加する方法、および認証用の Azure Active Directory (Azure AD) アクセス トークンを取得する方法については、[前提条件](./cloud-partner-portal-api-prerequisites.md)の記事を参照してください。
- これらの API を呼び出すために使用できる 2 つの[コンカレンシー制御](./cloud-partner-portal-api-concurrency-control.md)戦略。
- バージョン管理やエラー処理などの API に関する追加の[考慮事項](./cloud-partner-portal-api-considerations.md)。

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>パートナー センターへの移行後の CPP API の変更点

| **API** | **変更の説明** | **影響** |
| ------- | ---------------------- | ---------- |
| 公開、GoLive、キャンセル後 | 移行されたオファーの場合、応答ヘッダーは異なる形式になりますが、操作状態を取得する相対パスを示して、引き続き同じように動作します。 | オファーに対応する POST 要求のいずれかを送信する場合、オファーの移行状態に応じて Location ヘッダーは次の 2 つの形式のいずれかになります。<ul><li>移行されていないオファー<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>移行されたオファー<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| GET 操作 | 以前に応答の 'notification-email' フィールドをサポートしていたオファーの種類の場合、このフィールドは非推奨になり、移行されたオファーでは返されなくなります。 | 移行されたオファーの場合、要求で指定されたメール アドレスのリストに通知が送信されなくなります。 その代わりに、API サービスとパートナー センターの通知メール プロセスが連携され、メールが送信されます。 具体的には、パートナー センターの [アカウント設定] の [販売元の連絡先情報] セクションに設定されたメール アドレスに通知が送信され、操作の進行状況が通知されます。<br><br>パートナー センターの [[アカウント設定]](https://partner.microsoft.com/dashboard/account/management) の [販売元の連絡先情報] セクションに設定されているメール アドレスを確認し、通知用の正しいメール アドレスが指定されていることを確認します。  |

## <a name="common-tasks"></a>一般的なタスク

このリファレンスでは、以下の一般的なタスクを実行するための API について詳しく説明します。

### <a name="offers"></a>オファー

- [すべてのプランの取得](./cloud-partner-portal-api-retrieve-offers.md)
- [特定のプランの取得](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [プランの状態の取得](./cloud-partner-portal-api-retrieve-offer-status.md)
- [オファーの作成](./cloud-partner-portal-api-creating-offer.md)
- [プランの発行](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>操作

- [操作の取得](./cloud-partner-portal-api-retrieve-operations.md)
- [操作のキャンセル](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>アプリの発行

- [公開](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>その他のタスク

- [仮想マシン プランの価格設定](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>トラブルシューティング

- [認証エラーのトラブルシューティング](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
