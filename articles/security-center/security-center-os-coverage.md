---
title: Azure Security Center でサポートされているプラットフォーム | Microsoft Docs
description: このドキュメントでは、Azure Security Center でサポートされるプラットフォームの一覧を示します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: f02e45804234fc8f8aa2b966db7ddce5efc9cfd1
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998834"
---
# <a name="supported-platforms"></a>サポートされているプラットフォーム 

このページには Azure Security Center でサポートされているプラットフォームと環境が示されています。

## <a name="combinations-of-environments"></a>環境の組み合わせ <a name="vm-server"></a>

Azure Security Center は、さまざまな種類のハイブリッド環境で仮想マシンとサーバーをサポートします。

* Azure のみ
* Azure とオンプレミス
* Azure とその他のクラウド
* Azure、その他のクラウド、およびオンプレミス

Azure サブスクリプションでアクティブ化された Azure 環境では、Azure Security Center によって、サブスクリプション内にデプロイされている IaaS リソースが自動的に検出されます。

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

Security Center は [Log Analytics エージェント](../azure-monitor/platform/agents-overview.md#log-analytics-agent)に依存します。 次のページの説明に従って、このエージェントでサポートされているオペレーティング システムのいずれかがマシンで実行されていることを確認してください。

* [Windows 用の Log Analytics エージェントでサポートされているオペレーティング システム ](../azure-monitor/platform/agents-overview.md#supported-operating-systems)
* [Linux 用の Log Analytics エージェントでサポートされているオペレーティング システム](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

また、Log Analytics エージェントが [Security Center にデータを送信するように適切に構成されている](security-center-enable-data-collection.md#manual-agent)ことを確認してください

> [!TIP]
> Windows および Linux で使用できる特定の Security Center 機能の詳細については、「[マシンを対象とする機能](security-center-services.md)」を参照してください。

## <a name="managed-virtual-machine-services"></a>マネージド仮想マシン サービス <a name="virtual-machine"></a>

仮想マシンは、Azure Kubernetes (AKS)、Azure Databricks など、いくつかの Azure マネージド サービスの一部として、顧客サブスクリプションでも作成されます。 Security Center はこれらの仮想マシンも検出し、サポートされている OS が使用可能な場合は、Log Analytics エージェントをインストールして構成できます。

## <a name="cloud-services"></a>Cloud Services <a name="cloud-services"></a>

クラウド サービスで実行する仮想マシン もサポートされます。 監視されるのは、運用スロットで実行するクラウド サービスの Web ロールと worker ロールだけです。 Cloud Services の詳細については、「[Azure Cloud Services の概要](../cloud-services/cloud-services-choose-me.md)」をご覧ください。

Azure Stack に存在する VM の保護もサポートされています。 Security Center と Azure Stack との統合の詳細については、[Azure Stack 仮想マシンの Security Center へのオンボード](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [Security Center による Log Analytics エージェントを使用したデータの収集](security-center-enable-data-collection.md)について確認します。
- [Security Center でデータを管理および保護する](security-center-data-security.md)方法を確認します。
- [Azure Security Center を導入するための設計上の考慮事項を計画し、理解する](security-center-planning-and-operations-guide.md)方法について説明しています。