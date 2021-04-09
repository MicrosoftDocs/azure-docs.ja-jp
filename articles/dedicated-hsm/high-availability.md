---
title: 高可用性 - Azure の専用 HSM | Microsoft Docs
description: Azure の専用 HSM の高可用性の基本的な考慮事項について説明します。 この記事には例が含まれています。
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 85aa9d355b2440535ea13c8c15e95500bac22352
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98567254"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Azure Dedicated HSM の高可用性

Azure Dedicated HSM は、Microsoft の高可用データセンターによって支えられています。 ただし、どのような高可用データセンターでも、局地的な障害や、極端な状況ではリージョン レベルの障害に対して脆弱です。 Microsoft はリージョン内の異なるデータセンターに HSM デバイスをデプロイすることで、複数のデバイスをプロビジョニングする際にデバイスが単一のラックを共有しないようにしています。 Thales HA Group 機能を使用して 1 つのリージョン内のデータセンター全体でこのような HSM をペアリングすることで、さらに高いレベルの高可用性を達成できます。 また、ディザスター リカバリーの状況でリージョンのフェールオーバーに対応するために、複数のリージョンにまたがってデバイスをペアリングすることもできます。 このような多層の高可用性構成では、どのようなデバイスの障害も自動的に対処され、アプリケーションの機能を維持することができます。 また、すべてのデータセンターには予備のデバイスとコンポーネントがオンサイトに用意されているため、デバイスが故障しても適切なタイミングで交換できます。

## <a name="high-availability-example"></a>高可用性の例

ソフトウェア レベルで高可用性を実現するための HSM デバイスの構成方法については、「Thales Luna 7 HSM 管理ガイド」を参照してください。 このドキュメントは、[Thales HSM ページ](https://thalesdocs.com/gphsm/Content/luna/network/luna_network_releases.htm)から入手できます。

次の図は、高可用アーキテクチャを示しています。 リージョン内の複数のデバイスと、別のリージョン内のペアリングされた複数のデバイスを使用しています。 このアーキテクチャは、4 つ以上の HSM デバイスと仮想ネットワーク コンポーネントを使用しています。

![高可用性の図](media/high-availability/high-availability.png)

## <a name="next-steps"></a>次のステップ

デバイスのプロビジョニングやアプリケーションの設計、アプリケーションのデプロイ前に、高可用性やセキュリティなど、サービスのすべての主要概念を十分に理解しておくことをお勧めします。
その他の概念レベルのトピックを次に示します。

* [デプロイ アーキテクチャ](deployment-architecture.md)
* [物理的なセキュリティ](physical-security.md)
* [ネットワーク](networking.md)
* [サポート可能性](supportability.md)
* [Monitoring](monitoring.md)

高可用性に対応する HSM デバイスの構成の詳細については、Thales カスタマー サポート ポータルの「管理者ガイド」のセクション 6 を参照してください。
