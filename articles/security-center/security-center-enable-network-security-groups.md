---
title: Azure Security Center でネットワーク セキュリティ グループを有効にする | Microsoft Docs
description: このドキュメントでは、"**ネットワーク セキュリティ グループを有効にする**" という Azure Security Center の推奨事項を実装する方法について説明します。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f53ed853-ffaf-4530-a019-1906ba6f341b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 14b7cc8f8162574380ca21ac515af8b7d3d5ded9
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56111698"
---
# <a name="enable-network-security-groups-in-azure-security-center"></a>Azure Security Center でネットワーク セキュリティ グループを有効にする
Azure Security Center では、ネットワーク セキュリティ グループ (NSG) が有効になっていない場合、NSG を有効にするよう推奨されます。 NSG には、Virtual Network の VM インスタンスに対するネットワーク トラフィックを許可または拒否する一連のアクセス制御リスト (ACL) 規則が含まれています。 NSG は、サブネットまたはそのサブネット内の個々の VM インスタンスと関連付けることができます。 NSG がサブネットに関連付けられている場合、ACL 規則はそのサブネット内のすべての VM インスタンスに適用されます。 また、NSG を直接 VM に関連付けることにより、その個々の VM に対するトラフィックをさらに制限できます。 NSG の詳細については、「 [ネットワーク セキュリティ グループ (NSG) について](../virtual-network/security-overview.md)

NSG を有効にしていない場合、Security Center から 2 つの推奨事項が提示されます:サブネット上のネットワーク セキュリティ グループを有効にし、仮想マシン上でネットワーク セキュリティ グループを有効にします。 サブネットと VM のどちらのレベルに NSG を適用するか選択してください。

> [!NOTE]
> このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。  ステップ バイ ステップ ガイドではありません。
>
>

## <a name="implement-the-recommendation"></a>推奨事項の実装
1. **[推奨事項]** ブレードで、サブネットまたは仮想マシンの **[ネットワーク セキュリティ グループを有効にする]** を選択します。
   ![ネットワーク セキュリティ グループを有効にする][1]
2. これにより、選択した推奨事項に応じて **[サブネット用の欠けているネットワーク セキュリティ グループを構成する] または [仮想マシンに対して、不足しているネットワーク セキュリティ グループを構成します]** ブレードが開きます。 NSG を構成するサブネットまたは仮想マシンを選択します。

   ![Configure NSG for subnet][2]

   ![Configure NSG for VM][3]
3. **[ネットワーク セキュリティ グループの選択]** ブレードで、既存の NSG を選択するか、**[新規作成]** を選択して新しい NSG を作成します。

   ![[ネットワーク セキュリティ グループの選択]][4]

NSG を作成する場合は、[ネットワーク セキュリティ グループの管理](../virtual-network/manage-network-security-group.md)に関するページに記載された手順に従って NSG を作成し、セキュリティ規則を設定してください。

## <a name="see-also"></a>関連項目
この記事では、Security Center の推奨事項である、サブネットまたは仮想マシンの "ネットワーク セキュリティ グループを有効にする" を実装する方法について説明しました。 NSG の有効化の詳細については、以下の記事を参照してください。

* [ネットワーク セキュリティ グループ (NSG) について](../virtual-network/security-overview.md)
* [ネットワーク セキュリティ グループの管理](../virtual-network/manage-network-security-group.md)

セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md) 」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md) 」-- 推奨事項に従って Azure リソースを保護する方法について説明しています。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md) 」-- Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」-- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) 」-- パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」-- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティに関する最新のニュースと情報を入手できます。

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
