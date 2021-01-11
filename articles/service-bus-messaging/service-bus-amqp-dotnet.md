---
title: AMQP 1.0 での Azure Service Bus と .NET | Microsoft Docs
description: この記事では、AMQP (Advanced Messaging Queuing Protocol) を使用して .NET アプリケーションから Azure Service Bus を使用する方法について説明します。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 7a67ab74efc700e16f5b1689e9cc1f459ecf14bd
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067105"
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>AMQP 1.0 で .NET から Service Bus を使用する

AMQP 1.0 は、Service Bus パッケージ バージョン 2.1 以降でサポートされています。 [NuGet][NuGet] から Service Bus ビットをダウンロードすることによって確実に最新バージョンを入手できます。

## <a name="configure-net-applications-to-use-amqp-10"></a>AMQP 1.0 を使用するように .NET アプリケーションを構成する

既定では、Service Bus .NET クライアント ライブラリは AMQP プロトコルを使ってサービス バス サービスと通信します。 次のセクションで示すように、トランスポートの種類として AMQP を明示的に指定することもできます。 

現在のリリースでは、AMQP を使用する際にサポートされていない API 機能がいくつかあります。 これらのサポートされていない機能については、「[動作の違い](#behavioral-differences)」をご覧ください。 そのほか、AMQP を使用すると意味が変わる詳細な構成設定もいくつかあります。

### <a name="configuration-using-appconfig"></a>App.config を使用した構成

アプリケーションの設定は、App.config 構成ファイルを使って保存することをお勧めします。 Service Bus アプリケーションでは、App.config を使って Service Bus の接続文字列を保存できます。 App.config ファイルの例は次のとおりです。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

`Microsoft.ServiceBus.ConnectionString` 設定の値は、Service Bus への接続を構成するために使用される Service Bus 接続文字列です。 その形式は次のとおりです。

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

`namespace` と `SAS key` は、Service Bus 名前空間を作成するときに、[Azure portal][Azure portal] から取得します。 詳細については、「[Azure portal を使用して Service Bus 名前空間を作成する][Create a Service Bus namespace using the Azure portal]」を参照してください。

AMQP を使用する場合は、接続文字列に `;TransportType=Amqp` を付加します。 この表記により、クライアント ライブラリに対して、AMQP 1.0 を使用して Service Bus に接続するように通知します。

## <a name="message-serialization"></a>メッセージのシリアル化

既定のプロトコルを使用する場合、.NET クライアント ライブラリの既定のシリアル化の動作として、クライアント ライブラリと Service Bus サービスの間のトランスポートのために [DataContractSerializer][DataContractSerializer] 型を使用して [BrokeredMessage][BrokeredMessage] インスタンスをシリアル化します。 AMQP トランスポート モードを使用すると、クライアント ライブラリでは、[ブローカー メッセージ][BrokeredMessage]を AMQP メッセージにシリアル化するために、AMQP 型システムが使用されます。 このシリアル化によって、別のプラットフォームで実行されている可能性のある受信側アプリケーション (JMS API を使用して Service Bus にアクセスする Java アプリケーションなど) で、メッセージを受信して解釈できるようになります。

[BrokeredMessage][BrokeredMessage] インスタンスを構築すると、メッセージの本文として機能する .NET オブジェクトをパラメーターとしてコンストラクターに提供できます。 AMQP プリミティブ型にマップできるオブジェクトの場合、本文は AMQP データ型にシリアル化されます。 オブジェクトを AMQP プリミティブ型 (つまり、アプリケーションによって定義されているカスタム型) に直接マップできない場合、オブジェクトは [DataContractSerializer][DataContractSerializer] を使用してシリアル化され、シリアル化されたバイトが AMQP データ メッセージで送信されます。

.NET 以外のクライアントとの相互運用性を高めるには、メッセージ本文の AMQP 型に直接シリアル化できる .NET 型のみを使用してください。 次の表は、それらの型と、対応する AMQP 型システムへのマッピングを示します。

| .NET 本文のオブジェクト型 | 対応する AMQP の型 | AMQP 本文セクションの型 |
| --- | --- | --- |
| [bool] |boolean |AMQP 値 |
| byte |ubyte |AMQP 値 |
| ushort |ushort |AMQP 値 |
| uint |uint |AMQP 値 |
| ulong |ulong |AMQP 値 |
| sbyte |byte |AMQP 値 |
| short |short |AMQP 値 |
| INT |INT |AMQP 値 |
| long |long |AMQP 値 |
| float |float |AMQP 値 |
| double |double |AMQP 値 |
| decimal |decimal128 |AMQP 値 |
| char |char |AMQP 値 |
| DateTime |timestamp |AMQP 値 |
| Guid |uuid |AMQP 値 |
| byte[] |binary |AMQP 値 |
| string |string |AMQP 値 |
| System.Collections.IList |list |AMQP 値: コレクションに含まれるアイテムとして指定できるのは、この表で定義されているアイテムのみです。 |
| System.Array |array |AMQP 値: コレクションに含まれるアイテムとして指定できるのは、この表で定義されているアイテムのみです。 |
| System.Collections.IDictionary |map |AMQP 値: コレクションに含まれるアイテムとして指定できるのは、この表で定義されている項目のみです。注: String キーのみがサポートされます。 |
| Uri |記述子付き string (次の表を参照) |AMQP 値 |
| DateTimeOffset |記述子付き long (次の表を参照) |AMQP 値 |
| TimeSpan |記述子付き long (次の表を参照) |AMQP 値 |
| ストリーム |binary |AMQP データ (複数の場合あり)。 データ セクションには、Stream オブジェクトから読み取られた未加工のバイトが格納されます。 |
| その他のオブジェクト |binary |AMQP データ (複数の場合あり)。 DataContractSerializer またはアプリケーションから提供されるシリアライザーを使用するオブジェクトのシリアル化されたバイナリが格納されます。 |

| .NET 型 | 対応する AMQP の記述子付き型 | Notes |
| --- | --- | --- |
| Uri |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.AbsoluteUri |
| DateTimeOffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| TimeSpan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>` |TimeSpan.Ticks |

## <a name="behavioral-differences"></a>動作の違い

Service Bus .NET API の動作は、AMQP を使用する場合と既定のプロトコルを使用する場合では以下のようないくつかの細かい違いがあります。

* [OperationTimeout][OperationTimeout] プロパティは無視されます。
* `MessageReceiver.Receive(TimeSpan.Zero)` は `MessageReceiver.Receive(TimeSpan.FromSeconds(10))` として実装されます。
* ロック トークンによるメッセージの完了は、最初にメッセージを受信したメッセージ レシーバーでのみ実行できます。

## <a name="control-amqp-protocol-settings"></a>AMQP プロトコル設定を制御する

[.NET API](/dotnet/api/) では、AMQP プロトコルの動作を制御するいくつかの設定が公開されています。

* **[MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)** : リンクに適用する初期のクレジットを制御します。 既定値は 0 です。
* **[MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)** : 接続オープン時のネゴシエーションで提供される最大 AMQP フレーム サイズを制御します。 既定値は 65,536 バイトです。
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)** : 転送がバッチ可能である場合、この値でディスポジションを送信する場合の最大遅延が決まります。 既定では、送信側/受信側によって継承されます。 個々の送信側/受信側は、既定値 (20 ミリ秒) をオーバーライドできます。
* **[MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)** : AMQP 接続を TLS 接続で確立するかどうかを制御します。 既定値は **true**です。

## <a name="next-steps"></a>次のステップ

さらに詳しい情報については、 次のリンク先を参照してください。

* [Service Bus AMQP の概要]
* [AMQP 1.0 プロトコル ガイド]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: /dotnet/api/system.runtime.serialization.datacontractserializer?view=netcore-3.1
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azureservicebus-4.0.0
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: https://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Service Bus AMQP の概要]: service-bus-amqp-overview.md
[AMQP 1.0 プロトコル ガイド]: service-bus-amqp-protocol-guide.md