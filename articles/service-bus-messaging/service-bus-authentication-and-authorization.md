---
title: "Azure Service Bus の認証と承認 | Microsoft Docs"
description: "Shared Access Signature (SAS) 認証を使用して、アプリケーションを Service Bus に対して認証します。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 28fb41499c919e5006f1be7daa97610c2a0583af
ms.contentlocale: ja-jp
ms.lasthandoff: 08/10/2017

---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus の認証と承認

アプリケーションは、Shared Access Signature (SAS) 認証を使用して Azure Service Bus に対して認証できます。 Shared Access Signature 認証により、アプリケーションは、名前空間、または特定の権限が関連付けられているエンティティで構成されたアクセス キーを使用して Service Bus に対して認証できます。 次に、このキーを使用して、クライアントが Service Bus に対する認証に使用できる Shared Access Signature トークンを生成できます。

> [!IMPORTANT]
> Azure Active Directory Access Control (Access Control Service または ACS とも呼ばれます) は非推奨になったため、ACS ではなく SAS を使用してください。 SAS は、簡単で柔軟性が高く、使いやすい認証スキームを Service Bus に提供しています。 アプリケーションは、承認された "ユーザー" の概念を管理する必要がないシナリオで SAS を使用できます。 詳細については、 [このブログの投稿](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/)を参照してください。

## <a name="shared-access-signature-authentication"></a>Shared Access Signature 認証

[SAS 認証](service-bus-sas.md)により、特定の権限で Service Bus リソースにアクセスできるようになります。 Service Bus の SAS 認証には、Service Bus リソースに対する関連した権限を使用した暗号化キーの構成が伴います。 これにより、クライアントは SAS トークンを提示してリソースへのアクセス権を取得できます。このトークンは、アクセスされるリソース URI と、構成されたキーで署名された有効期限から成ります。

SAS のキーは Service Bus 名前空間で構成できます。 このキーは、その名前空間内のすべてのメッセージング エンティティに適用されます。 Service Bus のキューとトピックでキーを構成することもできます。 SAS は [Azure リレー](../service-bus-relay/relay-authentication-and-authorization.md)でもサポートされます。

SAS を使用するには、名前空間、キュー、トピックで [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) オブジェクトを構成します。 この規則は次の要素で構成されます。

* *KeyName* 。
* *PrimaryKey* は、SAS トークンの署名または検証に使用される暗号化キーです。
* *SecondaryKey* は、SAS トークンの署名または検証に使用される暗号化キーです。
* *Rights* 。

名前空間レベルで構成された承認規則では、対応するキーを使用して署名されたトークンによって、クライアントの名前空間内のすべてのエンティティへのアクセス権を付与できます。 Service Bus の名前空間、キュー、トピックでは、このような承認規則を最大 12 個構成できます。 既定では、すべての権限を持つ [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) は、最初にプロビジョニングするときに、各名前空間用に構成されます。

エンティティにアクセスするには、クライアントには、特定の [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)を使用して生成された SAS トークンが必要です。 SAS トークンの生成には、承認規則に関連付けられた暗号化キーによって、アクセスが要求されるリソース URI と、有効期限で構成されるリソース文字列の HMAC-SHA256 を使用します。

Service Bus の SAS 認証サポートは、Azure .NET SDK バージョン 2.0 以降に含まれています。 SAS には、 [SharedAccessAuthorizationRule](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)のサポートが含まれています。 接続文字列をパラメーターとして受け取るすべての API では、SAS 接続文字列がサポートされています。

## <a name="next-steps"></a>次のステップ

- SAS の詳細については、「[Shared Access Signature による Service Bus の認証](service-bus-sas.md)」を引き続きお読みください。
- [ACS 対応名前空間の変更点](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/)。
- Azure リレーの認証と承認についての対応する情報は、「[Azure Relay authentication and authorization (Azure リレーの認証と承認)](../service-bus-relay/relay-authentication-and-authorization.md)」を参照してください。 


