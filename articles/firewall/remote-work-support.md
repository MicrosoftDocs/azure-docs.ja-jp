---
title: Azure Firewall のリモート作業のサポート
description: この記事では、Azure Firewall でリモート作業要員に関する要件をどのようにサポートできるかを示します。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: victorh
ms.openlocfilehash: 3c0e2033ee559af38a6816bdfa611eea86b14dea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94658317"
---
# <a name="azure-firewall-remote-work-support"></a>Azure Firewall のリモート作業のサポート

Azure Firewall は、Azure 仮想ネットワーク リソースを保護する、クラウドベースのマネージド ネットワーク セキュリティ サービスです。 組み込みの高可用性とクラウドの無制限のスケーラビリティを備えた、完全にステートフルなサービスとしてのファイアウォールです。

## <a name="virtual-desktop-infrastructure-vdi-deployment-support"></a>仮想デスクトップ インフラストラクチャ (VDI) のデプロイのサポート

在宅勤務のポリシーでは、多くの IT 組織が容量、ネットワーク、セキュリティ、およびガバナンスにおける根本的な変化に対処する必要があります。 従業員は、在宅勤務中は、オンプレミス サービスに関連する多層型セキュリティ ポリシーによって保護されません。 Azure の仮想デスクトップ インフラストラクチャ (VDI) のデプロイは、組織がこのような環境の変化に迅速に対応するのに役立ちます。 ただし、これらの VDI デプロイとの間で送受信されるインターネット アクセスを保護する方法が必要になります。 Azure Firewall の [DNAT ルール](rule-processing.md)を、[脅威インテリジェンス](threat-intel.md) ベースのフィルター機能と共に使用して、VDI のデプロイを保護することができます。

## <a name="azure-windows-virtual-desktop-support"></a>Azure Windows Virtual Desktop のサポート

Windows Virtual Desktop は、Azure 上で実行される包括的なデスクトップおよびアプリの仮想化サービスです。 これは、簡素化された管理、マルチセッション Windows 10、Microsoft 365 Apps for enterprise 向けの最適化、リモート デスクトップ サービス (RDS) 環境のサポートを提供する、唯一の仮想デスクトップ インフラストラクチャ (VDI) です。 Windows デスクトップとアプリを Azure に数分でデプロイおよびスケーリングして、セキュリティとコンプライアンスの組み込み機能を利用することができます。 Windows Virtual Desktop では、仮想ネットワークへの受信アクセスを開く必要はありません。 しかし、仮想ネットワークで実行される Windows Virtual Desktop 仮想マシンに対して、一連の送信ネットワーク接続を許可する必要があります。 詳細については、「[Azure Firewall を使用して Windows Virtual Desktop のデプロイを保護する](protect-windows-virtual-desktop.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

[Windows Virtual Desktop](../virtual-desktop/index.yml) について、さらに詳しく学習します。