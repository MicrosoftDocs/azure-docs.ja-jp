---
title: 概念 - プライベート クラウドのアップグレード
description: Azure VMware Solution の主要なアップグレード プロセスと機能について説明します
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: f541aa4e4963cf40fad71201180ea118a1513fca
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752206"
---
# <a name="azure-vmware-solution-upgrade-concepts"></a>Azure VMware Solution のアップグレードの概念

Azure VMware Solution のプライベート クラウドの主な利点の 1 つは、管理者に代わってプラットフォームのメンテナンスが行われることです。 プラットフォーム メンテナンスには、VMware 検証済みソフトウェア バンドルへの自動アップグレードが含まれます。 アップグレードは定期的に行われ、ソフトウェアの最新の検証済みバージョンが常に使用できるようになります。

## <a name="azure-vmware-solution-private-cloud-software-upgrades"></a>Azure VMware Solution プライベート クラウド ソフトウェアのアップグレード

Azure VMware Solution のプライベート クラウド プラットフォームには、VMware vSphere、ESXi、vSAN、および NSX-T ソフトウェアの特定のバージョンが含まれています。 プライベート クラウド ソフトウェア バンドルは、新しいプライベート クラウドのインストールおよび既存のプライベート クラウドのアップグレードで使用するために検証されます。

ライフサイクル管理のアップグレード プロセスでは、ご使用のプライベート クラウドにダウンタイムが発生することはありません。 アップグレード プロセスにより、検証済みの Azure VMware Solution プライベート クラウド ソフトウェアの最新バージョンが自動的に使用されるようになります。 アップグレードは定期的に適用されるため、プライベート クラウドは、検証済みのソフトウェア バンドルの最新リリースから 1 バージョンを超えて遅れることはありません。 ご使用のプライベート クラウドへの計画的アップグレードが通知されます。 ご使用のプライベート クラウドが最新リリースの 1 バージョン以内である場合は、アップグレードを遅らせることができます。

重要な修正プログラムと更新プログラムは、検証時に適用されます。 必要な重要なアップグレードは事前に通知されます。 このポリシーにより、プライベート クラウドに重要な修正プログラムと更新プログラムが直ちに適用されます。

VMware ソフトウェアのバージョンについては、[プライベート クラウドとクラスターの概念に関する記事](concepts-private-clouds-clusters.md)と [FAQ](faq.md) をご覧ください。

## <a name="next-steps"></a>次のステップ

次のステップでは、[プライベート クラウドを作成](tutorial-create-private-cloud.md)します。

<!-- LINKS - external -->

<!-- LINKS - internal -->
