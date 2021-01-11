---
title: イベンド ハンドラーへのイベント配信の認証 (Azure Event Grid)
description: この記事では、Azure Event Grid のイベント ハンドラーへの配信を認証するさまざまな方法について説明します。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: abe16c9598c8c10caa832150aafac997dd7f1624
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460645"
---
# <a name="authenticate-event-delivery-to-event-handlers-azure-event-grid"></a>イベンド ハンドラーへのイベント配信の認証 (Azure Event Grid)
この記事では、イベント ハンドラーへのイベント配信の認証について説明します。 また、Azure Active Directory (Azure AD) または共有シークレットを使用して、Event Grid からイベントを受信するために使用される Webhook エンドポイントをセキュリティで保護する方法も示します。

## <a name="use-system-assigned-identities-for-event-delivery"></a>イベント配信にシステム割り当て ID を使用する
トピックまたはドメインに対してシステム割り当てのマネージド ID を有効にすることができ、その ID を使用して Service Bus のキューやトピック、イベント ハブ、ストレージ アカウントなどのサポートされている宛先にイベントを転送できます。

次に手順を示します。 

1. システム割り当て ID を持つトピックまたはドメインを作成するか、既存のトピックまたはドメインを更新して ID を有効にする。 
1. 配信先 (Service Bus キューなど) の適切なロール (Service Bus データ送信者など) に ID を追加する。
1. イベント サブスクリプションを作成する際に ID の使用を有効にして、イベントを配信先に配信する。 

詳細な手順については、「[マネージド ID を使用したイベント配信](managed-service-identity.md)」を参照してください。


## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Webhook エンドポイントへのイベント配信の認証
以下のセクションでは、Webhook エンドポイントへのイベント配信を認証する方法について説明します。 使用する方法に関係なく、検証ハンドシェイク メカニズムを使用する必要があります。 詳細については、「[Webhook のイベント配信](webhook-event-delivery.md)」を参照してください。 


### <a name="using-azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD) を使用する
Azure AD を使用して、Event Grid からイベントを受信するために使用される Webhook エンドポイントをセキュリティで保護できます。 Azure AD アプリケーションを作成し、アプリケーションで Event Grid を承認するためのロールとサービス プリンシパルを作成する必要があります。さらに、Azure AD アプリケーションを使用するようにイベント サブスクリプションを構成する必要があります。 Event Grid で Azure Active Directory を構成する方法については、[こちら](secure-webhook-delivery.md)をご覧ください。

### <a name="using-client-secret-as-a-query-parameter"></a>クエリ パラメーターとしてクライアント シークレットを使用する
イベント サブスクリプションの作成の一環として指定したWebhook の送信先 URL にクエリ パラメーターを追加することで、Webhook エンドポイントをセキュリティで保護することもできます。 クエリ パラメーターのいずれかを、[アクセス トークン](https://en.wikipedia.org/wiki/Access_token)などのクライアント シークレットまたは共有シークレットとして設定します。 Event Grid サービスでは、Webhook へのすべてのイベント配信要求にすべてのクエリ パラメーターを含めます。 Webhook サービスはシークレットを取得して検証できます。 クライアント シークレットが更新された場合は、イベント サブスクリプションも更新する必要があります。 このシークレットのローテーション中の配信エラーを回避するために、イベント サブスクリプションを新しいシークレットで更新する前に、一定期間、Webhook で古いシークレットと新しいシークレットの両方を受け入れるようにします。 

クエリ パラメーターにはクライアント シークレットを含めることができるため、特別な注意を払って処理されます。 これらは暗号化されて格納され、サービス オペレーターがアクセスすることはできません。 これらは、サービス ログ/トレースの一部として記録されません。 イベント サブスクリプションのプロパティを取得するときに、送信先クエリ パラメーターは既定では返されません。 たとえば、[--include-full-endpoint-url](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) パラメーターは、Azure [CLI](/cli/azure?view=azure-cli-latest) 内で使用されます。

Webhook へのイベント配信の詳細については、「[Webhook のイベント配信](webhook-event-delivery.md)」を参照してください。

> [!IMPORTANT]
Azure Event Grid は、**HTTPS** Webhook エンドポイントのみをサポートします。 


## <a name="next-steps"></a>次のステップ
トピックまたはドメインにイベントを発行するクライアントを認証する方法については、「[発行クライアントを認証する](security-authenticate-publishing-clients.md)」を参照してください。 
