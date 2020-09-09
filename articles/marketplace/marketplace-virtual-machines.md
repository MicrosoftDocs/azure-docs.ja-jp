---
title: Azure Marketplace での仮想マシンのオファー発行ガイド
description: この記事では、Azure Marketplace からデプロイされる仮想マシンとソフトウェアの無料試用版を発行するための要件について説明します。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/15/2020
ms.openlocfilehash: 9f408174b9a8dff925a9c834ea75bf725ec6f913
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87837346"
---
# <a name="publishing-guide-for-virtual-machine-offers"></a>仮想マシンのオファー発行ガイド

仮想マシン (VM) のイメージの発行は、Azure Marketplace にソリューションを発行するための主な手段の 1 つです。 このガイドでは、この種のオファーの要件について説明します。 

仮想マシンのオファーは、Azure Marketplace を通じてデプロイおよび課金されるトランザクション オファーです。 ユーザーに表示される行動喚起は、*今すぐ入手する* です。

## <a name="free-trial"></a>無料試用版 

ライセンス持ち込み (BYOL) 課金モデルを使用するときに、期限付きのソフトウェア ライセンスにアクセスすることで、オファーをテストするユーザー向けに手配できます。 

## <a name="test-drive"></a>体験版

IaaS (サービスとしてのインフラストラクチャ) アプリまたは SaaS (サービスとしてのソフトウェア) アプリを介して、1 つ以上の仮想マシンをデプロイできます。 *体験版* の発行オプションの利点は、案内役となるガイド ツアーをパートナーがホストしており、仮想マシンまたはソリューション全体のセットアップが自動化されていることです。 体験版を使用すると、顧客は追加コストなしで VM を評価できます。 試用版を体験する顧客は、既存の Azure 顧客である必要はありません。 

体験版に関する詳細については、「[体験版とは](what-is-test-drive.md)」を参照してください。

|必要条件  |詳細 |
|---------|---------|
| Azure Marketplace アプリを持っている   |  IaaS または SaaS を介した 1 台以上の仮想マシン。      |

## <a name="interactive-demo"></a>対話型デモ

このオファーでは、対話型デモを使って、ソリューションのガイド付きエクスペリエンスを顧客に提供します。 対話型デモの発行オプションの利点は、複雑なソリューションの複雑なセットアップなしで試用エクスペリエンスを提供できることです。 

## <a name="virtual-machine-offer"></a>仮想マシンのオファー

顧客に関連付けられているサブスクリプションに仮想アプライアンスをデプロイするときは、オファーの種類として*仮想マシン* を使用します。 VM は商取引が可能で、従量課金制またはライセンス持ち込み (BYOL) のライセンス モデルを使用します。 商用トランザクションは Microsoft がホストし、公開元に代わって顧客に請求します。 公開元にとっては、顧客と Microsoft との間で推奨される支払い関係 (Enterprise Agreement など) を利用できる利点があります。

> [!NOTE]
> 現在、Enterprise Agreement に関連付けられている年額コミットメントは VM の Azure 利用には使えますが、公開元のソフトウェア ライセンス料金には使えません。  
> 
> [!NOTE]
> イメージと価格をプライベート オファーとして発行することで､VM の検出とデプロイを特定の顧客に限定できます。 プライベート オファーとすることで､公開元が近しい顧客に排他的なオファーを作成し､カスタマイズされたソフトウェアと条件を提供できるようになります。 条件をカスタマイズすることによって､公開元は特別な価格と条件によるフィールド主導の取引､さらには限定的なソフトウェア リリースの利用などのさまざまなシナリオにスポットライトを当てることができます。 プライベート オファーにより、公開元はそうした詳細を組み込んだ新しいプランを作成し､限られた顧客に具体的な価格や製品を提供できます。  
>
> 詳細については、[Azure Marketplace でのプライベート オファー](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace)をご覧ください。  

| 要件 | 詳細 |  
|:--- |:--- | 
| 請求/メータリング | 対象の VM は、BYOL または従量課金制の月額請求をサポートしている必要があります。 |  
| Azure と互換性がある仮想ハード ディスク (VHD) | VM は、Windows または Linux 上に構築されている必要があります。 VHD の作成の詳細については、以下を参照してください。 <ul> <li>[Azure で動作保証済みの Linux ディストリビューション](../virtual-machines/linux/endorsed-distros.md) (Linux VHD 用)。</li> <li>[Azure と互換性のある VHD の作成](./partner-center-portal/azure-vm-create-offer.md) (Windows VHD 用)。</li> </ul> |  

>[!Note]
>クラウド ソリューション プロバイダー (CSP) のパートナー チャネル オプトインが利用できるようになりました。 Microsoft CSP パートナー チャネルを通じたオファーのマーケティングの詳細については、「[クラウド ソリューション プロバイダー](./cloud-solution-providers.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

まだ行っていない場合は、[Azure Marketplace を使用してクラウド ビジネスを拡大する](https://azuremarketplace.microsoft.com/sell)方法について学習します。

パートナー センターで登録して作業を開始するには、次のようにします。

- [パートナー センターにサインイン](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)し、オファーを作成するか完成させます。
- 詳細については、「[仮想マシン オファーを作成する](./partner-center-portal/azure-vm-create-offer.md)」を参照してください。
