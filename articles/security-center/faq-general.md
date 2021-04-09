---
title: Azure Security Center の FAQ - 一般的な質問
description: 脅威の防御、検出、対応を可能にする製品である Azure Security Center に関してよく寄せられる一般的な質問
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 3a8429d9dc6820b1f79c49d325872b61833f988d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095546"
---
# <a name="faq---general-questions-about-azure-security-center"></a>FAQ - Azure Security Center に関する一般的な質問

## <a name="what-is-azure-security-center"></a>Azure Security Center とは
Azure Security Center を使用すると、リソースのセキュリティを高度に視覚化して制御することで、脅威の回避、検出、および対応を行うことができます。 これにより、サブスクリプション全体に統合セキュリティの監視とポリシーの管理を提供し、気付かない可能性がある脅威を検出し、セキュリティ ソリューションの広範なエコシステムと連動します。

Security Center では、データの収集と格納に Log Analytics エージェントを使用します。 詳細については、「[Azure Security Center でのデータ収集](security-center-enable-data-collection.md)」を参照してください。


## <a name="how-do-i-get-azure-security-center"></a>Azure Security Center はどうしたら取得できますか。
Azure Security Center は、Microsoft Azure サブスクリプションで有効化し、[Azure Portal](https://azure.microsoft.com/features/azure-portal/) からアクセスします アクセスするには、[ポータルにサインイン](https://portal.azure.com)し、 **[参照]** を選択し、 **[Security Center]** までスクロールします。


## <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Azure Security Center の監視対象になる Azure リソースは何ですか。
Azure Security Center は、次の Azure リソースを監視します。

* 仮想マシン (VM) ( [Cloud Services](../cloud-services/cloud-services-choose-me.md)を含む)
* 仮想マシン スケール セット
* Azure サブスクリプションに統合済みのパートナー ソリューション (VM 上および App Service Environment 上の Web アプリケーション ファイアウォールなど)
* [製品概要に記載されているさまざまな Azure PaaS サービス](features-paas.md)


## <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Azure リソースの現在のセキュリティ状態を確認する方法を教えてください。
**[Security Center Overview]\(Security Center の概要\)** ページでは、お使いの環境の全体的なセキュリティ体制が [コンピューティング]、[ネットワーク]、[ストレージおよびデータ]、[アプリケーション] に分けて詳しく示されます。 リソースの種類にはそれぞれ、確認されたセキュリティの脆弱性を示すインジケーターがあります。 各タイルをクリックすると、Security Center によって特定されたセキュリティの問題の一覧が、サブスクリプション内のリソースのインベントリと共に表示されます。



## <a name="what-is-a-security-initiative"></a>セキュリティ イニシアチブとは
セキュリティ イニシアチブは、指定されたサブスクリプション内のリソースに対して推奨されるコントロール (ポリシー) のセットを定義します。 Azure Security Center では、セキュリティに関する会社の要件、各サブスクリプションでのアプリケーションの種類やデータの機密度に合わせて Azure サブスクリプションにイニシアチブを割り当てます。

Azure Security Center で有効になっているセキュリティ ポリシーが、セキュリティに関する推奨事項と監視を促進します。 詳細については、「[セキュリティ ポリシー、イニシアチブ、および推奨事項とは](security-policy-concept.md)」を参照してください。


## <a name="who-can-modify-a-security-policy"></a>セキュリティ ポリシーを変更できるのは誰ですか。
セキュリティ ポリシーを変更するには、**セキュリティ管理者** であるか、そのサブスクリプションの **所有者** である必要があります。

セキュリティ ポリシーを構成する方法については、 [Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md) を参照してください。


## <a name="what-is-a-security-recommendation"></a>セキュリティに関する推奨事項とは
Azure Security Center は、Azure リソースのセキュリティの状態を分析します。 潜在的なセキュリティの脆弱性が識別されると、推奨事項が作成されます。 推奨事項では、必要なコントロールを構成する手順を説明します。 次に例をいくつか示します。

* 悪意のあるソフトウェアを識別して削除するためのマルウェア対策をプロビジョニングする
* 仮想マシンへのトラフィックを制御するための[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)とルール
* Web アプリケーションを対象とする攻撃から保護するための Web アプリケーション ファイアウォールをプロビジョニングする
* 不足しているシステムの更新をデプロイする
* 推奨基準と一致しない OS 構成に対処する

ここにはセキュリティ ポリシーで有効な推奨事項のみが表示されています。


## <a name="what-triggers-a-security-alert"></a>セキュリティの警告をトリガーするものは何ですか。
Azure Security Center は、Azure のリソース、ネットワーク、パートナー ソリューション (マルウェア対策やファイアウォールなど) から、自動的にログ データを収集して分析し、結合します。 脅威が検出されると、セキュリティの警告が作成されます。 例には次の検出が含まれます。

* 既知の悪意のある IP アドレスと通信する、セキュリティ侵害された仮想マシン
* Windows エラー報告を使用して検出された高度なマルウェア
* 仮想マシンに対するブルート フォース攻撃
* マルウェア対策や Web アプリケーション ファイアウォールなどのセキュリティ ソリューションの統合パートナーからのセキュリティの警告


## <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Microsoft Security Response Center と Azure Security Center によって検出され、警告される脅威の違いは何ですか。
Microsoft Security Response Center (MSRC) では、Azure のネットワークとインフラストラクチャの選択的なセキュリティ監視を行い、第三者から脅威インテリジェンスと不正使用の報告を受け取ります。 不正利用者や許可されていない利用者が顧客データにアクセスしたことや、顧客による Azure の利用方法が利用規約の条件に従っていないことを MSRC が検出すると、セキュリティ インシデント マネージャーが顧客に通知します。 通常、通知は、Azure Security Center で指定されたセキュリティ担当者または Azure サブスクリプション所有者 (セキュリティ担当者が指定されていない場合) に電子メールで送られます。

Security Center は、顧客の Azure 環境を継続的に監視し、分析を適用して悪意のある可能性があるさまざまなアクティビティを自動的に検出する Azure サービスです。 検出されたアクティビティは、Security Center のダッシュボードにセキュリティの警告として表示されます。