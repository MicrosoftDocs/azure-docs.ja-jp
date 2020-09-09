---
title: Azure Firewall のリモート作業のサポート
description: この記事では、Azure Firewall でリモート作業要員に関する要件をどのようにサポートできるかを示します。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: victorh
ms.openlocfilehash: da5100fafc98ae38809c93e9b3db5ef41c58766a
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82863418"
---
# <a name="azure-firewall-remote-work-support"></a>Azure Firewall のリモート作業のサポート

Azure Firewall は、Azure 仮想ネットワーク リソースを保護する、クラウドベースのマネージド ネットワーク セキュリティ サービスです。 組み込みの高可用性とクラウドの無制限のスケーラビリティを備えた、完全にステートフルなサービスとしてのファイアウォールです。

## <a name="virtual-desktop-infrastructure-vdi-deployment-support"></a>仮想デスクトップ インフラストラクチャ (VDI) のデプロイのサポート

在宅勤務のポリシーでは、多くの IT 組織が容量、ネットワーク、セキュリティ、およびガバナンスにおける根本的な変化に対処する必要があります。 従業員は、在宅勤務中は、オンプレミス サービスに関連する多層型セキュリティ ポリシーによって保護されません。 Azure の仮想デスクトップ インフラストラクチャ (VDI) のデプロイは、組織がこのような環境の変化に迅速に対応するのに役立ちます。 ただし、これらの VDI デプロイとの間で送受信されるインターネット アクセスを保護する方法が必要になります。 Azure Firewall の [DNAT ルール](rule-processing.md)を、[脅威インテリジェンス](threat-intel.md) ベースのフィルター機能と共に使用して、VDI のデプロイを保護することができます。

## <a name="azure-windows-virtual-desktop-support"></a>Azure Windows Virtual Desktop のサポート

Windows Virtual Desktop は、Azure 上で実行される包括的なデスクトップおよびアプリの仮想化サービスです。 これは、簡素化された管理、マルチセッションの Windows 10、Office 365 ProPlus の最適化、およびリモート デスクトップ サービス (RDS) 環境のサポートを提供する唯一の仮想デスクトップ インフラストラクチャ (VDI) です。 Windows デスクトップとアプリを Azure に数分でデプロイおよびスケーリングして、セキュリティとコンプライアンスの組み込み機能を利用することができます。 Windows Virtual Desktop では、仮想ネットワークへの受信アクセスを開く必要はありません。 しかし、仮想ネットワークで実行される Windows Virtual Desktop 仮想マシンに対して、一連の送信ネットワーク接続を許可する必要があります。 詳細については、「[Azure Firewall を使用して Windows Virtual Desktop のデプロイを保護する](protect-windows-virtual-desktop.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

[Windows Virtual Desktop](https://docs.microsoft.com/azure/virtual-desktop/) について、さらに詳しく学習します。