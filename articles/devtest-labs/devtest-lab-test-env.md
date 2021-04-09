---
title: VM と PaaS のテスト環境に Azure DevTest Labs を使用する | Microsoft Docs
description: VM と PaaS のテスト環境シナリオに Azure DevTest Labs を使う方法を説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 978071aa160098612a5effebfb30d5d58b777df7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89299832"
---
# <a name="use-azure-devtest-labs-for-vm-and-paas-test-environments"></a>VM と PaaS のテスト環境に Azure DevTest Labs を使用する

Azure DevTest Labs を使うと多くの重要なシナリオを実装できますが、主要なシナリオは、DevTest Labs を使ってテスト担当者用のコンピューターをホストすることです。 

このシナリオでは、DevTest Labs には次のような利点があります。

- テスト担当者は、再利用可能なテンプレートとアーティファクトを使って Windows と Linux の環境を迅速にプロビジョニングすることで、アプリケーションの最新バージョンをテストできます。
- テスト担当者は、複数のテスト エージェントをプロビジョニングすることで、ロード テストをスケールアップできます。
- 管理者は、次のことが確実に行われるようにしてコストを管理できます。
  - テスト担当者は、必要なものより多くの VM を取得できません。
  - VM は、使われていないときはシャットダウンされます。

![Use DevTest Labs for training](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

この記事では、テスト担当者の要件を満たすために使われる各種 Azure DevTest Labs 機能のほか、ラボをセットアップするための詳細な手順について説明します。

## <a name="implementing-test-environments-with-azure-devtest-labs"></a>Azure DevTest Labs でテスト担当者向け環境を実装する
1. **ラボを作成する** 
   
    ラボは Azure DevTest Labs における開始点です。 ラボを作成したら、ラボへのユーザー (テスト担当者) の追加、コストを管理するためのポリシーの設定、迅速に作成可能な VM イメージの定義など、各種タスクを実行できます。  
   
    次の表のリンクをクリックして詳細を確認してください。
   
   | タスク | 学習内容 |
   | --- | --- |
   | [Azure DevTest Labs でのラボの作成](devtest-lab-create-lab.md) |Azure Portal で Azure DevTest Labs のラボを作成する方法について学びます。 |
2. **既製の Marketplace イメージとカスタム イメージを使って VM を数分で作成する** 
   
    Azure Marketplace にあるさまざまなイメージから既製のイメージを選択し、ラボで利用できるようにすることが可能です。 既製のイメージで要件を満たすことができない場合は、カスタム イメージを作成できます。それには、Azure Marketplace の既製のイメージを使ってラボ VM を作成し、必要なすべてのソフトウェアをインストールしてから、VM をラボにカスタム イメージとして保存します。

    カスタム イメージを使う場合は、イメージ ファクトリを使ってイメージを作成して配布することを検討してください。 イメージ ファクトリはコードとしての構成ソリューションであり、構成されたイメージの定期的なビルドと配布を自動的に行います。 これにより、ベース OS で VM が作成された後にシステムを手動で構成するために必要な時間を節約できます。
  
    次の表のリンクをクリックして詳細を確認してください。
   
   | タスク | 学習内容 |
   | --- | --- |
   | [Azure Marketplace イメージの構成](devtest-lab-configure-marketplace-images.md) |Azure Marketplace イメージで、テスト担当者に必要なイメージのみを選択できるようにする方法について説明します。|
   | [カスタム イメージの作成](devtest-lab-create-template.md) |必要なソフトウェアを事前にインストールしてカスタム イメージを作成し、テスト担当者がそのカスタム イメージを使って VM を迅速に作成できるようにします。|
   | [イメージ ファクトリについて](./devtest-lab-faq.md#blog-post) |イメージ ファクトリを設定して使う方法を説明するビデオをご覧ください。|

3. **テスト マシン用に、再利用可能なテンプレートを作成する** 
   
    Azure DevTest Labs における数式とは、VM の作成に使用できる既定のプロパティ値の一覧です。 ラボで数式を作成するには、イメージ、VM サイズ (CPU と RAM の組み合わせ)、仮想ネットワークを選択します。 各テスト担当者は、ラボの数式を確認して、VM の作成に使うことができます。 
   
    次の表のリンクをクリックして詳細を確認してください。
   
   | タスク | 学習内容 |
   | --- | --- |
   | [VM を作成するための DevTest ラボの数式の管理](devtest-lab-manage-formulas.md) |イメージ、VM サイズ (CPU と RAM の組み合わせ)、仮想ネットワークを選択して数式を作成する方法について学びます。|

3. **複数 VM のテスト環境を作成する** 
   
    Azure Resource Manager テンプレートを使うことで、Azure のソリューションのインフラストラクチャと構成を定義して、複数のテスト VM を一貫した状態で繰り返しデプロイできます。

    Azure PaaS のリソースは、Resource Manager テンプレートから環境にプロビジョニングでき、コスト管理に表示されます。 ただし、VM の自動シャットダウンは、PaaS のリソースには適用されません。

    次の表のリンクをクリックして詳細を確認してください。
   
   | タスク | 学習内容 |
   | --- | --- |
   | [Azure Resource Manager テンプレートを使用してマルチ VM 環境と PaaS リソースを作成する](devtest-lab-create-environment-from-arm.md) |テスト環境に一貫した状態で複数の VM をデプロイする方法について説明します。|

4. **VM を柔軟にカスタマイズできるようにするためのアーティファクトを作成する**

   アーティファクトは、VM のプロビジョニング後にアプリケーションをデプロイして構成するために使用します。 次に示すアーティファクトが対象です。

   - VM にインストールするツール (エージェント、Fiddler、Visual Studio など)。
   - VM 上で実行するアクション (リポジトリの複製など)。
   - テスト対象のアプリケーション。

   多くのアーティファクトは既にすぐに利用できるようになっています。 ただし、特定のニーズのためにさらにカスタマイズが必要な場合は、独自のカスタム アーティファクトを作成できます。

   次の表のリンクをクリックして詳細を確認してください。
   
   | タスク | 学習内容 |
   | --- | --- |
   | [DevTest ラボ VM のカスタム アーティファクトの作成](devtest-lab-artifact-author.md) |ラボの仮想マシン用に独自のカスタム アーティファクトを作成します。|
   | [Azure DevTest Labs で使用するカスタム アーティファクトおよび Azure Resource Manager テンプレートを格納するための Git リポジトリの追加](devtest-lab-add-artifact-repo.md) |独自のプライベート Git リポジトリにカスタム アーティファクトを格納する方法について説明します。|

5. **コストを管理する**
   
    Azure DevTest Labs では、ラボでポリシーを設定して、ラボでテスト担当者が作成できる VM の最大数を指定できます。 
   
    テスト担当者チームが作業スケジュールを設定していて、特定の時間にすべての VM を停止し、次の日に自動的に再起動させる必要がある場合は、ラボで自動シャットダウンと自動起動のポリシーを設定すれば簡単です。 
   
    さらに、アプリの開発が終了したときに、単一の PowerShell スクリプトを実行して、すべての VM を一度に削除することもできます。 
   
    次の表のリンクをクリックして詳細を確認してください。
   
   | タスク | 学習内容 |
   | --- | --- |
   | [ラボのポリシーの定義](devtest-lab-set-lab-policy.md) |ラボでポリシーを設定してコストを管理します。 |
   | [PowerShell スクリプトを使用したすべてのラボ VM の削除](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |テストが完了したときに、1 回の操作ですべてのラボを削除します。|

1. **仮想ネットワークをラボに追加する** 
   
    DevTest Labs は、ラボが作成されるたびに新しい仮想ネットワーク (VNET) を作成します。 たとえば ExpressRoute やサイト間 VPN を使って、独自の VNET を構成してある場合は、この VNET をラボの仮想ネットワーク設定に追加し、VM を作成するときに使うことができます。

    さらに、VM の作成時に VM をドメインに参加させる、Azure Active Directory ドメイン参加アーティファクトを使うことができます。 
   
    次の表のリンクをクリックして詳細を確認してください。
   
   | タスク | 学習内容 |
   | --- | --- |
   | [Azure DevTest Labs で仮想ネットワークを構成する](devtest-lab-configure-vnet.md) |Azure Portal を使って Azure DevTest Labs で仮想ネットワークを構成する方法について説明します。|

6. **各テスト担当者とラボを共有する**
   
    テスト担当者と共有したリンクを使用すれば、ラボに直接アクセスできます。 開発者は [Microsoft アカウント](devtest-lab-faq.md#what-is-a-microsoft-account)さえ持っていれば、Azure アカウントすら必要ありません。 テスト担当者は他のテスト担当者によって作成された VM を見るこはできません。  
   
    次の表のリンクをクリックして詳細を確認してください。
   
   | タスク | 学習内容 |
   | --- | --- |
   | [Azure DevTest Labs でのラボへのテスト担当者の追加](devtest-lab-add-devtest-user.md) |Azure Portal を使って、ラボにテスト担当者を追加します。|
   | [PowerShell スクリプトを使ってラボにテスト担当者を追加する](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |PowerShell を使って、ラボへのテスト担当者の追加を自動化します。 |
   | [ラボへのリンクの取得](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |ハイパーリンクを使ってテスト担当者がラボに直接アクセスできるようにする方法を説明します。|

7. **他のチームのためにラボの作成を自動化する** 
   
    カスタム設定も含め、ラボの作成は自動化できます。それには、Resource Manager テンプレートを作成し、それを使って同一のラボを繰り返し作成します。 
   
    次の表のリンクをクリックして詳細を確認してください。
   
   | タスク | 学習内容 |
   | --- | --- |
   | [Resource Manager テンプレートを使用したラボの作成](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Resource Manager テンプレートを使用して Azure DevTest Labs でラボを作成します。 |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
