---
title: Azure の OpenShift の概要 | Microsoft Docs
description: Azure の OpenShift の概要について説明します。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: haroldw
ms.openlocfilehash: 826085df8d928cab0a05527be8c464af5f4e9180
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58002517"
---
# <a name="openshift-in-azure"></a>Azure の OpenShift

OpenShift は、エンタープライズに Docker と Kubernetes を導入する、オープンで拡張可能なコンテナー アプリケーション プラットフォームです。  

OpenShift には、コンテナーのオーケストレーションと管理のための Kubernetes が含まれています。 これにより、開発者と操作を中心に据えたツールが追加され、次のことが可能になります。

- 迅速なアプリケーション開発。
- 容易なデプロイとスケール変更。
- チームおよびアプリケーション向けの長期的なライフサイクル メンテナンス。

以下の複数のバージョンの OpenShift を使用できます。

- OpenShift Container Platform
- OpenShift On Azure (2019 年第 1 四半期末頃に公開予定のフル マネージド OpenShift)
- OKD (旧称 OpenShift Origin)
- OpenShift Dedicated
- OpenShift Online

この記事で扱う 5 つのバージョンのうち、現時点でお客様が Azure にデプロイできるのは次の 2 つのみです: OpenShift Container Platform、OKD。

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform は Red Hat 製のエンタープライズ対応[市販バージョン](https://www.openshift.com)であり、Red Hat でサポートされています。 このバージョンでは、OpenShift Container Platform の必要な権利を購入します。インフラストラクチャ全体のインストールと管理は顧客が行います。

プラットフォーム全体を顧客が "所有" しているため、プラットフォームを自社のオンプレミスのデータセンターや、パブリック クラウド (Azure など) にインストールすることができます。

## <a name="openshift-on-azure"></a>OpenShift On Azure

OpenShift On Azure は Azure で実行される フル マネージド OpenShift です。 このサービスは、Microsoft と Red Hat によって共同で管理され、サポートされています。 クラスターは、お客様の Azure サブスクリプションにデプロイされます。 サービスは、現在プライベート プレビュー段階であり、2019 年第 1 四半期末頃に一般提供される予定です。 プライベート プレビューへの参加に関心をお持ちのお客様は、[申請フォーム](https://aka.ms/openshiftazureinterest)にご記入ください。  一般公開が近づくにつれて、サービスの詳細がさらに提供されます。

## <a name="okd-formerly-openshift-origin"></a>OKD (旧称 OpenShift Origin)

OKD は OpenShift の[オープン ソース](https://www.okd.io/)上流プロジェクトであり、コミュニティによりサポートされています。 OKD は CentOS または Red Hat Enterprise Linux (RHEL) にインストールできます。

## <a name="openshift-dedicated"></a>OpenShift Dedicated

Dedicated は、Red Hat によって管理される*シングルテナント*の OpenShift であり、OpenShift Container Platform を使用します。 すべての基になるインフラストラクチャ (VM、OpenShift クラスター、ネットワーク、ストレージなど) は、Red Hat によって管理されています。 クラスターはお客様専用であり、パブリック クラウド (Azure など) で実行されます。 開始クラスターには 4 つのアプリケーション ノードが含まれ、すべてのコストは年単位で前払いされます。

## <a name="openshift-online"></a>OpenShift Online

Online は、Red Hat によって管理されている、Container Platform を使用する*マルチテナント* OpenShift です。 すべての基になるインフラストラクチャ (VM、OpenShift クラスター、ネットワーク、ストレージなど) は、Red Hat によって管理されています。 

このバージョンで、顧客はコンテナーをデプロイしますが、コンテナーを実行するホストは制御できません。 Online はマルチテナントなので、他の顧客のコンテナーと同じ VM ホストにコンテナーが配置される場合があります。 コストは、コンテナーごとに課金されます。

## <a name="next-steps"></a>次の手順

- [Azure で OpenShift の共通の前提条件を構成する](./openshift-prerequisites.md)
- [OpenShift Container Platform の Azure へのデプロイ](./openshift-container-platform.md)
- [OKD を Azure にデプロイする](./openshift-okd.md)
- [OpenShift を Azure Stack にデプロイする](./openshift-azure-stack.md)
- [デプロイ後タスク](./openshift-post-deployment.md)
- [OpenShift デプロイのトラブルシューティング](./openshift-troubleshooting.md)
