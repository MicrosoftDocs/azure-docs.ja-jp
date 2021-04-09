---
title: Azure Relay の認証と承認 | Microsoft Docs
description: この記事では、Azure Relay サービスでの Shared Access Signature (SAS) 認証の概要について説明します。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 4b0a5c7a092155a006419eedd170a63abed42bb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87033379"
---
# <a name="azure-relay-authentication-and-authorization"></a>Azure Relay の認証と承認

アプリケーションは、Shared Access Signature (SAS) 認証を使用して Azure Relay に対して認証できます。 アプリケーションは SAS 認証により、Relay 名前空間で構成されたアクセス キーを使用して Azure Relay サービスへの認証を行うことができます。 次に、このキーを使用して、クライアントがリレー サービスに対する認証に使用できる Shared Access Signature トークンを生成できます。

## <a name="shared-access-signature-authentication"></a>Shared Access Signature 認証

[SAS 認証](../service-bus-messaging/service-bus-sas.md)により、特定の権限で Azure Relay リソースにアクセスできるようになります。 SAS 認証を使用するには、リソースに対する関連した権限を使用して暗号化キーを構成する必要があります。 これにより、クライアントは SAS トークンを提示してリソースへのアクセス権を取得できます。このトークンは、アクセスされるリソース URI と、構成されたキーで署名された有効期限から成ります。

SAS のキーは Relay 名前空間で構成できます。 Service Bus メッセージングとは異なり、[Relay ハイブリッド接続](relay-hybrid-connections-protocol.md)では、許可されていない送信者や匿名の送信者がサポートされます。 ポータルの次のスクリーンショットに示すように、エンティティの作成時に、そのエンティティの匿名アクセスを有効にすることができます。

![[ハイブリッド接続の作成] というタイトルのダイアログ ボックスには、[名前] テキスト ボックスと、[クライアント認証が必要] というラベルの付いたチェック ボックス (オンになっている) があります。][0]

SAS を使用するには、Relay 名前空間で、次の要素で構成される [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) オブジェクトを構成します。

* *KeyName* 。
* *PrimaryKey* は、SAS トークンの署名または検証に使用される暗号化キーです。
* *SecondaryKey* は、SAS トークンの署名または検証に使用される暗号化キーです。
* *Rights* 。

名前空間レベルで構成された承認規則では、対応するキーを使用して署名されたトークンによって、クライアントの名前空間内のすべてのリレー接続へのアクセス権を付与できます。 Relay 名前空間では、このような承認規則を最大 12 個構成できます。 既定では、すべての権限を持つ [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) は、最初にプロビジョニングするときに、各名前空間用に構成されます。

エンティティにアクセスするには、クライアントには、特定の [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)を使用して生成された SAS トークンが必要です。 SAS トークンの生成には、承認規則に関連付けられた暗号化キーによって、アクセスが要求されるリソース URI と、有効期限で構成されるリソース文字列の HMAC-SHA256 を使用します。

Azure Relay の SAS 認証サポートは、Azure .NET SDK バージョン 2.0 以降に含まれています。 SAS には、 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)のサポートが含まれています。 接続文字列をパラメーターとして受け取るすべての API では、SAS 接続文字列がサポートされています。

## <a name="next-steps"></a>次のステップ

- SAS の詳細については、「[Shared Access Signature による Service Bus の認証](../service-bus-messaging/service-bus-sas.md)」を引き続きお読みください。
- ハイブリッド接続機能の詳細については、[Azure Relay ハイブリッド接続プロトコル ガイド](relay-hybrid-connections-protocol.md)を参照してください。
- Service Bus メッセージングの認証と承認についての対応する情報については、「[Service Bus の認証と承認](../service-bus-messaging/service-bus-authentication-and-authorization.md)」を参照してください。 

[0]: ./media/relay-authentication-and-authorization/hcanon.png