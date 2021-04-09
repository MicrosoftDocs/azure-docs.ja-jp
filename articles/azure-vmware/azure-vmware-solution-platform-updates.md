---
title: Azure VMware Solution のプラットフォーム更新プログラム
description: Azure VMware Solution のプラットフォーム更新プログラムについて説明します。
ms.topic: reference
ms.date: 03/16/2021
ms.openlocfilehash: ce25df2cb221c032f6dd430f292522fe86e69ceb
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771772"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Azure VMware Solution のプラットフォーム更新プログラム

Azure VMware Solution の重要な更新プログラムは 2021 年 3 月から適用されます。 メンテナンスのタイムラインが記載された通知が Azure Service Health 経由で届きます。 Azure VMware Solution の主要なアップグレードプロセスと機能の詳細については、「[Azure VMware ソリューションのプライベート クラウドの更新とアップグレード](concepts-upgrades.md)」を参照してください。

## <a name="march-15-2021"></a>2021 年 3 月 15 日 

- Azure VMware Solution サービスでは、2021 年 3 月 19 日までの間、プライベート クラウド内の vCenter サーバーを vCenter Server 6.7 Update 3l バージョンに更新するためのメンテナンス作業を実施します。

- この間、VMware vCenter は使用できなくなり、VM を管理できなくなります (停止、開始、作成、削除)。 プライベート クラウドのスケーリング (サーバーとクラスターの追加/削除) も利用できなくなります。 VMware High Availability (HA) は、既存の VM の保護を提供するために動作し続けます。 
 
この vCenter バージョンの詳細については、[VMware vCenter Server 6.7 Update 3l のリリースノート](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html)を参照してください。

## <a name="march-4-2021"></a>2021 年 3 月 4 日

- Azure VMware Solution では、2021 年 3 月 15 日までの間、既存のプライベート クラウドの ESXi 修正プログラムが [VMware ESXi 6.7 修正プログラム リリース ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) に適用されます。

- vSphere スタックについて [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html) で文書化されている回避策も、2021 年 3 月 15 日までの間に適用されます。

>[!NOTE]
>これには中断がともなわず、Azure VMware Services にもワークロードにも影響を与えることがありません。 メンテナンス中、"_Lost network connectivity on DVPorts (DVPorts でネットワーク接続が失われました)_ " や "_Lost uplink redundancy on DVPorts (DVPorts でアップリンクの冗長性が失われました)_ " など、さまざまな VMware アラートが vCenter に表示され、メンテナンスの進行に合わせて自動的に消えます。

## <a name="post-update"></a>更新後
完了後、新しいバージョンの VMware コンポーネントが表示されます。 問題が発生した場合、または質問がある場合は、サポート チケットを開いて、サポート チームにお問い合わせください。





