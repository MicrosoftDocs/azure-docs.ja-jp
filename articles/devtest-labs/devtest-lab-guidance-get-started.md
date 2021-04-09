---
title: Azure DevTest Labs を使用する一般的なシナリオ
description: この記事では、Azure DevTest Labs と 2 つの一般的な方針を使用して、組織でサービスの使用を開始するための主要なシナリオを提供します。
ms.topic: article
ms.date: 06/20/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1adb9b009a250942bab7e9b0e3b4b3e52840341c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "85481597"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Azure DevTest Labs を使用する一般的なシナリオ
DevTest Labs は企業のニーズに応じてさまざまな要件を満たすように構成できます。  この記事では、一般的なシナリオについて説明します。 各シナリオでは、DevTest Labs を使用するメリットと、これらのシナリオの実装に使用するリソースについて説明します。  

- 開発者デスクトップ
- テスト環境
- トレーニング セッション、ハンズオン ラボ、およびハッカソン
- サンドボックス調査
- クラスルーム ラボ

## <a name="developer-desktops"></a>開発者デスクトップ
開発者は、さまざまなプロジェクトの開発用コンピューターに対し異なる要件を抱えることがよくあります。 DevTest Labs では、開発者は、最も一般的なシナリオに合わせて構成されている、オンデマンドの仮想マシンにアクセスできます。 DevTest Labs には次のような利点があります。

- 組織は、一般的な開発用コンピューターを用意することで、チーム間で一貫性を確保することができます。
- 開発者は、必要に応じた開発用コンピューターのプロビジョニング、または、[既存の事前構成済みコンピューターの要求](devtest-lab-add-claimable-vm.md)をすばやく行うことができます。
- 開発者は、サブスクリプション レベルのアクセス許可がなくても、セルフ サービス方式でリソースをプロビジョニングできます。
- IT または管理者は、[ネットワーク トポロジを事前に定義](devtest-lab-configure-vnet.md)でき、開発者は、特別なアクセスを必要とすることなく、単純で直感的な方法で直接それを活用できます。
- 開発者は、必要に応じて、開発用コンピューターを簡単に[カスタマイズ](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm)できます。
- 管理者は、次のことが確実に行われるようにしてコストを管理できます。
    - 開発者は、開発に必要なものより[多くの VM を取得できません](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user)
    - VM は、使われていないときは[シャットダウンされます](devtest-lab-set-lab-policy.md#set-auto-shutdown)
    - 特定のラボにのみ [VM インスタンス サイズのサブセットを許可](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes)します
    - 各ラボの[コスト ターゲットと通知を管理](devtest-lab-configure-cost-management.md)します

詳細については、「[開発者のための Azure DevTest Labs の使用](devtest-lab-developer-lab.md)」を参照してください。 

## <a name="test-environments"></a>テスト環境
テスト環境を作成し、これを企業全体にわたって管理するには、多大な労力が必要になります。 DevTest Labs では、テスト環境を簡単に作成、更新、または複製できます。 そのため、各チームは必要に応じて、完全に構成された環境にアクセスできます。 このシナリオでは、DevTest Labs には次のような利点があります。

- 組織は、一般的なテスト環境を用意することで、チーム間で一貫性を確保することができます。
- テスト担当者は、再利用可能なテンプレートを使って Windows と Linux の環境を迅速にプロビジョニングすることで、アプリケーションの最新バージョンをテストできます。
- 管理者は、Azure DevOps にラボを接続して、DevOps のシナリオを有効にできます。
- ラボの所有者は、次のことが確実に行われるようにしてコストを管理できます。
    - [環境内の VM は、使われていないときはシャットダウンされます](devtest-lab-set-lab-policy.md#set-auto-shutdown)
    - 特定のラボにのみ [VM インスタンス サイズのサブセットを許可](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes)します
    - 各ラボの[コスト ターゲットと通知を管理](devtest-lab-configure-cost-management.md)します

詳細については、「[VM と PaaS のテスト環境に Azure DevTest Labs を使用する](devtest-lab-test-env.md)」を参照してください。

## <a name="sandboxed-investigations"></a>サンドボックス調査
開発者はさまざまなテクノロジやインフラストラクチャの設計について頻繁に調査を行います。 既定では、DevTest Labs で作成されるすべての環境は、それぞれのリソース グループ内に作成されます。 DevTest Labs ユーザーは、これらのリソースへの読み取りアクセスのみを取得します。 ただし、開発者がより詳細な制御を必要とする場合、ラボ全体の設定を更新して、開発者が作成するすべての環境に関して、[共同作成者権限](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/)を元の DevTest Labs ユーザーに付与することができます。  DevTest Labs では、開発者にはラボで自分が作成した環境に対する共同作成者権限が自動的に付与されます。  このシナリオでは、開発者は自分の開発またはテスト環境に必要な Azure リソースを追加または変更することができます。 ラボの所有者は [[リソースごとのコスト]](devtest-lab-configure-cost-management.md#view-cost-by-resource) ページで、調査で使用される各環境のコストを追跡することができます。

詳細については、「[Set access rights to an environment resource group](https://aka.ms/dtl-sandbox)」 (環境リソース グループにアクセス権限を設定する) を参照してください。

## <a name="trainings-hands-on-labs-and-hackathons"></a>トレーニング、ハンズオン ラボ、およびハッカソン 
Azure DevTest Labs のラボは、ワークショップ、ハンズオン ラボ、トレーニング、ハッカソンなどの一時的なアクティビティの優れたコンテナーとして機能します。  このサービスを使用すると、ラボを作成し、各受講者がトレーニング用に同じ分離環境を作成するために使用できるカスタム テンプレートを提供できます。 このシナリオでは、DevTest Labs には次のような利点があります。

- [ポリシー](devtest-lab-set-lab-policy.md)により、受講者は仮想マシンなどのリソースを必要な数のみ取得します。
- 受講者の単一のアクションにより、事前構成され作成されたマシンが[要求](devtest-lab-add-claimable-vm.md)されます。
- ラボは、[ラボの URL](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) にアクセスすることにより受講者に共有されます。
- 仮想マシンの[有効期限](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs)により、マシンは不要になった時点で削除されます。
- トレーニング終了後、[ラボ](devtest-lab-delete-lab-vm.md#delete-a-lab)とすべての[関連リソース](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab)は簡単に削除することができます。

詳細については、「[トレーニングでの Azure DevTest Labs の使用](devtest-lab-training-lab.md)」を参照してください。  

## <a name="proof-of-concept-vs-scaled-deployment"></a>概念実証と規模拡大デプロイ
DevTest Labs の詳細を確認することに決めた場合には、概念実証と規模拡大デプロイという、将来に向けた 2 つの一般的な方法があります。  

**規模拡大デプロイ** は、数百または数千人の開発者を抱える企業全体に DevTest Labs を展開することを意図した、数週間/数か月間にわたるレビューと計画から構成されます。

**概念実証デプロイ** は、組織の価値を確立する単一のチームによる集約的な作業に焦点を置きます。 規模拡大デプロイにしようと思いがちですが、このアプローチは、概念実証オプションよりも失敗しがちになる傾向があります。 そのため、小規模で開始し、最初のチームから学習し、別の 2、3 のチームで同じアプローチを繰り返してから、得られた知識に基づいて規模拡大デプロイを計画することをお勧めします。 概念実証が成功したら、1 つまたは 2 つのチームを選択し、そのシナリオ (開発環境とテスト環境) を特定し、その現在のユース ケースを文書化して、DevTest Labs を展開することをお勧めします。

## <a name="next-steps"></a>次のステップ
次の記事を参照してください。

- [DevTest ラボの概念](devtest-lab-concepts.md)
- [DevTest Labs に関する FAQ](devtest-lab-faq.md)

