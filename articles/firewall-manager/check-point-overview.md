---
title: Check Point Cloudguard Connect を使用して Azure 仮想ハブをセキュリティで保護する
description: Azure 仮想ハブをセキュリティで保護するための Check Point CloudGuard Connect について説明します
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: victorh
ms.openlocfilehash: 3c61dc689d19e1a7d6f9b6dbefae846e9458d750
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93146860"
---
# <a name="secure-virtual-hubs-using-check-point-cloudguard-connect"></a>Check Point Cloudguard Connect を使用して仮想ハブをセキュリティで保護する

Check Point CloudGuard Connect は Azure Firewall Manager の信頼されたセキュリティ パートナーです。 グローバルに分散されたブランチ オフィスからインターネット (B2I) への接続または仮想ネットワークからインターネット (V2I) への接続を、高度な脅威防止を使用して保護します。 

Azure Firewall Manager での簡単な構成により、ブランチ ハブおよび仮想ネットワークの接続を、CloudGuard Connect のサービスとしてのセキュリティ (SECaaS) を通してインターネットにルーティングできます。 ハブから Check Point クラウド サービスに IPsec VPN トンネル内を転送中のトラフィックが保護されます。

Check Point ポータルで自動同期を有効にすると、Azure portal で "*保護付き*" とマークされたリソースは自動的にセキュリティで保護されます。 アセットを 2 回管理する必要はありません。 セキュリティで保護することを Azure portal で 1 回選択するだけです。

Check Point により、複数のセキュリティ サービスが 1 つにまとめられます。 統合されたセキュリティ トラフィックは、1 回だけ解読されて、1 つのパスで検査されます。 アプリケーション制御、URL フィルター、コンテンツ認識 (DLP) により、安全な Web の使用が強制され、データが保護されます。 IPS とウイルス対策により、ユーザーは既知のネットワーク攻撃から保護されます。 ボット対策により、コマンド アンド コントロール サーバーへの接続はブロックされ、ホストが感染した場合はユーザーに警告されます。

脅威エミュレーション (サンドボックス化) により、ユーザーは不明な脅威やゼロデイ脅威から保護されます。 Check Point SandBlast Zero-Day Protection は、クラウドでホストされたサンドボックス化テクノロジであり、ファイルの検疫と検査が迅速に行われます。 仮想サンドボックスで実行され、悪意のある動作がネットワークに入る前に検出されます。 損害が発生する前に脅威が防がれるため、スタッフの貴重な時間を脅威への対応に使わずに済みます。 

## <a name="deployment-example"></a>デプロイの例

Check Point CloudGuard Connect を信頼された Azure セキュリティ パートナーとしてデプロイする方法については、次のビデオをご覧ください。

> [!VIDEO https://www.youtube.com/embed/C8AuN76DEmU]

## <a name="next-steps"></a>次のステップ

- [セキュリティ パートナー プロバイダーのデプロイ](deploy-trusted-security-partner.md)