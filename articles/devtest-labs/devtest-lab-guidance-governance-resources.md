---
title: Azure DevTest Labs インフラストラクチャのガバナンス - リソース
description: この記事では、組織内での Azure DevTest Labs 用リソースの調整と管理について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1e470da5cd317d49f0d0734caa11eed6630d3f32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480917"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Azure DevTest Labs インフラストラクチャのガバナンス - リソース
この記事では、組織内での DevTest Labs 用リソースの調整と管理について説明します。 

## <a name="align-within-an-azure-subscription"></a>Azure サブスクリプション内で調整する 

### <a name="question"></a>Question
Azure サブスクリプション内に DevTest Labs リソースをどのように調整すればよいですか?

### <a name="answer"></a>回答
組織で一般的なアプリケーション開発用に Azure を使い始める前に、IT プランナーはまずサービスの全体的ポートフォリオの一部として機能を導入する方法を確認する必要があります。 確認では次の問題領域に対処する必要があります。

- アプリケーション開発ライフサイクルに関連するコストはどのように測定しますか?
- 組織は提案されたサービス オファリングと企業セキュリティ ポリシーをどのように調整しますか? 
- 開発環境と運用環境を分離するためにセグメント化が必要ですか? 
- 長期的な管理、安定性、成長を容易にするためにどのような制御が導入されますか?

**最初の推奨される方法** は、運用サブスクリプションと開発サブスクリプションの間の境界が線引きされている組織の Azure 分類を確認することです。 次の図では、提案される分類は開発/テスト環境と運用環境の論理的な分離に対応しています。 この方法では、組織は環境ごとに個別に関連するコストを追跡するための部門コードを導入できます。 詳細については、「[Prescriptive subscription governance](/azure/architecture/cloud-adoption/appendix/azure-scaffold)」(サブスクリプションの規範的なガバナンス) をご覧ください。 さらに、[Azure タグ](../azure-resource-manager/management/tag-resources.md)を使用して、追跡用と課金用のリソースを整理できます。

**2 番目の推奨される方法** は、Azure Enterprise Portal 内で DevTest サブスクリプションを有効にすることです。 これにより、組織は Azure エンタープライズ サブスクリプションでは通常使用できないクライアント オペレーティング システムを実行できます。 そして、計算に対してのみ支払い、ライセンスの心配はしなくてもよい、エンタープライズ ソフトウェアを使います。 Microsoft SQL Server などの IaaS 内のギャラリー イメージを含む指定されたサービスの課金が消費量のみに基づくことが保証されます。 Azure DevTest サブスクリプションの詳細については、Enterprise Agreement (EA) のお客様の場合は[こちら](https://azure.microsoft.com/offers/ms-azr-0148p/)を、従量課金制のお客様の場合は[こちら](https://azure.microsoft.com/offers/ms-azr-0023p/)をご覧ください。

![リソースとサブスクリプションの調整](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

このモデルでは、組織は大規模な Azure DevTest Labs を柔軟にデプロイできます。 組織は、100 から 1000 台の仮想マシンを使用するさまざまな部署で何百ものラボを並列で実行することをサポートできます。 構成管理とセキュリティ制御の同じ原則を共有できる一元的なエンタープライズ ラボ ソリューションの概念を促進します。

また、このモデルでは、組織が Azure サブスクリプションに関連付けられているリソースの制限を使い果たすことがないことも保証されます。 サブスクリプションとサービス制限の詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md)」をご覧ください。 DevTest Labs のプロビジョニング プロセスでは、多数のリソース グループを使用する可能性があります。 Azure DevTest サブスクリプションのサポート要求を通じて制限を増やすことを要求できます。 開発サブスクリプションの使用が増えても、運用サブスクリプション内のリソースは影響を受けません。 DevTest Labs のスケーリングの詳細については、「[DevTest Labs でのクォータと制限のスケール](devtest-lab-scale-lab.md)」をご覧ください。

考慮する必要がある一般的なサブスクリプション レベル制限は、運用サブスクリプションと開発サブスクリプションの両方をサポートするためにネットワークの IP 範囲を割り当てる方法です。 これらの割り当てでは、時間経過による増加を考慮する必要があります (Azure の実装を既定にするのではなく、企業がネットワーク スタックを管理する必要があるオンプレミスの接続または別のネットワーク トポロジを想定)。 推奨される方法は、小さいサブネットを含む複数の仮想ネットワークを使用するのではなく、大きい IP アドレス プレフィックスを割り当てられ、多数の大きいサブネットで分割された、いくつかの仮想ネットワークを使用することです。 たとえば、10 個のサブスクリプションで、10 個の仮想ネットワーク (サブスクリプションごとに 1 つ) を定義できます。 分離を必要としないすべてのラボでは、サブスクリプションの VNET 上の同じサブネットを共有できます。

## <a name="maintain-naming-conventions"></a>名前付け規則を維持する

### <a name="question"></a>Question
DevTest Labs 環境全体で名前付け規則を維持するにはどうすればよいですか?

### <a name="answer"></a>回答
現在の社内の名前付け規則を Azure の運用環境にまで拡張し、DevTest Labs 環境全体で一貫性を持たせたいことがあります。

DevTest Labs をデプロイするときは、開始時のポリシーを具体的に設けることをお勧めします。 一元化されたスクリプトと JSON テンプレートを使用してポリシーをデプロイすることで、一貫性を適用できます。 名前付けポリシーは、サブスクリプション レベルで適用される Azure のポリシーによって実装できます。 Azure Policy の JSON サンプルについては、「[Azure Policy のサンプル](../governance/policy/samples/index.md)」をご覧ください。

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>ラボあたりのユーザーの数と組織あたりのラボの数

### <a name="question"></a>Question 
ラボあたりのユーザー数と組織全体で必要なラボの総数の比率を決める方法はありますか?

### <a name="answer"></a>回答
同じ開発プロジェクトに関連付けられている部署と開発グループは、同じラボに関連付けることをお勧めします。 両方のグループに同じ種類のポリシー、イメージ、およびシャットダウン ポリシーを適用できます。 

また、地理的境界を考慮することが必要な場合があります。 たとえば、米国 (US) 北東部の開発者は、米国東部 2 でプロビジョニングされたラボを使用できます。 また、テキサス州ダラスおよびコロラド州デンバーの開発者は、米国中南部のリソースを使用できます。 外部のサード パーティと共同作業する場合は、社内開発者によって使用されていないラボに割り当てることができます。 

また、Azure DevOps プロジェクト内の特定のプロジェクトのラボを使用することもできます。 その後、指定された Azure Active Directory グループによってセキュリティを適用すると、両方のリソースのセットにアクセスできます。 ラボに割り当てられた仮想ネットワークは、ユーザーを統合する別の境界になる場合があります。

## <a name="deletion-of-resources"></a>リソースの削除

### <a name="question"></a>Question
ラボ内のリソースの削除を防ぐにはどうすればよいですか?

### <a name="answer"></a>回答
ラボ レベルで適切なアクセス許可を設定し、承認されたユーザーのみがリソースを削除したりラボのポリシーを変更したりできるようにすることをお勧めします。 開発者は、**DevTest Labs ユーザー** グループ内に配置する必要があります。 開発リーダーまたはインフラストラクチャ リーダーが、**DevTest Labs 所有者** になる必要があります。 ラボの所有者は 2 人だけにすることをお勧めします。 破損を防ぐため、このポリシーをコード リポジトリに拡張します。 ラボのユーザーは、リソースを使用する権限はありますが、ラボのポリシーを更新することはできません。 各組み込みグループがラボ内で持っているロールと権限の一覧については、「[Azure DevTest Labs での所有者とユーザーの追加](devtest-lab-add-devtest-user.md)」を参照してください。

## <a name="move-lab-to-another-resource-group"></a>ラボを別のリソース グループに移動する 

### <a name="question"></a>Question
別のリソース グループへのラボの移動はサポートされていますか?

### <a name="answer"></a>回答
はい。 ラボのホーム ページから [リソース グループ] ページに移動します。 次に、ツール バーの **[移動]** を選択し、別のリソース グループに移動するラボを選択します。 ラボを作成すると、リソース グループが自動的に作成されます。 ただし、企業の名前付け規則に従う別のリソース グループにラボを移動することもできます。 

## <a name="next-steps"></a>次のステップ
「[Manage cost and ownership](devtest-lab-guidance-governance-cost-ownership.md)」(コストと所有権の管理) をご覧ください。
