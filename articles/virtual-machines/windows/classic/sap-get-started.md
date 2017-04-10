---
title: "Windows 仮想マシンにおける SAP の使用 | Microsoft Docs"
description: "Microsoft Azure での Windows 仮想マシン (VM) における SAP の使用について説明します"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: MSSedusch
manager: timlt
editor: 
tags: azure-service-management
keywords: 
ms.assetid: 1b455be4-c02f-43e3-8d39-c2d5f216e646
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: campaign-page
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/04/2016
ms.author: sedusch
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 1d6326d5f79ea4e975abd1aa90b0d4a635f4c31a
ms.lasthandoff: 03/27/2017


---
# <a name="using-sap-on-windows-virtual-machines-in-azure"></a>Azure での Windows 仮想マシンにおける SAP の使用
クラウド コンピューティングという言葉は、今や多くの人々に認知され、小規模な企業から大企業、多国籍企業にいたるまで、IT 業界における存在感を日増しに高めています。 Microsoft Azure はマイクロソフトが提供する Cloud Services プラットフォームで、多方面にわたって新たな可能性を生み出しています。 アプリケーションを Cloud Services として迅速にプロビジョニングしたり、プロビジョニング解除したりすることができるようになった今、技術や予算の制約に縛られることはありません。 企業は、ハードウェア インフラストラクチャに貴重な時間と予算を費やすことなく、アプリケーションやビジネス プロセス、そして顧客とユーザーの利益にのみ目を向けることができます。

Microsoft Azure Virtual Machines を通じて、マイクロソフトは包括的な IaaS (Infrastructure as a Service) プラットフォームを提供しています。 SAP NetWeaver ベースのアプリケーションは、Azure Virtual Machines (IaaS) でサポートされます。 以下のホワイトペーパーでは、SAP NetWeaver ベースのアプリケーションを計画し、Azure の Windows 仮想マシンに実装する方法が説明されています。 [Linux 仮想マシン](../../linux/classic/sap-get-started.md)上に SAP NetWeaver ベースのアプリケーションを実装することもできます。

[!INCLUDE [virtual-machines-common-classic-sap-get-started](../../../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure---ha"></a>Azure における SAP NetWeaver - HA
タイトル: Azure への SAP NetWeaver の導入 - Azure 上の Windows Server フェールオーバー クラスターと SIOS DataKeeper を使用して SAP ASCS/SCS インスタンスをクラスタリングする

概要: このドキュメントでは、SIOS DataKeeper を使用して可用性に優れた SAP ASCS/SCS 構成を Azure 上にセットアップする方法を説明しています。 SAP では、その単一障害点となるコンポーネント (SAP ASCS/SCS、Enqueue Replication Services など) を、共有ディスクを使った Windows Server フェールオーバー クラスター構成によって保護しています。 これらの SAP コンポーネントは、SAP システムの機能に不可欠です。 それらのコンポーネントがサーバーの障害や VM の障害に耐えることができるよう、ベアメタル環境や Hyper-V 環境向けの Windows クラスター構成を使って高可用性機能を設定する必要があります。 2015 年 8 月時点では、こうしたクリティカルな SAP コンポーネントに要求される Windows ベースの高可用性構成に必要となる共有ディスクを Azure 単体で提供することができません。 しかし、SIOS 社の DataKeeper という製品を利用すれば、SAP ASCS/SCS に必要な Windows Server フェールオーバー クラスター構成を Azure IaaS プラットフォーム上に構築することが可能です。 このホワイト ペーパーには、SIOS Datakeeper によって提供される共有ディスクを使用して、Windows Server フェールオーバー クラスター構成を Azure にインストールする手順が説明されています。 このホワイト ペーパーでは、この高可用性構成を適切に機能させるために必要な Azure、Windows、SAP 側の構成について詳しく説明しています。 特定のプラットフォームに SAP ソフトウェアをインストールしてデプロイするときの主要なリソースである SAP インストール ドキュメントと SAP Notes を補足する内容となっています。

更新日: 2015 年 8 月

[このガイドを今すぐダウンロードする](http://go.microsoft.com/fwlink/?LinkId=613056)


