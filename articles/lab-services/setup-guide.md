---
title: Azure Lab Services ラボ迅速設定ガイド
description: ラボの作成者は、このガイドを使用して、学校でラボ アカウントをすばやく設定できます。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 07f0d92ebd926616f1318b430bec2de32f753f7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95021732"
---
# <a name="lab-setup-guide"></a>ラボ設定ガイド

このガイドでは、学校で学生向けのラボを作成する方法について説明します。

ラボを学生にパブリッシュするプロセスには、最大で数時間かかることがあります。 設定にかかる時間は、ラボで作成する仮想マシン (VM) の数によって異なります。 ラボが正しく機能することを確認し、学生の VM をパブリッシュするための十分な時間を確保するには、少なくとも 1 日は必要です。

## <a name="understand-the-lab-requirements-of-your-class"></a>クラスのラボ要件を理解する

新しいラボを設定する前に、以下の質問について調査する必要があります。

### <a name="what-software-requirements-does-the-class-have"></a>クラスにはどのようなソフトウェア要件があるか?

クラスの学習目標を確認して、ラボの VM にインストールする必要があるオペレーティング システム、アプリケーション、ツールを決定します。 ラボ VM を設定するには、次の 3 つのオプションがあります。

- **Azure Marketplace イメージを使用する**:Azure Marketplace には、ラボの作成時に使用できる数百のイメージが用意されています。 クラスによっては、クラスに必要なすべてのものが、これらのいずれかのイメージに最初から含まれている場合があります。

- **新しいカスタム イメージを作成する**:Azure Marketplace イメージを元にして、独自のカスタム イメージを作成できます。 その後、追加ソフトウェアのインストールや構成の変更を行ってカスタマイズできます。

- **既存のカスタム イメージを使用する**:以前に作成したカスタム イメージや、学校の他の管理者や教職員が作成したイメージを再利用できます。 カスタム イメージを使用するには、管理者に Shared Image Gallery を設定してもらう必要があります。  Shared Image Gallery は、カスタム イメージの保存と共有に使用できるリポジトリです。

> [!NOTE]
> Azure Marketplace イメージとカスタム イメージを有効にして、使用できる状態にするのは管理者の役割です。 IT 部門と連携して、必要なイメージを有効にする必要があります。 自分で作成したカスタム イメージは、自分が所有するラボ内での使用が自動的に有効になります。

### <a name="what-hardware-requirements-does-the-class-have"></a>クラスにはどのようなハードウェア要件があるか?

次のように、さまざまなサイズに基づいたコンピューティング サイズを選択できます。

- **入れ子にした仮想マシンのサイズ**:複数の入れ子にした VM をホストできる VM へのアクセス権を学生に許可できます。 たとえば、ネットワークまたは倫理的ハッキングのクラスには、このコンピューティング サイズを使用できます。

- **GPU サイズ**:高い計算能力が要求されるアプリケーションを学生が使用できるようにします。 この選択は、たとえば、人工知能や機械学習でよく使用されます。

適切な VM サイズを選択するためのガイダンスについては、次を参照してください。
- [VM のサイズ設定](./administrator-guide.md#vm-sizing)
- [物理ラボから Azure Lab Services への移動](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

> [!NOTE]
> どのコンピューティング サイズを利用できるかは地域に応じて異なるため、実際にラボで使用できるサイズの種類は限られる場合があります。 通常は、ニーズに最も適した最小のコンピューティング サイズを選択する必要があります。 Azure Lab Services では、後から必要に応じて、コンピューティング容量がより大きい新しいラボを設定できます。

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>外部の Azure リソースやネットワーク リソースに対してクラスはどのような依存関係を持っているか?
ラボ VM には、データベース、ファイル共有、ライセンス サーバーなどの外部リソースへのアクセスが必要になる場合があります。  ラボ VM で外部リソースを使用できるようにするには、IT 管理者と調整してください。

> [!NOTE]
> VM 上で直接ネットワーク リソースを提供することによって、外部リソースへのラボの依存度を下げられるかどうか検討してください。 たとえば、外部データベースからデータを読み取る必要性をなくすために、VM に直接データベースをインストールできます。  

### <a name="how-will-you-control-costs"></a>どのようにコストを管理するか?
Lab Services では、従量課金制の価格モデルを使用します。つまり、ラボ VM の実行時間分の料金のみが発生します。 コストを管理するため、次のオプションのうち、すべてまたはいずれかを使用できます。

- **[スケジュール]** :スケジュールを使用すると、ラボの VM を起動およびシャットダウンするタイミングを自動的に制御できます。
- **[クォータ]** :クォータを使用すると、スケジュールされた時間外に学生が VM にアクセスできる時間数を制御できます。  学生が VM を使用していて、クォータに達すると、その VM は自動的にシャットダウンされます。  クォータを増やさないかぎり、学生は VM を再起動できません。
- **[Automatic shutdown]\(自動シャットダウン\)** :自動シャットダウン設定を有効にした場合、学生がリモート デスクトップ プロトコル (RDP) セッションから切断すると Windows VM が自動的にシャットダウンされます。 既定では、この設定は無効になっています。

コストの管理方法については、次を参照してください。
- [コストの見積もり](./cost-management-guide.md#estimate-the-lab-costs)
- [コストを管理する](./cost-management-guide.md#manage-costs)

### <a name="how-will-students-save-their-work"></a>学生はどのようにして作業を保存するか?
各学生には、ラボが終わるまで有効な VM が割り当てられます。 学生は自分の作業結果を次の場所に保存できます。

- VM。
- OneDrive や GitHub などの外部の場所。 学生のラボ VM 上で学生用に OneDrive を自動的に構成できます。

> [!NOTE]
> クラス終了後に、学生がラボ以外の場所でも自分の保存済み作業に引き続きアクセスできるようにするため、作業の保存先は外部リポジトリにすることが推奨されています。

### <a name="how-will-students-connect-to-their-vms"></a>学生はどのようにして VM に接続するか?
Windows VM に RDP 接続する場合、学生には [Microsoft リモート デスクトップ クライアント](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)を使用することが推奨されます。 リモート デスクトップ クライアントでは、Mac、Chromebook、Windows デバイスがサポートされています。

Linux VM の場合、学生は Secure Shell (SSH) または RDP プロトコルを使用できます。 学生に RDP を使用して接続させるには、必要な RDP およびグラフィカル ユーザー インターフェイス (GUI) パッケージをインストールして構成する必要があります。

### <a name="will-students-also-use-microsoft-teams"></a>学生も Microsoft Teams を使用するか?
Azure Lab Services は Microsoft Teams と統合されているため、教職員が Teams 内でラボを作成して管理できます。  同様に、学生は Teams 内でラボにアクセスできます。

詳細については、[Microsoft Teams 内の Azure Lab Services](./lab-services-within-teams-overview.md)に関するページをご覧ください。

## <a name="set-up-your-lab"></a>ラボのセットアップ

クラスのラボの要件を理解したら、設定を開始できます。 その方法については、このセクションのリンクを参照してください。 Teams でラボを設定するための手順も記載されています。

1. **ラボを作成する**。 次のチュートリアルを参照してください。
    - [クラスルーム ラボを作成する](./tutorial-setup-classroom-lab.md#create-a-classroom-lab)
    - [Teams でラボを作成する](./how-to-get-started-create-lab-within-teams.md)

    > [!NOTE]
    > クラスに入れ子の仮想環境が必要な場合は、[入れ子になった仮想化の有効化](./how-to-enable-nested-virtualization-template-vm.md)に関するページを参照してください。

1. **イメージをカスタマイズして、ラボ VM を発行します**。 テンプレート VM と呼ばれる特別な VM に接続するには、次を参照してください。
    - [テンプレート VM を作成および管理する](./tutorial-setup-classroom-lab.md#publish-the-template-vm)
    - [共有イメージ ギャラリーを使用する](./how-to-use-shared-image-gallery.md)

    > [!NOTE]
    > Windows を使用している場合は、[Windows テンプレート VM の設定](./how-to-prepare-windows-template.md)に関するページも参照してください。 これらの手順には、学生用の OneDrive と Microsoft Office を設定するための手順が含まれています。

1. **VM プールと容量を管理します**。 VM の容量は、クラスでの必要に応じて簡単にスケールアップまたはスケールダウンできます。 VM 容量の増量は、新しい VM の設定が伴うため、数時間かかることがあります。 次の記事をご覧ください。
    - [VM プールを設定および管理する](./how-to-set-virtual-machine-passwords.md)
    - [Teams で Lab Services の VM プールを管理する](./how-to-manage-vm-pool-within-teams.md)

1. **ラボ ユーザーを追加して管理します**。 ラボにユーザーを追加するには、次を参照してください。
   - [ラボへのユーザーの追加](./tutorial-setup-classroom-lab.md#add-users-to-the-lab)
   - [招待をユーザーに送信する](./tutorial-setup-classroom-lab.md#send-invitation-emails-to-users)
   - [Teams で Lab Services ユーザー一覧を管理する](./how-to-manage-user-lists-within-teams.md)

    学生が使用できるアカウントの種類の詳細については、「[学生アカウント](./how-to-configure-student-usage.md#student-accounts)」を参照してください。
  
1. **コスト管理を設定します**。 スケジュールを設定し、クォータを決定し、自動シャットダウンを有効にするには、次のチュートリアルを参照してください。

   - [スケジュールを設定する](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)

        > [!NOTE]
        > インストールしたオペレーティング システムによっては、VM の起動に数分かかることがあります。 スケジュールされた時間帯にラボ VM が確実に使用できるよう、30 分前に VM を起動することが推奨されます。

   - [ユーザーのクォータ設定](./how-to-configure-student-usage.md#set-quotas-for-users)と[特定ユーザー用の追加クォータの設定](./how-to-configure-student-usage.md#set-additional-quotas-for-specific-users)
  
   - [切断時の自動シャットダウンを有効にする](./how-to-enable-shutdown-disconnect.md)

        > [!NOTE]
        > テンプレート VM には、スケジュールとクォータは適用されませんが、自動シャットダウン設定は適用されます。 
        > 
        > ラボを作成すると、テンプレート VM が作成されますが、起動はされません。 テンプレート VM を起動して接続を行い、そのラボの前提条件となるソフトウェアをインストールしてから、発行することができます。 テンプレート VM を発行すると、自動シャットダウンが実行されます (手動でシャットダウンしない場合)。 
        > 
        > テンプレート VM では、実行時に *コスト* が発生するため、実行しておく必要がない場合はテンプレート VM が確実にシャットダウンされるようにしてください。

    - [Teams 内で Lab Services のスケジュールを作成および管理する](./how-to-create-schedules-within-teams.md) 

1. **ダッシュボードを使用します**。 手順については、[クラスルーム ラボ用のダッシュボードの使用](./use-dashboard.md)に関する記事を参照してください。

    > [!NOTE]
    > ダッシュボードに表示される推定コストは、学生によるラボの使用に対して想定される最大のコストです。 たとえば、学生が使用していないクォータ時間に対しては、料金は発生 *しません*。 コストの予測には、テンプレート VM や共有イメージ ギャラリーの使用料金、またはラボ作成者がユーザー マシンを起動するときの料金は反映 "*されません*"。

## <a name="next-steps"></a>次のステップ

ラボ管理の一環として、次の記事を参照してください。
- [クラスルーム ラボの使用状況の追跡](tutorial-track-usage.md)  
- [クラスルーム ラボへのアクセス](tutorial-connect-virtual-machine-classroom-lab.md)
