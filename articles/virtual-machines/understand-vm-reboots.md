---
title: VM の再起動について
description: VM の再起動について - メンテナンスとダウンタイム
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: af371a8f7da5ef32e95d4096b69c5d52ce3e3700
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510401"
---
# <a name="understand-vm-reboots---maintenance-vs-downtime"></a>VM の再起動について - メンテナンスとダウンタイム
Azure の仮想マシンに影響する可能性のあるシナリオには、計画外のハードウェア メンテナンス、予期しないダウンタイム、および計画メンテナンスの 3 つがあります。

## <a name="unplanned-hardware-maintenance-event"></a>計画外のハードウェア メンテナンス イベント
計画外のハードウェア メンテナンスは、物理マシンに関連するハードウェアまたはプラットフォーム コンポーネントの故障が起こることを Azure プラットフォームが予測した場合に発生します。 プラットフォームが故障を予測すると、そのハードウェアでホストされている仮想マシンへの影響を軽減するために、計画外のハードウェア メンテナンス イベントが発生します。 Azure では、[ライブ マイグレーション](./maintenance-and-updates.md) テクノロジを使用して、障害が発生したハードウェアから正常な物理マシンに仮想マシンを移行します。 ライブ マイグレーションは、仮想マシンを短時間一時停止するだけの VM 保持操作です。 メモリ、開いているファイル、ネットワーク接続は保持されますが、イベントの前後にパフォーマンスが低下する可能性があります。 ライブ マイグレーションを使用できない場合、以下で説明する VM で予期しないダウンタイムが発生します。


## <a name="unexpected-downtime"></a>予期しないダウンタイム
予期しないダウンタイムは、ハードウェアまたは仮想マシンの物理インフラストラクチャが予期せず失敗した場合に発生します。 こうした不具合には、ネットワーク障害、ローカル ディスク障害、またはその他のラック レベルでの障害が挙げられます。 障害が検知されると、Azure プラットフォームは、同じデータセンター内の正常な物理マシンに仮想マシンを自動的に移行 (復旧) します。 復旧中に、仮想マシンでダウンタイム (再起動) が発生し、場合によっては一時ドライブが失われることがあります。 接続されている OS とデータ ディスクは常に保持されます。

仮想マシンでも、データセンター全体やリージョン全体に影響する停電や災害といった予期しない事象によってダウンタイムが発生することがあります。 こうしたシナリオにおいて、Azure は[可用性ゾーン](../availability-zones/az-overview.md)や[リージョン ペア](regions.md#region-pairs)といった保護オプションを提供します。

## <a name="planned-maintenance-events"></a>計画メンテナンス イベント
計画メンテナンス イベントは、全体の信頼性、パフォーマンス、仮想マシン上で実行されるプラットフォームのインフラストラクチャのセキュリティを向上させるために、基になる Azure プラットフォームに対して Microsoft が実行する定期的な更新です。 これらの更新のほとんどは、仮想マシンや Cloud Services に影響を及ぼすことなく実行されます (「[再起動を必要としないメンテナンス](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)」を参照してください)。 Azure プラットフォームでは、可能な限り VM 保持メンテナンスを使用しようとしますが、基盤となるインフラストラクチャに必要な更新を適用するために、仮想マシンの再起動が必要になる場合もまれにあります。 この場合、適切な時間帯に VM のメンテナンスを開始することで、メンテナンスによる再デプロイ操作を伴う Azure の計画メンテナンスを実行できます。 詳細については、[仮想マシンの計画メンテナンス](maintenance-and-updates.md)に関する記事を参照してください。

## <a name="reduce-downtime"></a>ダウンタイムの短縮
前述のようなイベントが 1 つ以上発生した場合にダウンタイムの影響を低減するため、下記のような高可用性のためのベスト プラクティスを仮想マシンに適用することをお勧めします。

* [Availability Zones](../availability-zones/az-overview.md) を使用してデータセンターの障害から保護する
* 冗長性実現のために複数の仮想マシンを[可用性セット](availability-set-overview.md)内に構成する
* [Linux のスケジュールされたイベント](/linux/scheduled-events.md)または [Windows のスケジュールされたイベント](/windows/scheduled-events.md)を使用して、VM に影響するイベントに事前に対応する
* 各アプリケーション層に対して別々の可用性セットを構成する
* [ロード バランサー](../load-balancer/load-balancer-overview.md)と可用性ゾーンまたはセットを結合する

## <a name="next-steps"></a>次のステップ
Azure の可用性オプションの詳細については、[可用性の概要](availability.md)に関するページを参照してください。
