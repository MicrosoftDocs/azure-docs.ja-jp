---
title: Azure Service Bus での AMQP 1.0 の概要
description: オープン標準プロトコルである Advanced Message Queuing Protocol (AMQP) が Azure Service Bus によってどのようにサポートされているかについて説明します。
ms.topic: article
ms.date: 02/17/2021
ms.openlocfilehash: b2ca126312f5fc3da2a7ff6e20a9ade252f489f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100653885"
---
# <a name="amqp-10-support-in-service-bus"></a>Service Bus での AMQP 1.0 サポート
Azure Service Bus クラウド サービスでは、主要な通信手段として [Advanced Message Queuing Protocol (AMQP) 1.0](http://docs.oasis-open.org/amqp/core/v1.0/amqp-core-overview-v1.0.html) が使用されています。 Microsoft は、AMQP を開発および進化させるために、過去 10 年間にわたって、お客様および競合するメッセージング ブローカーのベンダーの両方を含む、業界全体のパートナーと協力して、[OASIS AMQP 技術委員会](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp)において新しい拡張機能を開発してきました。 AMQP 1.0 は、ISO/IEC 標準です ([ISO 19464:20149](https://www.iso.org/standard/64955.html))。 

AMQP を使用すると、ベンダーおよび実装に中立で、オープンな標準プロトコルを使用したクロス プラットフォームのハイブリッド アプリケーションをビルドできます。 異なる言語とフレームワークを使用して作成され、異なるオペレーティング システムで実行可能であるコンポーネントを使用して、アプリケーションを構築できます。 これらのコンポーネントはすべて Service Bus に接続でき、構造化されたビジネス メッセージを効率よく完全な忠実度でシームレスに交換できます。

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>概要: AMQP 1.0 の紹介とその重要な理由
これまで、メッセージ指向のミドルウェア製品では、クライアント アプリケーションとブローカーの間の通信に独自プロトコルが使用されてきました。 つまり、特定のベンダーのメッセージング ブローカーを選択すると、そのベンダーのライブラリを使用してクライアント アプリケーションをそのブローカーに接続する必要があります。 この結果、アプリケーションを別の製品に移植するには、接続するすべてのアプリケーションをコード変更する必要があるため、そのベンダーへの依存の度合いが高くなります。 Java コミュニティでは、Java Message Service (JMS) や Spring Framework の抽象化などの言語固有の API 標準によってその苦労がいくらか軽減されていますが、機能の範囲が非常に狭く、他の言語を使用する開発者は考慮されていません。

さらに、異なるベンダーのメッセージング ブローカーを接続するのは面倒であり、 通常は、システム間でメッセージを移動し、独自のメッセージ形式間で変換を行うためにアプリケーション レベルのブリッジが必要になります。 これは頻繁に見られる要件です。たとえば、以前の異種システムに新しい統合インターフェイスを提供した場合や、合併後に IT システムを統合した場合などです。 AMQP を使用すると、接続ブローカーを直接相互接続できます。たとえば、[Apache Qpid Dispatch Router](https://qpid.apache.org/components/dispatch-router/index.html) のようなルーターや、[RabbitMQ](service-bus-integrate-with-rabbitmq.md) のようなブローカーネイティブの "shovels" などのルーターを使用できます。

ソフトウェア業界は動きの速いビジネスです。新しいプログラミング言語やアプリケーション フレームワークがときには困惑するほどのペースで導入されます。 同様に、IT システムの要件は時間の経過と共に進化しているため、開発者は最新のプラットフォームの機能を活用しようとします。 また、選択したメッセージング ベンダーがプラットフォームをサポートしていないこともあります。 メッセージング プロトコルが独自のものである場合、他のベンダーが新しいプラットフォームのライブラリを提供することはできません。 このため、メッセージング製品を継続して使用できるようにするには、ゲートウェイまたはブリッジの構築などの方法を使用する必要があります。

AMQP (Advanced Message Queuing Protocol) 1.0 はこのような問題に促されて開発されました。 ほとんどの金融サービス企業と同じようにメッセージ指向ミドルウェアのヘビー ユーザーである JP Morgan Chase で生み出されました。 目的はシンプルです。つまり、さまざまな言語、フレームワーク、オペレーティング システムを使用して構築されたコンポーネント (すべて広範なサプライヤーの最良のコンポーネントを使用) を使用してメッセージ ベースのアプリケーションを作成できるようにする、オープン標準メッセージング プロトコルを作ることです。

## <a name="amqp-10-technical-features"></a>AMQP 1.0 の技術的な特徴
AMQP 1.0 は、堅牢なクロス プラットフォーム メッセージング アプリケーションを作成するために使用できる、効率的で信頼性の高い回線レベルのメッセージング プロトコルです。 このプロトコルには、安全で信頼性の高い効率的なメッセージ転送を二者間で行うメカニズムを定義するという、シンプルな目的があります。 メッセージ自体は、異種混在の送信者と受信者が構造化されたビジネス メッセージを完全な忠実度で交換でき、移植できるデータ表現を使用してエンコード化されます。 最も重要な特徴の概要を次に示します。

* **効率性**: AMQP 1.0 は、プロトコル命令とそれを介して転送されるビジネス メッセージにバイナリ エンコードを使用する、接続指向のプロトコルです。 高度なフロー制御スキームが組み込まれており、ネットワークと接続コンポーネントの利用率が最大限に高められます。 そのため、このプロトコルは効率性、柔軟性、相互運用性のバランスを取るように設計されました。
* **信頼性**: AMQP 1.0 プロトコルを使用すると、ファイア アンド フォーゲット配信から、厳密に 1 回限り承認される信頼性の高い配信まで、信頼性を幅広く保証しながらメッセージを交換できます。
* **柔軟性**: AMQP 1.0 は、さまざまなトポロジのサポートに使用できる柔軟なプロトコルです。 同じプロトコルをクライアント間通信、クライアント/ブローカー間通信、ブローカー間通信に使用できます。
* **ブローカー モデルに依存しない**: AMQP 1.0 の仕様には、ブローカーによって使用されるメッセージング モデルの要件がありません。 これは、既存のメッセージング ブローカーに AMQP 1.0 のサポートを簡単に追加できることを意味します。

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 は Standard (大文字の S で始まる場合)
AMQP 1.0 は国際標準であり、ISO および IEC によって、ISO/IEC 19464:2014 として承認されています。

AMQP 1.0 は、2008 年以降、20 社を超える企業 (テクノロジ サプライヤーとエンド ユーザー企業の両方) で構成される中核的なグループにより開発されています。 その間、ユーザー企業は実際のビジネス要件を提供し、テクノロジ ベンダーはそれらの要件に合わせてプロトコルを進化させてきました。 プロセス全般にわたり、ベンダーはワークショップに参加し、実装間の相互運用性を検証するために協力してきました。

2011 年 10 月、開発作業は構造化情報標準促進協会 (OASIS) 内の専門委員会に移され、2012 年 10 月に OASIS AMQP 1.0 Standard がリリースされました。 この標準の開発中、次の企業が専門委員会に参加しました。

* **テクノロジ ベンダー**: Axway Software、Huawei Technologies、IIT Software、INETCO Systems、Kaazing、Microsoft、Mitre Corporation、Primeton Technologies、Progress Software、Red Hat、SITA、Software AG、Solace Systems、VMware、WSO2、Zenika。
* **ユーザー企業**: Bank of America、Credit Suisse、Deutsche Boerse、Goldman Sachs、JPMorgan Chase。

[OASIS AMQP 技術委員会](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp)の現在の議長は、Red Hat と Microsoft です。

オープン標準の利点としては、次のような点がよく挙げられます。

* ベンダー ロックインの可能性が下がる
* 相互運用性
* ライブラリとツールを広範に利用できる
* 陳腐化を予防できる
* 知識が豊富なスタッフを利用できる
* リスクが低く扱いやすい

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 と Service Bus
Azure Service Bus で AMQP 1.0 がサポートされるため、仲介型メッセージング機能 (Service Bus キューと発行/サブスクライブ) をさまざまなプラットフォームから効率的なバイナリ プロトコルを使って利用できます。 さらに、さまざまな言語、フレームワーク、およびオペレーティング システムを使って作成されたコンポーネントで構成されたアプリケーションを作成できます。

次の図は、標準の Java Message Service (JMS) API を使用して記述された Linux で実行される Java クライアントと、Windows で実行される .NET クライアントが、AMQP 1.0 を使用して Service Bus 経由でメッセージを交換する、サンプルのデプロイメントを示しています。

![2 つの Linux 環境と 2 つの Windows 環境でメッセージを交換する 1 つの Service Bus を示す図。][0]

**図 1: Service Bus と AMQP 1.0 を使用したクロスプラットフォーム メッセージングを示すサンプルのデプロイメント シナリオ**

Azure SDK 経由でサポートされているすべての Service Bus クライアント ライブラリが AMQP 1.0 を使用します。

- [.NET 用 Azure Service Bus](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Java 用 Azure Service Bus ライブラリ](/java/api/overview/azure/servicebus?preserve-view=true)
- [Java JMS 2.0 用 Azure Service Bus プロバイダー](how-to-use-java-message-service-20.md)
- [JavaScript および TypeScript 用の Azure Service Bus モジュール](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Python 用 Azure Service Bus ライブラリ](/python/api/overview/azure/servicebus?preserve-view=true)

[!INCLUDE [service-bus-websockets-options](../../includes/service-bus-websockets-options.md)]

また、AMQP 1.0 準拠の任意のプロトコル スタックから Service Bus を使用することもできます。

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="summary"></a>まとめ
* AMQP 1.0 は、プラットフォーム間共通のハイブリッド アプリケーションを構築するために使用できる、信頼性の高いオープンなメッセージング プロトコルです。 AMQP 1.0 は OASIS の標準です。

## <a name="next-steps"></a>次のステップ
さらに詳しい情報については、 次のリンク先を参照してください。

* [AMQP で .NET から Service Bus を使用する]
* [AMQP で Java から Service Bus を使用する]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[AMQP で .NET から Service Bus を使用する]: service-bus-amqp-dotnet.md
[AMQP で Java から Service Bus を使用する]: ./service-bus-java-how-to-use-jms-api-amqp.md

