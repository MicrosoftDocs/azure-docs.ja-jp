---
title: Azure Site Recovery の構成、プロセス、マスター ターゲットのサーバーについて
description: この記事では、Azure Site Recovery を使ってオンプレミスの VMware VM から Azure へのディザスター リカバリーを設定するときに使われる構成サーバー、プロセス サーバー、およびマスター ターゲット サーバーの概要を説明します
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: cd5ded18d1a8f1f5fd96212d37725bb5db13002f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "80062084"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Site Recovery のコンポーネント (構成、プロセス、およびマスター ターゲット) について

この記事では、VMware VM と物理サーバーを Azure にレプリケートするために[Site Recovery](site-recovery-overview.md) サービスで使用される構成、プロセス、およびマスター ターゲットの各サーバーについて説明します。

## <a name="configuration-server"></a>構成サーバー

オンプレミスの VMware VM と物理サーバーをディザスター リカバリーする場合、オンプレミスの Site Recovery 構成サーバーを展開します。

**設定** | **詳細** | **リンク**
--- | --- | ---
**Components**  | 構成サーバー マシンでは、構成サーバー、プロセス サーバー、マスター ターゲット サーバーなど、オンプレミスのすべての Site Recovery コンポーネントを実行します。<br/><br/> 構成サーバーを設定するときに、すべてのコンポーネントが自動的にインストールされます。 | 構成サーバーに関する FAQ を[確認する](vmware-azure-common-questions.md#configuration-server)。
**ロール** | 構成サーバーは、オンプレミスと Azure の間の通信を調整し、データのレプリケーションを管理します。 | Azure への [VMware](vmware-azure-architecture.md) および[物理サーバー](physical-azure-architecture.md)のディザスター リカバリーのアーキテクチャの詳細を確認する。
**VMware の要件** | オンプレミスの VMware VM のディザスター リカバリーを行うには、オンプレミスの高可用性 VMware VM として構成サーバーをインストールして実行する必要があります。 | 前提条件を[確認する](vmware-azure-deploy-configuration-server.md#prerequisites)。
**VMware の展開** | ダウンロードした OVA テンプレートを使用して構成サーバーを展開することをお勧めします。 この方法を使用すると、すべての要件と前提条件に準拠した構成サーバーを簡単に設定できます。<br/><br/> OVA テンプレートを使用して VMware VM を展開することが何らかの理由でできない場合は、物理マシンによってディザスター リカバリーを行うための以下の説明に従って、手動で構成サーバー マシンを設定できます。 | OVA テンプレートを使用して[展開する](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template)。
**物理サーバーの要件** | オンプレミスの物理サーバーでディザスター リカバリーを行う場合は、構成サーバーを手動で設定します。 | 前提条件を[確認する](physical-azure-set-up-source.md#prerequisites)。
**物理サーバーの展開** | 構成サーバーを VMware VM としてインストールできない場合は、物理サーバーにインストールすることができます。 | 構成サーバーを手動で[展開する](physical-azure-set-up-source.md#set-up-the-source-environment)。

## <a name="process-server"></a>プロセス サーバー

プロセス サーバーは、フェールオーバーおよびフェールバック中にレプリケーション データを処理し、オンプレミスの VMware VM と物理サーバー用の Mobility Service をインストールします。

**設定** | **詳細** | **リンク**
--- | --- | ---
**デプロイ**  | 既定では、構成サーバーが展開されるときに、プロセス サーバーがインストールされます。 <br/><br/> オンプレミスのプロセス サーバーは、オンプレミスの VMware VM と物理サーバーのディザスター リカバリーおよびレプリケーションに必要です。 | [詳細については、こちらを参照してください](vmware-azure-architecture.md#architectural-components)。
**役割 (オンプレミス)** | レプリケーションが有効になっているマシンからレプリケーション データを受け取ります。 <br/><br/> キャッシュ、圧縮、暗号化によってレプリケーション データを最適化し、Azure Storage に送信します。 <br/><br/> レプリケートするオンプレミスの VMware VM と物理サーバーに対して Site Recovery Mobility Service のプッシュ インストールを実行します。 <br/><br/> オンプレミスのマシンの自動検出を実行します。 | [詳細については、こちらを参照してください](vmware-azure-enable-replication.md)。
**役割 (Azure からのフェールバック)** | オンプレミス サイトからのフェールオーバー後に、オンプレミスの場所へのフェールバックを処理するために、Azure で Azure VM としてプロセス サーバーを設定します。<br/><br/> Azure 上のプロセス サーバーは一時的なものです。 Azure VM は、フェールバックの完了後に削除できます。 | [詳細については、こちらを参照してください](vmware-azure-set-up-process-server-azure.md)。
**スケーリング** | 大規模な展開では、オンプレミスで追加のスケール アウト プロセス サーバーを設定することができます。 追加のサーバーでは、レプリケートする多数のマシンおよび大量のレプリケーション トラフィックを処理することによって、容量をスケール アウトします。<br/><br/> レプリケーション トラフィックを負荷分散するために、2 つのプロセス サーバー間でマシンを移動できます。 | [詳細については、こちらを参照してください](vmware-azure-set-up-process-server-scale.md)。

## <a name="master-target-server"></a>マスター ターゲット サーバー

マスター ターゲット サーバーは、Azure からのフェールバック中にレプリケーション データを処理します。

- 既定では、マスター ターゲット サーバーは構成サーバーにインストールされます。
- 大規模なデプロイでは、フェールバック用に別のマスター ターゲット サーバーを追加できます。

## <a name="next-steps"></a>次のステップ

- VMware VM および物理サーバーのディザスター リカバリーの[アーキテクチャ](vmware-azure-architecture.md)を確認します。
- VMware VM および物理サーバーから Azure へのディザスター リカバリーの[要件と前提条件](vmware-physical-azure-support-matrix.md)を確認します。
