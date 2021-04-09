---
title: 共同販売の要件 | Azure Marketplace
description: Microsoft コマーシャル マーケットプレースのプランが共同販売の準備完了または共同販売インセンティブ対象ステータスの資格を得るために満たす必要のある要件について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.reviewer: stmummer
ms.date: 3/17/2021
ms.openlocfilehash: e67219e0b029ce401ffc05c009a5cc4a96680a9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593392"
---
# <a name="co-sell-requirements"></a>共同販売の要件

この記事では、さまざまなレベルの共同販売ステータスの要件を説明します。 共同販売をサポートするプランの種類の最新の一覧については、「[コマーシャル マーケットプレース プランの共同販売を構成する](co-sell-configure.md)」を参照してください。 共同販売の概要については、「[Microsoft のセールス チームとパートナーによる共同販売の概要](co-sell-overview.md)」を参照してください。

この表には、考えられるすべての共同販売ステータスが示されています。

| Status | 解説 |
| ------------ | ------------- |
| 共同販売の準備が未完了 | [共同販売の準備完了ステータスの最小要件](#requirements-for-co-sell-ready-status)が満たされていません。 |
| 共同販売の準備完了 | [共同販売の準備完了ステータスの要件](#requirements-for-co-sell-ready-status)がすべて満たされています。 |
| Azure IP 共同販売インセンティブ対象 | [これらの追加の要件](#requirements-for-azure-ip-co-sell-incentivized-status)に加えて、共同販売の準備完了の要件が満たされています。 |
| Business Applications 共同販売インセンティブ対象 | このステータスは、[Microsoft ビジネス アプリケーション ISV コネクト プログラム](business-applications-isv-program.md)の Dynamics 365 と Power Apps のプランに適用されます。[このステータスの要件](#requirements-for-business-applications-co-sell-incentivized-status)がすべて満たされていることを示します。 |
|||

## <a name="requirements-for-co-sell-ready-status"></a>共同販売の準備完了ステータスの要件

プランが共同販売の準備完了ステータスになるには、次の要件を満たす必要があります。

**すべてのパートナー**:

- MPN ID と[アクティブなコマーシャル マーケット アカウントをパートナー センターで](./partner-center-portal/create-account.md)用意する。
- パートナー センターで万全な[ビジネス プロファイル](/partner-center/create-a-marketing-profile)を用意する。 認定 Microsoft パートナーは、ビジネス ニーズに合った独自のソリューションと専門知識を探しているお客様に自社のビジネスを示すためにビジネス プロファイルを役立てて、[紹介](/partner-center/referrals)につなげることができます。
- **[Microsoft と共同販売する]** タブを完了し、プランをコマーシャル マーケットプレースに公開する。
- 共同販売の対象地域ごとの営業担当者と必要な部品表を用意する。

**サービス パートナー**:

- プランの種類が "_サービス ソリューション_" の場合、任意のコンピテンシー領域でアクティブな Gold コンピテンシーを取得している必要があります。

**ビジネス アプリケーション ISV**:

- Dynamics 365 & Power Apps (Dynamics 365 Business Central を除く) ソリューションでは、ISV Connect の登録が必要です。

### <a name="complete-the-co-sell-with-microsoft-tab"></a>[Microsoft と共同販売する] タブの完了

自分のプランを公開または更新する際は、「[コマーシャル マーケットプレース プランの共同販売を構成する](commercial-marketplace-co-sell.md)」で詳しく説明されているとおりに、 **[Microsoft と共同販売する]** タブですべての必須情報を入力します。 ここには、次のドキュメントの提供が含まれます。

- ソリューションまたはプランの概要資料
- ソリューションまたはプランの提案資料

Microsoft は、これらのドキュメントの作成に役立つテンプレートを提供しています。 [Microsoft と共同販売する] タブの必須およびオプションの情報について詳しくは、「[コマーシャル マーケットプレース プランの共同販売を構成する](commercial-marketplace-co-sell.md)」を参照してください。

### <a name="publish-your-offer-live"></a>オファーを発行する

共同販売の準備完了ステータスの資格を得るには、自分のプランまたはソリューションがコマーシャル マーケットプレースのオンライン ストアの少なくともいずれか (Azure Marketplace または Microsoft AppSource) に実際に公開されている必要があります。 コマーシャル マーケットプレースへのプランの公開については、「[プランの種類別の公開ガイド](publisher-guide-by-offer-type.md)」を参照してください。 これまでコマーシャル マーケットプレースでプランを公開したことがない場合は、[コマーシャル マーケットプレース アカウント](./partner-center-portal/create-account.md)を持っていることを確認してください。

## <a name="requirements-for-azure-ip-co-sell-incentivized-status"></a>Azure IP 共同販売インセンティブ対象ステータスの要件

Azure IP 共同販売インセンティブ対象ステータスは、次のプランの種類に適用されます。

- Azure Application
- Azure Container
- Azure Virtual Machine
- IoT Edge モジュール
- サービスとしてのソフトウェア (SaaS)

共同販売の準備完了ステータスの取得後、Azure IP 共同販売インセンティブ対象ステータスを取得するには、さらに 3 つの要件を満たす必要があります。

要件 1 - 次の実現:

- "_組織レベル_" で、過去 12 か月間に Azure 使用による収益のしきい値である 100,000 米国ドルを少なくとも生み出している。 これは、Azure ソリューションを組み合わせて達成することができます。 プランがコマーシャル マーケットプレースで取引可能な場合、100,000 米国ドルの請求売上しきい値を満たすことで、この要件を達成できます。

要件 2 - Azure ベースのソリューションに対する Microsoft の技術的な検証に合格:
- 技術的な検証では、自分のプランの 50% を超えるインフラストラクチャで Azure 上の反復可能な IP コードを使用していることを確認する必要があります。 コマーシャル マーケットプレース上の取引可能な Azure VM および Azure アプリケーション ソリューションは、この要件を既定で満たす必要がある点に注意してください。

要件 3 - 参照アーキテクチャ図の提供:
- 参照アーキテクチャ図を共同販売のドキュメントと一緒にパートナー センターにアップロードし、審査を受けます。 この図の作成のガイダンスについては、「[参照アーキテクチャ図](reference-architecture-diagram.md)」を参照してください。 図のアップロードの詳細については、「[コマーシャル マーケットプレース プランの共同販売を構成する](commercial-marketplace-co-sell.md)」を参照してください。

## <a name="requirements-for-business-applications-co-sell-incentivized-status"></a>Business Applications 共同販売インセンティブ対象ステータスの要件

このステータスは、ISV コネクト プログラムに登録されている、Dynamics 365 または Power Apps を基盤とする IP ベースのソリューションに適用されます (Dynamics 365 Business Central を除く)。 ただし、Microsoft 販売者がプランを貴社と共同販売するためには、そのプランが共同販売の準備完了ステータス (前述) の要件を満たしていることも必要です。

## <a name="next-steps"></a>次のステップ

- [コマーシャル マーケットプレース プランの共同販売を構成する](commercial-marketplace-co-sell.md)
