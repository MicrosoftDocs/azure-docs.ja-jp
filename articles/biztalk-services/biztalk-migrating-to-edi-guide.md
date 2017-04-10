---
title: "BizTalk Services への BizTalk Server EDI ソリューションの移行: テクニカル ガイド | Microsoft Docs"
description: "MABS (Microsoft Azure BizTalk Services) への EDI の移行"
services: biztalk-services
documentationcenter: na
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 61c179fa-3f37-495b-8016-dee7474fd3a6
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 71f9dd111ebdbe885f33d162b2ea320dfaa167bb
ms.openlocfilehash: 941a7f15da94d92e3c0f65ea61247608c18be029


---
# <a name="migrating-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>BizTalk Services への BizTalk Server EDI ソリューションの移行: テクニカル ガイド
執筆者: Tim Wieman、Nitin Mehrotra

校閲者: Karthik Bharthy

使用環境: Microsoft Azure BizTalk Services – 2014 年 2 月リリース。

## <a name="introduction"></a>はじめに
電子データ交換 (EDI) は、企業が電子的にデータを交換する最も一般的な手段の 1 つであり、企業間 (B2B) トランザクションとも呼ばれます。 BizTalk Server は、最初のリリース以降 10 年以上にわたって EDI をサポートしてきました。 BizTalk Services により、マイクロソフトは Microsoft Azure Platform で引き続き EDI ソリューションをサポートします。 B2B トランザクションはほとんどが組織の外部で行われるため、クラウド プラットフォームで実装されていた場合の方が実装が簡単です。 Microsoft Azure は、BizTalk Services を通じてこの機能を提供します。

一部の顧客は BizTalk Services を新しい EDI ソリューションに関して「未開発」のプラットフォームと見ていますが、多くの顧客は Azure に移行する必要のある BizTalk Server の EDI ソリューションを現在使用しています。 BizTalk Services EDI は BizTalk Server EDI のアーキテクチャと同じ主要エンティティを基にして構築されているため (取引先、エンティティ、アグリーメント)、BizTalk Server EDI のアーティファクトを BizTalk Services に移行できます。

このドキュメントでは、BizTalk Services への BizTalk Server EDI アーティファクトの移行に関連するいくつかの相違点について説明します。 このドキュメントは、BizTalk Server EDI の処理および取引先アグリーメントに関する実用的な知識を前提とします。 BizTalk Server EDI の詳細については、「 [BizTalk Server を使用した取引先管理](https://msdn.microsoft.com/library/bb259970.aspx)」をご覧ください。

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>BizTalk Services に移行できる BizTalk Server EDI アーティファクトのバージョン
BizTalk Server の EDI モジュールは、BizTalk Server 2010 でパートナー、プロファイル、アグリーメントを組み込んで再モデル化されたときに大幅に強化されました。 BizTalk Services は、取引先および取引先内部の部門を編成するために同じモデルを使用します。 その結果、BizTalk Server 2010 以降のバージョンから BizTalk Services への EDI アーティファクトの移行は非常に簡単になっています。 BizTalk Server 2010 より前のバージョンに関連付けられた EDI アーティファクトを移行するには、まず BizTalk Server 2010 にアップグレードした後、EDI アーティファクトを BizTalk Services に移行する必要があります。

## <a name="scenariosmessage-flow"></a>シナリオ/メッセージ フロー
BizTalk Server と同様に、BizTalk Services の EDI 処理は取引先管理 (TPM) ソリューションを中心にして構築されています。 TPM ソリューションには次の主要コンポーネントがあります。

* 取引先: B2B トランザクションにおける組織を表します。
* プロファイル: 取引先内部の部門を表します。
* 取引先アグリーメント (または単にアグリーメント): 2 つのパートナー/プロファイル間のビジネス アグリーメントを表します。

次の図は、BizTalk Server EDI ソリューションと BizTalk Services EDI ソリューションの類似点と相違点を示したものです。

![][EDImessageflow]

BizTalk Server および BizTalk Services での EDI ソリューション フローの主な相違点と類似点は以下のとおりです。

* BizTalk Server が EDIReceive パイプラインを使用して EDI メッセージを受信し、EDISend パイプラインを使用して EDI メッセージを送信するのと同じように、BizTalk Services は EDI 受信ブリッジを使用して EDI メッセージを受信し、EDI 送信ブリッジを使用して EDI メッセージを送信します。 BizTalk Server では、パイプラインは送信または受信ポートを使用してアグリーメントに関連付けられます。 BizTalk Services では、アグリーメント自体が送信または受信ブリッジを示します。
* BizTalk server では、EDIReceive パイプラインが EDI メッセージを処理した後、メッセージは SQL Server データベースにダンプされます。 その後、EdiSend パイプラインが SQL Server データベースからメッセージを取得して、処理し、取引先に送信します。
  
    BizTalk Services では、EDI 受信ブリッジは EDI メッセージを処理した後でメッセージを外部プロセスにルーティングします。 外部プロセスは、Microsoft Azure またはオンプレミスで実行できます。 外部プロセスは、EDI 送信ブリッジにメッセージをルーティングする必要があります。送信ブリッジは、基本的にメッセージをプルしません。 メッセージを処理した後、EDI 送信ブリッジは取引先にメッセージをルーティングします。

BizTalk Services が提供する使いやすい構成環境を使用すると、取引先間の B2B アグリーメントをすばやく作成およびデプロイ、Microsoft Azure Compute インスタンス (Web またはワーカー ロール)、Microsoft Azure SQL Database、Microsoft Azure ストレージ アカウントを構成する必要はありません。 さらに複雑なシナリオでは、取引先アグリーメントの「エッジの周辺」つまり取引先アグリーメント EDI ブリッジ処理の前後で、ワークフローまたは他のサービス処理を結び付ける必要があります。 詳しくは、BizTalk Services でのEDI メッセージ処理の間に以下のイベント シーケンスが発生します。

1. 取引先 Fabrikam から EDI メッセージを受信します。  取引先から EDI メッセージを受信するため、BizTalk Services は FTP、SFTP、AS2、HTTP/S などのトランスポート プロトコルをサポートしています。
2. 取引先アグリーメントの受信側処理が、EDI メッセージを XML 形式に分解します。  分解された EDI メッセージ (XML 形式) は、Service Bus Relay エンドポイント、Service Bus トピック、Service Bus キュー、BizTalk Services ブリッジなどの Service Bus エンドポイントにルーティングできます。
3. その後、さらなるカスタム処理のために分解された XML メッセージをエンドポイントから受け取ることができます。  これらのエンドポイントをオンプレミスのコンポーネントまたは Microsoft Azure Compute インスタンスで処理し、たとえば Windows Workflow (WF) や Windows Communication Foundation (WCF) サービスでメッセージをさらに処理できます。
4. 取引先アグリーメントの「送信側処理」は、XML メッセージから EDI 形式を構成して、取引先 Contoso に送信します。  取引先に EDI メッセージを送信するため、BizTalk Services は EDI メッセージの受信に使用されるものと同じプロトコルをサポートします。

このドキュメントではさらに、いくつかの BizTalk Server EDI アーティファクトの BizTalk Services への移行に関する概念的ガイダンスを提供します。

## <a name="sendreceive-ports-to-trading-partners"></a>取引先への送信/受信ポート
BizTalk Server では、取引先から EDI/XML メッセージを受信するための受信場所と受信ポートを設定し、取引先に EDI/XML メッセージを送信するための送信ポートを設定します。 その後、BizTalk Server 管理コンソールを使用して取引先アグリーメントにこれらのポートを関連付けます。 BizTalk Services では、取引先からメッセージを受信する場所および取引先にメッセージを送信する場所は、BizTalk Services ポータルで (トランスポート設定の一部である) 取引先アグリーメント自体の一部として構成されます。  したがって、BizTalk Services には「送信ポート」および「受信場所」という概念は実際にはありません。 詳しくは、「 [Azure BizTalk サービスでのアグリーメントの作成](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx)」をご覧ください。

## <a name="pipelines-bridges"></a>パイプライン (ブリッジ)
BizTalk Server の EDI では、パイプラインはメッセージ処理エンティティであり、アプリケーションの必要に応じて、特定の処理機能のためのカスタム ロジックを含むこともできます。 BizTalk Services でこれに相当するものは EDI ブリッジです。 ただし、現時点では、BizTalk Services の EDI ブリッジは「閉じて」います。  つまり、EDI ブリッジに独自のカスタム アクティビティを追加することはできません。 すべてのカスタム処理は、取引先アグリーメントの一部として構成されるブリッジにメッセージが入る前後のアプリケーションにおいて、EDI ブリッジの外部で実行する必要があります。 EAI ブリッジにはカスタム処理を行うためのオプションがあります。 カスタム処理が必要な場合は、EDI ブリッジによってメッセージが処理される前後で EAI ブリッジを使用できます。 詳しくは、「 [ブリッジにカスタム コードを組み込む方法](https://msdn.microsoft.com/library/azure/dn232389.aspx)」をご覧ください。

取引先アグリーメントがメッセージを受信する前、またはアグリーメントがメッセージを処理して Service Bus エンドポイントにルーティングした後に、カスタム コードを含むパブリッシュ/サブスクライブ フローおよび Service Bus のメッセージング キューとトピックの使用を、挿入できます。

メッセージ フローのパターンについては、このトピックの「 **シナリオ/メッセージ フロー** 」を参照してください。

## <a name="agreements"></a>アグリーメント
EDI 処理に使用される BizTalk Server 2010 の取引先アグリーメントに慣れている場合は、BizTalk Services の取引先アグリーメントもよく似ています。 アグリーメントの設定のほとんどは同じであり、同じ用語を使用します。 場合によっては、アグリーメントの設定は BizTalk Server の場合よりはるかに簡単です。 Microsoft Azure BizTalk Services は、X12、EDIFACT、AS2 のトランスポートをサポートします。

また、Microsoft Azure BizTalk Services には、BizTalk Server の取引先モジュールから BizTalk Services ポータルに取引先とアグリーメントを移行するための **TPM データ移行** ツールが用意されています。 TPM データ移行ツールは Tools パッケージの一部として使用でき、 [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057)からダウンロードできます。 パッケージには、ツールの使用方法とツールの基本的なトラブルシューティング情報を提供する readme も含まれます。

## <a name="schemas"></a>スキーマ
BizTalk Services で提供される EDI スキーマは BizTalk Services ソリューションで使用できます。  さらに、BizTalk Server の EDI スキーマも、EDI スキーマのルート ノードが BizTalk Server と BizTalk Services で同じなので、BizTalk Services で使用できます。 そのため、BizTalk Server の EDI スキーマを直接取得し、BizTalk Services を使用して開発する EDI ソリューションで使用できます。 [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057)からスキーマをダウンロードすることもできます。

## <a name="maps-transforms"></a>マップ (変換)
BizTalk Server のマップは、BizTalk Services では変換と呼ばれます。 BizTalk Server から BizTalk Services へのマップの移行は、実行するのが比較的複雑なタスクの 1 つです (マップの複雑さに依存します)。 BizTalk Services に使用するマッピング ツールは、BizTalk マッパーとは異なるものです。 マッパーはほとんど同じように見えますが、基になっているマップ形式が異なります。 ユーザーが使用できる functoid (BizTalk Services では **マップ操作** と呼ばれます) も異なります。  実際には、BizTalk Services で BizTalk マップを直接使用することはできません。 また、BizTalk Server で使用できる functoid の中には、BizTalk Services のマップ操作では使用できないものがあります。

### <a name="new-transform-operations"></a>新しい変換操作
使用できる変換マップ操作の一覧は BizTalk Server マッパーとまったく異なるように見えますが、BizTalk Services の変換には同じタスクを実行する新しい方法があります。 たとえば、BizTalk Services の変換では**リスト操作**を使用できます。 これは、BizTalk マッパーでは使用できなかったものです。  **リスト操作**を使用すると、"リスト" を作成して操作できます。リストは一連の項目 ("行" とも呼ばれます) であり、各項目は複数のメンバー ("列" とも呼ばれます) を持つことができます。  リストの並べ替え、条件に基づく項目の選択などが可能です。

BizTalk Services の変換での新機能のもう 1 つの例は、 **ループ操作**です。  BizTalk Server マッパーでは入れ子になったループを作成するのは困難です。  したがって、ループ マップ操作が BizTalk Services の変換に追加されています。

別の例は、 **If-Then-Else** 式のマップ操作です。  BizTalk マッパーでも if-then-else 操作を行うことはできましたが、見た目は簡単なタスクでも実行するには複数の functoid が必要でした。

### <a name="migrating-biztalk-server-maps"></a>BizTalk Server マップの移行
Microsoft Azure BizTalk Services では、BizTalk Server マップを BizTalk Services 変換に移行するためのツールが提供されています。 **BTMMigrationTool** は [BizTalk Services SDK のダウンロード](http://go.microsoft.com/fwlink/p/?LinkId=235057)で提供される **Tools** パッケージの一部として使用できます。 ツールについて詳しくは、「 [BizTalk マップを BizTalk Services の変換に移行する](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx)」をご覧ください。

[BizTalk Server のマップを BizTalk Services の変換に移行する](http://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx)方法については、BizTalk MVP である Sandro Pereira によるサンプルも参考にしてください。

## <a name="orchestrations"></a>オーケストレーション
BizTalk Server のオーケストレーション処理を Microsoft Azure に移行する必要がある場合は、Microsoft Azure は BizTalk Server のオーケストレーションの実行をサポートしていないため、オーケストレーションを書き直す必要があります。  オーケストレーションの機能は、Windows Workflow Foundation 4.0 (WF4) サービスで書き直すことができます。  現時点では BizTalk Server オーケストレーションから WF4 への移行がないため、これは完全な書き直しになります。 Windows Workflow に関するリソースを次に示します。

* "[*WCF ワークフロー サービスを Service Bus のキューおよびトピックに統合する方法*](https://msdn.microsoft.com/library/azure/hh709041.aspx)" (Paolo Salvatori)。 
* ["*Windows Workflow Foundation と Azure でのアプリの構築*" セッション](http://go.microsoft.com/fwlink/p/?LinkId=237314) (Build 2011 カンファレンス)。
* [*Windows Workflow Foundation デベロッパー センター*](http://go.microsoft.com/fwlink/p/?LinkId=237315) (MSDN)。
* "[*Windows Workflow Foundation 4 (WF4) のドキュメント*](https://msdn.microsoft.com/library/dd489441.aspx)" (MSDN)。

## <a name="other-considerations"></a>その他の考慮事項
以下では、BizTalk Services を使用するときに考慮する必要があることを示します。

### <a name="fallback-agreements"></a>フォールバック アグリーメント
BizTalk Server の EDI 処理には、「フォールバック アグリーメント」の概念があります。  現時点では、BizTalk Services にはフォールバック アグリーメントの概念は **ありません** 。  BizTalk Server でのフォールバック アグリーメントの使用方法については、BizTalk ドキュメントのトピック「[EDI 処理におけるアグリーメントのロール](http://go.microsoft.com/fwlink/p/?LinkId=237317)」および「[グローバルまたはフォールバック アグリーメントのプロパティの構成](https://msdn.microsoft.com/library/bb245981.aspx)」を参照してください。

### <a name="routing-to-multiple-destinations"></a>複数の送信先へのルーティング
現状の BizTalk Services ブリッジでは、パブリッシュ - サブスクライブ モデルを使用した複数の送信先へのメッセージのルーティングはサポートされていません。 代わりに、BizTalk Services ブリッジから Service Bus トピックにメッセージをルーティングできます。Service Bus トピックは、複数のサブスクリプションを使用して複数のエンドポイントでメッセージを受信できます。

## <a name="conclusion"></a>まとめ
Microsoft Azure BizTalk Services は定期的なマイルストーンで更新されて、新しい機能を追加されます。 更新のたびに、BizTalk Services とその他の Azure テクノロジを使用してエンド ツー エンドのソリューションの作成を容易にする機能のサポートが増えるはずです。

## <a name="see-also"></a>関連項目
[Azure 対応のエンタープライズ アプリケーションの開発](https://msdn.microsoft.com/library/azure/hh674490.aspx)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png



<!--HONumber=Nov16_HO3-->


