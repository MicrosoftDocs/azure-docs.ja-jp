---
title: "Shared Access Signature による Service Bus の認証 | Microsoft Docs"
description: "Shared Access Signature を使用して Service Bus を認証する方法と、Azure Service Bus における SAS 認証の詳細について説明します。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 09577d3160137b7879a5c128552d8dcbef89bb0d
ms.openlocfilehash: c025629c7700c0ee7b6495a922b9bf6823769cfa


---

# <a name="service-bus-authentication-with-shared-access-signatures"></a>Shared Access Signature による Service Bus の認証

*Shared Access Signatures* (SAS) は Service Bus メッセージングの主要なセキュリティ メカニズムです。 この記事では、SAS とそのしくみ、およびプラットフォームに依存しない方法で SAS を使用する方法について説明します。

SAS 認証により、アプリケーションは、名前空間、または特定の権限が関連付けられているメッセージ エンティティ (キューまたはトピック) で構成されたアクセス キーを使用して Service Bus に対して認証できます。 次に、このキーを使用して、クライアントが後で Service Bus に対する認証に使用できる SAS トークンを生成できます。

Service Bus の SAS 認証サポートは、Azure SDK バージョン 2.0 以降に含まれています。

## <a name="overview-of-sas"></a>SAS の概要

Shared Access Signature は、SHA-256 セキュア ハッシュまたは URI に基づいた認証メカニズムです。 SAS は、すべての Service Bus サービスによって使用される非常に強力なメカニズムです。 実際の使用では、SAS には*共有アクセス ポリシー*と *Shared Access Signature* (多くの場合*トークン*と呼ばれます) という&2; つのコンポーネントがあります。

Service Bus の SAS 認証には、Service Bus リソースに対する関連した権限を使用した暗号化キーの構成が伴います。 クライアントは SAS トークンを渡して Service Bus のリソースへのアクセスを要求します。 このトークンは、アクセスされるリソース URI と、構成されたキーで署名された有効期限で構成されます。

Shared Access Signature の承認規則は、Service Bus の [リレー](service-bus-fundamentals-hybrid-solutions.md#relays)、[キュー](service-bus-fundamentals-hybrid-solutions.md#queues)、および[トピック](service-bus-fundamentals-hybrid-solutions.md#topics)に構成できます。

SAS 認証では、次の要素が使用されます。

* [共有アクセス承認規則](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule): Base64 形式の 256 ビット プライマリ暗号化キー、オプションのセカンダリ キー、キー名と関連付けられている権限 (*Listen*、*Send*、*Manage* 権限のコレクション)。
* [Shared Access Signature](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) トークン: リソース文字列の HMAC-SHA256 を使用して生成されます。この文字列は、暗号化キーによって、アクセスされるリソースの URI と、有効期限で構成されます。 以下のセクションで説明する署名とその他の要素は、SAS トークンを構成するために文字列にフォーマットされています。

## <a name="shared-access-policy"></a>共有アクセス ポリシー

SAS を理解するうえで重要な点の&1; つは、SAS の基盤はポリシーであるということです。 各ポリシーについて、**名前**、**スコープ**、および**アクセス許可**という&3; 種類の情報を決定します。 **名前**とは、文字どおり名前を表します。そのスコープ内で一意の名前です。 スコープは簡単です。対象となるリソースの URI です。 Service Bus 名前空間の場合、スコープは、`https://<yournamespace>.servicebus.windows.net/` のような完全修飾ドメイン名 (FQDN) です。

ポリシーに使用可能な権限は、いずれも名前から意味がわかりやすくなっています。

* 送信
* リッスン
* [管理]

ポリシーを作成した後、そのポリシーには*プライマリ キー*と*セカンダリ キー*が割り当てられます。 これらは、暗号化された強力なキーです。 これらをなくしたり、外部に漏らしたりしないでください。これらは、常に [Azure Portal][Azure portal] から入手可能です。 生成されたキーのいずれかを使用できます。また、いつでも再生成できます。 ただし、再生成するか、またはポリシーのプライマリ キーを変更する場合は、ポリシーから作成された Shared Access Signature がすべて無効になります。

Service Bus の名前空間を作成するときに、**RootManageSharedAccessKey** という名前空間全体に対してポリシーが自動的に作成され、そのポリシーにはすべての権限が与えられます。 **root** としてログオンしないため、本当に正当な理由がない限り、このポリシーを使用しないでください。 ポータルの名前空間の **[構成]** タブで、追加のポリシーを作成できます。 重要なのは、Service Bus の 1 つのツリーのレベル (名前空間、キューなど) には、最大 12 個のポリシーだけを保持できることです。

## <a name="configuration-for-shared-access-signature-authentication"></a>Shared Access Signature 認証の構成
[SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 規則は、Service Bus の名前空間、キュー、トピックに構成できます。 Service Bus サブスクリプションに対する [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) の構成は現在サポートされていませんが、名前空間またはトピックに構成されている規則を使用して、サブスクリプションへのアクセスをセキュリティで保護できます。 この手順を示す作業サンプルについては、 [Service Bus サブスクリプションでの Shared Access Signature (SAS) 認証の使用](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) に関するサンプルを参照してください。

Service Bus の名前空間、キュー、トピックでは、このような規則を最大 12 個構成できます。 Service Bus 名前空間に構成されている規則は、その名前空間内のすべてのエンティティに適用されます。

![SAS](./media/service-bus-sas/service-bus-namespace.png)

この図では、*manageRuleNS**sendRuleNS* および *listenRuleNS* の承認規則は、キュー Q1 とトピック T1 の両方に適用されます。それに対し、*listenRuleQ* および *sendRuleQ* は、キュー Q1 にのみ適用され、*sendRuleT* はトピック T1 にのみ適用されます。

[SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) の主なパラメーターは次のとおりです。

| パラメーター | 説明 |
| --- | --- |
| *KeyName* |承認規則を説明する文字列。 |
| *PrimaryKey* |SAS トークンの署名と検証用の Base64 でエンコードされた 256 ビットのプライマリ キー。 |
| *SecondaryKey* |SAS トークンの署名と検証用の Base64 でエンコードされた 256 ビットのセカンダリ キー。 |
| *AccessRights* |承認規則によって付与されたアクセス権限の一覧。 これらの権限には、Listen、Send、Manage 権限の任意のコレクションを指定できます。 |

Service Bus 名前空間がプロビジョニングされると、[KeyName](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_KeyName) が **RootManageSharedAccessKey** に設定された [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) が既定で作成されます。

## <a name="generate-a-shared-access-signature-token"></a>Shared Access Signature (トークン) の生成

ポリシー自体は、Service Bus のアクセス トークンではありません。 これは、アクセス トークンの生成元のオブジェクトで、プライマリ キーまたはセカンダリ キーを使用します。 共有アクセス承認規則で指定された署名キーにアクセスできるクライアントは、SAS トークンを生成できます。 次の形式の文字列を注意深く作成することで、トークンが生成されます。

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

ここで `signature-string` は、CRLF を追加し、有効期限 (エポックとなる 1970 年 1 月 1 日の `00:00:00 UTC` からのミリ秒) を設定したトークンのスコープ (前のセクションで説明した**スコープ**) の SHA-256 ハッシュです。 

> [!NOTE]
> トークンの有効期限が短くならないように、有効期限の値を 32 ビットの符号なし整数以上、できれば長い (64 ビット) 整数としてエンコードすることをお勧めします。  
> 
> 

ハッシュは、次の擬似コードに似ていて、32 バイトを返します。

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

非ハッシュ値は、 **SharedAccessSignature** 文字列にあり、受信者は同じ結果が返されることを確認するために、同じパラメーターを使用してハッシュを計算できます。 URI はスコープを指定し、キー名はハッシュを計算するために使用するポリシーを識別します。 これは、セキュリティの観点からは重要です。 署名が、受信者 (Service Bus) が計算した署名と一致しない場合は、アクセスは拒否されます。 この時点で、送信者にはキーへのアクセス権があり、この送信者にポリシーで指定した権限を付与する必要があることが確認できます。

この操作には、エンコード済みのリソース URI を使用する必要があることに注意してください。 リソース URI とは、アクセスが要求される Service Bus リソースの完全な URI です。 たとえば、`http://<namespace>.servicebus.windows.net/<entityPath>` または `sb://<namespace>.servicebus.windows.net/<entityPath>` (つまり `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`) です。

署名に使用される共有アクセス承認規則は、この URI、またはその階層の親のいずれかで指定したエンティティに構成する必要があります。 たとえば、前の例では、`http://contoso.servicebus.windows.net/contosoTopics/T1` または `http://contoso.servicebus.windows.net` となります。

SAS トークンは、`signature-string` で使用される `<resourceURI>` の下のすべてのリソースで有効です。

SAS トークン内の [KeyName](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_KeyName) は、トークンの生成に使用される共有アクセス承認規則の **keyName** を表します。

*URL-encoded-resourceURI* は、署名の計算中に署名文字列で使用する URI と同じものにする必要があります。 [パーセントエンコード](https://msdn.microsoft.com/library/4fkewx0t.aspx)にする必要があります。

[SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) オブジェクトで使用されるキーは、定期的に再生成することをお勧めします。 一般的に、アプリケーションでは、SAS トークンの生成に [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) を使用する必要があります。 キーを再生成するときには、 [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) を古いプライマリ キーに置き換え、新しいキーを新しいプライマリ キーとして生成する必要があります。 これにより、古いプライマリ キーで発行され、まだ期限が切れていないトークンを引き続き承認に使用できます。

キーが侵害され、キーを取り消す必要がある場合は、[SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) の [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) と [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) の両方を再生成し、それらを新しいキーに置き換えることができます。 この手順により、古いキーで署名されたすべてのトークンが無効になります。

## <a name="how-to-use-shared-access-signature-authentication-with-service-bus"></a>Service Bus による Shared Access Signature 認証の使用方法

以下のシナリオには、承認規則の構成、SAS トークンの生成、クライアントの承認などが含まれます。

構成を説明して SAS 承認を使用する、Service Bus アプリケーションの完全に動作するサンプルについては、 [Service Bus による Shared Access Signature 認証](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8)に関するページを参照してください。 Service Bus サブスクリプションをセキュリティで保護するために名前空間またはトピックに構成された SAS 承認規則の使用を示す関連のサンプルについては、 [Service Bus サブスクリプションでの Shared Access Signature (SAS) 認証の使用](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c)に関するページを参照してください。

## <a name="access-shared-access-authorization-rules-on-a-namespace"></a>名前空間の共有アクセス承認規則へのアクセス

Service Bus 名前空間のルートに対する操作では、証明書の認証が必要です。 Azure サブスクリプションの管理証明書をアップロードする必要があります。 管理証明書をアップロードするには、[Azure Portal][Azure portal] を使用して[こちら](../cloud-services/cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate)の手順に従います。 Azure 管理証明書の詳細については、 [Azure 証明書の概要](../cloud-services/cloud-services-certs-create.md#what-are-management-certificates)に関するセクションを参照してください。

Service Bus 名前空間の共有アクセス承認規則にアクセスするためのエンドポイントは次のようになります。

```http
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/
```

Service Bus 名前空間に [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) オブジェクトを作成するには、JSON または XML としてシリアル化された規則情報を使用して、このエンドポイントに対して POST 操作を実行します。 次に例を示します。

```csharp
// Base address for accessing authorization rules on a namespace
string baseAddress = @"https://management.core.windows.net/<subscriptionId>/services/ServiceBus/namespaces/<namespace>/AuthorizationRules/";

// Configure authorization rule with base64-encoded 256-bit key and Send rights
var sendRule = new SharedAccessAuthorizationRule("contosoSendAll",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send });

// Operations on the Service Bus namespace root require certificate authentication.
WebRequestHandler handler = new WebRequestHandler
{
    ClientCertificateOptions = ClientCertificateOption.Manual
};
// Access the management certificate by subject name
handler.ClientCertificates.Add(GetCertificate(<certificateSN>));

HttpClient httpClient = new HttpClient(handler)
{
    BaseAddress = new Uri(baseAddress)
};
httpClient.DefaultRequestHeaders.Accept.Add(
    new MediaTypeWithQualityHeaderValue("application/json"));
httpClient.DefaultRequestHeaders.Add("x-ms-version", "2015-01-01");

// Execute a POST operation on the baseAddress above to create an auth rule
var postResult = httpClient.PostAsJsonAsync("", sendRule).Result;
```

同様に、名前空間に構成された承認規則を読み取るには、エンドポイントに対して GET 操作を使用します。

特定の承認規則を更新または削除するには、次のエンドポイントを使用します。

```http
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/{KeyName}
```

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>エンティティの共有アクセス承認規則へのアクセス

Service Bus のキューまたはトピックに構成された [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) オブジェクトへは、対応する [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) または [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription) の [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) コレクションを介してアクセスできます。

次のコードでは、キューの承認規則を追加する方法を示します。

```csharp
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString(
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## <a name="use-shared-access-signature-authorization"></a>Shared Access Signature 承認の使用

Service Bus .NET ライブラリと Azure .NET SDK を使用するアプリケーションでは、 [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) クラスを介して SAS 承認を使用できます。 次のコードでは、Service Bus キューにメッセージを送信するトークン プロバイダーの使用を示しています。

```csharp
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb",
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri,
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

アプリケーションは、接続文字列を受け入れるメソッドで SAS 接続文字列を使用することで、認証に SAS を使用することもできます。

Service Bus リレーで SAS 承認を使用するには、Service Bus 名前空間に構成されている SAS キーを使用できます。 名前空間 ([RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription) を指定した [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager)) のオブジェクトにリレーを明示的に作成する場合は、そのリレーに SAS 規則を設定するだけです。 Service Bus サブスクリプションで SAS 承認を使用するには、Service Bus 名前空間またはトピックに構成されている SAS キーを使用できます。

## <a name="use-the-shared-access-signature-at-http-level"></a>Shared Access Signature の使用 (HTTP レベル)

Service Bus ですべてのエンティティの Shared Access Signature を作成する方法を理解したので、HTTP POST を実行する準備ができました。

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 

この方法は、すべての機能に対して利用できます。 キュー、トピック、またはサブスクリプションに対して SAS を作成できます。 

送信者またはクライアントに SAS トークンを付与する場合は、送信者またはクライアントはキーを直接保持しないため、キーを取得するハッシュを反転できません。 そのため、管理者は、送信者またはクライアントがアクセスできる対象とアクセスする期間を制御できます。 重要なのは、ポリシーのプライマリ キーを変更する場合は、ポリシーから作成された Shared Access Signature がすべて無効になるということです。

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Shared Access Signature の使用 (AMQP レベル)

前のセクションでは、データを Service Bus に送信するために、HTTP POST 要求を使用して SAS トークンを使用する方法について説明しました。 ご存じのように、Service Bus へのアクセスには、AMQP (Advanced Message Queuing Protocol) を使用できます。AMQP は、多くのシナリオでパフォーマンス上の理由から好まれているプロトコルです。 SAS トークンと AMQP の併用については、[AMQP Claim-Based Security Version 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) のドキュメントを参照してください。このドキュメントは、2013 年から執筆されているドラフトですが、現在 Azure でもサポートされています。

Service Bus へのデータ送信を開始する前に、発行元から適切に定義された **$cbs** という AMQP ノードに対して、AMQP メッセージ内で SAS トークンを送信する必要があります (すべての SAS トークンを取得して検証するためにサービスで使用される "特別な" キューと考えることができます)。 発行元は、AMQP メッセージ内で **ReplyTo** フィールドを指定する必要があります。これは、サービスから発行元に対してトークンの検証結果を返信するノードです (発行元とサービス間の簡単な要求/応答のパターン)。 この応答ノードは、AMQP 1.0 仕様に記載されているように、"リモート ノードの動的作成" について話すことで "その場で" 作成されます。 発行元は SAS トークンが有効であることを確認した後に、次の処理に進み、サービスに対してデータを送信できるようになります。

次の手順は、[AMQP.Net Lite](https://github.com/Azure/amqpnetlite) ライブラリを使用して、AMQP プロトコルで SAS トークンを送信する方法を示しています。 この方法は、C\#で開発する公式の Service Bus SDK (WinRT、.Net Compact Framework、.Net Micro Framework、Mono など) を使用できない場合に有効です。当然ながら、HTTP レベル ("Authorization" ヘッダー内で送信される HTTP POST 要求と SAS トークン) の場合と同様に、要求ベースのセキュリティが AMQP レベルでどのように機能するかを理解するためにもこのライブラリは役立ちます。 AMQP についてこのような詳しい知識が不要な場合は、公式の Service Bus SDK と .NET Framework アプリケーションを利用できます。これがその役割を果たします。

### <a name="c35"></a>C (&)&#35;；

```csharp
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

`PutCbsToken()` メソッドは、サービスに対する TCP 接続を表す *connection* ([AMQP .NET Lite ライブラリ](https://github.com/Azure/amqpnetlite)に用意されている AMQP Connection クラス インスタンス) と、送信する SAS トークンである *sasToken* パラメーターを受け取ります。 

> [!NOTE]
> (SAS トークンを送信する必要がないときに使用されるユーザー名とパスワードを含む既定の PLAIN ではなく) **EXTERNAL に設定された SASL 認証メカニズム** を使用して、接続が作成されている点に注意してください。
> 
> 

次に、発行元は、SAS トークンの送信とサービスからの応答 (トークンの検証結果) の受信に使用される&2; つの AMQP リンクを作成します。

AMQP メッセージには一連のプロパティと、簡単なメッセージより多くの情報が含まれています。 SAS トークンはメッセージの本文です (コンストラクターを使用)。 **"ReplyTo"** プロパティは、受信側リンクで検証結果を受信するノード名に設定されます (必要に応じて名前を変更できます。名前はサービスで自動的に作成されます)。 最後の&3; つの application/custom プロパティは、実行する必要がある操作の種類を示すためにサービスで使用されます。 CBS ドラフト仕様に記載されているように、**操作名** ("put-token")、**トークンの種類** (この例では、"servicebus.windows.net:sastoken")、およびトークンを適用する**オーディエンスの "名前"** (エンティティ全体) を設定する必要があります。

発行元は、送信側リンクで SAS トークンを送信した後に、受信側リンクの応答を読み取る必要があります。 応答は、**"status-code"** というアプリケーション プロパティを含む簡単な AMQP メッセージです。このプロパティには、HTTP 状態コードと同じ値を含めることができます。

## <a name="rights-required-for-service-bus-operations"></a>Service Bus の操作に必要な権限

次の表に、Service Bus のリソースでのさまざまな操作に必要となるアクセス権を示します。

| 操作 | 必要な要求 | 要求のスコープ |
| --- | --- | --- |
| **名前空間** | | |
| 名前空間での承認規則を構成する |Manage |任意の名前空間アドレス |
| **サービス レジストリ** | | |
| プライベート ポリシーを列挙する |Manage |任意の名前空間アドレス |
| 名前空間でリッスンを開始する |リッスン |任意の名前空間アドレス |
| 名前空間のリスナーにメッセージを送信する |送信 |任意の名前空間アドレス |
| **キュー** | | |
| キューを作成する |Manage |任意の名前空間アドレス |
| キューを削除する |Manage |任意の有効なキュー アドレス |
| キューを列挙する |Manage |/$Resources/Queues |
| キューの説明を取得する |管理 |任意の有効なキュー アドレス |
| キューの承認規則を構成する |Manage |任意の有効なキュー アドレス |
| キューに送信する |送信 |任意の有効なキュー アドレス |
| キューからメッセージを受信する |リッスン |任意の有効なキュー アドレス |
| ピーク ロック モードでメッセージを受信した後にそのメッセージを破棄または終了する |リッスン |任意の有効なキュー アドレス |
| 後で取得するためにメッセージを保留する |リッスン |任意の有効なキュー アドレス |
| メッセージを配信不能にする |リッスン |任意の有効なキュー アドレス |
| メッセージのキュー セッションに関連付けられた状態を取得する |リッスン |任意の有効なキュー アドレス |
| メッセージのキュー セッションに関連付けられた状態を設定する |リッスン |任意の有効なキュー アドレス |
| **トピック** | | |
| トピックを作成する |Manage |任意の名前空間アドレス |
| トピックを削除する |Manage |任意の有効なトピック アドレス |
| トピックを列挙する |Manage |/$Resources/Topics |
| トピックの説明を取得する |管理 |任意の有効なトピック アドレス |
| トピックの承認規則を構成する |Manage |任意の有効なトピック アドレス |
| トピックに送信する |送信 |任意の有効なトピック アドレス |
| **サブスクリプション** | | |
| サブスクリプションを作成する |Manage |任意の名前空間アドレス |
| サブスクリプションを削除する |Manage |../myTopic/Subscriptions/mySubscription |
| サブスクリプションを列挙する |Manage |../myTopic/Subscriptions |
| サブスクリプションの説明を取得する |管理 |../myTopic/Subscriptions/mySubscription |
| ピーク ロック モードでメッセージを受信した後にそのメッセージを破棄または終了する |リッスン |../myTopic/Subscriptions/mySubscription |
| 後で取得するためにメッセージを保留する |リッスン |../myTopic/Subscriptions/mySubscription |
| メッセージを配信不能にする |リッスン |../myTopic/Subscriptions/mySubscription |
| トピック セッションに関連付けられた状態を取得する |リッスン |../myTopic/Subscriptions/mySubscription |
| トピック セッションに関連付けられた状態を設定する |リッスン |../myTopic/Subscriptions/mySubscription |
| **規則** | | |
| 規則を作成する |Manage |../myTopic/Subscriptions/mySubscription |
| 規則を削除する |Manage |../myTopic/Subscriptions/mySubscription |
| 規則を列挙する |管理またはリッスン |../myTopic/Subscriptions/mySubscription/Rules 

## <a name="next-steps"></a>次のステップ

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus の基礎](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用方法](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com


<!--HONumber=Feb17_HO3-->


