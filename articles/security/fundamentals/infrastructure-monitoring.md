---
title: Azure インフラストラクチャの監視
description: 脆弱性のスキャンなど、Azure の実稼働環境ネットワークのインフラストラクチャの監視の側面について説明します。
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 7b75c9dc874a41d4221c55a8b00dd12d943e80fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87542944"
---
# <a name="azure-infrastructure-monitoring"></a>Azure インフラストラクチャの監視   

## <a name="configuration-and-change-management"></a>構成と変更管理
Azure では、構成設定、およびハードウェア、ソフトウェアおよびネットワーク デバイスのベースライン構成が年 1 回審査および更新されます。 変更がテスト環境から運用環境に導入される前に、開発、検証、および承認されていること。

Azure ベースのサービスに必要なベースライン構成は、サービス チームと Azure のセキュリティおよびコンプライアンス チームによって審査されます。 サービス チームの審査は、運用サービスを展開する前の検証作業の一環として行われます。

## <a name="vulnerability-management"></a>脆弱性の管理
セキュリティ更新管理を行うことにより、システムを既知の脆弱性から保護できます。 Azure では、統合されたデプロイ システムを使用して、Microsoft ソフトウェアのセキュリティ更新プログラムの配布とインストールを管理します。 Azure は、Microsoft セキュリティ レスポンス センター (MSRC) のリソースを利用することもできます。 MSRC は、毎日 24 時間体制で、セキュリティ インシデントおよびクラウドの脆弱性を識別、監視、対応、および解決しています。

## <a name="vulnerability-scanning"></a>脆弱性のスキャン
脆弱性のスキャンは、サーバー オペレーティング システム、データベース、ネットワーク デバイス上で実行されています。 脆弱性スキャンは、四半期に 1 回以上実行します。 Azure は、独立監査機関と契約して、Azure 境界の侵入テストを実行します。 レッド チームの演習も定期的に実行され、結果はセキュリティの向上のために使用されます。

## <a name="protective-monitoring"></a>保護的監視
Azure Security には、有効な監視の要件が定義されています。 サービス チームは、これらの要件に従って有効な監視ツールを構成します。 有効な監視ツールには、Microsoft Monitoring Agent (MMA)、System Center Operations Manager などがあります。 これらのツールは、即時の対応が必要な状況になった場合に Azure セキュリティ担当者にタイム アラートを提供するように構成されています。

## <a name="incident-management"></a>インシデント管理
Microsoft では、インシデント発生時の対応を調整するためのセキュリティ インシデント管理プロセスを実装しています。

Microsoft がその装置または施設に保管されている顧客データへの不正なアクセスを検知した場合、または装置または施設への不正なアクセスにより顧客データの損失、漏洩、または改ざんが発生したことを認識した場合、Microsoft は以下の措置を取ります。

- セキュリティ インシデントが発生したお客様に直ちに通知する。
- 直ちにセキュリティ インシデントを調査し、それに関する詳細情報をお客様に提供する。
- セキュリティ インシデントによる影響を低減して、発生したる損害を最小限に抑えるための妥当で迅速な手続きを実行する。

役割が定義され、責任が割り当てられたインシデント管理フレームワークが確立されています。 Azure securityインシデント管理チームは、エスカレーションを含むセキュリティ インシデントの管理を担当し、必要な場合に専門家チームが関与することを保証します。 Azure 運用マネージャーは、セキュリティおよびプライバシー インシデントの調査と解決を監督します。

## <a name="next-steps"></a>次のステップ
Microsoft が提供する Azure アーキテクチャの保護の詳細については、以下を参照してください。

- [Azure の施設、建物、および物理上のセキュリティ](physical-security.md)
- [Azure インフラストラクチャの可用性](infrastructure-availability.md)
- [Azure 情報システムのコンポーネントと境界](infrastructure-components.md)
- [Azure ネットワーク アーキテクチャ](infrastructure-network.md)
- [Azure 実稼働環境のネットワーク](production-network.md)
- [Azure SQL Database のセキュリティ機能](infrastructure-sql.md)
- [Azure の実稼働環境の運用と管理](infrastructure-operations.md)
- [Azure インフラストラクチャの整合性](infrastructure-integrity.md)
- [Azure での顧客データの保護](protection-customer-data.md)
