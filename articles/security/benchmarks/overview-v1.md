---
title: Azure セキュリティ ベンチマーク V1 の概要
description: Azure セキュリティ ベンチマーク V1 の概要
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 39b6a5452c4bc0cba1e1153c266b83e87f6209ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90059099"
---
# <a name="overview-of-the-azure-security-benchmark-v1"></a>Azure セキュリティ ベンチマーク (v1) の概要

> [!NOTE]
> 最新の Azure セキュリティ ベンチマークは[こちら](overview.md)からご利用いただけます。

Azure セキュリティ ベンチマークには、Azure 上のアプリケーションとデータのセキュリティを向上させるための推奨事項が含まれています。

このベンチマークの対象は、クラウド中心のコントロール領域です。 これらのコントロールは、Center for Internet Security (CIS) コントロール バージョン 7.1 で説明されているような、よく知られたセキュリティ ベンチマークと一貫性があります。

Azure セキュリティ ベンチマークでは、次のコントロールが使用されます。 

- [ネットワークのセキュリティ](security-control-network-security.md)
- [ログ記録と監視](security-control-logging-monitoring.md)
- [ID およびアクセス制御](security-control-identity-access-control.md)
- [データ保護](security-control-data-protection.md)
- [脆弱性の管理](security-control-vulnerability-management.md)
- [インベントリと資産の管理](security-control-inventory-asset-management.md)
- [セキュリティで保護された構成](security-control-secure-configuration.md)
- [マルウェアからの防御](security-control-malware-defense.md)
- [データの復旧](security-control-data-recovery.md)
- [インシデント対応](security-control-incident-response.md)
- [侵入テストとレッド チーム演習](security-control-penetration-tests-red-team-exercises.md)

[Azure セキュリティ ベンチマーク v1 の Excel スプレッドシート](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets)をダウンロードすることもできます。

## <a name="azure-security-benchmark-recommendations"></a>Azure セキュリティ ベンチマークの推奨事項 

各推奨事項には次の情報が含まれます。 

- **Azure ID**: 推奨事項に対応する Azure セキュリティ ベンチマーク ID。 
- **CIS ID**: この推奨事項に対応する CIS ベンチマークの推奨事項。  
- **責任**: お客様またはサービス プロバイダー (またはその両方) が、この推奨事項の実装に責任があるかどうか。 セキュリティ責任は、パブリック クラウドで共有されます。 一部のセキュリティ コントロールはクラウド サービス プロバイダーのみが使用できるため、プロバイダーはそれらに対処する必要があります。 これらは一般的な見解です。個々のサービスによっては、責任が Azure セキュリティ ベンチマークに記載されているものと異なることがあります。 これらの違いについては、個々のサービスのベースラインの推奨事項で説明されています。 
- **[詳細]** :推奨事項の根拠と、その実装方法に関するガイダンスへのリンク。 推奨事項が Azure Security Center によってサポートされている場合は、その情報も一覧表示されます。

Azure セキュリティ ベンチマークの取り組みに関するフィードバックと積極的な参加をお待ちしております。 Azure セキュリティ ベンチマーク チームに直接情報を提供したい場合は、[https://aka.ms/AzSecBenchmark](https://aka.ms/AzSecBenchmark) のフォームに入力してください。

## <a name="next-steps"></a>次の手順

- 最初のセキュリティ コントロール: [ネットワーク セキュリティ](security-control-network-security.md)に関するページを参照する
- 「[Azure セキュリティ ベンチマークの概要](introduction.md)」を読む
- [Azure セキュリティ ベンチマーク v1 の Excel スプレッドシート](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets)をダウンロードする
