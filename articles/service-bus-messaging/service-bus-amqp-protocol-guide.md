---
title: Azure Service Bus と Event Hubs における AMQP 1.0 プロトコル ガイド | Microsoft Docs
description: Azure Service Bus と Event Hubs で使用されている AMQP 1.0 プロトコルの式と記述に関するガイド
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 2154221ebfe69b659ff83100ed614133e178ccdb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624491"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>Azure Service Bus と Event Hubs における AMQP 1.0 プロトコル ガイド

Advanced Message Queueing Protocol 1.0 は、2 つの当事者間でメッセージを非同期で、安全かつ確実に転送することを目的に標準化されたフレーミングと転送のためのプロトコルです。 Azure Service Bus メッセージングと Azure Event Hubs の主要なプロトコルとなっています。  

AMQP 1.0 は、ミドルウェア ベンダー (Microsoft、Red Hat など) と各種メッセージング ミドルウェア ユーザー (金融サービス業界を代表する JP モルガン・チェースなど) とを結び付ける、あまねく業界の協業の成果として生まれました。 AMQP プロトコルと拡張仕様の技術的な標準化は OASIS が担っており、今や国際標準の ISO/IEC 19494:2014 として正式に認定されるに至っています。 

## <a name="goals"></a>目標

この記事は、AMQP 1.0 メッセージング仕様の核となる概念を、[OASIS AMQP 技術委員会](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp)で策定された拡張機能の仕様と併せて紹介したものです。また、それらの仕様が Azure Service Bus でどのように実装され、構築されているかについても説明します。

この記事の目的は、あらゆるプラットフォームで既存の AMQP 1.0 クライアント スタックを使用している開発者が、AMQP 1.0 を介して Azure Service Bus と連携できるよう支援することです。

広く普及している汎用 AMQP 1.0 スタック ([Apache Qpid Proton](https://qpid.apache.org/proton/index.html)、[AMQP.NET Lite](https://github.com/Azure/amqpnetlite) など) には既に、セッションやリンクなど AMQP 1.0 プロトコルの主要な要素がすべて実装されています。 こうした基礎となる要素はしばしば、上位の API にラップされます。たとえば Apache Proton には実に 2 つの API が用意されています (命令型の Messenger API とリアクション型の Reactor API)。

以降の解説は、AMQP の接続とセッションとリンクの管理、さらにはフレーム転送の処理とフロー制御が、それぞれのスタック (Apache Proton-C など) で処理され、アプリケーションの開発者が気にかけるべき点は、たとえあってもごくわずかであることを想定しています。 たとえば接続するための機能や、何らかの形態の抽象オブジェクトとして *送信側* と *受信側* を作成する機能があって、そこに何らかの形でそれぞれ `send()` 操作と `receive()` 操作が存在するなど、概念上いくつかの基本的な API が存在するものとして話を進めます。

メッセージの読み取りやセッションの管理など、Azure Service Bus の高度な機能に触れる際は、AMQP の観点で説明しますが、これらの機能も、前提となる抽象化された API の上に階層化された擬似的な実装として解説します。

## <a name="what-is-amqp"></a>AMQP とは何か

AMQP は、フレーミングと転送のプロトコルです。 フレーミングとは、ネットワーク接続のいずれかの方向に流れるバイナリ データ ストリームに構造を与えることです。 接続された当事者間で交換される、*フレーム* と呼ばれる個々のデータ ブロックには、この構造によって輪郭が与えられます。 フレームをいつ転送するかや何をもって転送の完了と見なすかについて、通信を行う両方の当事者が共有の認識を持てるようにするのが、転送機能の役割となります。

かつて AMQP の作業部会によって作成されて既に失効している草案版が今も、一部のメッセージ ブローカーで使用されています。しかし作業部会によって最終的に標準化された AMQP 1.0 プロトコルでは、こうした草案版とは異なり、メッセージ ブローカーの存在や、メッセージ ブローカー内のエンティティに対する特定のトポロジについては一切規定されていません。

このプロトコルは、ピア ツー ピアの対称な通信で、キュー エンティティや発行/購読エンティティをサポートするメッセージ ブローカー (Azure Service Bus など) との対話を目的として使用できます。 また、Azure Event Hubs のように、通常のキューと対話パターンの異なるメッセージング インフラストラクチャとの対話にも使用できます。 イベント ハブは、イベントを受け取る際にはキューのような働きをする一方、そこからイベントが読み取られるときには、シリアル ストレージ サービスのような働きをします。テープ ドライブとやや似た動作といってもいいでしょう。 クライアントがデータ ストリームに対するオフセットを選ぶと、そのオフセットを起点として、ストリームの末尾まですべてのイベントがクライアントに配信されます。

AMQP 1.0 プロトコルでは拡張性を意図した設計が採用され、さらなる仕様によって、その機能を拡張できるようになっています。 この点に関して、このドキュメントでは 3 つの拡張仕様を紹介しています。 既存の Https/websocket インフラストラクチャ上の通信では、ネイティブの AMQP TCP ポートを構成することが難しいことがあります｡ バインディング仕様では、Websocket 経由の AMQP を階層化する方法を定義します。 管理上の用途や高度な機能を実現することを目的とし、要求/応答の形式でメッセージング インフラストラクチャとやり取りするために必要な基本的な対話の要素は、AMQP Management 仕様に規定されています。 フェデレーション承認モデルの統合に関して、承認トークンをリンクに関連付けたり、リンクに関連付けられている承認トークンを更新したりする方法については、AMQP の CBS (Claims Based Security) 仕様で規定されています。

## <a name="basic-amqp-scenarios"></a>基本的な AMQP のシナリオ

このセクションでは、Azure Service Bus での AMQP 1.0 の基本的な処理について説明します。接続やセッション、リンクを作成したり、Service Bus の各種エンティティ (キュー、トピック、サブスクリプションなど) との間でメッセージをやり取りしたりする方法を見ていきましょう。

AMQP の動作について最も権威のある情報源は [AMQP 1.0 仕様](http://docs.oasis-open.org/amqp/core/v1.0/amqp-core-overview-v1.0.html)です。しかし仕様の目的は、実装上の指針を厳密に記述することであり、プロトコルについてわかりやすく解説することではありません。 このセクションでは、Service Bus での AMQP 1.0 の使われ方を理解するうえで最低限必要な用語のみを紹介することに重点を置いています。 AMQP について大局的に扱った入門情報が必要な場合や、AMQP 1.0 についての広範な解説が必要である場合は、[こちらのビデオ コース][this video course]をご覧ください。

### <a name="connections-and-sessions"></a>接続とセッション

AMQP は、"*コンテナー*" という通信プログラムを呼び出します。コンテナーには、その内部の通信エンティティである "*ノード*" が存在します。 キューは、そうしたノードの 1 つです。 AMQP は、多重化に対応しているため、1 本の接続をノード間の複数の通信経路で使用することが可能です。たとえばアプリケーション クライアントは、2 つのキューのうち、一方からは受信しながら、同時に同じネットワーク接続上でもう一方のキューに対して送信を行うことができます。

![コンテナー間のセッションと接続を示す図。][1]

そのためネットワーク接続はコンテナーに固定されます。 ネットワーク接続は、クライアント ロールのコンテナーによって開始されます。クライアント ロールのコンテナーが、受信側ロールのコンテナーに対して送信 TCP ソケット接続を確立し、受信側ロールは受信 TCP 接続を待機してそれを受け入れます。 接続ハンドシェイクには、プロトコル バージョンのネゴシエーション、トランスポート レベルのセキュリティ (TLS/SSL) の使用に関する宣言 (またはネゴシエーション)、SASL に基づく接続スコープでの認証/承認ハンドシェイクが含まれます。

Azure Service Bus では、常時 TLS の使用が必須となります。 Azure Service Bus は、TCP ポート 5671 での接続に対応しています。これによって AMQP プロトコル ハンドシェイクに移行する前に、TCP 接続が TLS に重ね合わされます。また、Azure Service Bus は、TCP ポート 5672 での接続に対応しています。これによってサーバーは、AMQP に規定されたモデルに従って、TLS に必要な接続の更新を速やかに実施することができます。 AMQP WebSocket のバインディングでは、TCP ポート 443 上にトンネルが作成されます。このトンネルが AMQP の 5671 接続に相当します。

Service Bus では、接続と TLS のセットアップ後、SASL の機構に関して次の 2 つの選択肢が用意されています。

* SASL PLAIN: ユーザー名とパスワードの資格情報をサーバーに渡す目的で一般的に使用されます。 Service Bus はアカウントを持ちませんが、[Shared Access Security の規則](service-bus-sas.md)が指定されます。Shared Access Security の規則によって権限は付与され、また、この規則にキーが関連付けられます。 規則の名前はユーザー名として使用され、キー (base64 エンコード テキスト) はパスワードとして使用されます。 選択した規則に関連付けられている権限によって、対象の接続上で許可される操作が管理されます。
* SASL ANONYMOUS: 後述の CBS (Claims Based Security) モデルの使用をクライアントが希望しているとき、SASL の承認をバイパスする場合に使用します。 この方法を選択した場合、クライアント接続を短時間、匿名で確立することができます。その間にクライアントが対話できるのは CBS エンドポイントのみです。また、CBS ハンドシェイクも、その間に完了する必要があります。

トランスポート接続が確立された後、コンテナーはそれぞれ、処理する最大フレーム サイズを宣言し、その接続でアクティビティが存在しなければ、アイドル タイムアウトの経過後、一方的に接続を解除します。

またコンテナーは、同時にサポートされるチャネルの数も宣言します。 チャネルは、接続の上に形成される内から外に向かう一方向の仮想的な転送経路です。 セッションは、相互接続された個々のコンテナーからチャネルを取得して、双方向の通信経路を形成します。

セッションには、ウィンドウ型のフロー制御モデルが採用されています。つまりセッションの作成時に、それぞれの当事者が、その受信ウィンドウに受け入れるフレームの数を宣言します。 通信の当事者がフレームを交換する際、転送済みのフレームによってウィンドウが埋められていき、それが一杯になると、ウィンドウが *flow パフォーマティブ* を使って拡張されるか、リセットされるまで転送は中止されます (*パフォーマティブ* は、2 つの当事者間でやり取りされるプロトコル レベルのジェスチャに使用される AMQP 用語)。

基本的にこのウィンドウ型のモデルは、TCP のウィンドウ型フロー制御の概念と似ていますが、ソケット内のセッション レベルの概念である点が異なります。 このプロトコルの概念は、複数の同時セッションを可能にするものであり、ちょうど高速道路の追い越し車線のように、優先度の高いトラフィックが、抑制された通常のトラフィックを追い越すことができるようになっています。

現在、Azure Service Bus では各接続につき厳密に 1 つのセッションが使用されます。 Service Bus Standard の場合､Service Bus の最大フレーム サイズは 262,144 バイト (256 KB) です｡ Service Bus Premium および Event Hubs の場合は、これが 1,048,576 (1 MB) になります。 セッション レベルで特定のスロットル ウィンドウを Service Bus が強制的に適用することはありませんが、リンク レベルのフロー制御の一環として Service Bus は、ウィンドウを定期的にリセットします ([次のセクション](#links)を参照)。

接続、チャネル、セッションは一時的にしか存在しません。 根底の接続がダウンした場合、接続、TLS トンネル、SASL 承認コンテキスト、セッションを再度確立する必要があります。

### <a name="amqp-outbound-port-requirements"></a>AMQP 送信ポートの要件

TCP 経由で AMQP 接続を使用するクライアントでは、ローカル ファイアウォールでポート 5671 と 5672 を開く必要があります。 これらのポートと共に、[EnableLinkRedirect](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.enablelinkredirect) 機能が有効になっている場合は、追加のポートを開く必要がある場合があります。 `EnableLinkRedirect` は、メッセージの受信中に 1 ホップをスキップし、スループットを向上させることができるようにする新しいメッセージング機能です。 クライアントは、次の図に示すように、ポート範囲 104XX 経由でバックエンド サービスとの直接通信を開始します。 

![宛先ポートの一覧][4]

これらのポートがファイアウォールによってブロックされている場合、.NET クライアントは、SocketException ("アクセス許可で禁じられた方法でソケットにアクセスしようとしました") で失敗します。 この機能を無効にするには、接続文字列で `EnableAmqpLinkRedirect=false` を設定します。これにより、クライアントはポート 5671 を介してリモート サービスと通信するように強制されます。


### <a name="links"></a>リンク

AMQP では、メッセージがリンクを介して転送されます。 リンクは、メッセージを一方向に転送することを目的としてセッション上に形成される通信経路です。転送ステータスのネゴシエーションは、接続された当事者どうしがリンクを介し、双方向に実行します。

![2 つのコンテナー間のリンク接続を結ぶセッションを示すスクリーンショット。][2]

リンクは、送信側または受信側のどちらのコンテナーからでも随時、既存のセッション上に作成できます。これは、転送とその経路の開始が、ソケット接続を作成した当事者にのみ許可される HTTP や MQTT などの他の多くのプロトコルと AMQP が異なる点です。

リンクを開始したコンテナーは、もう一方のコンテナーにリンクを受け入れるよう要求すると共に、送信側と受信側のどちらかのロールを選択します。 したがって、どちらのコンテナーからでも一方向または双方向の通信経路の作成を開始することができます。この双方向の通信経路がリンクのペアとしてモデル化されています。

リンクは名前を持ち、ノードに関連付けられます。 冒頭で述べたようにノードは、コンテナー内で通信を行うエンティティです。

Service Bus におけるノードは、キューやトピック、サブスクリプション (またはキューやサブスクリプションの配信不能サブキュー) と一対一で対応します。 そのため AMQP で使用されるノード名は、Service Bus の名前空間内のエンティティの相対名になります。 キューに `myqueue` という名前を付けた場合、その名前は AMQP のノード名にもなります｡ トピックのサブスクリプションは HTTP API の命名規則を踏襲し、"subscriptions" というリソース コレクションに分類されます。したがって、サブスクリプションが **sub** で、トピックが **mytopic** である場合、対応する AMQP ノード名は **mytopic/subscriptions/sub** となります。

ローカル ノード名は、接続する側のクライアントがリンクを作成するときにも必要となります。つまり Service Bus は、こうしたノード名について関与せず、そもそもノード名を解釈しません。 AMQP 1.0 クライアント スタックが、そのクライアントのスコープにおいてこれらの短期ノード名に重複がないことを特定のスキームを使って保証するのが一般的です。

### <a name="transfers"></a>転送

リンクが確立されると、そのリンク上でメッセージを転送できる状態となります。 AMQP では、送信側からリンクを介して受信側にメッセージを移動する明示的なプロトコル ジェスチャ (*transfer* パフォーマティブ) によって転送が実行されます。 転送は、その結果についての共通認識が両者の間に確立されたことを意味する "settled (解決済み)" の状態になったときに完了します。

![送信側と受信側の間でのメッセージの転送と、それによって生じる処理を示す図。][3]

最も単純なケースとして、送信側はメッセージを "未解決" の状態で送信することができます。これはクライアントが転送の結果に関心がなく、受信側からは、その操作の結果についてのフィードバックが一切得られないことを意味します。 このモードは、Service Bus により AMQP プロトコル レベルでサポートされますが、いずれのクライアント API にも公開されていません。

通常のケースでは、未解決の状態で送信されたメッセージに対して、受信側が *disposition* パフォーマティブを使用して受理または拒否を表明します。 拒否されるのは、受信側が何らかの理由でメッセージを受け入れることができないときです。拒否されたメッセージには、その理由についての情報 (AMQP によって定義されたエラーの構造体) が格納されます。 Service Bus の内部的なエラーが原因でメッセージが拒否された場合、追加情報が構造体に格納されてサービスから返されます。サポート リクエストを申請する場合は、この構造体を使用して、診断の手掛かりをサポート担当者に提出することができます。 エラーについては、後ほど詳しく説明します。

特殊な拒否の形態として *released* 状態があります。これは、その転送に反対する技術的な理由が受信側にあるわけではないものの、転送を解決することにも関心がないことを意味します。 たとえば、Service Bus クライアントにメッセージが配信されたとき、メッセージの配信そのものに問題があるわけではないものの、そのメッセージを処理することによって発生する作業を実行できないために、そのメッセージを "破棄" することをクライアントが選ぶケースです。 この状態のバリエーションとして *modified* があります。これは、released 状態のメッセージへの変更を許可するものです。 現在のところ、Service Bus ではこの状態は使用されていません。

AMQP 1.0 仕様では、disposition 状態としてさらに *received* が規定されています。特にリンクの復旧処理に役立てられます。 接続とセッションが失われた場合、リンクの復旧によって、接続とセッションを新たに作成し、その上にリンクの状態と保留中の配信を再構築することができます。

Service Bus は、リンクの復旧をサポートしていません。クライアントと Service Bus との接続が喪失し、未解決のメッセージ転送が保留状態となった場合、そのメッセージ転送は失われます。この場合、クライアントは再度接続してリンクを確立し直し、転送を再試行する必要があります。

そのようなこともあって、Service Bus と Event Hubs は "At Least Once (1 回以上)" 転送をサポートしています｡こうして､送信側は、メッセージが保存され受け付けられたことを確信することができます。ただし､AMQP レベル (メッセージ転送の重複を回避するために、リンクを復旧し、配信状態をネゴシエートし続ける) での "Exactly Once (1 回限り)" 転送はサポートされていません。

送信の重複のリスクを回避する手段として、Service Bus は、キューやトピックに対するオプション機能として重複検出をサポートしています。 重複検出では、ユーザー定義の時間内に受信したすべてのメッセージの ID が記録され、その時間内に同じ ID で送信されたメッセージはすべて警告なしで削除されます。

### <a name="flow-control"></a>フロー制御

これまで説明してきたセッション レベルのフロー制御モデルに加え、各リンクには独自のフロー制御モデルが存在します。 セッション レベルのフロー制御は、一度に処理すべきフレーム数が多くなりすぎないようコンテナーを保護するものです。これに対し、リンク レベルのフロー制御では、そのリンクから受け取るメッセージの数とそのタイミングが、アプリケーションの管理下に置かれます。

![送信元、送信先、送信元ポート、宛先ポート、およびプロトコル名を示すログのスクリーンショット。 宛先ポート 10401 (0x28 A 1) の最初の行が黒で囲まれています。][4]

リンク上で転送が行われるのは、送信側に十分な *リンク クレジット* があるときだけです。 リンク クレジットは、受信側が *flow* パフォーマティブ (適用対象はリンク) を使って設定するカウンターです。 送信側は、自身にリンク クレジットが割り当てられているとき、メッセージを配信することでそのクレジットを消費します。 メッセージを配信するたびに、残りのリンク クレジットが 1 つ減らされます。 リンク クレジットを使い果たすと、配信は停止します。

Service Bus は受信側ロールになるとすぐ、メッセージを直ちに送信できるよう送信側に対して十分なリンク クレジットを与えます。 リンク クレジットが使用される過程で、Service Bus は送信側に対して、ときどき *flow* パフォーマティブを送信し、リンク クレジットの残数を更新します。

送信側ロールの Service Bus はメッセージを送信し、未使用のリンク クレジットを消費します。

API レベルでの "receive" 呼び出しは、*flow* パフォーマティブに変換されて、クライアントから Service Bus に送信されます。Service Bus は、ロックされていない最初のメッセージをキューから取り出し、ロックして転送することによって、そのクレジットを消費します。 すぐに配信できるメッセージが存在しない場合、その特定のエンティティとの間で確立されたリンクごとの未使用クレジットが到着順に記録されます。配信できるメッセージが到着して、未使用クレジットを使用できる状態になると、メッセージはロックされて転送されます。

メッセージに対するロックは、その転送が *accepted*、*rejected*、*released* のいずれかの状態に解決されると解除されます。 終端の状態が *accepted* であるとき、メッセージは Service Bus から削除されます。 転送の状態がそれ以外になった場合、メッセージは Service Bus に残り、次の受信者に配信されます。 たび重なる reject または release でエンティティに割り当てられた最大配信カウントに達すると、メッセージは、Service Bus によって自動的にそのエンティティの配信不能キューに移動されます。

現時点の Service Bus API にそのようなオプションは直接公開されていませんが、それよりも低レベルの AMQP プロトコル クライアントでは、リンク クレジット モデルを使用すれば、receive 要求ごとに 1 単位のクレジットを発行する "プル式" の対話を "プッシュ式" のモデルに変えることができます。つまり、リンク クレジットを大量に発行しておき、メッセージが利用可能になったときに、それ以上の対話操作を行わなくてもそれらのメッセージを受信できるようなモデルを実現できます。 プッシュは、[MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) プロパティまたは [MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) プロパティの設定を通じてサポートされます。 これらの設定が 0 以外であるとき、AMQP クライアントでリンク クレジットとして使用されます。

このとき、エンティティ内のメッセージに対するロックの有効期限は、メッセージが通信回線上に送り出されたときではなく、エンティティからメッセージを取得したときを起点として計時されることに注意してください。 メッセージを受信する用意ができていることをクライアントがリンク クレジットを発行することによって表明したときは常に、当然そのネットワークを介して能動的にメッセージをプルしようとしていること、またそれらを処理する準備が整っているものと解釈されます。 それ以外の場合、メッセージが配信される前に、メッセージに対するロックの有効期限が切れます。 リンク クレジットによるフロー制御は、送り出された有効なメッセージを受信側がすぐに処理できることを前提としています。

以降のセクションでは、各種 API 操作におけるパフォーマティブの流れを大まかに説明します。 セクションごとに異なる論理操作について説明しています。 そうした対話操作の中には、必要になったときにしか実行されない "緩慢" な処理もあります。 メッセージの送信側を作成しても、最初のメッセージが送信または要求されるまで、ネットワークの対話が実行されない場合があります。

次の表の矢印は、パフォーマティブのフロー方向を示しています。

#### <a name="create-message-receiver"></a>メッセージの受信側の作成

| Client | Service Bus |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**receiver**,<br/>source={entity name},<br/>target={client link ID}<br/>) |クライアントがエンティティに受信側として接続 |
| Service Bus がそのリンクの終端を接続して応答 |<-- attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**sender**,<br/>source={entity name},<br/>target={client link ID}<br/>) |

#### <a name="create-message-sender"></a>メッセージの送信側の作成

| Client | Service Bus |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**sender**,<br/>source={client link ID},<br/>target={entity name}<br/>) |アクションなし |
| アクションなし |<-- attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**receiver**,<br/>source={client link ID},<br/>target={entity name}<br/>) |

#### <a name="create-message-sender-error"></a>メッセージの送信側の作成 (エラー)

| Client | Service Bus |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**sender**,<br/>source={client link ID},<br/>target={entity name}<br/>) |アクションなし |
| アクションなし |<-- attach(<br/>name={link name},<br/>handle={numeric handle},<br/>role=**receiver**,<br/>source=null,<br/>target=null<br/>)<br/><br/><-- detach(<br/>handle={numeric handle},<br/>closed=**true**,<br/>error={error info}<br/>) |

#### <a name="close-message-receiversender"></a>メッセージ受信側/送信側のクローズ

| Client | Service Bus |
| --- | --- |
| --> detach(<br/>handle={numeric handle},<br/>closed=**true**<br/>) |アクションなし |
| アクションなし |<-- detach(<br/>handle={numeric handle},<br/>closed=**true**<br/>) |

#### <a name="send-success"></a>送信 (成功)

| Client | Service Bus |
| --- | --- |
| --> transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,,more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |アクションなし |
| アクションなし |<-- disposition(<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID},<br/>settled=**true**,<br/>state=**accepted**<br/>) |

#### <a name="send-error"></a>送信 (エラー)

| Client | Service Bus |
| --- | --- |
| --> transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,,more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |アクションなし |
| アクションなし |<-- disposition(<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID},<br/>settled=**true**,<br/>state=**rejected**(<br/>error={error info}<br/>)<br/>) |

#### <a name="receive"></a>受信

| Client | Service Bus |
| --- | --- |
| --> flow(<br/>link-credit=1<br/>) |アクションなし |
| アクションなし |< transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| --> disposition(<br/>role=**receiver**,<br/>first={delivery ID},<br/>last={delivery ID},<br/>settled=**true**,<br/>state=**accepted**<br/>) |アクションなし |

#### <a name="multi-message-receive"></a>複数メッセージの受信

| Client | Service Bus |
| --- | --- |
| --> flow(<br/>link-credit=3<br/>) |アクションなし |
| アクションなし |< transfer(<br/>delivery-id={numeric handle},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| アクションなし |< transfer(<br/>delivery-id={numeric handle+1},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| アクションなし |< transfer(<br/>delivery-id={numeric handle+2},<br/>delivery-tag={binary handle},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| --> disposition(<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID+2},<br/>settled=**true**,<br/>state=**accepted**<br/>) |アクションなし |

### <a name="messages"></a>メッセージ

以降のセクションでは、標準 AMQP メッセージ セクションから、Service Bus でどのプロパティが使用され、Service Bus API セットに対してどのように対応付けられるかを説明します。

アプリケーションで定義する必要があるプロパティはすべて、AMQP の `application-properties` マップにマッピングされる必要があります。

#### <a name="header"></a>header

| フィールド名 | 使用法 | API 名 |
| --- | --- | --- |
| durable |- |- |
| priority |- |- |
| ttl |このメッセージの有効期限 |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| first-acquirer |- |- |
| delivery-count |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="properties"></a>properties

| フィールド名 | 使用法 | API 名 |
| --- | --- | --- |
| message-id |このメッセージに対するアプリケーション定義の自由形式 ID。 重複検出に使用されます。 |[MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| user-id |アプリケーション定義のユーザー ID。Service Bus では解釈されません。 |Service Bus API を介してアクセスすることはできません。 |
| to |アプリケーション定義の送信先 ID。Service Bus では解釈されません。 |[To](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| subject |アプリケーション定義のメッセージ用途 ID。Service Bus では解釈されません。 |[Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| reply-to |アプリケーション定義の応答パス ID。Service Bus では解釈されません。 |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| correlation-id |アプリケーション定義の相関関係 ID。Service Bus では解釈されません。 |[CorrelationId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| content-type |アプリケーション定義の本文コンテンツ タイプ ID。Service Bus では解釈されません。 |[ContentType](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| content-encoding |アプリケーション定義の本文コンテンツ エンコーディング ID。Service Bus では解釈されません。 |Service Bus API を介してアクセスすることはできません。 |
| absolute-expiry-time |メッセージの有効期限が切れる絶対時刻を宣言します。 入力時には無視され (ヘッダーの TTL が優先され)、出力時に強制力を持ちます。 |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| creation-time |メッセージが作成された時刻を宣言します。 Service Bus では使用されません。 |Service Bus API を介してアクセスすることはできません。 |
| group-id |関連する一連のメッセージに対するアプリケーション定義の ID。 Service Bus のセッションに使用されます。 |[SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| group-sequence |セッション内のメッセージの相対シーケンス番号を識別するカウンター。 Service Bus では無視されます。 |Service Bus API を介してアクセスすることはできません。 |
| reply-to-group-id |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>メッセージの注釈

AMQP メッセージ プロパティの一部ではなく、かつ、メッセージ上で `MessageAnnotations` として渡される他の Service Bus メッセージはほとんどありません。

| 注釈マップ キー | 使用法 | API 名 |
| --- | --- | --- |
| x-opt-scheduled-enqueue-time | メッセージがエンティティ上に出現する時刻を宣言します。 |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc) |
| x-opt-partition-key | メッセージを受信するパーティションを指示する、アプリケーションで定義されたキー。 | [PartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey) |
| x-opt-via-partition-key | 転送キュー経由でメッセージを送信するためにトランザクションが使用される場合の、アプリケーションで定義されたパーティション キーの値。 | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey) |
| x-opt-enqueued-time | メッセージをエンキューする実際の時刻を表す、サービスで定義された UTC 時刻。 インポート時には無視されます。 | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) |
| x-opt-sequence-number | メッセージに割り当てられる、サービスで定義された一意の番号。 | [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) |
| x-opt-offset | サービスで定義された、エンキューされるメッセージのシーケンス番号。 | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber) |
| x-opt-locked-until | サービスで定義される。 メッセージがキュー/サブスクリプションでロックされるまでの日時。 | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc) |
| x-opt-deadletter-source | サービスで定義される。 配信不能キューからメッセージが受信された場合の、元のメッセージのソース。 | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource) |

### <a name="transaction-capability"></a>トランザクション機能

トランザクションにより、複数の操作が 1 つの実行スコープにグループ化されます。 性質上、このようなトランザクションによって、操作の特定のグループに属する操作がすべて成功するか、すべて失敗するかのいずれかになる必要があります。
操作は、識別子 `txn-id` によってグループ化されます。

トランザクション操作の場合、クライアントは、グループ化する必要がある操作を制御する `transaction controller` として機能します｡ Service Bus サービスは、`transactional resource` として機能し、`transaction controller` による要求に従って作業を実行します。

クライアントとサービスは、クライアントによって確立された `control link` 経由で通信します。 `declare` および `discharge` メッセージは、トランザクションを個別に割り当てて完了するために、制御リンク経由でコントローラーから送信されます (トランザクション作業の区別を表しているわけではありません)。 実際の送受信は、このリンク上では実行されません。 要求された各トランザクション操作は、目的の `txn-id` によって明示的に識別されるため、接続上の任意のリンクで実行される可能性があります。 作成した未送信のトランザクションが存在するときに、制御リンクが閉じられると、このようなトランザクションはすべて、ただちにローグバックされ、これらに対してさらにトランザクション作業の実行を試行した場合は、エラーが発生します。 制御リンク上のメッセージが事前に確定されることはありません。

どの接続でも、独自の制御リンクを開始して、トランザクションを開始および終了できる必要があります。 サービスは、`coordinator` として機能する専用のターゲットを定義します。 クライアント/コントローラーは、このターゲットへの制御リンクを確立します。 制御リンクは、エンティティの境界の外にあります。つまり、複数のエンティティに対するトランザクションの開始と送信に、同じ制御リンクを使用できます。

#### <a name="starting-a-transaction"></a>トランザクションの開始

トランザクションの作業を開始するには、 コントローラーがコーディネータから `txn-id` を取得する必要があります。 `declare` 型のメッセージを送信することで、これを行います。 宣言が成功すると、コーディネーターは処理出力で応答し、それを通じて、割り当てられた `txn-id` が伝えられます。

| クライアント (コント ローラー) | Direction | Service Bus (コーディネーター) |
| :--- | :---: | :--- |
| attach(<br/>name={link name},<br/>... ,<br/>role=**sender**,<br/>target=**Coordinator**<br/>) | ------> |  |
|  | <------ | attach(<br/>name={link name},<br/>... ,<br/>target=Coordinator()<br/>) |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (**Declare()** )}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=**Declared**(<br/>**txn-id**={transaction ID}<br/>))|

#### <a name="discharging-a-transaction"></a>トランザクションの送信

コントローラーは、`discharge` メッセージをコーディネーターに送信することで、トランザクション作業を完結します。 コントローラーは、送信の本文に `fail` フラグを設定することでトランザクション作業をコミットまたはロールバックしようとしていることを示します。 コーディネーターが送信を完了できない場合、`transaction-error` を伝送する次のような出力と共にメッセージが拒否されます。

> 注: fail=true はトランザクションのロールバックを示し、fail=false はコミットを示します。

| クライアント (コント ローラー) | Direction | Service Bus (コーディネーター) |
| :--- | :---: | :--- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={transaction ID}<br/>))|
| | . . . <br/>トランザクション作業<br/>(別のリンク上で)<br/> . . . |
| transfer(<br/>delivery-id=57, ...)<br/>{ AmqpValue (<br/>**Discharge(txn-id=0,<br/>fail=false)** )}| ------> |  |
| | <------ | disposition( <br/> first=57, last=57, <br/>state=**Accepted()** )|

#### <a name="sending-a-message-in-a-transaction"></a>トランザクションでのメッセージの送信

すべてのトランザクション作業は、txn-id を伝送するトランザクションの配信ステータス `transactional-state` を使って行われます。メッセージを送信する場合は、トランザクションのステータスは、メッセージの転送フレームによって伝送されます。 

| クライアント (コント ローラー) | Direction | Service Bus (コーディネーター) |
| :--- | :---: | :--- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={transaction ID}<br/>))|
| transfer(<br/>handle=1,<br/>delivery-id=1, <br/>**state=<br/>TransactionalState(<br/>txn-id=0)** )<br/>{ payload }| ------> |  |
| | <------ | disposition( <br/> first=1, last=1, <br/>state=**TransactionalState(<br/>txn-id=0,<br/>outcome=Accepted()** ))|

#### <a name="disposing-a-message-in-a-transaction"></a>トランザクションでのメッセージの処理

メッセージの処理には、`Complete` / `Abandon` / `DeadLetter` / `Defer` のような操作が含まれています。 トランザクションの中でこれらの操作を実行するには、disposition を使って `transactional-state` を渡します。

| クライアント (コント ローラー) | Direction | Service Bus (コーディネーター) |
| :--- | :---: | :--- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={transaction ID}<br/>))|
| | <------ |transfer(<br/>handle=2,<br/>delivery-id=11, <br/>state=null)<br/>{ payload }|  
| disposition( <br/> first=11, last=11, <br/>state=**TransactionalState(<br/>txn-id=0,<br/>outcome=Accepted()** ))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Advanced Service Bus の機能

このセクションでは、現在 AMQP の OASIS 技術委員会で策定が進められている AMQP の拡張機能の草案に基づく Azure Service Bus の高度な機能について取り上げます。 Service Bus には、最新バージョンの草案が実装されています。変更は、草案が標準ステータスになった時点で採用される見込みです。

> [!NOTE]
> Service Bus Messaging の高度な操作は、要求/応答パターンを介してサポートされます。 これらの操作の詳細は、「[AMQP 1.0 in Service Bus: request/response-based operations (Service Bus の AMQP 1.0: 要求応答ベースの操作)](service-bus-amqp-request-response.md)」の記事に説明されています。
> 
> 

### <a name="amqp-management"></a>AMQP Management

草案段階の拡張機能として、この記事ではまず AMQP Management 仕様について説明します。 この仕様には、メッセージング インフラストラクチャに対して AMQP を介して管理操作を行うための、AMQP プロトコル上に階層化された一連のプロトコルが定義されています。 仕様で定義されているのは、*create*、*read*、*update*、*delete* など、メッセージング インフラストラクチャ内のエンティティを管理するための一般的な操作と一連のクエリ操作です。

これらのジェスチャにはいずれも、クライアントとメッセージング インフラストラクチャとの間で要求/応答の対話を伴います。そのため、この仕様には、その対話パターンを AMQP 上にモデル化する方法 (つまりクライアントがメッセージング インフラストラクチャに接続し、セッションを開始した後、リンクのペアを作成する) が定義されています。 クライアントは、一方のリンクでは送信側として機能し、もう一方のリンクでは受信側として機能します。こうして形成された一対のリンクが双方向チャネルとしての役割を果たします。

| 論理操作 | Client | Service Bus |
| --- | --- | --- |
| 要求の応答経路を作成 |--> attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**sender**,<br/>source=**null**,<br/>target=”myentity/$management”<br/>) |アクションなし |
| 要求の応答経路を作成 |アクションなし |\<-- attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**receiver**,<br/>source=null,<br/>target=”myentity”<br/>) |
| 要求の応答経路を作成 |--> attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**receiver**,<br/>source=”myentity/$management”,<br/>target=”myclient$id”<br/>) | |
| 要求の応答経路を作成 |アクションなし |\<-- attach(<br/>name={*link name*},<br/>handle={*numeric handle*},<br/>role=**sender**,<br/>source=”myentity”,<br/>target=”myclient$id”<br/>) |

リンクのペアが作成されていれば、要求/応答の実装は簡単です。つまり要求とはメッセージであり、そのパターンを認識するメッセージング インフラストラクチャ内のエンティティに送信されます。 その要求メッセージでは、*properties* セクションの *reply-to* フィールドが、応答の配信に使用されるリンクの *target* ID に設定されます。 要求を処理する側のエンティティは、その要求を処理した後、指定された *reply-to* ID と一致する *target* ID を持ったリンクを介して応答を配信します。

当然このパターンにはクライアント コンテナーが必要です。また、応答の宛先としてクライアントが生成する ID は、すべてのクライアントを通じて一意で、なおかつセキュリティ上の理由から推測されにくいことが条件となります。

管理プロトコルやそれと同じパターンを使用するすべてのプロトコルのメッセージ交換は、アプリケーション レベルで実行され、新たに AMQP プロトコル レベルのジェスチャは定義されていません。 これは意図的な措置です。こうした拡張機能と仕様に準拠している AMQP 1.0 スタックの利点をアプリケーションがそのまま活かせるようになっています。

Service Bus には現在、管理仕様の核となる機能が実装されていませんが、管理仕様によって定義されている要求/応答パターンは、CBS (Claims Based Security) 機能および､以降のセクションで取り上げる高度な機能のほぼすべてで基礎となるものです。

### <a name="claims-based-authorization"></a>Claims-Based Authorization

AMQP Claims-Based-Authorization (CBS) 仕様草案は、管理仕様の要求/応答パターンをベースとし、フェデレーション方式のセキュリティ トークンを AMQP で使用するための一般的なモデルを規定しています。

冒頭で説明した AMQP の既定のセキュリティ モデルは SASL をベースとしており、AMQP 接続ハンドシェイクと統合されています。 SASL を使用することの利点は、SASL に備わっている拡張可能なモデルです。そのモデルにあらかじめ定義されている一連のメカニズムを、SASL の形式に正しく対応した任意のプロトコルから利用することができます。 そうしたメカニズムの代表的な例としては、"PLAIN" (ユーザー名とパスワードを転送するための機構)、"EXTERNAL" (TLS レベルのセキュリティにバインドするための機構)、"ANONYMOUS" (明示的な認証/承認が存在しないことを示すための機構) のほか、認証/承認の資格情報やトークンを受け渡しするためのさまざまな機構が挙げられます。

AMQP への SASL の統合には、2 つの欠点があります。

* すべての資格情報とトークンの適用範囲が、対象となる接続に限定される。 メッセージング インフラストラクチャでは、エンティティごとに差別化されたアクセス制御を提供する必要がある場合があります。たとえばトークンのベアラーに、キュー A に対する送信を許可しつつ、キュー B への送信を禁止するケースを考えてみましょう。承認コンテキストは接続に固定されるため、単一の接続を使用しながら、キュー A とキュー B に異なるアクセス トークンを使用することはできません。
* 一般にアクセス トークンは有効期間が限られている。 ユーザーは、定期的にトークンを取得し直す必要があります。ユーザーのアクセス権が変更されている場合には、新しいトークンの発行を拒否する余地がトークンの発行者に与えられなければなりません。 AMQP の接続は、長時間にわたって持続する場合があります。 SASL モデルでは、トークンを設定する機会が接続時にしか用意されていません。つまり、メッセージング インフラストラクチャは、トークンの有効期限が切れたときにクライアントとの接続を切断するか、または、その間にアクセス権が失効されたクライアントとの接続をリスクを承知で維持するか、のどちらかを選ぶ必要があります。

Service Bus によって実装される AMQP CBS 仕様により、これらの問題の両方に対する洗練された回避策が有効になります。これによりクライアントは、各ノードにアクセス トークンを関連付け、メッセージ フローを中断することなく、期限切れ前にそれらのトークンを更新することができます。

CBS には、メッセージング インフラストラクチャによって提供される *$cbs* という名前の仮想管理ノードが定義されています。 トークンの受理は、この管理ノードがメッセージング インフラストラクチャ内の他のノードに代わって行います。

このプロトコル ジェスチャは、管理仕様で規定されている要求/応答のやり取りです。 つまりクライアントは、 *$cbs* ノードとの間に 2 本のリンクを確立し、送信リンクで要求を渡した後、受信リンクで応答を待機します。

要求メッセージには、次のアプリケーション プロパティがあります。

| Key | 省略可能 | 値の型 | 値の内容 |
| --- | --- | --- | --- |
| operation |いいえ |string |**put-token** |
| type |いいえ |string |格納されるトークンの種類。 |
| name |いいえ |string |発行先 (トークンの適用先)。 |
| expiration |はい |timestamp |トークンの有効期限。 |

トークンの関連付けの対象となるエンティティは、*name* プロパティによって識別されます。 Service Bus では、キュー (またはトピック/サブスクリプション) のパスになります。 トークンの種類は、*type* プロパティによって識別されます。

| トークンの種類 | トークンの説明 | 本文の型 | Notes |
| --- | --- | --- | --- |
| amqp:jwt |JSON Web トークン (JWT) |AMQP 値 (string) |まだ使用できません。 |
| amqp:swt |Simple Web Token (SWT) |AMQP 値 (string) |AAD/ACS によって発行された SWT トークンでのみサポートされます。 |
| servicebus.windows.net:sastoken |Service Bus SAS トークン |AMQP 値 (string) |- |

トークンには、権限を与える作用があります。 Service Bus は、3 つの基本的な権限として、"Send" (送信を許可)、"Listen" (受信を許可)、"Manage" (エンティティの操作を許可) を認識します。 AAD/ACS によって明示的に発行された SWT トークンは、これらの権限を要求 (claim) として含んでいます。 Service Bus の SAS トークンは、名前空間またはエンティティに対して構成されている規則を参照し、それらの規則が権限で構成されています。 したがって、その規則に関連付けられているキーでトークンに署名すると、トークンが個別の権限を表すようになります。 エンティティには、*put-token* を使ってトークンが関連付けられます。接続クライアントは、このトークンの権限に従ってエンティティを操作することができます。 クライアントが "*送信側*" の役割を担うリンクには "Send" 権限が、"*受信側*" の役割を担うリンクには "Listen" 権限が必要となります。

応答メッセージには、次の *application-properties* 値があります。

| Key | 省略可能 | 値の型 | 値の内容 |
| --- | --- | --- | --- |
| status-code |いいえ |INT |HTTP 応答コード **[RFC2616]** 。 |
| status-description |はい |string |ステータスの説明。 |

クライアントは、メッセージング インフラストラクチャ内の任意のエンティティに対し、*put-token* を繰り返し呼び出すことができます。 トークンの有効範囲は現在のクライアントに限定され、現在の接続に固定されます。つまり、保持されているトークンは、接続が失われた時点でサーバーによって破棄されます。

Service Bus の現在の実装では、SASL の "ANONYMOUS" 方式との組み合わせでしか CBS を使用できません。 常に SASL ハンドシェイクの前に、SSL/TLS 接続が存在していることが必要です。

したがって、選択した AMQP 1.0 クライアントが ANONYMOUS のメカニズムをサポートしている必要があります。 匿名アクセスは、だれが接続を作成しているのかを Service Bus が関知しない状況下で、初回接続ハンドシェイク (初回セッションの作成を含む) が実行されることを意味します。

許可される操作は、接続とセッションが確立された後、 *$cbs* ノードにリンクを割り当て、*put-token* 要求を送信することだけです。 接続が確立されてから 20 秒以内に、*put-token* 要求を使用して特定のエンティティ ノードに有効なトークンを正しく設定する必要があります。それに失敗すると、接続は Service Bus によって一方的に破棄されます。

以後トークンの期限を追跡する役割は、クライアントが果たすこととなります。 トークンの有効期限が切れるとすぐ、個々のエンティティとの間でその接続上に形成されていたリンクはすべて Service Bus によって破棄されます。 問題の発生を防ぐためにクライアントは随時、仮想 *$cbs* 管理ノードを介し、同じ *put-token* ジェスチャを使用して、ノードのトークンを新しいトークンに差し替えることができます。このとき、他のリンク上を流れるペイロード トラフィックの障害になることはありません。

### <a name="send-via-functionality"></a>経由送信の機能

[経由送信 / 送信元の転送](service-bus-transactions.md#transfers-and-send-via)は、Service Bus が指定されたメッセージを別のエンティティ経由で宛先エンティティに転送できる機能です。 これは、単一のトランザクション内の複数のエンティティにわたる操作を実行するために使用されます。

この機能により、送信元を作成して `via-entity` へのリンクを確立します。 リンクの確立中に、このリンクでのメッセージ/転送の実際の宛先を確立するために、追加の情報が渡されます。 接続に成功すると、以降はこのリンク上で送信されるすべてのメッセージは自動的に *via-entity* 経由で *destination-entity* に転送されます。 

> 注:このリンクを確立する前に、*via-entity* と *destination-entity* の両方に対して認証が実行される必要があります。

| Client | Direction | Service Bus |
| :--- | :---: | :--- |
| attach(<br/>name={link name},<br/>role=sender,<br/>source={client link ID},<br/>target= **{via-entity}** ,<br/>**properties=map [(<br/>com.microsoft:transfer-destination-address=<br/>{destination-entity} )]** ) | ------> | |
| | <------ | attach(<br/>name={link name},<br/>role=receiver,<br/>source={client link ID},<br/>target={via-entity},<br/>properties=map [(<br/>com.microsoft:transfer-destination-address=<br/>{destination-entity} )] ) |

## <a name="next-steps"></a>次のステップ

AMQP の詳細については、次のリンクを参照してください。

* [Service Bus AMQP の概要]
* [パーティション分割された Service Bus のキューとトピックにおける AMQP 1.0 のサポート]
* [Windows Server 用 Service Bus の AMQP]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Service Bus AMQP の概要]: service-bus-amqp-overview.md
[パーティション分割された Service Bus のキューとトピックにおける AMQP 1.0 のサポート]: 
[AMQP in Service Bus for Windows Server]: /previous-versions/service-bus-archive/dn574799(v=azure.100)
