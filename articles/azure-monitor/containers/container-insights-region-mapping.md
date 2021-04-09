---
title: Container insights のリージョン マッピング
description: Container insights、Log Analytics ワークスペース、カスタム メトリックの間でサポートされるリージョン マッピングについて説明します。
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: f9e910b1352109608becb82609e85e26d27d2cd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728877"
---
# <a name="region-mappings-supported-by-container-insights"></a>Container insights によってサポートされるリージョン マッピング

 Container insights を有効にした場合、Log Analytics ワークスペースと AKS クラスターのリンク、および Azure Monitor に送信されるカスタム メトリックの収集は、特定のリージョンでのみサポートされます。

## <a name="log-analytics-workspace-supported-mappings"></a>Log Analytics ワークスペースでサポートされるマッピング

サポートされている AKS リージョンの一覧は[リージョン別に利用できる製品](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)に関するページを参照してください。 Log Analytics ワークスペースは、次の表に示すリージョンを除き、同じリージョンにある必要があります。 更新プログラムについては、[AKS リリース ノート](https://github.com/Azure/AKS/releases)をご覧ください。


|**AKS クラスター リージョン** | **Log Analytics ワークスペース リージョン** |
|-----------------------|------------------------------------|
|**アフリカ** | |
|SouthAfricaNorth |西ヨーロッパ |
|SouthAfricaWest |西ヨーロッパ |
|**オーストラリア** | |
|AustraliaCentral2 |AustraliaCentral |
|**ブラジル** | |
|BrazilSouth | SouthCentralUS |
|**カナダ** ||
|CanadaEast |CanadaCentral |
|**ヨーロッパ** | |
|FranceSouth |FranceCentral |
|UKWest |UKSouth |
|**インド** | |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**日本** | |
|JapanWest |JapanEast |
|**韓国** | |
|KoreaSouth |KoreaCentral |
|**米国** | |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|


<sup>1</sup> 容量の制約により、新しいリソースを作成するときにリージョンを使用できません。 これには Log Analytics ワークスペースが含まれます。 ただし、リージョン内のリンクされた既存のリソースは引き続き動作します。

## <a name="custom-metrics-supported-regions"></a>カスタム メトリックでサポートされるリージョン

Azure Kubernetes Services (AKS) クラスター ノードおよびポッドからのメトリックの収集は、次の [Azure リージョン](../essentials/metrics-custom-overview.md#supported-regions)でのみカスタム メトリックとしての公開がサポートされています。

## <a name="next-steps"></a>次のステップ

AKS クラスターの監視を開始するには、[Container insights を有効にする方法](container-insights-onboard.md)に関するページを確認し、監視を有効にするための要件と使用できる方法を理解してください。  
