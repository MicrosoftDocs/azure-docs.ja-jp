---
title: "Microsoft Azure Stack Development Kit のアーキテクチャ | Microsoft Docs"
description: "Microsoft Azure Stack Development Kit のアーキテクチャを表示します。"
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: e8ff17e069bd9237bf06cd79b4c222c50a999eda
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Microsoft Azure Stack Development Kit のアーキテクチャ

*適用対象: Azure Stack 開発キット*

Azure Stack Development Kit は、Azure Stack の単一ノード デプロイです。 すべてのコンポーネントは、1 台のホスト マシンで実行されている仮想マシンにインストールされます。 

## <a name="logical-architecture-diagram"></a>論理アーキテクチャ図
次の図は、Azure Stack 開発キットとそのコンポーネントの論理アーキテクチャを示しています。

![](media/azure-stack-architecture/image1.png)

## <a name="virtual-machine-roles"></a>仮想マシンのロール
Azure Stack 開発キットは、ホスト上の次の仮想マシンを使ってサービスを提供します。

| 名前 | Description |
| ----- | ----- |
| **AzS-ACS01** | Azure Stack ストレージ サービス。|
| **AzS-ADFS01** | Active Directory フェデレーション サービス (ADFS)。  |
| **AzS-BGPNAT01** | エッジ ルーター。Azure Stack 用の NAT 機能と VPN 機能を提供します。 |
| **AzS-CA01** | Azure Stack ロール サービス用の証明機関サービス。|
| **AzS-DC01** | Microsoft Azure Stack 用の Active Directory、DNS、DHCP サービス。|
| **AzS-ERCS01** | 緊急回復コンソールの VM。 |
| **AzS-GWY01** | テナント ネットワーク用の VPN サイト間接続などのエッジ ゲートウェイ サービス。|
| **AzS-NC01** | ネットワーク コントローラー。Azure Stack ネットワーク サービスを管理します。  |
| **AzS-SLB01** | テナントおよび Azure Stack インフラストラクチャ サービス用の、Azure Stack の負荷分散マルチプレクサー サービス。  |
| **AzS-SQL01** | Azure Stack インフラストラクチャ ロール用の内部データ ストア。  |
| **AzS-WAS01** | Azure Stack 管理ポータルと Azure Resource Manager のサービス。|
| **AzS-WASP01**| Azure Stack ユーザー (テナント) ポータルと Azure Resource Manager のサービス。|
| **AzS-XRP01** | Microsoft Azure Stack 用のインフラストラクチャ管理コントローラー。計算、ネットワーク、ストレージのリソースプロバイダーなどがあります。|


## <a name="next-steps"></a>次のステップ
[Azure Stack をデプロイする](azure-stack-deploy.md)

[最初のシナリオを試す](azure-stack-first-scenarios.md)

