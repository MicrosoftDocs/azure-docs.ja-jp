---
title: Azure DevTest Labs の実装を調整する
description: この記事では、組織での Azure DevTest Labs の実装の調整に関するガイダンスを提供します。
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1958e818f014b7419a1a33e9453fbad460dfc159
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92330616"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>Azure DevTest Labs の実装を調整する
この記事では、Azure DevTest Labs を短時間でデプロイして実装するための推奨される方法を示します。 次の図は、規範的なガイダンスとして全体的なプロセスに注目したものです。業界のさまざまな要件とシナリオが柔軟にサポートされていることがわかります。

![Azure DevTest Labs の実装手順](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>前提条件
この記事では、DevTest Labs をパイロット実装する前に、次のものが既にあることを前提としています。

- **Azure サブスクリプション**:パイロット チームに、Azure サブスクリプションにリソースをデプロイするためのアクセス権があること。 ワークロードが開発とテストのみの場合、使用できるイメージを追加し、Windows 仮想マシンの料金を抑えるため、Enterprise DevTest オファーを選択することをお勧めします。
- **オンプレミス アクセス**:必要な場合、オンプレミスへのアクセスが既に構成されていること。 オンプレミス アクセスは、サイト間 VPN 接続または ExpressRoute を使用して実現できます。 ExpressRoute による接続を確立するには何週間もかかるのが普通なので、プロジェクトを始める前に ExpressRoute を用意しておくことをお勧めします。
- **パイロット チーム**:DevTest Labs を使用する初期開発プロジェクト チームと該当する開発またはテスト アクティビティが特定され、チームの要件/目標/目的が確立されていること。

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>マイルストーン 1:最初のネットワーク トポロジと設計を確立する
Azure DevTest Labs ソリューションをデプロイするときに最初に注目するのは、仮想マシンに対して計画されている接続を確立することです。 必要な手順の概要は以下のとおりです。

1. Azure で DevTest Labs サブスクリプションに割り当てる **初期 IP アドレス範囲** を定義します。 このステップでは、将来の拡張に備えて十分な大きさのブロックを提供できるように、予想される VM 使用数を予測する必要があります。
2. DevTest Labs への **望ましいアクセス方法** を明らかにします (たとえば、外部/内部アクセス)。 このステップでの重要なポイントは、仮想マシンにパブリック IP アドレスを割り当てるかどうかを決定することです (つまり、インターネットから直接アクセスできるようにするか)。
3. Azure クラウド環境とオンプレミスの残りの部分の **接続方法** を決定して確立します。 ExpressRoute による強制ルーティングが有効になっている場合は、企業のファイアウォールを通過するように仮想マシンでプロキシを適切に構成することが必要になる可能性があります。
4. VM を **ドメインに参加させる** 場合は、クラウド ベースのドメイン (たとえば AAD ディレクトリ サービス) またはオンプレミスのドメインのどちらに参加するかを決定します。 オンプレミスの場合は、仮想マシンが参加する Active Directory 内の組織単位 (OU) を決定します。 さらに、ユーザーに参加へのアクセス権があることを確認します (または、ドメインにマシン レコードを作成できるサービス アカウントを確立します)

## <a name="milestone-2-deploy-the-pilot-lab"></a>マイルストーン 2:パイロット ラボをデプロイする
ネットワーク トポロジを設定した後は、以下の手順で最初のパイロット ラボを作成できます。

1. 最初の DevTest Labs 環境を作成します。
2. ラボで使用できる VM イメージとサイズを決定します。 DevTest Labs で使用するために Azure にカスタム イメージをアップロードできるかどうかを決定します。
3. ラボにとって最初の Azure ロールベースのアクセス制御 (Azure RBAC) を作成して、ラボへのアクセスをセキュリティ保護します (ラボ所有者とラボ ユーザー)。 DevTest Labs での ID に対しては Azure Active Directory と同期された Active Directory アカウントを使用することをお勧めします。
4. スケジュール、コスト管理、クレーム可能 VM、カスタム イメージ、数式など、ポリシーを使用するように DevTest Labs を構成します。
5. Azure Repos/Git などのオンライン リポジトリを設定します。
6. パブリック リポジトリかプライベート リポジトリまたは両方の組み合わせのいずれを使用するかを決定します。 デプロイと長期的な維持のために JSON テンプレートを整理します。
7. 必要な場合は、カスタム成果物を作成します。 この手順は省略可能です。 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>マイルストーン 3:ドキュメント化、サポート、学習、改善する
最初のパイロット チームは、作業を始めるために詳細なサポートを必要とする場合があります。 その経験を利用して、引き続き Azure DevTest Labs をロールアウトするために適切なドキュメントとサポートを用意します。

1. 新しい DevTest Labs リソースにパイロット チームを紹介します (デモ、ドキュメント)
2. パイロット チームの経験に基づき、必要に応じてドキュメントを計画して配布します
3. 新しいチームのオンボード プロセスを定型化します (ラボの作成と構成、アクセスの提供など)
4. 最初の利用状況に基づき、IP アドレス空間の当初の予測が合理的で正確かどうかを検証します
5. 適切なコンプライアンスとセキュリティ レビューが完了したことを確認します

## <a name="next-steps"></a>次のステップ
このシリーズの次の記事をご覧ください。[Azure DevTest Labs インフラストラクチャのガバナンス](devtest-lab-guidance-governance-resources.md)
