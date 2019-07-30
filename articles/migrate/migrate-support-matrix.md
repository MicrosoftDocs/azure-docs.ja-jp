---
title: Azure Migrate のサポート マトリックス
description: Azure Migrate サービスに対するサポートの設定および制限の概要について説明します。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: raynew
ms.openlocfilehash: b2ca1b9118ecc3d112a49bb4c79b413c46fe67cb
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810146"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrate のサポート マトリックス

[Azure Migrate サービス](migrate-overview.md)を使用すると、コンピューターを評価したり、Microsoft Azure クラウドに移行したりできます。 この記事では、Azure Migrate のシナリオやデプロイに対する一般的なサポートの設定および制限を要約しています。


## <a name="azure-migrate-versions"></a>Azure Migrate のバージョン

Azure Migrate サービスのバージョンには、次の 2 つがあります。

- **現在のバージョン**: このバージョンを使用すると、新しい Azure Migrate プロジェクトを作成したり、オンプレミスの評価を検出したり、評価と移行を調整したりできます。 [詳細情報](whats-new.md#azure-migrate-new-version)。
- **以前のバージョン**: 以前のバージョンの Azure Migrate (オンプレミスの VMware VM の評価のみがサポートされていました) を使用している顧客の場合は、現在のバージョンを使用するようにしてください。 以前のバージョンでは、新しい Azure Migrate プロジェクトを作成したり、新しい検出を実行したりできません。

## <a name="supported-migration-scenarios"></a>サポートされる移行シナリオ

次の表は、サポートされる移行シナリオをまとめたものです。

**Deployment** | **詳細*** 
--- | --- 
**オンプレミスの評価** | VMware VM および Hyper-V VM 上で実行されているオンプレミスのワークロードとデータを評価します。 Azure Migrate Server Assessment と Microsoft Data Migration Assistant (DMA) のほか、Cloudamize、Corent Tech、および Turbonomic Server を含むサードパーティ ツールを使用して評価します。
**Azure へのオンプレミスの移行** | 物理サーバー、VMware VM、Hyper-V VM、および AWS/GCP インスタンス上で実行されているワークロードとデータを Azure に移行します。 Azure Migrate Server Assessment と Azure Database Migration Service (DMS) のほか、Carbonite や CorentTech を含むサードパーティ ツールを使用して移行します。

具体的なツールのサポートは、次のように要約されます。

**ツール** | **評価/移行** | **詳細**
--- | --- | ---
Azure Migrate Server Assessment | 評価 | [Hyper-V](tutorial-prepare-hyper-v.md) と [VMware](tutorial-prepare-vmware.md) のサーバー評価を試みます。
Cloudamize | 評価 | [詳細情報](https://www.cloudamize.com/platform#tab-0)。
CorentTech | 評価 | [詳細情報](https://www.corenttech.com/)。
Turbonomic | 評価 | [詳細情報](https://turbonomic.com/solutions/technologies/azure-cloud/)。
Azure Migrate Server Migration | 移行 | [Hyper-V](tutorial-migrate-hyper-v.md) と [VMware](tutorial-migrate-vmware.md) のサーバー移行を試みます。
Carbonite | 移行 | [詳細情報](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure)。
CorentTech | 移行 | [詳細情報](https://www.corenttech.com/)。


## <a name="azure-migrate-projects"></a>Azure Migrate プロジェクト

**サポート** | **詳細**
--- | ---
Subscription | サブスクリプションでは 1 つの Azure Migrate プロジェクトを使用できます。
Azure のアクセス許可 | Azure Migrate プロジェクトを作成するには、サブスクリプションに共同作成者または所有者アクセス許可が必要です。
VMware VM  | 1 つのプロジェクトで最大 35,000 の VMware VM を評価します。
Hyper-V VM | 1 つのプロジェクトで最大 10,000 の Hyper-V VM を評価します。

プロジェクトには、VMware VM と Hyper-V VM の両方を評価の制限まで含めることができます。


## <a name="vmware-assessment-and-migration"></a>VMware の評価と移行

VMware VM に対する Azure Migrate Server Assessment と Server Migration のサポート マトリックスを[確認](migrate-support-matrix-vmware.md)してください。

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V の評価と移行

Hyper-V VM に対する Azure Migrate Server Assessment と Server Migration のサポート マトリックスを[確認](migrate-support-matrix-hyper-v.md)してください。


## <a name="next-steps"></a>次の手順

- 移行のために [VMware VM を評価](tutorial-assess-vmware.md)します。
- 移行のために [Hyper-V VM を評価](tutorial-assess-hyper-v.md)します。

