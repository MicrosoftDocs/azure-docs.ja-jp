---
title: Azure Firewall Manager のデプロイ概要
description: Azure Firewall Manager に必要なデプロイ手順の概要について説明します。
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: ceb6e84b31067f7289b9e003a4fb273ce717de33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079100"
---
# <a name="azure-firewall-manager-deployment-overview"></a>Azure Firewall Manager のデプロイ概要

Azure Firewall Manager をデプロイする方法は複数ありますが、次の一般的な手順をお勧めします。

## <a name="general-deployment-process"></a>一般的なデプロイ プロセス

### <a name="hub-virtual-networks"></a>ハブ仮想ネットワーク

1.  ファイアウォール ポリシーを作成する

    - 新しいポリシーの作成
<br>*or*<br>
    - 基本ポリシーを派生させてローカル ポリシーをカスタマイズします。
<br>*or*<br>
    - 既存の Azure Firewall から規則をインポートします。 複数のファイアウォールにわたって適用すべき NAT 規則はポリシーから削除してください。
1. ハブとスポークのアーキテクチャを作成する
   - Azure Firewall Manager を使用してハブ仮想ネットワークを作成し、仮想ネットワーク ピアリングを使用して、そこにスポーク仮想ネットワークをピアリングします。
<br>*or*<br>
    - 仮想ネットワークを作成して仮想ネットワーク接続を追加し、仮想ネットワーク ピアリングを使用して、そこにスポーク仮想ネットワークをピアリングします。

3. セキュリティ プロバイダーを選択し、ファイアウォール ポリシーを関連付ける。 現在、サポートされるプロバイダーは Azure Firewall だけです。

   - これは、ハブ仮想ネットワークの作成時に行います。
<br>*or*<br>
    - 既存の仮想ネットワークをハブ仮想ネットワークに変換します。 複数の仮想ネットワークを変換することもできます。

4. ハブ仮想ネットワーク ファイアウォールへのトラフィックをルーティングするユーザー定義ルートを構成する。


### <a name="secured-virtual-hubs"></a>セキュリティ保護付き仮想ハブ

1. ハブとスポークのアーキテクチャを作成する

   - Azure Firewall Manager を使用してセキュリティ保護付き仮想ハブを作成し、仮想ネットワーク接続を追加します。<br>*or*<br>
   - 仮想 WAN ハブを作成し、仮想ネットワーク接続を追加します。
2. セキュリティ プロバイダーを選択する

   - セキュリティ保護付き仮想ハブの作成中に完了します。<br>*or*<br>
   - 既存の仮想 WAN ハブをセキュリティ保護付き仮想ハブに変換します。
3. ファイアウォール ポリシーを作成してハブに関連付ける

   - Azure Firewall を使用する場合にのみ適用されます。
   - サードパーティのサービスとしてのセキュリティ (SECaaS) ポリシーは、パートナー管理エクスペリエンスを介して構成されます。
4. トラフィックをセキュリティ保護付きハブにルーティングするようにルート設定を構成する

   - トラフィックをフィルター処理とログ記録のためにセキュリティ保護付きハブに簡単にルーティングするには、スポークの仮想ネットワーク上のユーザー定義ルート (UDR) を使用せずに、セキュリティ保護付き仮想ハブのルート設定ページを使用します。

> [!NOTE]
> - 1 つのリージョンで仮想 WAN ごとに複数のハブを使用することはできません。 ただし、リージョンに複数の仮想 WAN を追加して、これを実現することができます。
> - vWAN のハブの IP 空間を重複させることはできません。
> - ハブ VNet 接続は、ハブと同じリージョンにある必要があります。
>
> その他の既知の問題については、「[Azure Firewall Manager とは](overview.md#known-issues)」を参照してください。

## <a name="convert-virtual-networks"></a>仮想ネットワークの変換

既存の仮想ネットワークをハブ仮想ネットワークに変換する場合、次の情報が当てはまります。

- 仮想ネットワークに既に Azure Firewall がある場合、既存のファイアウォールに関連付けるファイアウォール ポリシーを選択します。 ファイアウォール ポリシーによってファイアウォール規則が置き換えられている間、ファイアウォール プロビジョニング状態は更新中になります。 プロビジョニング状態中も、ファイアウォールはトラフィックを処理し続けるので、ダウンタイムは発生しません。 Firewall Manager または Azure PowerShell を使用して既存の規則をファイアウォール ポリシーにインポートできます。
- 仮想ネットワークに Azure Firewall が関連付けられていない場合、ファイアウォールがデプロイされ、新しいファイアウォールにファイアウォール ポリシーが関連付けられます。

## <a name="next-steps"></a>次のステップ

- [チュートリアル:Azure portal を使用して Azure Firewall Manager でクラウド ネットワークをセキュリティで保護する](secure-cloud-network.md)