---
title: "Azure Security Center でのインターネットに接続するエンドポイント経由のアクセスの制限 | Microsoft Docs"
description: "このドキュメントでは、**インターネットに接続するエンドポイント経由のアクセスを制限する**という Azure Security Center の推奨事項を実装する方法について説明します。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 727d88c9-163b-4ea0-a4ce-3be43686599f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 6b7d2da8ababba65146503ecfbe6fd6e142a359c
ms.openlocfilehash: f7309c617f1705205e2c9f1b1b48d141391d45da
ms.lasthandoff: 02/06/2017


---
# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>Azure Security Center でのインターネットに接続するエンドポイント経由のアクセスの制限
Azure Security Center では、ネットワーク セキュリティ グループ (NSG) のいずれかに、"あらゆる" 発信元 IP アドレスからのアクセスを許可する&1; つ以上の受信規則が含まれている場合に、インターネットに接続するエンドポイント経由のアクセスを制限するよう推奨します。 "あらゆる" 発信元に対してアクセスを解放すると、攻撃者がリソースにアクセスできるようになる可能性があります。 Security Center では、これらの受信規則を編集して、実際にアクセスを必要とする発信元 IP アドレスにアクセスを制限するよう推奨します。

この推奨事項は、ソースとして "任意" が指定された Web 以外のすべてのポートに対して生成されます。

> [!NOTE]
> このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。 ステップ バイ ステップ ガイドではありません。
>
>

## <a name="implement-the-recommendation"></a>推奨事項の実装
1. **[推奨事項]** ブレードで、**[インターネットに接続するエンドポイント経由のアクセスを制限する]** を選択します。

   ![インターネットに接続するエンドポイント経由のアクセスの制限][1]
2. **[Restrict access through Internet facing endpoint (インターネットに接続するエンドポイント経由のアクセスの制限)]**ブレードが開きます。 このブレードには、セキュリティの問題が発生する可能性のある受信規則が適用された仮想マシン (VM) の一覧が表示されます。 VM を選択します。

   ![Select a VM][2]
3. **[NSG]** ブレードに、ネットワーク セキュリティ グループの情報、関連する受信規則、関連する VM が表示されます。 **[受信の規則を編集する]** を選択して、受信規則の編集に進みます。

   ![ネットワーク セキュリティ グループ ブレード][3]
4. **[受信セキュリティ規則]** ブレードで編集する受信規則を選択します。 この例では、 **[AllowWeb]**を選択します。

   ![受信セキュリティ規則][4]

   **[既定の規則]** を選択して、すべての NSG に含まれる既定の規則のセットを確認することもできます。 既定の規則は削除できませんが、割り当てられている優先順位が低いため、作成した規則を優先させることができます。 既定の規則の詳細については[こちら](../virtual-network/virtual-networks-nsg.md#default-rules)を参照してください。

   ![既定のルール][5]
5. **[AllowWeb]** ブレードで、受信規則のプロパティを編集して、**[ソース]** を IP アドレスまたは IP アドレスのブロックにします。 受信規則のプロパティの詳細については、「 [NSG ルール](../virtual-network/virtual-networks-nsg.md#nsg-rules)」をご覧ください。

   ![受信規則の編集][6]

## <a name="see-also"></a>関連項目
この記事では、"インターネットに接続するエンドポイント経由のアクセスの制限" という Security Center の推奨事項を実装する方法について説明しました。 NSG と規則の有効化の詳細については、次の記事をご覧ください。

* [ネットワーク セキュリティ グループ (NSG) について](../virtual-network/virtual-networks-nsg.md)
* [Azure ポータルを使用して NSG を管理する方法](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

セキュリティ センターの詳細については、次を参照してください。

* 「[Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md)」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* 「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」-- 推奨事項に従って Azure リソースを保護する方法について説明しています。
* 「[Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)」-- Azure リソースの正常性を監視する方法について説明しています。
* 「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」-- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) 」-- パートナー ソリューションの正常性状態を監視する方法について説明しています。
* 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」-- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/)-- Azure のセキュリティに関する最新のニュースと情報を入手できます。

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png

