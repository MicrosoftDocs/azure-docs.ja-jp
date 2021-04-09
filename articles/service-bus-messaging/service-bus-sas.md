---
title: Shared Access Signature による Azure Service Bus アクセス制御
description: Shared Access Signature を使用して Service Bus のアクセスの制御を行う方法と、Azure Service Bus における SAS 承認の詳細について説明します。
ms.topic: article
ms.date: 01/19/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: d210da4b653a20dd273dfce723f0bf9d5dbf743b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737819"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Shared Access Signature による Service Bus のアクセスの制御

この記事では、*Shared Access Signature* (SAS)、そのしくみ、およびプラットフォームに依存しない方法で共有アクセス署名を使用する方法について説明します。

SAS は、承認規則に基づいて Service Bus へのアクセスを保護します。 これらは、名前空間またはメッセージング エンティティ (キューまたはトピック) のいずれかに構成されます。 承認規則は、名前を持ち、特定の権限に関連付けられており、暗号化キーのペアを保持しています。 Service Bus SDK または独自のコードから規則の名前とキーを使って SAS トークンを生成します。 その後、クライアントはトークンを Service Bus に渡して、要求する操作に対する承認を証明することができます。

> [!NOTE]
> Azure Service Bus では、Azure Active Directory (Azure AD) を使用する Service Bus 名前空間とそのエンティティへのアクセスの承認がサポートされます。 Azure AD によって返された OAuth 2.0 トークンを使用するユーザーまたはアプリケーションの承認では、Shared Access Signatures (SAS) よりも優れたセキュリティが提供され、使いやすくなります。 Azure AD を使用すれば、コードにトークンを格納する必要がなく、潜在的なセキュリティ脆弱性のリスクはありません。
>
> Microsoft では、可能な場合は、Azure Service Bus アプリケーションで Azure AD を使用することをお勧めします。 詳細については、次の記事を参照してください。
> - [Azure Service Bus エンティティにアクセスするために Azure Active Directory を使用してアプリケーションを認証および承認する](authenticate-application.md)。
> - [Azure Service Bus リソースにアクセスするために Azure Active Directory を使用してマネージド ID を認証する](service-bus-managed-service-identity.md)

## <a name="overview-of-sas"></a>SAS の概要

Shared Access Signature は、簡単なトークンを使う要求ベースの承認メカニズムです。 SAS を使うと、ネットワーク経由でキーが渡されることがなくなります。 キーは、後でサービスによって検証が可能な情報に暗号で署名するために使われます。 SAS は、承認規則と照合キーをクライアントが直接所有しているユーザー名とパスワードの方式と同じように使うことができます。 また、SAS は使用できます、フェデレーション セキュリティ モデルと同じように使うこともできます。その場合、クライアントは時間制限のある署名されたアクセス トークンをセキュリティ トークン サービスから受け取り、署名キーを所有することはありません。

Service Bus での SAS 認証は、アクセス権が関連付けられている名前付きの [Shared Access Authorization 規則](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)と、プライマリおよびセカンダリの暗号化キーのペアによって構成されます。 キーは、Base64 で表された 256 ビットの値です。 規則の構成は、名前空間レベルおよび Service Bus の[リレー](../azure-relay/relay-what-is-it.md)、[キュー](service-bus-messaging-overview.md#queues)、[トピック](service-bus-messaging-overview.md#topics)で行うことができます。

[Shared Access Signature](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) のトークンには、選択された承認規則、アクセスする必要があるリソースの URI、有効期限、および選択された承認規則のプライマリまたはセカンダリ暗号化キーを使ってこれらのフィールドについて計算された HMAC-SHA256 暗号化署名が含まれます。

## <a name="shared-access-authorization-policies"></a>共有アクセス承認ポリシー

各 Service Bus 名前空間と各 Service Bus エンティティには、規則で構成された共有アクセス承認ポリシーがあります。 名前空間レベルのポリシーは、個々のポリシー構成に関係なく、名前空間内のすべてのエンティティに適用されます。

各承認ポリシー規則について、**名前**、**スコープ**、および **権限** という 3 種類の情報を決定します。 **名前** とは、文字どおり名前を表します。そのスコープ内で一意の名前です。 スコープは簡単です。対象となるリソースの URI です。 Service Bus 名前空間の場合、スコープは、`https://<yournamespace>.servicebus.windows.net/` のような完全修飾ドメイン名 (FQDN) です。

ポリシー規則での権限は、次のものの組み合わせです。

* "送信" - エンティティにメッセージを送信する権限です
* "リッスン" - 受信 (キュー、サブスクリプション) および関連するすべてのメッセージ処理の権限を付与します
* "管理" - エンティティの作成や削除など、名前空間のトポロジを管理する権限です。

"管理" 権限には "送信" 権限と "受信" 権限が含まれます。

名前空間ポリシーまたはエンティティ ポリシーは最大 12 個の共有アクセス承認規則を保持でき、それぞれが基本権限と送信および受信の組み合わせをカバーする、3 つの規則セットに対応できます。 この制限は、SAS ポリシー ストアがユーザーまたはサービス アカウント ストアのためのものではないことを明確に示します。 お使いのアプリケーションがユーザー ID またはサービス ID に基づいて Service Bus へのアクセスを許可する必要がある場合は、認証とアクセス チェックの後で SAS トークンを発行するセキュリティ トークン サービスを実装する必要があります。

承認規則には、"*主キー*" と "*セカンダリ キー*" が割り当てられます。 これらは、暗号化された強力なキーです。 これらをなくしたり、外部に漏らしたりしないでください。これらは、常に [Azure portal][Azure portal] から入手可能です。 生成されたキーのいずれかを使用できます。また、いつでも再生成できます。 ポリシーのキーを再生成または変更すると、そのキーに基づいてそれまでに発行されたすべてのトークンが、すぐに無効になります。 ただし、そのようなトークンを基にして作成された進行中の接続は、トークンの有効期限が切れるまで動作し続けます。

Service Bus の名前空間を作成すると、**RootManageSharedAccessKey** という名前のポリシー規則が、その名前空間に対して自動的に作成されます。 このポリシーには、名前空間全体の管理アクセス許可があります。 この規則は管理 **root** アカウントと同じように扱い、アプリケーションでは使わないようにすることをお勧めします。 ポータルの名前空間の **[構成]** タブ、PowerShell または Azure CLI を使って、追加のポリシー規則を作成できます。

## <a name="best-practices-when-using-sas"></a>SAS を使用する際のベスト プラクティス
アプリケーションで Shared Access Signature を使用する場合は、次の 2 つの潜在的なリスクに注意する必要があります。

- SAS が漏えいすると、それを取得した人はだれでも使用できるため、Service Bus リソースが侵害される可能性があります。
- クライアント アプリケーションに提供された SAS が期限切れになり、アプリケーションでサービスから新しい SAS を取得できない場合は、アプリケーションの機能が損なわれる可能性があります。

Shared Access Signature の使用に関する次の推奨事項に従うと、これらのリスクの軽減に役立ちます。

- **必要に応じて、クライアントで SAS が自動的に更新されるようにする**:クライアントでは、SAS を提供するサービスを使用できない場合に再試行する時間を考慮して、有効期限までに余裕を持って SAS を更新する必要があります。 SAS が、有効期間内の完了が予想される短時間で数の少ない即時操作に使用される予定の場合は、SAS が更新されることが想定されないため、不要である可能性があります。 ただし、クライアントが SAS 経由で日常的に要求を実行する場合は、有効期限に注意が必要になる可能性があります。 重要な考慮事項は、SAS の有効期限を短くする必要性 (前述のように) と、(更新が正常に完了する前に SAS の期限が切れることによる中断を避けるために) クライアントで早めに更新を要求する必要性とのバランスです。
- **SAS の開始時刻に注意する**:SAS の開始時刻を **[現在]** に設定すると、クロック スキュー (コンピューターの違いによる現在時刻の差) により、最初の数分間にエラーが間欠的に発生する場合があります。 一般に、開始時刻は 15 分以上前になるように設定します。 または、まったく設定せず、すべての場合ですぐに有効になるようにします。 同じことが、一般的には有効期限にも適用されます。 どの要求でも、最大 15 分のクロック スキューが前後に発生する可能性があることに注意してください。 
- **アクセス先のリソースを具体的に指定する**:セキュリティのベスト プラクティスは、必要最小限の特権をユーザーに付与することです。 ユーザーに必要なのは、1 つのエンティティへの読み取りアクセスだけの場合は、すべてのエンティティへの読み取り/書き込み/削除アクセスではなく、その 1 つのエンティティへの読み取りアクセスだけをユーザーに許可します。 攻撃者の手中にある SAS の機能を低下させるため、SAS が侵害された場合に損害を抑えるのにも役立ちます。
- **場合によっては SAS を使用しないようにする**:Event Hubs に対する特定の操作に関連するリスクが、SAS の利点を上回ることがあります。 このような操作では、ビジネス ルールの検証、認証、および監査の後に Event Hubs に書き込む中間層サービスを作成します。
- **常に HTTPS を使用する**:常に HTTPS を使用して SAS を作成または配布します。 SAS が HTTP 経由で渡され、傍受された場合、中間者攻撃を実行している攻撃者は、SAS を読み取って、意図したユーザーと同様に使用することができます。そのため、機微なデータが侵害されたり、悪意のあるユーザーによるデータ破損が発生したりする可能性があります。

## <a name="configuration-for-shared-access-signature-authentication"></a>Shared Access Signature 認証の構成

[SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 規則は、Service Bus の名前空間、キュー、トピックに構成できます。 Service Bus サブスクリプションに対する [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) の構成は現在サポートされていませんが、名前空間またはトピックに構成されている規則を使用して、サブスクリプションへのアクセスをセキュリティで保護できます。 この手順を示す作業サンプルについては、 [Service Bus サブスクリプションでの Shared Access Signature (SAS) 認証の使用](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) に関するサンプルを参照してください。

![SAS](./media/service-bus-sas/service-bus-namespace.png)

この図では、*manageRuleNS* *sendRuleNS* および *listenRuleNS* の承認規則は、キュー Q1 とトピック T1 の両方に適用されます。それに対し、*listenRuleQ* および *sendRuleQ* は、キュー Q1 にのみ適用され、*sendRuleT* はトピック T1 にのみ適用されます。

## <a name="generate-a-shared-access-signature-token"></a>Shared Access Signature トークンの生成

承認規則の名前およびその署名キーの 1 つにアクセスできるすべてのクライアントは、SAS トークンを生成できます。 次の形式の文字列を作成することで、トークンが生成されます。

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

- `se` - トークンの有効期限。 エポック 1970 年 1 月 1 日 `00:00:00 UTC` (UNIX エポック) からトークンの期限が切れるまでの秒数を示す整数。
- `skn` - 承認規則の名前。
- `sr` - アクセスされているリソースの URL でエンコードされた URI。
- `sig` - URL でエンコードされた HMACSHA256 署名。 ハッシュ計算は次の擬似コードのようになり、生のバイナリ出力の base64 が返されます。

    ```
    urlencode(base64(hmacsha256(urlencode('https://<yournamespace>.servicebus.windows.net/') + "\n" + '<expiry instant>', '<signing key>')))
    ```

SAS トークンを生成する C# コードの例を次に示します。

```csharp
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

> [!IMPORTANT]
> さまざまなプログラミング言語を使用して SAS トークンを生成する例については、[SAS トークンの生成](/rest/api/eventhub/generate-sas-token)に関する記事を参照してください。 


受信側が同じパラメーターでハッシュを再計算して、発行者が有効な署名キーを所有していることを確認できるように、トークンにはハッシュされていない値が含まれています。

リソース URI とは、アクセスが要求される Service Bus リソースの完全な URI です。 たとえば、`http://<namespace>.servicebus.windows.net/<entityPath>` または `sb://<namespace>.servicebus.windows.net/<entityPath>` (つまり `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`) です。 

**URI は [パーセント エンコード](/dotnet/api/system.web.httputility.urlencode)になっている必要があります。**

署名に使用される共有アクセス承認規則は、この URI、またはその階層の親のいずれかで指定したエンティティに構成する必要があります。 たとえば、前の例では、`http://contoso.servicebus.windows.net/contosoTopics/T1` または `http://contoso.servicebus.windows.net` となります。

SAS トークンは、`signature-string` で使われている `<resourceURI>` がプレフィックスになっているすべてのリソースで有効です。


## <a name="regenerating-keys"></a>キーの再生成

[SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) オブジェクトで使用されるキーは、定期的に再生成することをお勧めします。 ときどきキーをローテーションできるように、主キーとセカンダリ キーのスロットが存在します。 お使いのアプリケーションが通常は主キーを使っている場合は、主キーをセカンダリ キー スロットにコピーし、主キーだけを再生成できます。 その後、セカンダリ スロットの古い主キーを使ってアクセスを続けてきたクライアント アプリケーションに、新しい主キーの値を構成します。 すべてのクライアントが更新されたら、セカンダリ キーを再生成して、最終的に古い主キーの使用を終了できます。

キーが侵害されたことがはっきりしているか疑わしく、キーを取り消す必要がある場合は、[SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) の [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) と [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) の両方を再生成し、それらを新しいキーに置き換えることができます。 この手順により、古いキーで署名されたすべてのトークンが無効になります。

## <a name="shared-access-signature-authentication-with-service-bus"></a>Service Bus による Shared Access Signature 認証

以下で説明するシナリオには、承認規則の構成、SAS トークンの生成、クライアントの承認などが含まれます。

構成を説明して SAS 承認を使用する、Service Bus アプリケーションのサンプルについては、[Service Bus による Shared Access Signature 認証](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/ManagingEntities/SASAuthorizationRule)に関するページを参照してください。

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>エンティティの共有アクセス承認規則へのアクセス

Service Bus .NET Framework ライブラリでは、Service Bus のキューまたはトピックに構成された [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) オブジェクトに、対応する [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) または [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription) の [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) コレクションを介してアクセスできます。

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

Service Bus .NET ライブラリと Azure .NET SDK を使用するアプリケーションでは、 [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) クラスを介して SAS 承認を使用できます。 次のコードでは、Service Bus キューにメッセージを送信するトークン プロバイダーの使用を示しています。 ここで示した使用法の代わりに、以前に発行されたトークンをトークン プロバイダーのファクトリ メソッドに渡すこともできます。

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

また、トークン プロバイダーを直接使って、他のクライアントに渡すトークンを発行することもできます。

接続文字列には、規則名 (*SharedAccessKeyName*) と規則キー (*SharedAccessKey*) または以前に発行されたトークン (*SharedAccessSignature*) を含めることができます。 接続文字列を受け付けるコンストラクターまたはファクトリ メソッドに渡される接続文字列にこれらが存在する場合、SAS トークン プロバイダーが自動的に作成されて設定されます。

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

前のセクションでは、データを Service Bus に送信するために、HTTP POST 要求を使用して SAS トークンを使用する方法について説明しました。 ご存じのように、Service Bus へのアクセスには、AMQP (Advanced Message Queuing Protocol) を使用できます。AMQP は、多くのシナリオでパフォーマンス上の理由から好まれているプロトコルです。 SAS トークンと AMQP の併用については、[AMQP Claim-Based Security Version 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) のドキュメントを参照してください。このドキュメントは、2013 年から執筆されているドラフトですが、現在も Azure でサポートされています。

Service Bus へのデータ送信を開始する前に、発行元から適切に定義された **$cbs** という AMQP ノードに対して、AMQP メッセージ内で SAS トークンを送信する必要があります (すべての SAS トークンを取得して検証するためにサービスで使用される "特別な" キューと考えることができます)。 発行元は、AMQP メッセージ内で **ReplyTo** フィールドを指定する必要があります。これは、サービスから発行元に対してトークンの検証結果を返信するノードです (発行元とサービス間の簡単な要求/応答のパターン)。 この応答ノードは、AMQP 1.0 仕様に記載されているように、"リモート ノードの動的作成" について話すことで "その場で" 作成されます。 発行元は SAS トークンが有効であることを確認した後に、次の処理に進み、サービスに対してデータを送信できるようになります。

次の手順は、[AMQP.NET Lite](https://github.com/Azure/amqpnetlite) ライブラリを使用して、AMQP プロトコルで SAS トークンを送信する方法を示しています。 この方法は、C\# で開発する公式の Service Bus SDK (WinRT、.NET Compact Framework、.NET Micro Framework、Mono など) を使用できない場合に有効です。 当然ながら、HTTP レベル ("Authorization" ヘッダー内で送信される HTTP POST 要求と SAS トークン) の場合と同様に、要求ベースのセキュリティが AMQP レベルでどのように機能するかを理解するためにもこのライブラリは役立ちます。 AMQP についてこのような詳しい知識が不要な場合は、公式の Service Bus SDK と .NET Framework アプリケーションを利用できます。これがその役割を果たします。

### <a name="c35"></a>C&#35;

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
> (SAS トークンを送信する必要がないときに使用されるユーザー名とパスワードを含む既定の PLAIN ではなく) **ANONYMOUS に設定された SASL 認証メカニズム** を使用して、接続が作成されている点に注意してください。
>
>

次に、発行元は、SAS トークンの送信とサービスからの応答 (トークンの検証結果) の受信に使用される 2 つの AMQP リンクを作成します。

AMQP メッセージには一連のプロパティと、簡単なメッセージより多くの情報が含まれています。 SAS トークンはメッセージの本文です (コンストラクターを使用)。 **"ReplyTo"** プロパティは、受信側リンクで検証結果を受信するノード名に設定されます (必要に応じて名前を変更できます。名前はサービスで自動的に作成されます)。 最後の 3 つの application/custom プロパティは、実行する必要がある操作の種類を示すためにサービスで使用されます。 CBS ドラフト仕様に記載されているように、**操作名** ("put-token")、**トークンの種類** (この例では、`servicebus.windows.net:sastoken`)、およびトークンを適用する **オーディエンスの "名前"** (エンティティ全体) を設定する必要があります。

発行元は、送信側リンクで SAS トークンを送信した後に、受信側リンクの応答を読み取る必要があります。 応答は、**"status-code"** というアプリケーション プロパティを含む簡単な AMQP メッセージです。このプロパティには、HTTP 状態コードと同じ値を含めることができます。

## <a name="rights-required-for-service-bus-operations"></a>Service Bus の操作に必要な権限

次の表に、Service Bus のリソースでのさまざまな操作に必要となるアクセス権を示します。

| 操作 | 必要な要求 | 要求のスコープ |
| --- | --- | --- |
| **Namespace** | | |
| 名前空間での承認規則を構成する |管理する |任意の名前空間アドレス |
| **サービス レジストリ** | | |
| プライベート ポリシーを列挙する |管理する |任意の名前空間アドレス |
| 名前空間でリッスンを開始する |リッスン |任意の名前空間アドレス |
| 名前空間のリスナーにメッセージを送信する |Send |任意の名前空間アドレス |
| **キュー** | | |
| キューを作成する |管理する |任意の名前空間アドレス |
| キューを削除する |管理する |任意の有効なキュー アドレス |
| キューを列挙する |管理する |/$Resources/Queues |
| キューの説明を取得する |管理する |任意の有効なキュー アドレス |
| キューの承認規則を構成する |管理する |任意の有効なキュー アドレス |
| キューに送信する |Send |任意の有効なキュー アドレス |
| キューからメッセージを受信する |リッスン |任意の有効なキュー アドレス |
| ピーク ロック モードでメッセージを受信した後にそのメッセージを破棄または終了する |リッスン |任意の有効なキュー アドレス |
| 後で取得するためにメッセージを保留する |リッスン |任意の有効なキュー アドレス |
| メッセージを配信不能にする |リッスン |任意の有効なキュー アドレス |
| メッセージのキュー セッションに関連付けられた状態を取得する |リッスン |任意の有効なキュー アドレス |
| メッセージのキュー セッションに関連付けられた状態を設定する |リッスン |任意の有効なキュー アドレス |
| 後で配信するメッセージのスケジュールを設定する (例: [ScheduleMessageAsync()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_)) |リッスン | 任意の有効なキュー アドレス
| **トピック** | | |
| トピックを作成する |管理する |任意の名前空間アドレス |
| トピックを削除する |管理する |任意の有効なトピック アドレス |
| トピックを列挙する |管理する |/$Resources/Topics |
| トピックの説明を取得する |管理する |任意の有効なトピック アドレス |
| トピックの承認規則を構成する |管理する |任意の有効なトピック アドレス |
| トピックに送信する |Send |任意の有効なトピック アドレス |
| **サブスクリプション** | | |
| サブスクリプションの作成 |管理する |任意の名前空間アドレス |
| サブスクリプションを削除する |管理する |../myTopic/Subscriptions/mySubscription |
| サブスクリプションを列挙する |管理する |../myTopic/Subscriptions |
| サブスクリプションの説明を取得する |管理する |../myTopic/Subscriptions/mySubscription |
| ピーク ロック モードでメッセージを受信した後にそのメッセージを破棄または終了する |リッスン |../myTopic/Subscriptions/mySubscription |
| 後で取得するためにメッセージを保留する |リッスン |../myTopic/Subscriptions/mySubscription |
| メッセージを配信不能にする |リッスン |../myTopic/Subscriptions/mySubscription |
| トピック セッションに関連付けられた状態を取得する |リッスン |../myTopic/Subscriptions/mySubscription |
| トピック セッションに関連付けられた状態を設定する |リッスン |../myTopic/Subscriptions/mySubscription |
| **ルール** | | |
| 規則を作成する |管理する |../myTopic/Subscriptions/mySubscription |
| 規則を削除する |管理する |../myTopic/Subscriptions/mySubscription |
| 規則を列挙する |管理またはリッスン |../myTopic/Subscriptions/mySubscription/Rules

## <a name="next-steps"></a>次のステップ

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用方法](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com
