---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/15/2020
ms.author: cephalin
ms.openlocfilehash: 0fccb1ed092190f88d197c9bf2cd17678a4d1731
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "83649031"
---
Azure VM、ストレージ、ネットワーク接続、Web フレームワーク、管理、統合機能を含む App Service のプラットフォーム コンポーネントは、積極的に保護され、強化されています。 App Service は、徹底したコンプライアンス チェックを継続的に行い、以下の点を確認しています。

- お客様のアプリのリソースが、他のお客様の Azure リソースから[セキュリティで保護](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)されていること。
- 新たに発見された脆弱性に対応できるように、[VM インスタンスとランタイム ソフトウェアが定期的に更新されている](../articles/app-service/overview-patch-os-runtime.md)こと。 
- アプリと他の Azure リソース ([SQL Database](https://azure.microsoft.com/services/sql-database/) など) 間のシークレット (接続文字列など) の通信が Azure 内にとどまり、ネットワーク境界を越えないこと。 保存時にシークレットが常に暗号化されていること。
- [ハイブリッド接続](../articles/app-service/app-service-hybrid-connections.md)などの App Service 接続機能を介したすべての通信が暗号化されていること。 
- Azure PowerShell、Azure CLI、Azure SDK、REST API などのリモート管理ツールとの接続がすべて暗号化されていること。
- 24 時間体制の脅威管理によって、マルウェア、分散型サービス拒否 (DDoS)、man-in-the-middle (MITM) などの脅威からインフラストラクチャとプラットフォームが保護されていること。

Azure のインフラストラクチャとプラットフォームのセキュリティの詳細については、 [Azure セキュリティ センター](https://azure.microsoft.com/overview/trusted-cloud/)を参照してください。