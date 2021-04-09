---
title: SAP 用の Microsoft Azure の認定 | Microsoft Docs
description: 現在の構成と、Azure プラットフォーム上の SAP の認定の最新の一覧です。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/21/2020
ms.author: juergent
ms.custom: ''
ms.openlocfilehash: 76f67204b41a36d10541974e78a22f7d065e144b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101669687"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Microsoft Azure で実行されているSAP の認定と構成

SAP および Microsoft は、お客様に対して相互にメリットを持つ強力なパートナーシップの中で互いに連携している長い歴史があります。 Microsoft では、定期的にプラットフォームを更新し、Microsoft Azure が SAP のワークロードを実行する最適なプラットフォームであることを保証するための新しい認定の詳細を SAP に提出しています。 次の表に、Azure がサポートする構成と、増加する SAP 認定資格の一覧を示します。 この一覧は概要一覧であり、公式の SAP の一覧と多少異なる場合があります。 詳細データを取得する方法については、「[Azure デプロイでサポートされている SAP ソフトウェア](./sap-supported-product-on-azure.md)」に説明されています

## <a name="sap-hana-certifications"></a>SAP HANA 認定
参照:

- ネイティブな Azure VM と HANA L インスタンスの SAP HANA によるサポートに関する「[SAP HANA certified IaaS platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)」(SAP HANA 認定の IaaS プラットフォーム)。

| SAP 製品 | サポート対象 OS | Azure のサービス |
| --- | --- | --- |
| SAP HANA Developer Edition (SQLODBC、ODBO (Windows のみ)、ODBC、JDBC ドライバー、HANA Studio、HANA データベースで構成される HANA クライアント ソフトウェアを含む) | Red Hat Enterprise Linux、SUSE Linux Enterprise | D シリーズ VM ファミリ |
| Business One on HANA | SUSE Linux Enterprise | DS14_v2、M32ts、M32ls、M64ls、M64s <br /> [SAP HANA 認定 IaaS プラットフォーム](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux、SUSE Linux Enterprise | GS5 用に制御された可用性。 以下を完全サポート: M64s、M64ms、M128s、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2、M416s_v2、M416ms_v2、 <br /> SAP HANA on Azure (L インスタンス) [SAP HANA 認定 IaaS プラットフォーム](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Suite on HANA、OLTP | Red Hat Enterprise Linux、SUSE Linux Enterprise | M64s、M64ms、M128s、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2、 <br /> M416s_v2、M416ms_v2、SAP HANA on Azure (Large Instances) [SAP HANA 認定 IaaS プラットフォーム](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enterprise for BW、OLAP | Red Hat Enterprise Linux、SUSE Linux Enterprise | GS5、M64s、M64ms、M128s、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2、 <br /> M416s_v2、M416ms_v2、SAP HANA on Azure (Large Instances) [SAP HANA 認定 IaaS プラットフォーム](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux、SUSE Linux Enterprise | GS5、M64s、M64ms、M128s、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2、 <br /> M416s_v2、M416ms_v2、SAP HANA on Azure (Large Instances) <br /> [SAP HANA 認定 IaaS プラットフォーム](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

SAP では、[SAP HANA 認定 IaaS プラットフォーム](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)に高可用 "クラスタリング" ではなく、"スケールアウト" のシノニムとして "クラスタリング" という用語を使用している点に注意してください。

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver 認定
以下の SAP 製品に関して Microsoft Azure は認定を受けており、マイクロソフトと SAP から完全サポートを受けることができます。
参照:

- 「[1928533 - SAP Applications on Azure:Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533)」(1928533 - Azure 上の SAP アプリケーション: サポート対象の製品と Azure VM の種類)。これは SAP TREX、SAP LiveCache、SAP Content Server を含むすべての SAP NetWeaver ベース アプリケーションに該当します。 SAP HANA を除くすべてのデータベース。


| SAP 製品 | ゲスト OS | RDBMS | 仮想マシンの種類 |
| --- | --- | --- | --- |
| SAP Business Suite ソフトウェア | Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux、Oracle Linux |SQL Server、Oracle (Windows および Oracle Linux のみ)、DB2、SAP ASE |A5 から A11、D11 から D14、DS11 から DS14、DS11_v2 から DS15_v2、GS1 から GS5、D2s_v3 から D64s_v3、D2as_v4 から D64as_v4、E2s_v3 から E64s_v3、E2as_v4 から E64as_v4、M64s、M64ms、M128s、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2、M416s_v2、M416ms_v2 |
| SAP Business All-in-One | Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux、Oracle Linux |SQL Server、Oracle (Windows および Oracle Linux のみ)、DB2、SAP ASE |A5 から A11、D11 から D14、DS11 から DS14、DS11_v2 から DS15_v2、GS1 から GS5、D2s_v3 から D64s_v3、D2as_v4 から D64as_v4、E2s_v3 から E64s_v3、E2as_v4 から E64as_v4、M64s、M64ms、M128s、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2、M416s_v2、M416ms_v2 |
| SAP BusinessObjects BI | Windows |該当なし |A5 から A11、D11 から D14、DS11 から DS14、DS11_v2 から DS15_v2、GS1 から GS5、D2s_v3 から D64s_v3、D2as_v4 から D64as_v4、E2s_v3 から E64s_v3、E2as_v4 から E64as_v4、M64s、M64ms、M128s、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2、M416s_v2、M416ms_v2 |
| SAP NetWeaver | Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux、Oracle Linux |SQL Server、Oracle (Windows および Oracle Linux のみ)、DB2、SAP ASE |A5 から A11、D11 から D14、DS11 から DS14、DS11_v2 から DS15_v2、GS1 から GS5、D2s_v3 から D64s_v3、D2as_v4 から D64as_v4、E2s_v3 から E64s_v3、E2as_v4 から E64as_v4、M64s、M64ms、M128s、M128ms、M64ls、M32ls、M32ts、M208s_v2、M208ms_v2、M416s_v2、M416ms_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Azure でサポートされているその他の SAP ワークロード

| SAP 製品 | ゲスト OS | RDBMS | 仮想マシンの種類 |
| --- | --- | --- | --- |
| SAP Business One on SQL Server | Windows  | SQL Server | すべての種類の NetWeaver 認定 VM<br /> [SAP Note #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10.01 MS SP08 | Windows と Linux | | すべての種類の NetWeaver 認定 VM<br /> SAP Note #2451795 |
| SAP ビジネス オブジェクト BI プラットフォーム | Windows と Linux | | SAP Note #2145537 |
| SAP Data Services 4.2 | | | SAP Note #2288344 |
| SAP Hybris Commerce Platform  | Windows | SQL Server、Oracle | すべての種類の NetWeaver 認定 VM <br /> [Hybris のドキュメント](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP Hybris Commerce Platform  | SLES 12 以降 | SAP HANA | すべての種類の NetWeaver 認定 VM <br /> [Hybris のドキュメント](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP Hybris Commerce Platform  | RHEL 7 以降 | SAP HANA | すべての種類の NetWeaver 認定 VM <br /> [Hybris のドキュメント]https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP (Hybris) Commerce Platform 1811 以降  | Windows、SLES、または RHEL | SQL Azure DB | すべての種類の NetWeaver 認定 VM <br /> [Hybris のドキュメント](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html) |
