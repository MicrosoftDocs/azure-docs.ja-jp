---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 12/14/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: c26c037455b6d14a906894ec39bf46630826950b
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551484"
---
Azure は、定期的にプラットフォームを更新して、仮想マシンのホスト インフラストラクチャの信頼性、パフォーマンス、セキュリティの向上に努めています。 これらの更新は、ホスティング環境のソフトウェア コンポーネントの修正から、ネットワーク コンポーネントのアップグレード、ハードウェアの使用停止まで、広い範囲に及びます。 これらの更新のほとんどは、ホストされている仮想マシンに影響を及ぼしません。 ただし、更新プログラムが影響を及ぼす場合に Azure が最も影響の小さな更新方法を選択することがあります。

- 再起動を伴わない更新が可能な場合、VM は、ホストの更新中に一時停止するか、または更新済みのホストにライブ移行します。

- 再起動を伴うメンテナンスの場合は、メンテナンスの予定日時が知らされます。 Azure により、都合の良いときに自分でメンテナンスを開始できる時間枠も与えられます。 Azure は、計画されたプラットフォーム メンテナンスのために VM を再起動する必要があるケースを減らすためのテクノロジに投資しています。 

このページでは、2 種類のメンテナンスが Azure でどのように実行されるかについて説明します。 計画外のイベント (停止) の詳細については、Azure での Windows 仮想マシンの可用性の管理 ([Windows](../articles/virtual-machines/windows/manage-availability.md) または [Linux](../articles/virtual-machines/linux/manage-availability.md)) に関する記事を参照してください。

[Windows](../articles/virtual-machines/windows/scheduled-events.md) または [Linux](../articles/virtual-machines/linux/scheduled-events.md) 向けの Scheduled Events を使用して、今後のメンテナンスに関する VM 内通知を受け取ることができます。

計画メンテナンスを管理する場合の「方法」については、[Linux](../articles/virtual-machines/linux/maintenance-notifications.md) または [Windows](../articles/virtual-machines/windows/maintenance-notifications.md) の計画メンテナンスの通知の処理に関するページを参照してください。

## <a name="maintenance-not-requiring-a-reboot"></a>再起動を必要としないメンテナンス

再起動を必要としないほとんどのメンテナンスでは、VM の一時停止を 10 秒未満に留めることを目標としています。 場合によっては、メモリ保持メンテナンス メカニズムが使用されます。この場合、VM は最大で 30 秒間一時停止状態になり、RAM 内のメモリは保持されます。 その後仮想マシンが再開され、仮想マシンの時計が自動的に同期されます。 Azure では、ライブ マイグレーション テクノロジの使用を拡大すると共に、一時停止期間を短縮するためのメモリ保持メンテナンス メカニズムの改善に努めています。

これらの再起動を伴わないメンテナンス操作は障害ドメインごとに適用され、警告の正常性シグナルを受信した場合、進行が停止します。 

こうしたタイプの更新が一部のアプリケーションに影響を及ぼすことがあります。 VM を別のホストにライブ移行する場合、一部の機密性の高いワークロードでは、VM の一時停止に至るまでの数分間にわずかなパフォーマンスの低下が見られることがあります。 そのようなアプリケーションでは、[Windows](../articles/virtual-machines/windows/scheduled-events.md) または [Linux](../articles/virtual-machines/linux/scheduled-events.md) 向けの Scheduled Events を使用して VM のメンテナンスを準備することでメリットが得られ、Azure のメンテナンス中に影響を受けません。 Azure では、このような非常に機密性の高いアプリケーションのためのメンテナンス管理機能にも取り組んでいます。 


## <a name="maintenance-requiring-a-reboot"></a>再起動を伴うメンテナンス

計画メンテナンスで VM の再起動が必要になるまれなケースでは、事前に通知が届きます。 計画済みメンテナンスには、"セルフサービス期間" と "予定メンテナンス期間" の 2 つのフェーズがあります。

**セルフサービス期間**では、ご利用の VM に対するメンテナンスをユーザーが開始することができます。 この期間に、個々の VM にその状態を照会し、自分が最後に行ったメンテナンス要求の結果を確認します。

セルフサービス メンテナンスを開始すると、VM は、既に更新済みのノードに再デプロイされます。 VM が再起動されるため、一時ディスクは失われ、仮想ネットワーク インターフェイスに関連付けられた動的 IP アドレスは更新されます。

セルフサービス メンテナンスを開始したときに、そのプロセス中にエラーが発生して操作が停止された場合、VM は更新されませんセルフサービス メンテナンスを再試行するオプションが提供されます。 

セルフサービス期間が過ぎると、**予定メンテナンス期間**が始まります。 この期間中、ユーザーはメンテナンス期間について照会することはできますが、メンテナンスを開始することはできません。

再起動を必要とするメンテナンスの管理については、[Linux](../articles/virtual-machines/linux/maintenance-notifications.md) または [Windows](../articles/virtual-machines/windows/maintenance-notifications.md) の計画メンテナンスの通知の処理に関するページを参照してください。 

### <a name="availability-considerations-during-scheduled-maintenance"></a>予定メンテナンス実施時の可用性に関する考慮事項 

予定メンテナンス期間まで待つ場合、ご利用の VM の可用性を最大限に保つために考慮すべき事柄がいくつかあります。 

#### <a name="paired-regions"></a>ペアになっているリージョン

各 Azure リージョンは、同じ geo 内の別のリージョンと組み合わせて、リージョン ペアにして使用します。 予定メンテナンス フェーズでは、リージョン ペアの一方のリージョンの VM だけが更新されます。 たとえば、米国中北部の VM が更新されるとき、同時に米国中南部の VM が更新されることはありません。 ただし、北ヨーロッパなどのその他のリージョンは、米国東部と同時にメンテナンスされる可能性があります。 各リージョンに対して適切に VM を分散させるためには、リージョン ペアの動作を理解することが大切です。 詳細については、[Azure リージョン ペア](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)に関するページを参照してください。

#### <a name="availability-sets-and-scale-sets"></a>可用性セットとスケール セット

Azure VM にワークロードをデプロイするとき、可用性セット内に VM を作成することで、アプリケーションの高可用性を確保することができます。 これにより、障害発生時や再起動が多いメンテナンス イベントの間であっても、少なくとも 1 つの VM を確実に使用できるようになります。

可用性セット内の個々の VM は最大 20 個の更新ドメイン (UD) に分散されます。 予定メンテナンス中は、どの時点において 1 つの更新ドメインのみが更新されます。 更新ドメインが更新される順序は、必ずしも順番どおりではありません。 

Virtual Machine Scale Sets は、同一の VM のセットをデプロイして管理するために使用できる Azure コンピューティング リソースです。 スケール セットは、可用性セット内の VM と同じように、複数の更新ドメインに対して分散するように自動的にデプロイされます。 可用性セットと同様、スケール セットの場合も、予定メンテナンス中に更新される更新ドメインは一度に 1 つだけです。

高可用性を実現するための VM の構成について詳しくは、[Windows](../articles/virtual-machines/windows/manage-availability.md) 仮想マシンの可用性管理に関するページまたは [Linux](../articles/virtual-machines/linux/manage-availability.md) 仮想マシンの可用性管理に関するページを参照してください。
