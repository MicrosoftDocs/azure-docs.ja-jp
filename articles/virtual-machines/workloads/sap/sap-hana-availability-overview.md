---
title: Azure VM 上の SAP HANA の可用性 - 概要 | Microsoft Docs
description: Azure ネイティブ VM での SAP HANA の運用について説明します。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 757dfc34e3be12d09b8f965a2bb0295adb712c11
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102504237"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>Azure 仮想マシンの SAP HANA の高可用性

Azure VM 上の SAP HANA のようなミッション クリティカルなデータベースに対して、多数の Azure 機能を使用できます。 この記事では、Azure VM でホストされている SAP HANA インスタンスの可用性を実現する方法について説明します。 記事では、Azure の SAP HANA の可用性を高めるために Azure インフラストラクチャを使って実装できるシナリオをいくつか紹介します。 

## <a name="prerequisites"></a>前提条件

この記事は、次のような Azure におけるサービスとしてのインフラストラクチャ (IaaS) の基本事項について理解していることを前提としています。 

- Azure Portal または PowerShell を使って仮想マシンまたは仮想ネットワークをデプロイする方法。
- JSON (JavaScript Object Notation) テンプレートを使うためのオプションなど、Azure クロスプラットフォーム コマンドライン インターフェイス (CLI) の使用。

また、この記事では、SAP HANA インスタンスのインストール、および SAP HANA インスタンスの管理と運用について熟知している必要もあります。 HANA システム レプリケーションのセットアップと運用をよく理解していることが、特に重要です。 これには、SAP HANA データベースのバックアップおよび復元などのタスクが含まれます。

以下の記事では、Azure で SAP HANA の使用に関する優れた概要を提供しています。

- [Azure VM への単一インスタンスの SAP HANA の手動インストール](./hana-get-started.md)
- [Azure VM での SAP HANA システム レプリケーションのセットアップ](sap-hana-high-availability.md)
- [Azure VM での SAP HANA のバックアップ](./sap-hana-backup-guide.md)

また、SAP HANA に関する以下の記事をよく理解しておくこともお勧めします。

- [SAP HANA の高可用性](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [FAQ: SAP HANA の高可用性](https://archive.sap.com/documents/docs/DOC-66702)
- [SAP HANA のシステム レプリケーションを実行する](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 SPS 01 の新機能:高可用性](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [SAP HANA システム レプリケーションのネットワークに関する推奨事項](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [SAP HANA システム レプリケーション](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [SAP HANA サービスの自動再起動](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [SAP HANA システム レプリケーションの構成](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Azure での VM デプロイについて理解することも必要ですが、Azure で可用性アーキテクチャを定義する前に、「[Azure での Windows 仮想マシンの可用性の管理](../../availability.md)」を読むことをお勧めします。

## <a name="service-level-agreements-for-azure-components"></a>Azure コンポーネントのサービス レベル アグリーメント

Azure では、ネットワーク、ストレージ、VM などのコンポーネントごとに異なる可用性 SLA を備えています。 すべての SLA がドキュメントに記載されています。 詳細については、[Microsoft Azure サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)に関するページをご覧ください。 

「[Virtual Machines の SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)」では、3 つの異なる構成に対する、次の 3 つの異なる SLA について説明しています。

- OS ディスクとすべてのデータ ディスクに対して [Azure Premium SSD](../../managed-disks-overview.md) を使用する単一の VM。 このオプションは、月次で 99.9% の稼働時間を提供します。
- [Azure 可用性セット](../../windows/tutorial-availability-sets.md)で編成されている複数 (2 台以上) の VM。 このオプションは、月次で 99.95% の稼働時間を提供します。
- [可用性ゾーン](../../../availability-zones/az-overview.md)で編成されている複数 (2 台以上) の VM。 このオプションは、月次で 99.99% の稼働時間を提供します。

Azure コンポーネントで提供可能な SLA に対する可用性の要件を評価してください。 その後、SAP HANA のシナリオを選択して、必要な可用性レベルを実現してください。

## <a name="next-steps"></a>次のステップ

- 「[1 つの Azure リージョン内での SAP HANA の可用性](./sap-hana-availability-one-region.md)」を確認する
- 「[Azure リージョンの枠を越えた SAP HANA の可用性](./sap-hana-availability-across-regions.md)」を確認する 















