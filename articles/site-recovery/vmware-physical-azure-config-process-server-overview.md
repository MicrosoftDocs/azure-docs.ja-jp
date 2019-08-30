---
title: Azure Site Recovery の構成サーバー、プロセス サーバー、およびマスター ターゲット サーバーについて | Microsoft Docs
description: この記事では、Azure Site Recovery を使ってオンプレミスの VMware VM から Azure へのディザスター リカバリーを設定するときに使われる構成サーバー、プロセス サーバー、およびマスター ターゲット サーバーの概要を説明します
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: raynew
ms.openlocfilehash: d835eda044c2a6d1e0e7c678073711e45fde7395
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972139"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Site Recovery のコンポーネント (構成、プロセス、およびマスター ターゲット) について

この記事では、[Site Recovery](site-recovery-overview.md) サービスを使用して VMware VM と物理サーバーを Azure にレプリケートするときに使用される構成サーバー、プロセス サーバー、およびマスター ターゲット サーバーについて説明します。

## <a name="configuration-server"></a>構成サーバー

オンプレミスの VMware VM と物理サーバーをディザスター リカバリーする場合、オンプレミスに展開された Site Recovery 構成サーバーが必要です。

**設定** | **詳細** | **リンク**
--- | --- | ---
**コンポーネント**  | 構成サーバー マシンでは、構成サーバー、プロセス サーバー、マスター ターゲット サーバーなど、オンプレミスのすべての Site Recovery コンポーネントを実行します。<br/><br/> 構成サーバーを設定するときに、すべてのコンポーネントが自動的にインストールされます。 | 構成サーバーに関する FAQ を[確認する](vmware-azure-common-questions.md#configuration-server)。
**ロール** | 構成サーバーは、オンプレミスと Azure の間の通信を調整し、データのレプリケーションを管理します。 | Azure への [VMware](vmware-azure-architecture.md) および[物理サーバー](physical-azure-architecture.md)のディザスター リカバリーのアーキテクチャの詳細を確認する。
**VMware の要件** | オンプレミスの VMware VM のディザスター リカバリーを行うには、オンプレミスの高可用性 VMware VM として構成サーバーをインストールして実行する必要があります。 | 前提条件を[確認する](vmware-azure-deploy-configuration-server.md#prerequisites)。
**VMware の展開** | ダウンロードした OVA テンプレートを使用して構成サーバーを展開することをお勧めします。 この方法を使用すると、すべての要件と前提条件に準拠した構成サーバーを簡単に設定できます。<br/><br/> OVA テンプレートを使用して VMware VM を展開することが何らかの理由でできない場合は、物理マシンによってディザスター リカバリーを行うための以下の説明に従って、手動で構成サーバー マシンを設定できます。 | OVA テンプレートを使用して[展開する](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template)。
**物理サーバーの要件** | オンプレミスの物理サーバーでディザスター リカバリーを行う場合は、構成サーバーを手動で設定します。 | 前提条件を[確認する](physical-azure-set-up-source.md#prerequisites)。
**物理サーバーの展開** | 構成サーバーを VMware VM としてインストールできない場合は、物理サーバーにインストールすることができます。 | 構成サーバーを手動で[展開する](physical-azure-set-up-source.md#set-up-the-source-environment)。


## <a name="process-server"></a>プロセス サーバー

**設定** | **詳細** | **リンク**
--- | --- | ---
**Deployment**  | オンプレミスの VMware VM と物理サーバーをディザスター リカバリーおよびレプリケーションする場合、オンプレミスにプロセス サーバーが必要です。 既定では、プロセス サーバーは構成サーバーの展開時に構成サーバーにインストールされます。 | [詳細情報](vmware-azure-architecture.md?#architectural-components)。
**役割 (オンプレミス)** | - レプリケーションが有効になっているマシンからレプリケーション データを受け取ります。<br/> - レキャッシュ、圧縮、暗号化によってプリケーション データを最適化し、Azure Storage に送信します。<br/> - レプリケートするオンプレミスの VMware VM と物理サーバーに対して Site Recovery Mobility Service のプッシュ インストールを実行します。<br/> - オンプレミス マシンの自動検出を実行します。 | [詳細情報](vmware-physical-azure-config-process-server-overview.md#process-server)。 
**役割 (Azure からのフェールバック)** | オンプレミス サイトからのフェールオーバー後に、オンプレミスの場所へのフェールバックを処理するために、Azure で Azure VM としてプロセス サーバーを設定します。<br/><br/> Azure 上のプロセス サーバーは一時的なものです。 Azure VM は、フェールバックの完了後に削除できます。 | [詳細情報](vmware-azure-set-up-process-server-azure.md)。
**スケーリング** | 大規模な展開では、オンプレミスで追加のスケール アウト プロセス サーバーを設定することができます。 追加のサーバーでは、レプリケートする多数のマシンおよび大量のレプリケーション トラフィックを処理することによって、容量をスケール アウトします。<br/><br/> レプリケーション トラフィックを負荷分散するために、2 つのプロセス サーバー間でマシンを移動できます。 | [詳細情報](vmware-azure-set-up-process-server-scale.md)。


## <a name="master-target-server"></a>マスター ターゲット サーバー

マスター ターゲット サーバーは、Azure からのフェールバック中にレプリケーション データを処理します。

- 構成サーバーに既定でインストールされます。
- 大規模なデプロイでは、フェールバック用に別のマスター ターゲット サーバーを追加できます。


## <a name="next-steps"></a>次の手順
- VMware VM および物理サーバーのディザスター リカバリーの[アーキテクチャ](vmware-azure-architecture.md)を確認します。
- VMware VM および物理サーバーから Azure へのディザスター リカバリーの[要件と前提条件](vmware-physical-azure-support-matrix.md)を確認します。 
