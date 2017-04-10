---
title: "Load Balancer カスタム プローブと正常性状態の監視 | Microsoft Docs"
description: "Azure Load Balancer でカスタム プローブを使用して、Load Balancer の背後にあるインスタンスを監視する方法を説明します"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: ce2550ca8301fd12d61cca143b2851b84f1a0f50
ms.openlocfilehash: 01afa3a08bbb56d4c6b8b18c5eb07f49706c6482

---

# <a name="understand-load-balancer-probes"></a>Load Balancer プローブを理解する

Azure Load Balancer には、プローブを使用してサーバー インスタンスの正常性を監視する機能があります。 プローブが応答できない場合、Load Balancer は異常なインスタンスへの新しい接続の送信を停止します。 既存の接続への影響はなく、新しい接続が正常なインスタンスに送信されます。

クラウド サービス ロール (worker ロールと Web ロール) では、ゲスト エージェントを使用してプローブを監視します。 Load Balancer の背後にある仮想マシンを使用する場合は、TCP または HTTP カスタム プローブを構成する必要があります。

## <a name="understand-probe-count-and-timeout"></a>プローブの数とタイムアウトについて

プローブの動作は、以下の要素によって変わってきます。

* インスタンスを実行中としてラベル付けできる成功プローブの数。
* インスタンスが停止中としてラベル付けされる失敗プローブの数。

インスタンスが実行中か停止中かは、SuccessFailCount に設定されているタイムアウトと頻度の値によって決まります。 Azure ポータルの場合、タイムアウトは頻度の値の 2 倍に設定されます。

エンドポイントに対して負荷分散されたすべてのインスタンス (負荷分散されたセット) のプローブ構成は、同じにする必要があります。 つまり、特定のエンドポイントの組み合わせに対してホストされる同じサービス内の各ロール インスタンスまたは仮想マシンに、異なるプローブ構成を設定することはできません。 たとえば、各インスタンスには、同一のローカル ポートとタイムアウトが必要です。

> [!IMPORTANT]
> Load Balancer プローブは、IP アドレス 168.63.129.16 を使用します。 このパブリック IP アドレスを使うことで、独自の IP Azure Virtual Network を使用するシナリオで、内部プラットフォーム リソースへの通信が容易になります。 仮想パブリック IP アドレス 168.63.129.16 は、すべてのリージョンで使用され、変更されることはありません。 この IP アドレスは、すべてのローカル ファイアウォール ポリシーで許可することをお勧めします。 これをセキュリティ リスクとして考慮する必要はありません。内部 Azure プラットフォームのみが、そのアドレスからのメッセージをソースにできるためです。 これを行わないと、同じ IP アドレス範囲の 168.63.129.16 を構成し、重複する IP アドレスがあるようなさまざまなシナリオで、予期しない動作が発生します。

## <a name="learn-about-the-types-of-probes"></a>プローブの種類について

### <a name="guest-agent-probe"></a>ゲスト エージェント プローブ

このプローブは、Azure Cloud Services でのみ使用できます。 Load Balancer は、仮想マシン内のゲスト エージェントを使用してリッスンし、インスタンスが準備完了状態の場合のみ (ビジー、リサイクル中、停止中などの状態でない場合) HTTP 200 OK 応答を返します。

詳細については、[正常性プローブのサービス定義ファイル (csdef) の構成](https://msdn.microsoft.com/library/azure/ee758710.aspx)に関するページまたは「[インターネットに接続するロード バランサー (クラウド サービス用) の作成の開始](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services)」をご覧ください。

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>ゲスト エージェント プローブがインスタンスを異常としてマークする状況

ゲスト エージェントが HTTP 200 OK で応答できない場合、Load Balancer はそのインスタンスを応答不能と見なし、インスタンスへのトラフィックの送信を停止します。 Load Balancer はインスタンスへの ping を続けます。 ゲスト エージェントが HTTP 200 で応答すると、Load Balancer はそのインスタンスへのトラフィックの送信を再開します。

Web ロールを使用する場合、Web サイト コードは通常、Azure ファブリックやゲスト エージェントでは監視されない w3wp.exe で実行されます。 つまり、w3wp.exe が失敗 (HTTP 500 応答など) してもゲスト エージェントに報告されず、Load Balancer はそのインスタンスをローテーションから除外しません。

### <a name="http-custom-probe"></a>HTTP カスタム プローブ

カスタム HTTP Load Balancer プローブは、既定のゲスト エージェント プローブをオーバーライドします。つまり、ユーザーは独自のカスタム ロジックを作成してロール インスタンスの正常性を特定できます。 Load Balancer は、既定では 15 秒ごとにエンドポイントを調査します。 タイムアウト期間内 (既定値は 31 秒) にインスタンスが HTTP 200 で応答した場合、そのインスタンスは Load Balancer ローテーション内にあると見なされます。

これは、Load Balancer ローテーションからインスタンスを削除するユーザー独自のロジックを実装する必要がある場合に役立ちます。 たとえば、CPU が 90% を超え、200 以外の状態が返される場合は、そのインスタンスが削除されるようにすることができます。 w3wp.exe を使用する Web ロールがある場合も、Web サイトの自動監視を意味します。Web サイト コードが失敗したときに、Load Balancer プローブに 200 以外の状態が返されるためです。

> [!NOTE]
> HTTP カスタム プローブは、相対パスと HTTP プロトコルのみをサポートします。 HTTPS はサポートされていません。

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>HTTP カスタム プローブがインスタンスを異常としてマークする状況

* HTTP アプリケーションが 200 以外の HTTP 応答コード (403、404、500 など) を返す。 これは、アプリケーション インスタンスをすぐに使用不能にする肯定応答です。
* タイムアウト期間後、HTTP サーバーがまったく応答しない。 設定されているタイムアウト値によっては、プローブが停止中としてマークされる前に (つまり、SuccessFailCount プローブが送信される前に)、複数のプローブ要求が応答なしになる可能性があります。
* サーバーが TCP リセットによって接続を閉じた。

### <a name="tcp-custom-probe"></a>TCP カスタム プローブ

TCP プローブは、定義済みのポートに 3 ウェイ ハンドシェイクを実行して、接続を開始します。

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>TCP カスタム プローブがインスタンスを異常としてマークする状況

* タイムアウト期間後、TCP サーバーがまったく応答しない。 プローブが停止中としてマークされるタイミングは、プローブが停止中としてマークされる前に応答なしになるように構成された、失敗したプローブ要求の数によって異なります。
* プローブがロール インスタンスから TCP リセットを受信した。

HTTP 正常性プローブまたは TCP プローブの構成の詳細については、「 [リソース マネージャーで PowerShell を使用して、インターネットに接続するロード バランサーの作成を開始する](load-balancer-get-started-internet-arm-ps.md)」を参照してください。

## <a name="add-healthy-instances-back-into-load-balancer-rotation"></a>正常なインスタンスを Load Balancer ローテーションに戻す

次の場合に、TCP と HTTP プローブは正常と見なされ、ロール インスタンスを正常としてマークします。

* VM の初回起動時に、Load Balancer が正のプローブを取得します。
* (前述した) SuccessFailCount の数により、ロール インスタンスを正常としてマークするために必要な成功プローブの値が定義されます。 ロール インスタンスが削除された場合、そのロール インスタンスを実行中としてマークするには、連続して成功したプローブの数は、SuccessFailCount の値以上である必要があります。

> [!NOTE]
> ロール インスタンスの正常性が変動する場合、Load Balancer は、さらに長い時間待機してから、ロール インスタンスを正常な状態に戻します。 これは、ユーザーとインフラストラクチャを保護するポリシーによって実行されます。

## <a name="use-log-analytics-for-load-balancer"></a>Load Balancer のログ分析を使用する

[Load Balancer のログ分析](load-balancer-monitor-log.md) を使用すると、プローブの正常性状態とプローブの数を確認できます。 ログ記録と共に Power BI または Azure Operational Insights を使用することで、Load Balancer の正常性状態の統計情報を提供することができます。



<!--HONumber=Nov16_HO3-->


