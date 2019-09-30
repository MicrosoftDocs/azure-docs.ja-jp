---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 8/22/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: b2a7bbef2c421281780c0191fa32381468899bbf
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020341"
---
Azure では、定期的にそのプラットフォームを更新して、仮想マシンのホスト インフラストラクチャの信頼性、パフォーマンス、セキュリティの向上に努めています。 これらの更新の目的は、ホスティング環境のソフトウェア コンポーネントの修正から、ネットワーク コンポーネントのアップグレード、ハードウェアの使用停止まで、広い範囲に及びます。 

更新は、ホストされている VM にはほとんど影響しません。 更新が影響を及ぼす場合、Azure は影響が最小となる更新方法を選択します。

- 更新で再起動を必要としない場合、VM は、ホストの更新中に一時停止されるか、または既に更新済みのホストにライブ移行されます。

- メンテナンスで再起動が必要な場合は、計画メンテナンスが通知されます。 Azure により、都合の良いときに自分でメンテナンスを開始できる時間枠も与えられます。 セルフ メンテナンスの時間枠は、そのメンテナンスが緊急でない限り、通常は 35 日間です。 Azure は、計画されたプラットフォーム メンテナンスで VM の再起動を必要とするケースの数を減らすためのテクノロジに投資しています。 

このページでは、2 種類のメンテナンスが Azure でどのように実行されるかについて説明します。 計画外のイベント (停止) の詳細については、 [Windows 向けの VM の可用性の管理](../articles/virtual-machines/windows/manage-availability.md)に関する記事または [Linux](../articles/virtual-machines/linux/manage-availability.md) 向けの該当する記事をご覧ください。

VM 内で、[Linux](../articles/virtual-machines/linux/scheduled-events.md) または [Windows 向けの Scheduled Events を使用](../articles/virtual-machines/windows/scheduled-events.md)して、今後のメンテナンスに関する通知を受け取ることができます。

計画メンテナンスを管理する方法については、[Linux 向けの計画メンテナンスの通知の処理](../articles/virtual-machines/linux/maintenance-notifications.md)に関する記事または [Windows](../articles/virtual-machines/windows/maintenance-notifications.md) 向けの該当する記事をご覧ください。

## <a name="maintenance-that-doesnt-require-a-reboot"></a>再起動を必要としないメンテナンス

前述のように、ほとんどのプラットフォーム更新は、お客様の VM に影響しません。 影響を及ぼさない更新が不可能な場合、Azure は、お客様の VM への影響が最小となる更新メカニズムを選択します。 

影響がゼロではないメンテナンスでは、ほとんどの場合、VM の一時停止は 10 秒未満です。 場合によっては、Azure はメモリ保持メンテナンスのメカニズムを使用します。 これらのメカニズムでは、最大 30 秒間 VM が一時停止され、RAM 内のメモリが保持されます。 その後、VM が再開され、クロックが自動的に同期されます。 

メモリ保持メンテナンスは、Azure VM の 90% 以上で機能します。 G、M、N、および H シリーズでは機能しません。 Azure では、ライブ マイグレーション テクノロジの使用を拡大すると共に、一時停止期間を短縮するためにメモリ保持メンテナンス メカニズムの改善に取り組んでいます。  

再起動を必要としないメンテナンス操作は、一度に 1 つの障害ドメインに適用されます。 それらは、警告の正常性シグナルを受信した場合、停止します。 

こうした種類の更新が、一部のアプリケーションに影響を与える可能性があります。 VM を別のホストにライブ移行する場合、影響を受けやすい一部のワークロードでは、VM の一時停止に至るまでの数分間にわずかなパフォーマンスの低下が見られることがあります。 VM のメンテナンスに対する準備をして Azure のメンテナンスの影響を減らすには、そのようなアプリケーションに [Linux](../articles/virtual-machines/linux/scheduled-events.md) または [Windows 向けの Scheduled Events を使用](../articles/virtual-machines/windows/scheduled-events.md)してみてください。 また、Azure ではそのような、影響がゼロではない、[Azure Dedicated Host](../articles/virtual-machines/windows/dedicated-hosts.md) および [Isolated VM](../articles/security/fundamentals/isolation-choices.md) に対するプラットフォーム メンテナンスについても完全に管理できるようになっています。 メンテナンス管理機能はプレビュー段階であり、[サインアップ フォーム](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6lJf7DwiQxNmz51ksQvxV9UNUM3UllWUjBMTFZQUFhHUDI0VTBPQlJFNS4u)を送信することでアクセスを要求できます。 影響がゼロではないプラットフォーム更新をスキップし、35 日間のローリング期間内の都合のよいときに、それらの更新をバッチとして適用することを選択できます。

### <a name="live-migration"></a>ライブ マイグレーション

ライブ マイグレーションは、再起動を必要とせず、VM のメモリを保持する操作です。 これにより一時停止または凍結が発生しますが、その継続時間は通常は 5 秒未満です。 G、M、N、H シリーズを除く、サービスとしてのインフラストラクチャ (IaaS) VM はすべて、ライブ マイグレーションに対応しています。 対象となる VM は、Azure フリートにデプロイされている IaaS VM の 90% 以上に相当します。 

Azure プラットフォームでは、次のシナリオでライブ マイグレーションを開始します。
- Azure の計画メンテナンス
- ハードウェア障害
- 割り当ての最適化

計画メンテナンスの一部のシナリオでライブ マイグレーションが使用されており、Scheduled Events を使用して、ライブ マイグレーションの操作がいつ開始するか事前に把握できます。

Azure Machine Learning アルゴリズムでハードウェア障害の発生が予測されている場合や、お客様が VM の割り当てを最適化したい場合に、ライブ マイグレーションを使用して VM を移動することもできます。 機能低下しているハードウェアのインスタンスを検出する予測モデリングの詳細については、[予測機械学習とライブ マイグレーションによる Azure VM の回復性の向上](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure)に関する記事をご覧ください。 ライブ マイグレーションの通知は、Azure portal の Monitor および Service Health のログ、およびScheduled Events に表示されます (これらのサービスを使用している場合)。

## <a name="maintenance-that-requires-a-reboot"></a>再起動を必要とするメンテナンス

計画メンテナンスで VM の再起動が必要になるまれなケースでは、事前に通知が届きます。 計画メンテナンスには、"セルフサービス フェーズ" と "予定メンテナンス フェーズ" の 2 つのフェーズがあります。

通常 4 週間続く*セルフ サービス フェーズ*中に、ご利用の VM でメンテナンスを開始します。 セルフ サービスの一環として、個々の VM に照会し、その状態と自分が最後に行ったメンテナンス要求の結果を確認できます。

セルフサービス メンテナンスを開始すると、VM は、既に更新済みのノードに再デプロイされます。 VM が再起動されるため、一時ディスクは失われ、仮想ネットワーク インターフェイスに関連付けられた動的 IP アドレスは更新されます。

セルフサービス メンテナンス中にエラーが発生した場合、その操作が停止し、VM は更新されず、セルフサービス メンテナンスを再試行するオプションが提供されます。 

セルフ サービス フェーズが終了すると、*予定メンテナンス フェーズ*が開始します。 このフェーズの間、ユーザーはメンテナンス フェーズについて照会することはできますが、自分でメンテナンスを開始することはできません。

再起動を必要とするメンテナンスを管理する方法について詳しくは、[Linux 向けの計画メンテナンスの通知の処理](../articles/virtual-machines/linux/maintenance-notifications.md)に関する記事または [Windows](../articles/virtual-machines/windows/maintenance-notifications.md) 向けの該当する記事をご覧ください。 

### <a name="availability-considerations-during-scheduled-maintenance"></a>予定メンテナンス中の可用性に関する考慮事項 

予定メンテナンス フェーズまで待つ場合、ご利用の VM の可用性を最大限に保つために考慮すべき事柄がいくつかあります。 

#### <a name="paired-regions"></a>ペアになっているリージョン

各 Azure リージョンは、同じ地理的近傍内の別のリージョンとペアになっています。 それらは合わせて 1 つのリージョン ペアになります。 予定メンテナンス フェーズの間、Azure はリージョン ペアの一方のリージョンの VM だけを更新します。 たとえば、Azure は米国中北部の VM の更新中、同時に米国中南部の VM を更新することはありません。 ただし、北ヨーロッパなどのその他のリージョンは、米国東部と同時にメンテナンスされる可能性があります。 各リージョンに対して適切に VM を分散させるためには、リージョン ペアの動作を理解することが大切です。 詳細については、[Azure リージョン ペア](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)に関するページを参照してください。

#### <a name="availability-sets-and-scale-sets"></a>可用性セットとスケール セット

Azure VM にワークロードをデプロイするとき、*可用性セット*内に VM を作成して、アプリケーションの高可用性を確保することができます。 可用性セットを使用すると、再起動を必要とする停止またはメンテナンス イベントの間であっても、少なくとも 1 つの VM を確実に使用できるようになります。

可用性セット内の個々の VM は最大 20 個の更新ドメイン (UD) に分散されます。 予定メンテナンス中は、一度に 1 つの UD だけが更新されます。 UD は必ずしも順番に更新されるとは限りません。 

仮想マシン *スケール セット*は、同一の VM のセットを単一のリソースとしてデプロイして管理するために使用できる Azure コンピューティング リソースです。 スケール セットは、可用性セット内の VM と同じように、UD をまたがって自動的にデプロイされます。 可用性セットと同様に、スケール セットを使用する場合、予定メンテナンス中に更新される UD は一度に 1 つだけです。

高可用性のための VM の設定の詳細については、 [Windows 向けの VM の可用性の管理](../articles/virtual-machines/windows/manage-availability.md)に関する記事または [Linux](../articles/virtual-machines/linux/manage-availability.md) 向けの該当する記事をご覧ください。
