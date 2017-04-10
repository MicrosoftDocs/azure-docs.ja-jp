---
title: "Azure Service Bus の認証と承認 | Microsoft Docs"
description: "Shared Access Signature (SAS) 認証の概要です。"
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
ms.date: 03/21/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 8e113a78519444fd1e0a9da89ec95aa0dccebb2b
ms.lasthandoff: 03/27/2017


---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus の認証と承認
アプリケーションは、Shared Access Signature (SAS) 認証または Azure Active Directory Access Control (Access Control Service または ACS とも呼ばれます) を使用して、Azure Service Bus に対して認証できます。 Shared Access Signature 認証により、アプリケーションは、名前空間、または特定の権限が関連付けられているエンティティで構成されたアクセス キーを使用して Service Bus に対して認証できます。 次に、このキーを使用して、クライアントが Service Bus に対する認証に使用できる Shared Access Signature トークンを生成できます。

> [!IMPORTANT]
> SAS は、簡単で柔軟性が高く、使いやすい認証スキームを Service Bus に提供しているため、ACS よりも推奨されています。 アプリケーションは、承認された "ユーザー" の概念を管理する必要がないシナリオで SAS を使用できます。 

## <a name="shared-access-signature-authentication"></a>Shared Access Signature 認証
[SAS 認証](service-bus-sas.md)により、特定の権限で Service Bus リソースにアクセスできるようになります。 Service Bus の SAS 認証には、Service Bus リソースに対する関連した権限を使用した暗号化キーの構成が伴います。 これにより、クライアントは SAS トークンを提示してリソースへのアクセス権を取得できます。このトークンは、アクセスされるリソース URI と、構成されたキーで署名された有効期限から成ります。

SAS のキーは Service Bus 名前空間で構成できます。 このキーは、その名前空間内のすべてのメッセージング エンティティに適用されます。 Service Bus のキューとトピックでキーを構成することもできます。 SAS は [Azure リレー](../service-bus-relay/relay-authentication-and-authorization.md)でもサポートされます。

SAS を使用するには、名前空間、キュー、トピックで、次の要素で構成される [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) オブジェクトを構成します。

* *KeyName* 。
* *PrimaryKey* は、SAS トークンの署名または検証に使用される暗号化キーです。
* *SecondaryKey* は、SAS トークンの署名または検証に使用される暗号化キーです。
* *Rights* 。

名前空間レベルで構成された承認規則では、対応するキーを使用して署名されたトークンによって、クライアントの名前空間内のすべてのエンティティへのアクセス権を付与できます。 Service Bus の名前空間、キュー、トピックでは、このような承認規則を最大 12 個構成できます。 既定では、すべての権限を持つ [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) は、最初にプロビジョニングするときに、各名前空間用に構成されます。

エンティティにアクセスするには、クライアントには、特定の [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)を使用して生成された SAS トークンが必要です。 SAS トークンの生成には、承認規則に関連付けられた暗号化キーによって、アクセスが要求されるリソース URI と、有効期限で構成されるリソース文字列の HMAC-SHA256 を使用します。

Service Bus の SAS 認証サポートは、Azure .NET SDK バージョン 2.0 以降に含まれています。 SAS には、 [SharedAccessAuthorizationRule](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)のサポートが含まれています。 接続文字列をパラメーターとして受け取るすべての API では、SAS 接続文字列がサポートされています。

## <a name="acs-authentication"></a>ACS 認証
ACS を使用した Service Bus の認証は、付属の "-sb" ACS 名前空間を通じて管理されます。 付属の ACS 名前空間を Service Bus 名前空間用に作成すると、Azure クラシック ポータルを使用して Service Bus 名前空間を作成することはできません。この場合、この名前空間は、PowerShell コマンドレット [New-AzureSBNamespace](/powershell/servicemanagement/azure.compute/v1.6.1/New-AzureSBNamespace) を使用して作成する必要があります。 次に例を示します。

```powershell
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $true
```

ACS 名前空間を作成しないようにするには、次のコマンドを発行します。

```powershell
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $false
```

たとえば、**contoso.servicebus.windows.net** という名前の Service Bus 名前空間を作成する場合、**contoso-sb.accesscontrol.windows.net** という名前の付属の ACS 名前空間が自動的にプロビジョニングされます。 2014 年 8 月以前に作成されたすべての名前空間では、付属の ACS 名前空間も作成されていました。

この付属の ACS 名前空間では、すべての権限を持つ既定のサービス ID "所有者" が既定でプロビジョニングされます。 適切な信頼関係を構成することで、ACS を通じて任意の Service Bus エンティティに対する細かい制御が可能になります。 Service Bus エンティティへのアクセスを管理するために追加のサービス ID を構成できます。

エンティティにアクセスするために、クライアントは資格情報を提示して適切な要求を伴った ACS からの SWT トークンを要求します。 その後、SWT トークンは要求の一部として Service Bus に送信され、エンティティへのアクセスのためにクライアントの承認が有効になります。

Service Bus の ACS 認証サポートは、Azure .NET SDK バージョン 2.0 以降に含まれています。 この認証には、 [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider)のサポートが含まれています。 接続文字列をパラメーターとして受け取るすべての API では、ACS 接続文字列がサポートされています。

## <a name="next-steps"></a>次のステップ
SAS の詳細については、「[Shared Access Signature による Service Bus の認証](service-bus-sas.md)」を引き続きお読みください。

Azure リレーの認証と承認についての対応する情報は、「[Azure Relay authentication and authorization (Azure リレーの認証と承認)](../service-bus-relay/relay-authentication-and-authorization.md)」を参照してください。 


