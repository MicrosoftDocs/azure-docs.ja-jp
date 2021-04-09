---
title: 認証と承認の問題のトラブルシューティング - Azure Event Hubs
description: この記事では、Azure Event Hubs での認証と承認に関する問題のトラブルシューティングについて説明します。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 368fd8efda1b828f99bc41da0743768989c1a601
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92329612"
---
# <a name="troubleshoot-authentication-and-authorization-issues---azure-event-hubs"></a>認証と承認の問題のトラブルシューティング - Azure Event Hubs
[接続の問題のトラブルシューティング](troubleshooting-guide.md)に関する記事では、Azure Event Hubs での接続に関する問題に対してトラブルシューティングを行うためのヒントを提供します。 この記事では、Azure Event Hubs での認証と承認に関する問題のトラブルシューティングに役立つヒントと推奨事項を示します。 

## <a name="if-you-are-using-azure-active-directory"></a>Azure Active Directory を使用している場合
Azure Active Directory (Azure AD) を使用して Azure Event Hubs で認証および承認する場合は、イベント ハブにアクセスする ID が、適切な **リソース スコープ** (コンシューマー グループ、イベント ハブ、名前空間、リソース グループ、またはサブスクリプション) の適切な **Azure ロール** のメンバーであることを確認してください。

### <a name="azure-roles"></a>Azure ロール
- Event Hubs リソースへの完全なアクセスのための [Azure Event Hubs データ所有者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner)。
- 送信アクセス用の [Azure Event Hubs データ送信者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver)。
- 受信アクセス用の [Azure Event Hubs データ受信者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender)。

スキーマ レジストリの組み込みロールについては、[スキーマ レジストリのロール](schema-registry-overview.md#azure-role-based-access-control)に関する記事を参照してください。

### <a name="resource-scopes"></a>リソース スコープ
- **コンシューマー グループ**:このスコープでは、ロールの割り当てはこのエンティティにのみ適用されます。 現時点で、Azure portal は、このレベルのセキュリティ プリンシパルに Azure ロールを割り当てることをサポートしていません。 
- **[イベント ハブ]** : ロールの割り当ては、イベント ハブ エンティティとそれ以下のコンシューマー グループに適用されます。
- **名前空間**:ロールの割り当ては、名前空間以下とそれに関連付けられているコンシューマー グループに対する Event Hubs のトポロジ全体にわたります。
- **[リソース グループ]** :ロールの割り当ては、リソース グループのすべての Event Hubs リソースに適用されます。
- **サブスクリプション**:ロールの割り当ては、サブスクリプションのすべてのリソース グループ内のすべての Event Hubs リソースに適用されます。

詳細については、次の記事を参照してください。

- [Event Hubs リソースにアクセスするために Azure Active Directory でアプリケーションを認証する](authenticate-application.md)
- [Azure Active Directory を使用して Event Hubs リソースへのアクセスを承認する](authorize-access-azure-active-directory.md)

## <a name="if-you-are-using-shared-access-signatures-sas"></a>Shared Access Signature (SAS) を使用している場合
[SAS](authenticate-shared-access-signature.md) を使用している場合は、次の手順に従ってください。 

- 確実に、正しい SAS キーを使用します。 そうでない場合は、正しい SAS キーを使用します。
- キーに適切なアクセス許可 (送信、受信、または管理) があることを確認します。 そうでない場合は、必要なアクセス許可を持つキーを使用します。 
- キーの有効期限が切れているかどうかを確認します。 有効期限が切れる前に SAS を更新することをお勧めします。 クライアントと Event Hubs サービス ノードの間にクロック スキューがある場合、クライアントに認識される前に認証トークンの有効期限が切れる可能性があります。 現在の実装では、最大 5 分のクロック スキューが考慮されます。つまり、トークンは期限切れになる 5 分前にクライアントによって更新されます。 そのため、クロック スキューが 5 分を超えると、クライアントでは断続的な認証エラーが確認される場合があります。
- **SAS の開始時刻** が **[今すぐ]** に設定されている場合は、クロックス キュー (さまざまなコンピューター上での現在時刻の差) が原因で、最初の数分間に断続的なエラーが発生する場合があります。 開始時刻を 15 分以上前になるように設定するか、まったく設定しないでください。 同じことが、一般的には有効期限にも適用されます。 

詳細については、次の記事を参照してください。 

- [Shared Access Signature (SAS) を使用して認証する](authenticate-shared-access-signature.md) 
- [Shared Access Signature を使用する Event Hubs リソースへのアクセスの承認](authorize-access-shared-access-signature.md)

## <a name="next-steps"></a>次のステップ

次の記事をご覧ください。

* [接続の問題のトラブルシューティング](troubleshooting-guide.md)
