---
title: アクセスとアプリケーションの制御に関するチュートリアル - Azure Security Center
description: このチュートリアルでは、Just-In-Time VM アクセス ポリシーとアプリケーション制御ポリシーを構成する方法を示します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: cc88561f8882b1712648a261d1f0208a18bab26d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095478"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>チュートリアル:Azure Security Center でリソースを保護する
Security Center は、アクセスおよびアプリケーション制御を使用して悪意のあるアクティビティをブロックすることで、脅威にさらされる状態を軽減します。 Just-In-Time (JIT) 仮想マシン (VM) アクセスは、VM への永続的なアクセスを拒否できるようにして攻撃に対する露出を減らします。 代わりに、必要な場合にのみ VM への制御および監査されたアクセスを提供します。 適応型アプリケーション制御を使用すると、VM 上で実行できるアプリケーションを制御することでマルウェアに対する VM の保護を強化できます。 Security Center は、機械学習によって VM で実行されているプロセスを分析し、この情報を利用することで、お客様が許可リスト登録に関する規則を適用するのを支援します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Just-In-Time VM アクセス ポリシーの構成
> * アプリケーション制御ポリシーの構成

## <a name="prerequisites"></a>前提条件
このチュートリアルで説明されている機能を実行するには、Azure Defender が有効になっている必要があります。 無料試用版が提供されています。 アップグレードするには、「[Azure Defender を有効にする](enable-azure-defender.md)」をご覧ください。

## <a name="manage-vm-access"></a>VM アクセスの管理
JIT VM アクセスを使用すると、Azure VM へのインバウンド トラフィックをロックダウンすることができるので、攻撃に対する露出が減り、VM への接続が必要な場合は簡単にアクセスできます。

管理ポートを常に開放しておく必要はありません。 管理ポートを開放しておく必要があるのは、管理タスクやメンテナンス タスクを実行する場合など、VM に接続している間だけです。 Just-In-Time が有効になっている場合、Security Center ではネットワーク セキュリティ グループ (NSG) ルールが使用されます。このルールにより管理ポートへのアクセスが制限されるため、攻撃者は管理ポートをターゲットにすることができなくなります。

[Just-In-Time アクセスを使用して管理ポートをセキュリティで保護する](security-center-just-in-time.md)方法に関するページのガイダンスに従ってください。

## <a name="harden-vms-against-malware"></a>マルウェアに対する VM の保護の強化
適応型アプリケーション制御では、構成済みリソース グループに対する実行が許可される一連のアプリケーションを定義できます。これにはさまざまな利点がありますが、たとえばマルウェアに対する VM の保護の強化に役立ちます。 Security Center は、機械学習によって VM で実行されているプロセスを分析し、この情報を利用することで、お客様が許可リスト登録に関する規則を適用するのを支援します。

[適応型アプリケーション制御を使用して、マシンの攻撃対象領域を減らす](security-center-adaptive-application.md)方法に関するページのガイダンスに従ってください。

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、脅威にさらされる状態を以下の方法で軽減する方法について説明しました。

> [!div class="checklist"]
> * 必要な場合にのみ VM への制御および監査されたアクセスを提供するための Just-In-Time VM アクセス ポリシーの構成
> * VM で実行できるアプリケーションを制御するための適応型アプリケーション制御ポリシーの構成

次のチュートリアルに進み、セキュリティ インシデントへの対応について学習してください。

> [!div class="nextstepaction"]
> [チュートリアル:セキュリティ インシデントへの対応](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
