---
title: Event Hubs の Azure スキーマ レジストリ (プレビュー)
description: この記事では、Azure Event Hubs での Schema Registry のサポート (プレビュー) に関する概要について説明します。
ms.topic: overview
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: a876651b76aa259754623854b8fc4a7c6c8a939e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92330497"
---
# <a name="azure-schema-registry-in-event-hubs-preview"></a>Event Hubs の Azure スキーマ レジストリ (プレビュー)
多くのイベント ストリーミングおよびメッセージング シナリオでは、イベントまたはメッセージのペイロードに、Apache Avro のようなスキーマ駆動型の形式を使用してシリアル化または逆シリアル化された構造化データが含まれています。 送信側と受信側のどちらであっても、JSON スキーマのようなスキーマ ドキュメントでデータの整合性を検証したい場合があります。 スキーマ駆動型の形式の場合、メッセージ コンシューマーでスキーマを使用できるようにすることは、コンシューマーでデータを逆シリアル化できるようにするための前提条件です。 

**Azure Schema Registry** は Event Hubs の機能で、イベント駆動型およびメッセージング中心のアプリケーション用のスキーマ ドキュメントの中央リポジトリとして機能します。 プロデューサーとコンシューマー アプリケーションの間でスキーマを管理して共有することなくデータを交換したり、それぞれ異なる速度で発展させることができる柔軟性を提供します。 また、Schema Registry には、再利用可能なスキーマのための単純なガバナンス フレームワークが用意されており、グループ化構成体 (スキーマ グループ) を使用してスキーマ間のリレーションシップを定義します。

> [!NOTE]
> - 現在、**Schema Registry** 機能は **プレビュー** 段階にあります。運用環境のワークロードにはお勧めできません。
> - この機能は **Standard** と **Dedicated** レベルでのみ使用でき、**Basic** レベルでは使用できません。

Apache Avro のようなスキーマ駆動型のシリアル化フレームワークを使用すると、共有スキーマにシリアル化メタデータを外部化するすることで、JSON などのタグ付きの形式の場合と同様に、すべてのデータ セットに含まれる型情報およびフィールド名の、メッセージごとのオーバーヘッドを大幅に削減することもできます。 スキーマをイベントと一緒にイベント インフラストラクチャ内に保持しておくことで、シリアル化または逆シリアル化に必要なメタデータに常にアクセスできるため、スキーマを間違えることがありません。 

## <a name="event-hubs-namespace"></a>Event Hubs 名前空間
Event Hubs 名前空間で、イベント ハブ (または Kafka トピック) と共にスキーマ グループをホストできるようになりました。 スキーマ レジストリをホストし、複数のスキーマ グループを持つことができます。 スキーマ レジストリは Azure Event Hubs でホストされますが、すべての Azure メッセージング サービスとその他のメッセージまたはイベント ブローカーで汎用的に使用できます。 これらの各スキーマ グループは、一連のスキーマに対して個別にセキュリティ保護できるリポジトリです。 グループは、特定のアプリケーションまたは組織単位に合わせることができます。 

## <a name="schema-groups"></a>スキーマ グループ
スキーマ グループは、ビジネス条件に基づく類似したスキーマの論理グループです。 スキーマ グループには、複数のバージョンのスキーマを保持できます。 スキーマ グループの互換性適用設定によって、新しいスキーマ バージョンに下位互換性を持たせることができます。

グループ化メカニズムによって課せられるセキュリティ境界によって、名前空間が複数のパートナー間で共有されている場合に、メタデータを通じて企業秘密が誤って漏洩するのを防ぐことができます。 また、同じ名前空間を共有する他のアプリケーションから独立して、アプリケーション所有者がスキーマを管理することもできます。


## <a name="schemas"></a>スキーマ
スキーマでは、プロデューサーとコンシューマーの間のコントラクトを定義します。 Event Hubs スキーマ レジストリで定義されるスキーマは、イベント データ外のコントラクトを管理するのに役立つため、ペイロードのオーバーヘッドを除去できます。 スキーマには、名前、型 (例: record、array など)、互換性モード (none、forward、backward、full)、およびシリアル化の種類 (現時点では Avro のみ) が含まれます。 複数のバージョンのスキーマを作成して、特定のバージョンのスキーマを取得して使用することができます。 

## <a name="client-sdks"></a>クライアント SDK
Avro シリアライザーを含む次のライブラリのいずれかを使用できます。これは、Schema Registry のスキーマ識別子と Avro エンコード データを含むペイロードをシリアル化および逆シリアル化するために使用できます。

- [.NET - Microsoft.Azure.Data.SchemaRegistry.ApacheAvro](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro)
- [Java - azure-data-schemaregistry-avro](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/)
- [Python - azure-schemaregistry-avroserializer](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer)
- [JavaScript - @azure/schema-registry-avro](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro)
- [Apache Kafka](https://github.com/Azure/azure-schema-registry-for-kafka/) - Azure Schema Registry によって提供される Kafka 統合 Apache Avro シリアライザーと逆シリアライザーを実行します。 Azure Schema Registry 用の Java クライアントの Apache Kafka クライアント シリアライザーは、任意の Apache Kafka シナリオで、Apache Kafka® ベースのデプロイまたはクラウド サービスと共に使用できます。 

次の図は、Event Hubs におけるスキーマ レジストリの情報フローを示しています。 

:::image type="content" source="./media/schema-registry-overview/flow-diagram.png" alt-text="フロー図":::

## <a name="standard-vs-dedicated-limits"></a>Standard と Dedicated の制限
Event Hubs の Standard レベルと Dedicated レベルの間で同じ、または異なる制限 (例: 名前空間内のスキーマ グループの数) については、[Schema Registry の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#schema-registry-limitations)に関するページを参照してください

## <a name="azure-role-based-access-control"></a>Azure ロールベースのアクセス制御
プログラムを使用してスキーマ レジストリにアクセスする場合は、Azure Active Directory (Azure AD) にアプリケーションを登録し、Azure ロールベースのアクセス制御 (Azure RBAC) ロールのいずれかにアプリケーションのセキュリティ プリンシパルを追加する必要があります。

| Role | 説明 | 
| ---- | ----------- | 
| 所有者 | Schema Registry グループおよびスキーマの読み取り、書き込み、および削除を行います。 |
| 共同作成者 | Schema Registry グループおよびスキーマの読み取り、書き込み、および削除を行います。 |
| [Schema Registry Reader (プレビュー)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Schema Registry グループおよびスキーマの読み取りと一覧表示を行います。 |
| [Schema Registry Contributor (プレビュー)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Schema Registry グループおよびスキーマの読み取り、書き込み、および削除を行います。 |

Azure portal を使用してアプリケーションを登録する方法については、[Azure AD にアプリを登録する](../active-directory/develop/quickstart-register-app.md)方法に関するページを参照してください。 コードで使用するクライアント ID (アプリケーション ID)、テナント ID、およびシークレットをメモしておきます。 

## <a name="next-steps"></a>次の手順

- Azure portal を使用してスキーマ レジストリを作成する方法については、[Azure portal を使用して Event Hubs スキーマ レジストリを作成する](create-schema-registry.md)方法に関するページを参照してください。
- 次の **Schema Registry Avro クライアント ライブラリ** サンプルを参照してください。
    - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro/tests/Samples)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/src/samples)
    - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro/samples )
    - [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer/samples )
    - [Azure Schema Registry の Kafka Avro 統合](https://github.com/Azure/azure-schema-registry-for-kafka/tree/master/csharp/avro/samples)
