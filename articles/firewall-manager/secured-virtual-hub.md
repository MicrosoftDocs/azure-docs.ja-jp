---
title: セキュリティ保護付き仮想ハブとは
description: セキュリティ保護付き仮想ハブについて
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: victorh
ms.openlocfilehash: c840bf9e82b8dcdb1fbf9b380ea847b3d1b08dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91948070"
---
# <a name="what-is-a-secured-virtual-hub"></a>セキュリティ保護付き仮想ハブとは

仮想ハブは、他のリソースからの接続を可能にする Microsoft が管理する仮想ネットワークです。 Azure portal の仮想 WAN から仮想ハブを作成すると、仮想ハブ VNet とゲートウェイ (オプション) がそのコンポーネントとして作成されます。

*セキュリティ保護付き* 仮想ハブは、Azure Firewall Manager によって構成された関連するセキュリティ ポリシーとルーティング ポリシーを持つ [Azure Virtual WAN ハブ](../virtual-wan/virtual-wan-about.md#resources)です。 セキュリティ保護付き仮想ハブを使用すると、トラフィック ガバナンスと保護のためのネイティブ セキュリティ サービスを使用して、ハブアンドスポーク アーキテクチャと推移的アーキテクチャを簡単に作成できます。 

セキュリティ保護付き仮想ハブを使用して、仮想ネットワーク (V2V)、仮想ネットワークおよびブランチ オフィス (B2V) の間のトラフィック、インターネットへのトラフィック (B2I/V2I) をフィルター処理できます。 セキュリティ保護付き仮想ハブには自動ルーティング機能があります。 ファイアウォールを介してトラフィックをルーティングするために、独自の UDR (ユーザー定義ルート) を構成する必要はありません。

Azure Firewall、サードパーティのサービスとしてのセキュリティ (SECaaS) プロバイダー、またはその両方など、ネットワーク トラフィックを保護および管理するために必要なセキュリティ プロバイダーを選択することができます。 現在、セキュリティ保護付きハブは、ブランチ間 (B2B) フィルター処理と複数のハブにまたがるフィルター処理をサポートしていません。 詳細については、「[Azure Firewall Manager とは](overview.md#known-issues)」を参照してください。 

## <a name="create-a-secured-virtual-hub"></a>セキュリティ保護付き仮想ハブを作成する

Azure portal で Firewall Manager を使用して、新しいセキュリティ保護付き仮想ハブを作成するか、以前に Azure Virtual WAN を使用して以前作成した既存の仮想ハブを変換することができます。

## <a name="next-steps"></a>次のステップ

セキュリティ保護付き仮想ハブを作成し、それを使用してハブアンドスポーク ネットワークを保護および管理する方法については、[チュートリアル:Azure portal を使用して Azure Firewall Manager でクラウド ネットワークをセキュリティで保護する](secure-cloud-network.md)を参照してください。