---
title: Azure Cosmos DB での保存時の暗号化
description: Azure Cosmos DB で保存時のデータの暗号化が提供される方法と、その実装方法について説明します。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: seodec18
ms.openlocfilehash: abe4bb3a53e3177857cb8041fd255ec916985054
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93089867"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Azure Cosmos DB でのデータの暗号化 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

保存時の暗号化という語句は、一般的にはソリッド ステート ドライブ (SSD) やハード ディスク (HDD) などの不揮発性メモリ デバイスでデータを暗号化することを意味します。 Cosmos DB では、プライマリ データベースを SSD に保存します。 そのメディア添付ファイルとバックアップは Azure Blob ストレージに保存され、このストレージは通常 HDD でバックアップされます。 Cosmos DB の保存時の暗号化のリリースにより、すべてのデータベース、メディア添付ファイル、バックアップが暗号化されます。 データの転送中 (ネットワーク経由) と保存時 (非揮発性メモリ) にデータが暗号化されることで、エンド ツー エンドの暗号化が実現しました。

PaaS サービスである Azure Cosmos DB は使用方法がとても簡単です。 Azure Cosmos DB に保存されているすべてのユーザー データは、保存時と転送時に暗号化されているため、操作は何も必要ありません。 つまり、保存時の暗号化機能は、既定で "オン" になっています。 オンまたはオフにするコントロールはありません。 Azure Cosmos DB では、アカウントが実行されているすべてのリージョンで AES 256 暗号化が使われます。 [可用性とパフォーマンスの SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) を引き続き満たしながら、この機能を提供します。 Azure Cosmos アカウントに格納されているデータは、Microsoft が管理するキー (サービス マネージド キー) を使用して自動的かつシームレスに暗号化されます。 必要に応じて、[カスタマー マネージド キー](how-to-setup-cmk.md)の記事で説明されているように、独自のキーを使用して 2 つ目の暗号化レイヤーを追加することもできます。

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Azure Cosmos DB に保存時の暗号化の実装

保存時の暗号化は、セキュリティで保護されたキー ストレージ システム、暗号化ネットワーク、暗号化 API など、多くのセキュリティ テクノロジを使用して実装されています。 データの暗号化を解除して処理するシステムは、キーを管理するシステムと通信する必要があります。 図には、暗号化されたデータの格納とキーの管理がどのように分離されているかを示しています。 

:::image type="content" source="./media/database-encryption-at-rest/design-diagram.png" alt-text="設計の図" border="false":::

ユーザー要求の基本的なフローは、次のとおりです。
- ユーザー データベース アカウントが準備され、ストレージ キーが管理サービス リソース プロバイダーへの要求を通じて取得されます。
- ユーザーは、HTTPS/セキュリティで保護された転送を通じて、Cosmos DB への接続を作成します。 (SDK が詳細を抽象化します。)
- ユーザーは、先に作成したセキュリティで保護された接続経由で、格納対象である JSON ドキュメントを送信します。
- ユーザーがインデックスを無効にしていない限り、JSON ドキュメントのインデックスが作成されます。
- JSON ドキュメントとインデックス データの両方が、セキュリティで保護されたストレージに書き込まれます。
- データはセキュリティで保護されたストレージから定期的に読み取られ、Azure の暗号化された Blob ストアにバックアップされます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>Q:Storage Service Encryption を有効にすると、Azure Storage の料金はどれくらい増えますか?
A:追加コストはかかりません。

### <a name="q-who-manages-the-encryption-keys"></a>Q:だれが暗号化キーを管理するのですか?
A:キーは Microsoft によって管理されます。

### <a name="q-how-often-are-encryption-keys-rotated"></a>Q:暗号化キーが交換される頻度はどの程度ですか?
A:マイクロソフトには、暗号化キーのローテーションに関する一連の内部ガイドラインがあり、Cosmos DB はそれに従っています。 個々のガイドラインは公開されていません。 Microsoft が公開している[セキュリティ開発ライフ サイクル (SDL)](https://www.microsoft.com/sdl/default.aspx) は、内部用ガイダンスのサブセットと見なされ、開発者に便利なベスト プラクティスが収められています。

### <a name="q-can-i-use-my-own-encryption-keys"></a>Q:独自の暗号化キーを使用できますか?
A:はい。この機能は新しい Azure Cosmos DB アカウントで使用できるようになっており、アカウントの作成時に行う必要があります。 詳細については、[カスタマー マネージド キー](./how-to-setup-cmk.md)のドキュメントを参照してください。

### <a name="q-what-regions-have-encryption-turned-on"></a>Q:どのリージョンで暗号化が有効になっていますか?
A:すべての Azure Cosmos DB リージョンで、すべてのユーザー データに対して暗号化が有効になっています。

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>Q:暗号化はパフォーマンス (待機時間およびスループット) の SLA に影響しますか?
A:パフォーマンスの SLA への影響や変化はないため、既存および新規のすべてのアカウントで保存時の暗号化が有効になります。 最新の保証の詳細については、「[Cosmos DB の SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db)」 ページを参照してください。

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>Q:ローカル エミュレーターは、保存時の暗号化に対応していますか?
A:エミュレーターは、スタンドアロンの開発/テスト ツールであり、管理された Cosmos DB サービスで使用するキー管理サービスを使用していません。 機密性の高いエミュレーター テスト データを格納するドライブで BitLocker を有効にすることをお勧めします。 [エミュレーターは既定のデータ ディレクトリの変更](local-emulator.md)と、よく知られた場所の使用をサポートしています。

## <a name="next-steps"></a>次のステップ

* 独自のキーを使用して 2 つ目の暗号化レイヤーを追加することもできます。詳細については、[カスタマー マネージド キー](how-to-setup-cmk.md)の記事を参照してください。
* Cosmos DB のセキュリティと最新の機能強化の概要については、[Azure Cosmos データベースのセキュリティ](database-security.md)に関するページを参照してください。
* マイクロソフトが取得している認証の詳細については、[Azure トラスト センター](https://azure.microsoft.com/support/trust-center/)に関するページを参照してください。