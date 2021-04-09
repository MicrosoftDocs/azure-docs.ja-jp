---
title: Azure Storage キューと Service Bus キューの比較
description: Azure によって提供される 2 種類のキューの相違点と共通点について説明します。
ms.topic: article
ms.date: 11/04/2020
ms.openlocfilehash: 31992aa2012009c51cbeae78010ae8ced65fc872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96928309"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Storage キューと Service Bus キューの比較
この記事では、Microsoft Azure によって提供されている Storage キューと Service Bus キューという 2 種類のキューの相違点と共通点について説明します。 この情報を使用すると、どちらのソリューションが自分のニーズに最も適しているかについて、より多くの情報に基づいて判断できるようになります。

## <a name="introduction"></a>はじめに
Azure では **Storage キュー** と **Service Bus キュー** の 2 種類のキュー メカニズムをサポートしています。

**Storage キュー** は [Azure Storage](https://azure.microsoft.com/services/storage/) インフラストラクチャの一部です。 これらにより、多数のメッセージを格納することができます。 メッセージには、HTTP または HTTPS を使用して、認証された呼び出しを介して世界中のどこからでもアクセスできます。 キュー メッセージの許容される最大サイズは 64 KB です。 キューには、ストレージ アカウントの総容量の上限を超えない限り、数百万のメッセージを含めることができます。 キューは通常、非同期的な処理用に作業のバックログを作成するために使用されます。 詳細については、[Azure Storage キューの概要](../storage/queues/storage-queues-introduction.md)に関するページを参照してください。

**Service Bus キュー** は、より大きな [Azure メッセージング](https://azure.microsoft.com/services/service-bus/) インフラストラクチャの一部です。このインフラストラクチャでは、キュー処理やパブリッシュ/サブスクライブなどの高度な統合パターンがサポートされています。 これらは、複数の通信プロトコル、データ コントラクト、信頼ドメイン、ネットワーク環境などにまたがるアプリケーションやアプリケーション コンポーネントの統合を目的としています。 Service Bus のキュー、トピック、サブスクリプションの詳細については、「[Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)」をご覧ください。


## <a name="technology-selection-considerations"></a>テクノロジの選択に関する考慮事項
Storage キューと Service Bus キューの機能セットは、多少異なります。 ご自身の特定のソリューションのニーズに応じて、どちらか一方を選択することも、両方を選択することもできます。

特定のソリューションの目的に合うキュー テクノロジを判断する際、ソリューション設計者および開発者はこれらの推奨事項を検討する必要があります。 

### <a name="consider-using-storage-queues"></a>Storage キューの使用を検討する
ソリューション設計者または開発者として、次の場合に **Storage キューの使用を検討してください**。

* アプリケーションは 80 ギガバイトを超えるメッセージをキューに格納する必要がある場合。
* アプリケーションでキュー内のメッセージ処理の進行状況を追跡したい場合。 これは、メッセージを処理しているワーカーがクラッシュした場合に役に立ちます。 別のワーカーでその情報を使用して、前のワーカーが中断した時点から処理を再開できます。
* キューに対して実行されたすべてのトランザクションのサーバー側のログが必要な場合。

### <a name="consider-using-service-bus-queues"></a>Service Bus キューの使用を検討する
ソリューション設計者または開発者として、次の場合に **Service Bus キューの使用を検討してください**。

* ソリューションで、キューをポーリングせずにメッセージを受信できる必要がある場合。 Service Bus では、Service Bus がサポートする TCP ベースのプロトコルを使用し、長いポーリングの受信操作を使用することによってこれを実現できます。
* キューによるメッセージの配信が先入れ先出し (FIFO) の順序で行われることが保証される必要がある場合。
* ソリューションで、自動重複検出をサポートする必要がある場合。
* アプリケーションでメッセージを実行時間の長い並列ストリームとして処理する必要がある場合 (メッセージは、自身の [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) プロパティを使用してストリームに関連付けられます)。 このモデルでは、処理を行うアプリケーションの各ノードは、メッセージではなくストリームに対して競合します。 処理を行うノードにストリームが渡されると、そのノードはトランザクションを使用してアプリケーション ストリームの状態を確認できます。
* 複数のメッセージをキューに送信したりキューから受信したりする際にトランザクション動作と原子性が必要な場合。
* アプリケーションで処理するメッセージのサイズが 64 KB を超えることはあっても 256 KB の制限に到達することはないと考えられる場合。
* ロールベースのアクセス モデルを提供して、キューの送信側と受信側に異なる権限/アクセス許可を与える必要がある場合。 詳細については、次の記事を参照してください。
    - [マネージド ID による認証](service-bus-managed-service-identity.md)
    - [アプリケーションからの認証](authenticate-application.md)
* キューのサイズが 80 GB を超えることはない場合。
* AMQP 1.0 標準ベースのメッセージング プロトコルを使用する必要がある場合。 AMQP の詳細情報については「[Service Bus AMQP の概要](service-bus-amqp-overview.md)」をご覧ください。
* 最終的には、キューベースのポイントツーポイント通信から、パブリッシュ/サブスクライブ メッセージング パターンへの移行を想定している場合。 このパターンは、追加の受信者 (サブスクライバー) の統合を可能にします。 各受信者は、キューに送信されたメッセージの一部またはすべての独立したコピーを受け取ります。 
* メッセージング ソリューションで、追加のインフラストラクチャ コンポーネントを作成しなくても "At-Most-Once" の配信保証をサポートする必要がある場合。
* ソリューションで、バッチ メッセージの発行および使用が必要な場合。

## <a name="compare-storage-queues-and-service-bus-queues"></a>Storage キューと Service Bus キューの比較
次のセクションの表は、キューの機能を論理的にグループ化したものです。 これらを使用すると、Azure Storage キューと Service Bus キューの両方で使用できる機能を一目で比較できます。

## <a name="foundational-capabilities"></a>基本的な機能
このセクションでは、Storage キューと Service Bus キューで提供される基本的なキュー機能の一部を比較します。

| 比較条件 | Storage キュー | Service Bus キュー |
| --- | --- | --- |
| 順序の保証 |**いいえ** <br/><br>詳細については、「[関連情報](#additional-information)」セクションの最初のメモをご覧ください。</br> | **はい - 先入れ先出し法 (FIFO)**<br/><br>([メッセージ セッション](message-sessions.md)を使用) |
| 配信保証 |**At-Least-Once** |**At-Least-Once** (PeekLock 受信モードを使用。 これは既定値です) <br/><br/>**At-Most-Once** (ReceiveAndDelete 受信モードを使用) <br/> <br/> さまざまな[受信モード](service-bus-queues-topics-subscriptions.md#receive-modes)に関する詳細  |
| 分割不可能な操作のサポート |**いいえ** |**はい**<br/><br/> |
| 受信動作 |**非ブロッキング**<br/><br/>(新しいメッセージがない場合はすぐに完了します) |**タイムアウトあり/なしのブロッキング**<br/><br/>(長いポーリングまたは ["Comet 手法"](https://go.microsoft.com/fwlink/?LinkId=613759) を提供します)<br/><br/>**非ブロッキング**<br/><br/>(.NET マネージド API のみを使用) |
| プッシュ型 API |**いいえ** |**はい**<br/><br/>[QueueClient.OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) と [MessageSessionHandler.OnMessage](/dotnet/api/microsoft.servicebus.messaging.messagesessionhandler.onmessage#Microsoft_ServiceBus_Messaging_MessageSessionHandler_OnMessage_Microsoft_ServiceBus_Messaging_MessageSession_Microsoft_ServiceBus_Messaging_BrokeredMessage__) セッション .NET API。 |
| 受信モード |**Peek & Lease** |**Peek & Lock**<br/><br/>**Receive & Delete** |
| 排他アクセス モード |**リース ベース** |**ロック ベース** |
| リース/ロックの期間 |**30 秒 (既定値)**<br/><br/>**7 日間 (最大)** ([UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API を使用してメッセージ リースを更新または変更できます) |**60 秒 (既定値)**<br/><br/>[RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API を使用してメッセージのロックを更新できます。 |
| リース/ロックの粒度 |**メッセージ レベル**<br/><br/>各メッセージには異なるタイムアウト値を設定できます。この値は [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API を使用して、メッセージの処理中に必要に応じて更新できます。 |**キュー レベル**<br/><br/>(各キューのすべてのメッセージに同じロック粒度が適用されますが、[RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) API を使用してロックを更新できます) |
| 一括受信 |**はい**<br/><br/>(メッセージを取得するときに最大 32 のメッセージ数を明示的に指定します) |**はい**<br/><br/>(プレフェッチ プロパティを有効にして暗黙的に行うか、トランザクションを使用して明示的に行います) |
| 一括送信 |**いいえ** |**はい**<br/><br/>(トランザクションまたはクライアント側のバッチ処理を使用) |

### <a name="additional-information"></a>関連情報
* Storage キューのメッセージは一般的に先入れ先出しですが、順番が変わることがあります。 たとえば、メッセージの処理中にクライアント アプリケーションがクラッシュしたために、メッセージの表示タイムアウト期間が過ぎた場合などです。 表示タイムアウトが過ぎると、別の worker がデキューするために、メッセージがキューに再度表示されます。 そのとき、新しく表示可能になったメッセージが再デキューのためにキューに配置されることがあります。
* Service Bus キューで FIFO パターンを保証するには、メッセージング セッションを使用する必要があります。 **Peek & Lock** モードで受信したメッセージの処理中にアプリケーションがクラッシュした場合、キューの受信側は、次にメッセージング セッションを受け取ったときに、メッセージの TTL (time-to-live) 期間が経過した後、失敗したメッセージから開始します。
* Storage キューは、次のような標準的なキューイング シナリオをサポートするように設計されています。
    - アプリケーション コンポーネントを分離してスケーラビリティや耐障害性を向上させる
    - 負荷平準化
    - プロセス ワークフローの構築
* Service Bus セッションのコンテキストでのメッセージ処理に関する不整合は、セッション状態を使用してセッションのメッセージ シーケンスの処理の進捗に関連するアプリケーションの状態を格納することと、受け取ったメッセージの解決に関するトランザクションの使用とセッション状態の更新によって回避できます。 このような一貫性機能は、他のベンダー製品では "*厳密に 1 回の処理*" と呼ばれることがあります。 トランザクション エラーは明らかにメッセージの再配信の原因となるため、この用語は正確には適切ではありません。
* Storage キューでは、開発者とオペレーション チームの双方に対し、キュー、テーブル、BLOB で一貫性のあるプログラミング モデルを提供します。
* Service Bus キューは、1 つのキューのコンテキストでローカル トランザクションをサポートします。
* Service Bus でサポートされている **Receive and Delete** モードを使用すると、メッセージング操作の数 (および関連するコスト) を削減できますが、配信の確実性が低下します。
* Storage キューではメッセージのリースを延長することのできるリースを提供します。 この機能により、ワーカー プロセスでメッセージのリースを短くすることができます。 そのため、ワーカーがクラッシュした場合にすぐに別のワーカーがメッセージを再度処理できます。 また、現在のリース時間より長い処理時間が必要になった場合に、ワーカーがメッセージのリースを延長できます。
* Storage キューでは、メッセージをエンキューまたはデキューするときに表示のタイムアウトを設定できます。 また、実行時にメッセージを別のリース値で更新したり、同じキューのメッセージを別の値で更新したりすることもできます。 Service Bus ロック タイムアウトは、キューのメタデータで定義されます。 ただし、[RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) メソッドを呼び出すことによって、ロックを更新できます。
* Service Bus キューのブロッキング受信操作の最大タイムアウトは 24 日です。 ただし、REST ベースのタイムアウトの最大値は 55 秒です。
* Service Bus でサポートされているクライアント側のバッチ処理を使用すると、キュー クライアントで複数のメッセージをバッチ処理して 1 回の送信操作で処理を完了できます。 バッチ処理は非同期送信操作でのみ使用できます。
* 200 TB が上限の Storage キュー (アカウントを仮想化すればさらに確保可能) や無制限キューのような機能により、SaaS プロバイダーにとって理想的なプラットフォームになります。
* Storage キューでは、柔軟性が高くパフォーマンスに優れた委任アクセス制御メカニズムを提供します。

## <a name="advanced-capabilities"></a>拡張機能
このセクションでは、Storage キューと Service Bus キューで提供される拡張機能を比較します。

| 比較条件 | Storage キュー | Service Bus キュー |
| --- | --- | --- |
| スケジュールされた配信 |**はい** |**はい** |
| 自動的な配信不能レタリング |**いいえ** |**はい** |
| キューの有効期間の増加 |**はい**<br/><br/>(表示のタイムアウトのインプレース更新を使用) |**はい**<br/><br/>(専用の API 関数を使用) |
| 有害なメッセージのサポート |**はい** |**はい** |
| インプレース更新 |**はい** |**はい** |
| サーバー側のトランザクション ログ |**はい** |**いいえ** |
| Storage のメトリック |**はい**<br/><br/>**分単位のメトリック** は、可用性、TPS、API 呼び出し数、エラー数などのリアルタイムのメトリックを提供します。 これらはすべてリアルタイムで、分単位で集計され、運用環境での発生から数分以内に報告されます。 詳細については、「[Storage Analytics Metrics について](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics)」を参照してください。 |**はい**<br/><br/>([GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues) の呼び出しによる一括クエリ) |
| 状態管理 |**いいえ** |**はい**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus)、[Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus)、[Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus)、[Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| メッセージの自動転送 |**いいえ** |**はい** |
| キューの消去機能 |**はい** |**いいえ** |
| メッセージ グループ |**いいえ** |**はい**<br/><br/>(メッセージング セッションを使用) |
| メッセージ グループ単位のアプリケーション状態 |**いいえ** |**はい** |
| 重複検出 |**いいえ** |**はい**<br/><br/>(送信側で構成可能) |
| メッセージ グループの参照 |**いいえ** |**はい** |
| ID によるメッセージ セッションの取得 |**いいえ** |**はい** |

### <a name="additional-information"></a>関連情報
* 両方のキュー テクノロジには、メッセージを後で配信するようにスケジュールできる機能があります。
* キューの自動転送を使用すると、数千のキューのメッセージを 1 つのキューに自動転送して、受信側アプリケーションはそのキューからメッセージを処理することができます。 このメカニズムを使用して、各メッセージ パブリッシャー間でセキュリティの確保、フロー制御、ストレージ分離を実現できます。
* Storage キューでは、メッセージの内容の更新がサポートされています。 この機能を使用すると、状態情報と進行状況の増分更新をメッセージに永続化して、メッセージの処理を最初からではなく前回のチェックポイントから開始することができます。 Service Bus キューでは、メッセージ セッションを使用することで同じ機能を実現できます。 セッションでは、アプリケーションの処理状態を保存および取得できます ([SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) および [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState) を使用)。
* Service Bus キューは[配信不能レタリング](service-bus-dead-letter-queues.md)をサポートします。 これは、次の条件を満たすメッセージを分離するのに役立ちます。
    - 受信側アプリケーションでメッセージを正常に処理できない 
    - 有効期限が切れた Time-to-live (TTL) プロパティが原因で、メッセージが宛先に届かない。 TTL の値は、メッセージがキューに保持される期間を指定します。 Service Bus では、TTL が期限切れになると、メッセージが $DeadLetterQueue という特殊なキューに移動されます。
* Storage キューで "有害な" メッセージを検出するには、アプリケーションでメッセージをデキューするときにメッセージの [DequeueCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount) プロパティを確認します。 **DequeueCount** が一定のしきい値を超えている場合は、そのメッセージをアプリケーション定義の "配信不能" キューに移動します。
* Storage キューでは、キューに対して実行されたすべてのトランザクションの詳細なログとメトリックの集計値を取得できます。 これらのオプションはいずれも、デバッグや、アプリケーションでの Storage キューの使い方を理解するのに役立ちます。 また、アプリケーションのパフォーマンス チューニングやキューの使用コストの削減にも役立ちます。
* Service Bus によってサポートされるメッセージ セッションでは、論理グループに属するメッセージを受信者に関連付けることができます。 これにより、メッセージとそれぞれの受信側の間にセッションのような関係が作成されます。 Service Bus でこの拡張機能を有効にするには、メッセージの [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) プロパティを設定します。 受信側では、特定のセッション ID をリッスンして、指定したセッション ID を共有するメッセージを受信できます。
* Service Bus キューの重複検出機能により、[MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) プロパティの値に基づいて、キューまたはトピックに送信された重複するメッセージが自動的に削除されます。

## <a name="capacity-and-quotas"></a>容量およびクォータ
このセクションでは、[容量および適用可能なクォータ](service-bus-quotas.md)の観点から Storage キューと Service Bus キューを比較します。

| 比較条件 | Storage キュー | Service Bus キュー |
| --- | --- | --- |
| 最大キュー サイズ |**500 TB**<br/><br/>([1 つのストレージ アカウントの容量](../storage/common/storage-introduction.md#queue-storage)に制限) |**1 GB ～ 80 GB**<br/><br/>(キューの作成時と[パーティション分割を有効化](service-bus-partitioning.md)するときに定義します。追加情報セクションをご覧ください) |
| 最大メッセージ サイズ |**64 KB**<br/><br/>(**Base64** エンコードを使用する場合は 48 KB)<br/><br/>Azure では、キューと BLOB を組み合わせることでサイズの大きいメッセージをサポートし、1 つのアイテムに対して最大 200 GB までのメッセージをエンキューできます。 |**256 KB** ～ **1 MB**<br/><br/>(ヘッダーと本文の両方を含む。ヘッダーの最大サイズは 64 KB)。<br/><br/>[サービス レベル](service-bus-premium-messaging.md)に依存します。 |
| メッセージの最大 TTL |**無限** (API バージョン 2017-07-27 以降) |**TimeSpan.Max** |
| キューの最大数 |**無制限** |**10,000**<br/><br/>(サービス名前空間あたり) |
| 同時クライアントの最大数 |**無制限** |**5,000** |

### <a name="additional-information"></a>関連情報
* Service Bus では、キューのサイズが制限されます。 キューの作成時に、キューの最大サイズを指定します。 これは 1 GB から 80 GB の間で指定できます。 キューのサイズがこの制限に達すると、追加の受信メッセージは拒否され、呼び出し元は例外を受け取ります。 Service Bus でのクォータの詳細情報については、「[Service Bus のクォータ](service-bus-quotas.md)」をご覧ください。
* パーティション分割は [Premium レベル](service-bus-premium-messaging.md)ではサポートされていません。 Standard メッセージング レベルでは、Service Bus キューおよびトピックは、1 GB (既定値)、2 GB、3 GB、4 GB、5 GB で作成できます。 パーティション分割が有効な場合、Service Bus は指定されたサイズごとにエンティティの 16 個のコピー (16 個のパーティション) を作成します。 そのため、サイズが 5 GB のキューを作成すると、パーティションが 16 個であるため、最大キュー サイズは 5 * 16 = 80 GB になります。  パーティション分割したキューまたはトピックの最大サイズは、[Azure portal][Azure portal] で確認できます。
* Storage キューでは、内容が XML セーフでないメッセージに対しては **Base64** エンコードを使用する必要があります。 メッセージを **Base64** エンコードを使用する場合、ユーザー ペイロードの上限は 64 KB ではなく 48 KB になります。
* Service Bus キューでは、キューに格納される各メッセージはヘッダーと本文の 2 つの部分で構成されます。 メッセージの合計サイズが、サービス レベルでサポートされる最大メッセージ サイズを超えることはできません。
* クライアントが TCP プロトコルで Service Bus キューと通信する場合は、1 つの Service Bus キューに対するコンカレント接続の最大数が 100 に制限されます。 この数は送信側と受信側で共有されます。 このクォータに達すると、追加の接続要求は拒否され、呼び出し元のコードが例外を受け取ります。 この制限は、REST ベースの API を使用してキューに接続するクライアントには適用されません。
* 1 つの Service Bus Service の名前空間で 10,000 を超える数のキューが必要な場合は、Azure サポート チームに連絡してキューの数を増やすことができます。 Service Bus でキューの数を 10,000 より多くするために、[Azure Portal][Azure portal] を使用して追加の名前空間を作成することもできます。

## <a name="management-and-operations"></a>管理と操作
このセクションでは、Storage キューと Service Bus キューで提供される管理機能を比較します。

| 比較条件 | Storage キュー | Service Bus キュー |
| --- | --- | --- |
| 管理プロトコル |**HTTP/HTTPS 経由の REST** |**HTTPS 経由の REST** |
| ランタイム プロトコル |**HTTP/HTTPS 経由の REST** |**HTTPS 経由の REST**<br/><br/>**AMQP 1.0 Standard (TCP と TLS)** |
| .NET API |**はい**<br/><br/>(.NET Storage Client API) |**はい**<br/><br/>(.NET Service Bus API) |
| ネイティブ C++ |**はい** |**はい** |
| Java API |**はい** |**はい** |
| PHP API |**はい** |**はい** |
| Node.js API |**はい** |**はい** |
| 任意のメタデータのサポート |**はい** |**いいえ** |
| キューの名前付け規則 |**最大 63 文字**<br/><br/>(キューの名前は小文字で指定する必要があります) |**最大 260 文字**<br/><br/>(キューのパスと名前では大文字と小文字は区別されません) |
| キューの長さを取得する機能 |**はい**<br/><br/>(メッセージが TTL を過ぎて期限切れになっても削除されていない場合は概算値となります) |**はい**<br/><br/>(特定の時点の正確な値) |
| Peek 機能 |**はい** |**はい** |

### <a name="additional-information"></a>関連情報
* Storage キューでは、キューの説明に名前と値のペアの形式の任意の属性を適用できます。
* 両方のキュー テクノロジには、メッセージをロックせずに表示できる機能も用意されています。この機能は、キューのエクスプローラー/ブラウザー ツールを実装する際に便利です。
* Service Bus の .NET ブローカー メッセージング API は、全二重 TCP 接続を使用して HTTP 経由の REST より高いパフォーマンスを発揮します。また、AMQP 1.0 標準プロトコルもサポートしています。
* Storage キューの名前は 3 ～ 63 文字の間で指定でき、小文字、数字、ハイフンを使用できます。 詳細については、「[キューおよびメタデータの名前付け](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata)」をご覧ください。
* Service Bus のキュー名は最大 260 文字までの長さで指定でき、名前付け規則の制限は厳しくありません。 Service Bus のキュー名には、文字、数字、ピリオド、ハイフン、アンダースコアを使用できます。

## <a name="authentication-and-authorization"></a>認証と権限承認
このセクションでは、Storage キューと Service Bus キューでサポートされる認証および承認機能について説明します。

| 比較条件 | Storage キュー | Service Bus キュー |
| --- | --- | --- |
| 認証 |**対称キー** |**対称キー** |
| セキュリティ モデル |SAS トークンを介した委任アクセス。 |SAS |
| ID プロバイダー フェデレーション |**いいえ** |**はい** |

### <a name="additional-information"></a>関連情報
* どちらのキュー テクノロジでも、すべての要求を認証する必要があります。 匿名アクセスを使用するパブリック キューはサポートされていません。 [SAS](service-bus-sas.md) を使用すると、書き込み専用 SAS、読み取り専用 SAS、フルアクセス SAS を発行することで、このシナリオに対応できます。
* Storage キューによって提供される認証方式では、対称キーの使用が必要です。 このキーは、SHA-256 アルゴリズムで計算され、**Base64** 文字列としてエンコードされたハッシュベース メッセージ認証コード (HMAC) です。 各プロトコルの詳細情報については、「[Azure ストレージ サービスの認証](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services)」をご覧ください。 Service Bus キューでは、対称キーを使用する類似のモデルをサポートします。 詳細については、「[Service Bus での共有アクセス署名認証](service-bus-sas.md)」をご覧ください。

## <a name="conclusion"></a>まとめ
2 つのテクノロジをより深く理解することにより、使用するキュー テクノロジとその状況について、より多くの十分な情報を得たうえでの決定を行うことができます。 Storage キューと Service Bus キューを使用する状況についての判断は明らかにさまざまな要因に依存します。 それらの要因がアプリケーションとそのアーキテクチャの個々のニーズに大きく依存している場合もあります。 

Storage キューは、次のような理由から選択することをお勧めします。

- お使いのアプリケーションで Microsoft Azure のコア機能が既に使用されている場合 
- サービス間の基本的な通信とメッセージングが必要な場合 
- サイズが 80 GB を超えるキューが必要

Service Bus キューには、次のような多くの高度な機能が用意されています。 したがって、ハイブリッド アプリケーションを構築する場合や、お使いのアプリケーションでこれらの機能が必要な場合は、これを選択することをお勧めします。

- [セッション](message-sessions.md)
- [トランザクション](service-bus-transactions.md)
- [重複検出](duplicate-detection.md)
- [自動的な配信不能レタリング](service-bus-dead-letter-queues.md)
- [永続的な発行とサブスクライブの機能](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) 



## <a name="next-steps"></a>次のステップ
次の記事では、Storage キューや Service Bus キューの使用に関する詳細情報を提供します。

* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [キュー Storage Service を使用する方法](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Service Bus の仲介型メッセージングを使用したパフォーマンス向上のためのベスト プラクティス](service-bus-performance-improvements.md)
* [Azure Service Bus のキューとトピックの概要 (ブログの投稿)](https://www.serverless360.com/blog/azure-service-bus-queues-vs-topics)
* [The Developer's Guide to Service Bus (Service Bus の開発者向けガイド)](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Using the Queuing Service in Azure (Azure でのキュー サービスの使用)](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

