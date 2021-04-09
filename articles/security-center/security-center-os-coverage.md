---
title: Azure Security Center でサポートされているプラットフォーム | Microsoft Docs
description: このドキュメントでは、Azure Security Center でサポートされるプラットフォームの一覧を示します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 88dc0760f320a99b0cbc99b7637dc34dd11dfecc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103465448"
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

Security Center は [Log Analytics エージェント](../azure-monitor/agents/agents-overview.md#log-analytics-agent)に依存します。 次のページの説明に従って、このエージェントでサポートされているオペレーティング システムのいずれかがマシンで実行されていることを確認してください。

* [Windows 用の Log Analytics エージェントでサポートされているオペレーティング システム ](../azure-monitor/agents/agents-overview.md#supported-operating-systems)
* [Linux 用の Log Analytics エージェントでサポートされているオペレーティング システム](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

また、Log Analytics エージェントが [Security Center にデータを送信するように適切に構成されている](security-center-enable-data-collection.md#manual-agent)ことを確認してください

Windows および Linux で使用できる特定の Security Center 機能の詳細については、「[マシンを対象とする機能](security-center-services.md)」を参照してください。

> [!NOTE]
> Azure Defender はサーバーを保護するように設計されていますが、**Azure Defender for server** の機能のほとんどは Windows 10 マシンでサポートされています。 現在サポートされていない機能の 1 つとして、[Security Center の統合 EDR ソリューション: Microsoft Defender for Endpoint](security-center-wdatp.md) があります。

## <a name="managed-virtual-machine-services"></a>マネージド仮想マシン サービス <a name="virtual-machine"></a>

仮想マシンは、Azure Kubernetes (AKS)、Azure Databricks など、いくつかの Azure マネージド サービスの一部として、顧客サブスクリプションでも作成されます。 Security Center はこれらの仮想マシンも検出し、サポートされている OS が使用可能な場合は、Log Analytics エージェントをインストールして構成できます。

## <a name="cloud-services"></a>Cloud Services <a name="cloud-services"></a>

クラウド サービスで実行する仮想マシン もサポートされます。 監視されるのは、運用スロットで実行するクラウド サービスの Web ロールと worker ロールだけです。 Cloud Services の詳細については、「[Azure Cloud Services の概要](../cloud-services/cloud-services-choose-me.md)」をご覧ください。

Azure Stack Hub に存在する VM の保護もサポートされています。 Security Center と Azure Stack Hub との統合の詳細については、[Azure Stack Hub 仮想マシンの Security Center へのオンボード](quickstart-onboard-machines.md?pivots=azure-portal#onboard-your-azure-stack-hub-vms)に関するページを参照してください。 

## <a name="next-steps"></a>次のステップ

- [Security Center による Log Analytics エージェントを使用したデータの収集](security-center-enable-data-collection.md)について確認します。
- [Security Center でデータを管理および保護する](security-center-data-security.md)方法を確認します。
- [Azure Security Center を導入するための設計上の考慮事項を計画し、理解する](security-center-planning-and-operations-guide.md)方法について説明しています。