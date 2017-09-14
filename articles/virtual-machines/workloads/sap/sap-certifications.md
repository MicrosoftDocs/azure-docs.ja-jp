---
title: "SAP 用の Microsoft Azure の認定資格 | Microsoft Docs"
description: "現在の構成と、Azure プラットフォーム上の SAP の認定資格の最新の一覧です。"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: rclaus
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: e4d5c78299903659a18aa9b8d04495e215bcca0d
ms.contentlocale: ja-jp
ms.lasthandoff: 08/10/2017

---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Microsoft Azure で実行されているSAP の認定資格と構成

SAP およびマイクロソフトは、お客様に対して相互にメリットを持つ強力なパートナーシップの中で互いに連携している長い歴史があります。 マイクロソフトでは、定期的にプラットフォームを更新し、Microsoft Azureが SAP のワークロードを実行する最適なプラットフォームであることを保証するための新しい認定資格の詳細を SAP に提出しています。 次の表に、サポート対象の構成と増加する認定資格の一覧を示します。 

## <a name="sap-hana-certifications"></a>SAP HANA 認定

| SAP 製品 | サポート対象 OS | Azure のサービス |
| --- | --- | --- |
| SAP HANA Developer Edition (SQLODBC、ODBO (Windows のみ)、ODBC、JDBC ドライバー、HANA Studio、HANA データベースで構成される HANA クライアント ソフトウェアを含む) |Red Hat Enterprise Linux、SUSE Linux Enterprise | D シリーズ VM ファミリ |
| HANA One |Red Hat Enterprise Linux、SUSE Linux Enterprise |DS14_v2 (一般公開時) |
| SAP S/4 HANA |Red Hat Enterprise Linux、SUSE Linux Enterprise |GS5、SAP HANA on Azure (L インスタンス) 用に制御された可用性 |
| Suite on HANA、OLTP |Red Hat Enterprise Linux、SUSE Linux Enterprise |非運用シナリオでの単一ノード デプロイメント用 GS5、SAP HANA on Azure (L インスタンス) |
| HANA Enterprise for BW、OLAP |Red Hat Enterprise Linux、SUSE Linux Enterprise |単一ノード デプロイメント用 GS5、SAP HANA on Azure (L インスタンス) |
| SAP BW/4 HANA |Red Hat Enterprise Linux、SUSE Linux Enterprise |単一ノード デプロイメント用 GS5、SAP HANA on Azure (L インスタンス) |

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver 認定資格
以下の SAP 製品に関して Microsoft Azure は認定を受けており、マイクロソフトと SAP から完全サポートを受けることができます。

| SAP 製品 | ゲスト OS | RDBMS | 仮想マシンの種類 |
| --- | --- | --- | --- |
| SAP Business Suite ソフトウェア |Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux、Oracle Linux |SQL Server、Oracle (Windows および Oracle Linux のみ)、DB2、SAP ASE |A5 から A11、D11 から D14、DS11 から DS14、DS11_v2 から DS15_v2、GS1 から GS5 |
| SAP Business All-in-One |Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux |SQL Server、Oracle (Windows および Oracle Linux のみ)、DB2、SAP ASE |A5 から A11、D11 から D14、DS11 から DS14、DS11_v2 から DS15_v2、GS1 から GS5 |
| SAP BusinessObjects BI |Windows |該当なし |A5 から A11、D11 から D14、DS11 から DS14、DS11_v2 から DS15_v2、GS1 から GS5 |
| SAP NetWeaver |Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux |SQL Server、Oracle (Windows および Oracle Linux のみ)、DB2、SAP ASE |A5 から A11、D11 から D14、DS11 から DS14、DS11_v2 から DS15_v2、GS1 から GS5 |

