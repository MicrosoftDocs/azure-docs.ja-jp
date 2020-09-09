---
title: Azure Lab Services クラスルーム ラボ迅速設定ガイド
description: ラボ作成者はこのガイドに従って、学校内で使用するラボ アカウントをすばやく作成できます。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ad1a679c556316c2d23a713ffa5ac5dfe86cce0e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445578"
---
# <a name="classroom-lab-setup-guide"></a>クラスルーム ラボ設定ガイド

ラボを学生にパブリッシュするプロセスは、ラボに作成する仮想マシン (VM) の数にもよりますが、最大で数時間かかることがあります。 ラボを設定して正常動作を確認し、学生の VM をパブリッシュするための十分な時間を確保するには、少なくとも 1 日は必要です。

## <a name="understand-the-lab-requirements-of-your-class"></a>クラスのラボ要件を理解する

新しいラボを設定する前に、以下の質問について調査する必要があります。

### <a name="what-software-requirements-does-the-class-have"></a>クラスにはどのようなソフトウェア要件があるか?

クラスの学習目標に基づいて、ラボの VM にインストールする必要があるオペレーティング システム、アプリケーション、ツールを決定します。 ラボ VM を設定するには、次の 3 つのオプションがあります。

- **Azure Marketplace イメージを使用する**:Azure Marketplace には、ラボの作成時に使用できる数百のイメージが用意されています。 クラスによっては、クラスに必要なすべてのものが、これらのいずれかのイメージに最初から含まれている場合があります。

- **新しいカスタム イメージを作成する**:Azure Marketplace のイメージを出発点として使用して、追加のソフトウェアをインストールし、構成を変更して、それをカスタマイズすることによって、独自のカスタム イメージを作成できます。

- **既存のカスタム イメージを使用する**:以前に作成した、または学校の他の管理者や教職員が作成した既存のカスタム イメージを再利用できます。 これには、カスタム イメージを保存するリポジトリである Shared Image Gallery を管理者が構成している必要があります。

> [!NOTE]
> Azure Marketplace イメージとカスタム イメージを有効にして、使用できる状態にするのは管理者の役割です。 IT 部門と連携して、必要なイメージを有効にしてもらう必要があります。 自分で作成したカスタム イメージは、自分が所有するラボ内での使用が自動的に有効になります。

### <a name="what-hardware-requirements-does-the-class-have"></a>クラスにはどのようなハードウェア要件があるか?

次のさまざまなコンピューティング サイズを選択できます。

- 入れ子の仮想化サイズ。入れ子にした複数の VM をホストできる 1 つの VM に学生がアクセスできるようにします。 たとえば、ネットワーク コースには、このコンピューティング サイズを使用できます。

- GPU サイズ。高い計算能力が要求されるアプリケーションを学生が使用できるようにします。 たとえば、この選択肢は、人工知能と機械学習に適しています。

使用可能なコンピューティング サイズの完全な一覧については、[VM のサイズ設定](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing)に関するガイドを参照してください。

> [!NOTE]
> 使用可能なコンピューティング サイズはリージョンによって異なるため、ラボのリージョンによっては、サイズが少ない場合があります。 一般に、ニーズに最も近い最小のコンピューティング サイズを選択する必要があります。 Azure Lab Services では、後から必要に応じて、コンピューティング容量が異なる新しいラボを設定できます。

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>外部の Azure リソースやネットワーク リソースに対してクラスはどのような依存関係を持っているか?

ラボ VM でデータベース、ファイル共有、ライセンス サーバーなどの外部リソースを使用する必要がある場合は、管理者と連携して、ラボがこれらのリソースにアクセスできるようにします。

仮想ネットワークによってセキュリティで保護されて*いない* Azure リソースにアクセスする場合、管理者に追加の構成を求める必要はありません。 これらのリソースには、パブリック インターネット経由でアクセスできます。

> [!NOTE]
> VM 上で直接リソースを提供することによって、外部リソースへのラボの依存度を下げられるかどうか検討してください。 たとえば、外部データベースからデータを読み取る必要性をなくすために、VM に直接データベースをインストールできます。  

### <a name="how-will-costs-be-controlled"></a>コストをどのように管理するか?

Lab Services では、従量課金制の価格モデルを使用します。つまり、ラボ VM の実行時間分の料金のみが発生します。 コストを管理するには、通常、次の 3 つのオプションを組み合わせて使用します。

- **[スケジュール]** :スケジュールによって、ラボの VM を起動およびシャットダウンするタイミングを自動的に制御できます。
- **[クォータ]** :クォータは、スケジュールされた時間外に学生が VM にアクセスできる時間数を制御します。 学生が VM を使用している間にクォータに達した場合、VM は自動的にシャットダウンされます。 クォータが増やされない限り、学生は VM を再起動できません。
- **自動シャットダウン**:有効にすると、学生がリモート デスクトップ プロトコル (RDP) セッションから切断後、一定の時間が経過したら、自動シャットダウン設定によって Windows VM が自動的にシャットダウンされます。 既定では、この設定は無効です。  

    > [!NOTE]
    > この設定は現在、Windows にしかありません。

### <a name="how-will-students-save-their-work"></a>学生はどのようにして作業を保存するか?

各学生には、ラボの有効期間中、各人専用の VM が割り当てられます。 学生は次のいずれかを選択できます。

- VM に直接保存する。
- OneDrive や GitHub などの外部の場所に保存する。

学生のラボ VM 上で学生用に OneDrive を自動的に構成できます。

> [!NOTE]
> ラボ以外の場所およびクラスの終了後、学生が保存した作業に引き続きアクセスできるよう、学生は外部リポジトリに作業を保存することが推奨されています。

### <a name="how-will-students-connect-to-their-vm"></a>学生はどのようにして VM に接続するか?

Windows VM への RDP の場合、学生は [Microsoft リモート デスクトップ クライアント](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)を使用することが推奨されています。 リモート デスクトップ クライアントでは、Mac、Chromebook、および Windows がサポートされています。

Linux VM の場合、学生は SSH または RDP を使用できます。 学生に RDP を使用して接続させるには、必要な RDP および GUI パッケージをインストールして構成する必要があります。

## <a name="set-up-your-lab"></a>ラボのセットアップ

クラスのラボの要件を理解したら、設定を開始できます。 ラボの設定方法については、このセクションのリンク先を参照してください。

1. **ラボを作成します。** 手順については、[クラスルーム ラボの作成](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab)に関するチュートリアルを参照してください。

    > [!NOTE]
    > クラスに入れ子の仮想化が必要な場合は、[入れ子の仮想化の有効化](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm)の手順を参照してください。

1. **イメージをカスタマイズして、ラボ VM を発行します。** テンプレート VM と呼ばれる特別な VM に接続します。 次のガイドの手順を参照してください。
    - [テンプレート VM を作成および管理する](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [共有イメージ ギャラリーを使用する](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Windows を使用している場合は、[Windows テンプレート VM の準備](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template)の手順も参照してください。 これらの手順には、学生が使用する OneDrive と Office を設定するための手順が含まれています。

1. **VM プールと容量を管理します。** VM の容量は、クラスでの必要に応じて簡単にスケールアップまたはスケールダウンできます。 新しい VM の設定が必要なため、VM の容量の増加には数時間かかる場合があることに注意してください。 [VM プールの設定と管理](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords)の手順を参照してください。

1. **ラボ ユーザーを追加して管理します。** ラボにユーザーを追加するには、次のチュートリアルの手順を参照してください。
   - [ラボへのユーザーの追加](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [招待をユーザーに送信する](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    学生が使用できるアカウントの種類の詳細については、「[学生アカウント](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts)」を参照してください。
  
1. **コスト管理を設定します。** ラボのコストを管理するには、スケジュール、クォータ、および自動シャットダウンを設定します。 次のチュートリアルを参照してください。

   - [スケジュールを設定する](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > インストールしたオペレーティング システムの種類によっては、VM の起動に数分かかることがあります。 スケジュールされた時間帯にラボ VM が確実に使用できるよう、30 分前に VM を起動することが推奨されます。

   - [ユーザーのクォータ設定](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)と[特定ユーザー用の追加クォータの設定](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [切断時の自動シャットダウンを有効にする](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > テンプレート VM には、スケジュールとクォータは適用されませんが、自動シャットダウン設定は適用されます。 
        > 
        > ラボを作成すると、テンプレート VM が作成されますが、起動はしません。 起動して接続を行い、そのラボの前提条件となるソフトウェアをインストールしてから、発行することができます。 テンプレート VM を発行すると、テンプレート VM がシャットダウン済みでない場合は、自動シャットダウンが実行されます。 
        > 
        > テンプレート VM は、実行時に**コスト**が発生するため、実行しておく必要がない場合はテンプレート VM が確実にシャットダウンされるようにしてください。 


1. **ダッシュボードを使用します。** 手順については、[ラボのダッシュボードの使用](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard)に関するページを参照してください。

    > [!NOTE]
    > ダッシュボードに表示される推定コストは、学生によるラボの使用に対して想定できる最大のコストです。 たとえば、学生が使用していないクォータ時間に対しては、料金は発生*しません*。 コストの予測には、テンプレート VM や共有イメージ ギャラリーの使用料金、またはラボ作成者がユーザー マシンを起動するときの料金は反映 "*されません*"。

## <a name="next-steps"></a>次のステップ

- [クラスルーム ラボの使用状況を追跡する](tutorial-track-usage.md)
  
- [クラスルーム ラボへのアクセス](tutorial-connect-virtual-machine-classroom-lab.md)