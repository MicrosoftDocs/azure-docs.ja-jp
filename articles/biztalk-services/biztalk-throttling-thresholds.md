---
title: "BizTalk Services でのスロットルについて | Microsoft Docs"
description: "調整のしきい値についてと、結果として生じる BizTalk Services のランタイムの動作について説明します。 調整は、メモリ使用率とメッセージの数に基づいて行われます。 MABS、WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: f6663cf2-cda4-4bac-855e-27d2ad5c4fa4
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 71f9dd111ebdbe885f33d162b2ea320dfaa167bb
ms.openlocfilehash: e14f42497d5ee0c89fe1fa0824431e2d82e6555a


---
# <a name="biztalk-services-throttling"></a>BizTalk Services: 調整
Azure BizTalk Services は、メモリ使用率およびメッセージの同時処理数という 2 つの条件に基づいてサービスの調整を実装します。 このトピックでは、スロットルのしきい値を示し、スロットル条件が発生したときのランタイムの動作について説明します。

## <a name="throttling-thresholds"></a>調整のしきい値
調整の対象としきい値の一覧を次の表に示します。

|  | Description | 低いしきい値 | 高いしきい値 |
| --- | --- | --- | --- |
| メモリ |使用可能な合計システム メモリ/PageFileBytes の割合。 <p><p>使用可能な合計 PageFileBytes は、システムの RAM の約 2 倍です。 |60% |70% |
| メッセージ処理 |メッセージの同時処理数 |40 * コアの数 |100 * コアの数 |

高いしきい値に達すると、調整が開始されます。 低いしきい値に達すると、調整は停止します。 たとえば、サービスで 65% のシステム メモリを使用しているとします。 この状況では、調整は行われません。 一方、サービスで 70% のシステム メモリの使用を開始するとします。 この状況では、調整が行われ、サービスによるシステム メモリの使用が 60% (低いしきい値) になるまで調整が継続されます。

Azure BizTalk Services では、調整の状態 (通常と調整された状態) および調整の期間が追跡されます。

## <a name="runtime-behavior"></a>ランタイムの動作
Azure BizTalk Services が調整状態になると、次のことが発生します。

* 調整はロール インスタンス単位で行われます。 次に例を示します。<br/>
  RoleInstanceA は調整中です。 RoleInstanceB は調整されていません。 この状況では、RoleInstanceB のメッセージは予期したとおりに処理されます。 RoleInstanceA のメッセージは破棄され、次のエラーで失敗します。<br/><br/>
  **サーバーがビジー状態です。もう一度実行してください。**<br/><br/>
* どの取り出し元も、メッセージのポーリングやダウンロードを行いません。 次に例を示します。<br/>
  パイプラインは外部の FTP ソースからメッセージを取り出します。 取り出しを実行するロール インスタンスは調整状態になります。 この状況では、パイプラインは、ロール インスタンスが調整を停止するまで追加のメッセージのダウンロードを停止します。
* 応答がクライアントに送信され、クライアントはメッセージを再送信できるようになります。
* 調整が解決されるまで待つ必要があります。 具体的には、低いしきい値に達するまで待つ必要があります。

## <a name="important-notes"></a>重要
* 調整を無効にすることはできません。
* 調整のしきい値を変更することはできません。
* 調整はシステム全体で実装されます。
* Azure SQL Database サーバーには、組み込みの調整もあります。

## <a name="additional-azure-biztalk-services-topics"></a>Azure BizTalk Services に関するその他のトピック
* [Azure BizTalk Services SDK のインストール](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [チュートリアル: Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Visual Studio でのプロジェクトの作成](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>関連項目
* [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk Services: Azure クラシック ポータルを使用したプロビジョニング](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [BizTalk Services: サービスの状態のチャート](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: [ダッシュボード]、[監視]、および [スケール] タブ](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk サービス: バックアップと復元に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk サービス: 発行者名および発行者キー](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>




<!--HONumber=Nov16_HO3-->


