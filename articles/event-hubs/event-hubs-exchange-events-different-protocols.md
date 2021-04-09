---
title: Azure Event Hubs - さまざまなプロトコルを使用してイベントを交換する
description: この記事では、さまざまなプロトコル (AMQP、Apache Kafka、および HTTPS) を使用するコンシューマーとプロデューサーが、Azure Event Hubs の使用時にイベントを交換する方法を示しています。
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: cbc6999e3ede73b948ce034769966922b4b0f282
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89010321"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>さまざまなプロトコル (AMQP、Kafka、および HTTPS) を使用するコンシューマーとプロデューサー間でイベントを交換する
Azure Event Hubs は、コンシューマーとプロデューサー用に 3 つのプロトコル (AMQP、Kafka、および HTTPS) をサポートしています。 これらのプロトコルのそれぞれにメッセージの独自の表示方法があるため、当然、次のような疑問が生じます。アプリケーションがあるプロトコルでイベント ハブにイベントを送信し、それを別のプロトコルで使用する場合、イベントがコンシューマーに表示されるときにイベントのさまざまな部分や値はどのように見えるのでしょうか。 この記事では、プロデューサーとコンシューマーの両方で、イベント内の値がコンシューマー側アプリケーションによって正しく解釈されるようにするためのベスト プラクティスについて説明します。

この記事のアドバイスは、コード スニペットの開発で一覧にあるバージョンが使用されるクライアントに対応しています。

* Kafka Java クライアント (バージョン 1.1.1、https://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients) で入手可能
* Java 用 Microsoft Azure Event Hubs クライアント (バージョン 1.1.0、https://github.com/Azure/azure-event-hubs-java) で入手可能
* .NET 用 Microsoft Azure Event Hubs クライアント (バージョン 2.1.0、https://github.com/Azure/azure-event-hubs-dotnet) で入手可能
* Microsoft Azure Service Bus (バージョン 5.0.0、https://www.nuget.org/packages/WindowsAzure.ServiceBus) で入手可能
* HTTPS (プロデューサーのみをサポート)

その他の AMQP クライアントの動作は少し異なる場合があります。 AMQP には適切に定義された型のシステムがありますが、型システムの間での言語固有の型のシリアル化の詳細は、クライアントが AMQP メッセージの一部にアクセスする方法と同様に、クライアントによって異なります。

## <a name="event-body"></a>イベント本文
すべての Microsoft AMQP クライアントは、イベント本文を未解釈のバイトのバッグとして表します。 プロデューサー アプリケーションは、バイトのシーケンスをクライアントに渡し、コンシューマー アプリケーションはそのシーケンスをクライアントから受信します。 バイト シーケンスの解釈は、アプリケーション コード内で行われます。

HTTPS 経由でイベントを送信するときに、イベント本文は POST されたコンテンツであり、これも未解釈のバイトとして扱われます。 指定された ByteArraySerializer と ByteArrayDeserializer を次のコードに示すように使用すると、Kafka のプロデューサーまたはコンシューマーで同じ状態を簡単に実現することができます。

### <a name="kafka-byte-producer"></a>Kafka byte[] のプロデューサー

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>Kafka byte[] のコンシューマー
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

このコードでは、アプリケーションの 2 つの部分の間に透明なバイトのパイプラインを作成し、アプリケーション開発者が好きな方法で (たとえば、イベントのユーザー設定の型または送信側情報に基づいて、実行時に逆シリアル化を決定する方法など)、手動でシリアル化と逆シリアル化を行えるようにします。

1 つの固定されたイベント本文の型を持つアプリケーションは、他の Kafka シリアライザーとデシリアライザーを使用してデータを透過的に変換できる場合があります。 たとえば、JSON を使用しているアプリケーションがあるとします。 JSON 文字列の構築と解釈は、アプリケーション レベルで行われます。 Event Hubs レベルでは、イベント本文は文字列であり、UTF-8 エンコーディングで文字を表す一連のバイトです。 この場合、Kafka のプロデューサーまたはコンシューマーは、次のコードに示されているように、指定された StringSerializer または StringDeserializer を活用することができます。

### <a name="kafka-utf-8-string-producer"></a>Kafka UTF-8 文字列のプロデューサー
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>Kafka UTF-8 文字列のコンシューマー
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

AMQP 側では、Java と .NET の両方に、UTF-8 バイト シーケンス間で文字列に変換する方法が組み込まれています。 Microsoft AMQP クライアントは、イベントを EventData という名前のクラスとして表します。 次の例は、UTF-8 文字列を AMQP プロデューサーの EventData イベント本文にシリアル化する方法と、EventData イベント本文を AMQP コンシューマーの UTF-8 文字列に逆シリアル化する方法を示しています。

### <a name="java-amqp-utf-8-string-producer"></a>Java AMQP UTF-8 文字列のプロデューサー
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Java AMQP UTF-8 文字列のコンシューマー
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>C# .NET UTF-8 文字列のプロデューサー
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>C# .NET UTF-8 文字列のコンシューマー
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

Kafka はオープン ソースであるため、アプリケーション開発者はすべてのシリアライザーまたはデシリアライザーの実装を検査でき、AMQP 側で互換性のあるバイト シーケンスを生成または使用するコードを実装できます。

## <a name="event-user-properties"></a>イベント ユーザー プロパティ

ユーザー設定のプロパティは、AMQP クライアント (Microsoft AMQP クライアントではプロパティと呼ばれています) と Kafka (ヘッダーと呼ばれています) の両方から設定および取得することができます。 HTTPS の送信側は、POST 操作での HTTP ヘッダーとして指定することによって、イベントにユーザー プロパティを設定できます。 ただし、Kafka はイベント本文とイベント ヘッダーの両方の値をバイト シーケンスとして扱います。 それに対し AMQP クライアントでは、プロパティ値には型があり、AMQP 型システムに従ってプロパティ値をエンコードすることによって伝達されます。

HTTPS は特例です。 送信する時点では、すべてのプロパティ値は UTF-8 テキストです。 Event Hubs サービスでは、適切なプロパティの値を AMQP でエンコードされた 32 ビットおよび 64 ビット符号付き整数、64 ビット浮動小数点数、およびブール値に変換する解釈の量に制限があります。 これらの型のいずれかにも適合しないプロパティ値は、文字列として扱われます。

これらの方法を組み合わせたプロパティの入力は、Kafka コンシューマーでは未加工の AMQP でエンコードされたバイト シーケンス (AMQP 型情報など) が表示されることを意味します。 それに対し AMQP コンシューマーでは、Kafka プロデューサーから送信された型指定のないバイト シーケンスが表示され、これはアプリケーションで解釈する必要があります。

AMQP または HTTPS のプロデューサーからプロパティを受信する Kafka コンシューマーでは、Kafka エコシステムの他のデシリアライザーの後にモデル化されている、AmqpDeserializer クラスを使用します。 これは、データ バイトを Java の型に逆シリアル化するために、AMQP でエンコードされたバイト シーケンスの型情報を解釈します。

ベスト プラクティスとして、AMQP または HTTPS を介して送信されるメッセージにプロパティを含めることをお勧めします。 Kafka コンシューマーは、これを使用して、AMQP の逆シリアル化にヘッダー値が必要かどうかを判断することができます。 プロパティの値は重要ではありません。 必要なものは、Kafka コンシューマーがヘッダーの一覧で見つけ、必要に応じてその動作を調整できる、既知の名前だけです。

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>AMQP から Kafka へ、パート 1: プロパティを使用して C# (.NET) でイベントを作成および送信する
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>AMQP から Kafka へ、パート 2: AmqpDeserializer を使用して Kafka コンシューマーでそれらのプロパティを逆シリアル化する
```java
final AmqpDeserializer amqpDeser = new AmqpDeserializer();

ConsumerRecord<Long, Bytes> cr = /* receive event */
final Header[] headers = cr.headers().toArray();

final Header headerNamedMyStringProperty = /* find header with key "MyStringProperty" */
final Header headerNamedMyIntegerProperty = /* find header with key "MyIntegerProperty" */
final Header headerNamedAMQPheaders = /* find header with key "AMQPheaders", or null if not found */

// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // The deserialize() method requires no prior knowledge of a property's type.
    // It returns Object and the application can check the type and perform a cast later.
    Object propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyStringProperty.value());
    if (propertyOfUnknownType instanceof String) {
        final String propertyString = (String)propertyOfUnknownType;
        // do work here
    }
    propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyIntegerProperty.value());
    if (propertyOfUnknownType instanceof Integer) {
        final Integer propertyInt = (Integer)propertyOfUnknownType;
        // do work here
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

アプリケーションがプロパティに期待される型を認識している場合は、後でキャストの必要がない逆シリアル化の方法がありますが、プロパティが期待された型ではない場合はエラーがスローされます。

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP から Kafka へ、パート 3： Kafka コンシューマーでの AmqpDeserializer の別の使用方法
```java
// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // Property "MyStringProperty" is expected to be of type string.
    try {
        final String propertyString = amqpDeser.deserializeString(headerNamedMyStringProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a string
    }

    // Property "MyIntegerProperty" is expected to be a signed integer type.
    // The method returns long because long can represent the value range of all AMQP signed integer types.
    try {
        final long propertyLong = amqpDeser.deserializeSignedInteger(headerNamedMyIntegerProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a signed integer
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

その他の方向の移動は、より複雑です。Kafka プロデューサーによって設定されるヘッダーは、AMQP コンシューマーでは常に Raw 型バイト (Java 用の Microsoft Azure Event Hubs クライアントの場合は型 org.apache.qpid.proton.amqp.Binary、Microsoft の .NET AMQP クライアントの場合は `System.Byte[]`) として表示されるためです。 最も簡単な方法は、Kafka 提供のシリアライザーのいずれかを使用して、Kafka プロデューサー側のヘッダー値のバイトを生成し、AMQP コンシューマー側で互換性のある逆シリアル化のコードを記述することです。

AMQP から Kafka への場合と同様に、推奨されるベスト プラクティスは、Kafka を介して送信されるメッセージにプロパティを含めることです。 AMQP コンシューマーは、プロパティを使用して、ヘッダー値に逆シリアル化が必要かどうかを判断できます。 プロパティの値は重要ではありません。 必要なものは、AMQP コンシューマーがヘッダーの一覧で見つけ、必要に応じてその動作を調整できる、既知の名前だけです。 Kafka プロデューサーを変更できない場合は、コンシューマー側アプリケーションでプロパティ値がバイナリ型かバイト型かを確認し、型に基づいて逆シリアル化を試みることもできます。

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Kafka から AMQP へ、パート 1: プロパティを使用して Kafka からイベントを作成および送信する
```java
final String topicName = /* topic name */
final ProducerRecord<Long, String> pr = new ProducerRecord<Long, String>(topicName, /* other arguments */);
final Headers h = pr.headers();

// Set headers using Kafka serializers
IntegerSerializer intSer = new IntegerSerializer();
h.add("MyIntegerProperty", intSer.serialize(topicName, 1234));

LongSerializer longSer = new LongSerializer();
h.add("MyLongProperty", longSer.serialize(topicName, 5555555555L));

ShortSerializer shortSer = new ShortSerializer();
h.add("MyShortProperty", shortSer.serialize(topicName, (short)22222));

FloatSerializer floatSer = new FloatSerializer();
h.add("MyFloatProperty", floatSer.serialize(topicName, 1.125F));

DoubleSerializer doubleSer = new DoubleSerializer();
h.add("MyDoubleProperty", doubleSer.serialize(topicName, Double.MAX_VALUE));

StringSerializer stringSer = new StringSerializer();
h.add("MyStringProperty", stringSer.serialize(topicName, "hello world"));

// BEST PRACTICE: include a property which indicates that properties will need deserialization
h.add("RawHeaders", intSer.serialize(0));
```

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>Kafka から AMQP へ、パート 2: C# (.NET) でそれらのプロパティを手動で逆シリアル化する
```csharp
EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.Properties.ContainsKey("RawHeaders"))
{
    // Kafka serializers send bytes in big-endian order, whereas .NET on x86/x64 is little-endian.
    // Therefore it is frequently necessary to reverse the bytes before further deserialization.

    byte[] rawbytes = ed.Properties["MyIntegerProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    int myIntegerProperty = BitConverter.ToInt32(rawbytes, 0);

    rawbytes = ed.Properties["MyLongProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    long myLongProperty = BitConverter.ToInt64(rawbytes, 0);

    rawbytes = ed.Properties["MyShortProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    short myShortProperty = BitConverter.ToInt16(rawbytes, 0);

    rawbytes = ed.Properties["MyFloatProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    float myFloatProperty = BitConverter.ToSingle(rawbytes, 0);

    rawbytes = ed.Properties["MyDoubleProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    double myDoubleProperty = BitConverter.ToDouble(rawbytes, 0);

    rawbytes = ed.Properties["MyStringProperty"] as System.Byte[];
string myStringProperty = Encoding.UTF8.GetString(rawbytes);
}
```

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Kafka から AMQP へ、パート 3: Java でそれらのプロパティを手動で逆シリアル化する
```java
final EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.getProperties().containsKey("RawHeaders")) {
    byte[] rawbytes =
        ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyIntegerProperty")).getArray();
    int myIntegerProperty = 0;
    for (byte b : rawbytes) {
        myIntegerProperty <<= 8;
        myIntegerProperty |= ((int)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyLongProperty")).getArray();
    long myLongProperty = 0;
    for (byte b : rawbytes) {
        myLongProperty <<= 8;
        myLongProperty |= ((long)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyShortProperty")).getArray();
    short myShortProperty = (short)rawbytes[0];
    myShortProperty <<= 8;
    myShortProperty |= ((short)rawbytes[1] & 0x00FF);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyFloatProperty")).getArray();
    int intbits = 0;
    for (byte b : rawbytes) {
        intbits <<= 8;
        intbits |= ((int)b & 0x00FF);
    }
    float myFloatProperty = Float.intBitsToFloat(intbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyDoubleProperty")).getArray();
    long longbits = 0;
    for (byte b : rawbytes) {
        longbits <<= 8;
        longbits |= ((long)b & 0x00FF);
    }
    double myDoubleProperty = Double.longBitsToDouble(longbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyStringProperty")).getArray();
String myStringProperty = new String(rawbytes, StandardCharsets.UTF_8);
}
```

## <a name="next-steps"></a>次のステップ
この記事では、プロトコル クライアントを変更したり独自のクラスターを実行したりせずに Event Hubs にストリーム配信する方法を紹介しました。 Event Hubs と Kafka 用 Event Hubs の詳細については、次の記事を参照してください。  

* [Event Hubs について確認する](./event-hubs-about.md)
* [Kafka 用 Event Hubs について確認する](event-hubs-for-kafka-ecosystem-overview.md)
* [GitHub で Kafka 用 Event Hubs の他のサンプルを調べる](https://github.com/Azure/azure-event-hubs-for-kafka)
* [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) を使用して[オンプレミスの Kafka からクラウドの Event Hubs にイベントをストリーム配信する。](event-hubs-kafka-mirror-maker-tutorial.md)
* [ネイティブの Kafka アプリケーション](event-hubs-quickstart-kafka-enabled-event-hubs.md)、[Apache Flink](event-hubs-kafka-flink-tutorial.md)、または [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md) を使用して Event Hubs にストリーム配信する方法について確認する
