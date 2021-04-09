---
title: Azure DevTest Labs のコストと所有権の管理
description: この記事では、コストを最適化し、環境全体で所有権を整合させるのに役立つ情報を提供します。
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: dfac69a055c9b0c75032622caf7fb8502fad3406
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92328268"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Azure DevTest Labs インフラストラクチャのガバナンス - コストと所有権の管理
コストと所有権は、開発およびテスト用の環境の構築を検討するときの主な懸案事項です。 このセクションでは、コストを最適化し、環境全体で所有権を整合させるのに役立つ情報を提供します。

## <a name="optimize-for-cost"></a>コストを最適化する

### <a name="question"></a>Question
DevTest Labs 環境内でコストを最適化にはどうすればよいですか?

### <a name="answer"></a>Answer
DevTest Labs にはコストの最適化に役立つ機能がいくつか組み込まれています。 ユーザーのアクティビティを制限するには、[コスト管理としきい値](devtest-lab-configure-cost-management.md)と[ポリシー](devtest-lab-set-lab-policy.md)に関する記事をご覧ください。 

開発とテストのワークロードに DevTest Labs を利用するときは、Enterprise Agreement の一部として [Enterprise Dev/Test サブスクリプションのベネフィット](https://azure.microsoft.com/offers/ms-azr-0148p/)の利用を検討できます。 または、従量課金制のお客様の場合は、[従量課金制の DevTest オファー](https://azure.microsoft.com/offers/ms-azr-0023p/)を検討できます。

このアプローチにはさまざまなメリットがあります。

- Windows 仮想マシン、クラウド サービス、HDInsight、App Service、および Logic Apps について特別な低料金の Dev/Test 価格
- 他の Azure サービスについての魅力的な Enterprise Agreement (EA) 価格
- ギャラリー内の専用の Dev/Test イメージ (Windows 8.1 と Windows 10 を含む) へのアクセス
 
Enterprise Dev/Test サブスクリプション内で実行されている Azure リソースを利用できるのは、アクティブな Visual Studio サブスクライバーのみ (標準サブスクリプション、年次クラウド サブスクリプション、月次クラウド サブスクリプション) です。 ただし、エンド ユーザーはアプリケーションにアクセスして、フィードバックを提供したり、受け入れテストを実行したりできます。 このサブスクリプション内でのリソースの使用はアプリケーションの開発およびテストに限定され、アップタイムの保証はありません。

DevTest のオファーを使用する場合、このベネフィットはアプリケーションの開発とテストだけが対象であることに注意してください。 Azure DevOps および HockeyApp の使用を除いて、このサブスクリプションでの使用には、金銭的な補償を伴う SLA は付随しません。

## <a name="define-role-based-access-across-your-organization"></a>組織全体でロールベースのアクセスを定義する
### <a name="question"></a>Question
IT 部門が管理作業を行い開発者/テスト担当者がそれぞれの作業を行うことができるように、DevTest Labs 環境に対して Azure ロールベースのアクセス制御を定義するにはどうすればよいですか。 

### <a name="answer"></a>Answer
おおまかなパターンはありますが、詳細は組織によって異なります。

全社的 IT 部門は必要なもののみを所有し、プロジェクトおよびアプリケーション チームが必要なレベルの制御を行えるようにする必要があります。 一般にこれは、全社的 IT 部門はサブスクリプションを所有し、ネットワークの構成などの中核的 IT 機能を扱うことを意味します。 サブスクリプションの **所有者** のセットは小規模にする必要があります。 これらの所有者は、必要に応じて追加の所有者を指名したり、"パブリック IP なし" などのサブスクリプション レベルのポリシーを適用したりできます。

レベル 1 やレベル 2 のサポートなど、サブスクリプション全体へのアクセスを必要とするユーザーのサブセットが存在する場合があります。 その場合は、このようなユーザーに **共同作成者** のアクセス権を付与してリソースを管理できるようにしながら、ポリシーへのアクセスやその変更は許可しないことをお勧めします。

DevTest Labs のリソースは、プロジェクト/アプリケーション チームの近くにいる所有者によって所有される必要があります。 所有者は、マシンおよび必要なソフトウェアについての要件を理解しているためです。 ほとんどの組織では、一般に、この DevTest Labs リソースの所有者はプロジェクト/開発リーダーです。 この所有者は、ラボ環境内のユーザーとポリシーを管理でき、DevTest Labs 環境にあるすべての VM を管理できます。

プロジェクト/アプリケーション チームのメンバーは、DevTest Labs ユーザー ロールに追加する必要があります。 これらのユーザーは、仮想マシンを (ラボおよびサブスクリプション レベルのポリシーに従って) 作成できます。 また、自分が所有する仮想マシンを管理することもできます。 他のユーザーに属している仮想マシンを管理することはできません。

詳細については、「[Azure enterprise scaffold – prescriptive subscription governance](/azure/architecture/cloud-adoption/appendix/azure-scaffold)」(Azure エンタープライズ スキャフォールディング - 規範的なサブスクリプション ガバナンス) ドキュメントをご覧ください。


## <a name="next-steps"></a>次のステップ
「[Corporate policy and compliance](devtest-lab-guidance-governance-policy-compliance.md)」(会社のポリシーとコンプライアンス) をご覧ください。
