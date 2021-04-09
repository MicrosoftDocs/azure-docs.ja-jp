---
title: Azure DevTest Labs でのラボへの VM の追加 | Microsoft Docs
description: Azure portal を使用して、Azure DevTest Labs のラボに仮想マシンを追加する方法について説明します。 ベースとしてカスタム イメージまたは数式のいずれかを選択できます。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 07efcf93b963009494021b90e6c730c4615301ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91297625"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs でラボに VM を追加する
[最初の VM 作成](tutorial-create-custom-lab.md#add-a-vm-to-the-lab)の作業を行った方は、おそらくプレインストールされている[マーケットプレイス イメージ](devtest-lab-configure-marketplace-images.md)から VM を追加したことと思います。 その後さらに VM をラボに追加する必要が生じた場合は、"*ベース*" ([カスタム イメージ](devtest-lab-create-template.md)または [数式](devtest-lab-manage-formulas.md)) を選択することもできます。 このチュートリアルでは、Azure Portal を使用して、DevTest Labs でラボに VM を追加します。

この記事では、ラボで VM のアーティファクトを管理する方法についても説明します。

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs でのラボへの VM の追加手順
1. [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
1. **[すべてのサービス]** を選択し、 **[DEVOPS]** セクションの **[DevTest Labs]** を選択します。 **[DEVOPS]** セクションで **[DevTest Labs]** の隣の [*] (星) を選択した場合。 この操作により、次に簡単にアクセスできるように左側のナビゲーション メニューに **[DevTest Labs]** が追加されます。 その後は、左側のナビゲーション メニューの **[DevTest Labs]** を選択できます。

    ![[すべてのサービス] - [DevTest Labs] を選択する](./media/devtest-lab-create-lab/all-services-select.png)
1. ラボの一覧で、VM を作成するラボを選択します。
2. ラボの **[概要]** ページで、 **[+ 追加]** を選択します。

    ![VM ボタンを追加する](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. **[ベースの選択]** ページで、VM のマーケットプレイス イメージを選択します。
1. **[仮想マシン]** ページの **[基本設定]** タブで、次のようにします。
    1. **[仮想マシン名]** テキスト ボックスに、VM の名前を入力します。 テキスト ボックスには自動生成された一意の名前が自動的に入力されます。 この名前は、電子メール アドレス内のユーザー名に対応しており、その後に一意の 3 桁の数字が続きます。 この機能により、マシンを作成するたびにマシンの名前を考えて入力する時間を節約できます。 必要に応じて、任意の名前でこの自動入力されるフィールドをオーバーライドすることができます。 VM の自動入力される名前をオーバーライドするには、 **[仮想マシン名]** テキスト ボックスに名前を入力します。
    2. **[ユーザー名]** に、仮想マシンの管理者権限を付与するユーザー名を入力します。 マシンの **ユーザー名** は、自動生成された一意の名前で事前入力されます。 この名前は、電子メール アドレス内のユーザー名に対応しています。 この機能により、新しいマシンを作成するたびにユーザー名を決める時間を節約できます。 ここでも、必要に応じて、任意のユーザー名でこの自動入力されるフィールドをオーバーライドすることができます。 ユーザー名の自動入力される値をオーバーライドするには、 **[ユーザー名]** テキスト ボックスに値を入力します。 このユーザーには、仮想マシンの **管理者** 権限が付与されます。
    3. ラボで最初の VM を作成する場合は、ユーザーの **パスワード** を入力します。 このパスワードをラボに関連付けられている Azure Key Vault での既定のパスワードとして保存するには、 **[既定のパスワードとして保存する]** を選択します。 既定のパスワードは、次の名前でキー コンテナーに保存されます:**VmPassword**。 ラボで次の VM を作成しようとすると、**VmPassword** が **パスワード** に自動的に選択されます。 値をオーバーライドするには、 **[保存されているシークレットを使用する]** チェック ボックスをオフにして、パスワードを入力します。

        ![[基本設定] が選択されて [ユーザー設定] の値が入力された [ラボ リソースの作成] ページを示すスクリーンショット。](./media/tutorial-create-custom-lab/new-virtual-machine.png)

        最初にシークレットをキー コンテナーに保存してから、ラボで VM を作成するときにそれを使用することもできます。 詳細については、「[Azure DevTest Labs でキー コンテナーにシークレットを格納する](devtest-lab-store-secrets-in-key-vault.md)」を参照してください。 キー コンテナーに格納されているパスワードを使用するには、 **[保存されているシークレットを使用する]** を選択し、シークレット (パスワード) に対応するキー値を指定します。
    4. **[その他のオプション]** セクションで、 **[サイズの変更]** を選択します。 定義済みの項目のいずれかを選択して、作成する VM のプロセッサ コア、RAM サイズ、ハード ドライブ サイズを指定します。
    5. **[成果物を追加または削除]** を選択します。 基本イメージに追加する成果物を選択して構成します。
    **注:** DevTest Labs やアーティファクトの構成に関する経験がない場合は、「[既存のアーティファクトの VM への追加](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm)」セクションを参照し、終了してからここに戻ってください。
2. 上部で **[詳細設定]** タブに切り替えて、次の操作を行います。
    1. VM が含まれる仮想ネットワークを変更するには、 **[Vnet の変更]** を選択します。
    2. サブネットを変更するには、 **[サブネットの変更]** を選択します。
    3. VM の IP アドレスが **パブリック、プライベート、共有** のいずれかを指定します。
    4. VM を自動的に削除するには、**有効期限の日付と時刻** を指定します。
    5. ラボ ユーザーが VM を要求できるようにするには、 **[このマシンを要求可能にする]** オプションで **[はい]** を選択します。
    6. ラボ ユーザーが使用できる **VM のインスタンス** の数を指定します。

        ![ベースの選択](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
1. **[作成]** を選択して、指定した VM をラボに追加します。

   ラボのページには VM の作成状況が表示されます。最初は **[作成中]** 、VM が起動した後は **[実行中]** と表示されます。

    ![VM の作成の状態](./media/tutorial-create-custom-lab/vm-creation-status.png)

## <a name="add-an-existing-artifact-to-a-vm"></a>既存のアーティファクトの VM への追加
VM を作成するときに、既存のアーティファクトを追加できます。 各ラボには、パブリック DevTest ラボ アーティファクト リポジトリのアーティファクトと、独自のアーティファクト リポジトリに作成または追加されたアーティファクトが含まれます。

* Azure DevTest Labs "*アーティファクト*" を使用すると、VM のプロビジョニング時に実行される "*アクション*" (Windows PowerShell スクリプトの実行、Bash コマンドの実行、ソフトウェアのインストールなど) を指定できます。
* アーティファクト "*パラメーター*" を使用すると、特定のシナリオのアーティファクトをカスタマイズできます。

アーティファクトの作成方法については、 [DevTest Labs VM で使用するための独自のアーティファクトを作成する方法](devtest-lab-artifact-author.md)に関する記事を参照してください。

1. [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
1. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
1. ラボの一覧で、使用する VM が含まれるラボを選択します。
1. **[仮想マシン]** を選択します。
1. 目的の VM を選択します。
1. **[アーティファクトの管理]** を選択します。
1. **[アーティファクトの適用]** を選択します。
1. **[アーティファクトの適用]** ウィンドウで、VM に追加するアーティファクトを選択します。
1. **[アーティファクトの追加]** ウィンドウで、必須のパラメーター値を入力します。必要に応じて、省略可能なパラメーターも入力します。
1. **[追加]** を選択してアーティファクトを追加し、**[アーティファクトの適用]** ウィンドウに戻ります。
1. VM に必要なアーティファクトを引き続き追加します。
1. アーティファクトを追加したら、 [アーティファクトの実行順を変更する](#change-the-order-in-which-artifacts-are-run)ことができます。 [アーティファクトの表示または変更](#view-or-modify-an-artifact)に戻ることもできます。
1. アーティファクトの追加が完了したら、**[適用]** を選択します。

## <a name="change-the-order-in-which-artifacts-are-run"></a>アーティファクトの実行順序の変更
既定では、アーティファクトのアクションは VM に追加された順序で実行されます。
次の手順では、アーティファクトの実行順序を変更する方法を示しています。

1. **[アーティファクトの適用]** ウィンドウの上部で、VM に追加されたアーティファクトの数を示すリンクを選択します。

    ![Number of artifacts added to VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. **[選択されたアーティファクト]** ウィンドウに、目的の順序になるようにアーティファクトをドラッグ アンド ドロップします。 **注:** アーティファクトをドラッグできない場合は、アーティファクトの左側からドラッグしていることを確認してください。
1. 完了したら、 **[OK]** を選択します。

## <a name="view-or-modify-an-artifact"></a>アーティファクトの表示または変更
次の手順では、アーティファクトのパラメーターを表示または変更する方法を示しています。

1. **[アーティファクトの適用]** ウィンドウの上部で、VM に追加されたアーティファクトの数を示すリンクを選択します。

    ![Number of artifacts added to VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. **[選択されたアーティファクト]** ウィンドウで、表示または編集するアーティファクトを選択します。
1. **[アーティファクトの追加]** ウィンドウで必要な変更を行ったら、**[OK]** を選択して **[アーティファクトの追加]** ウィンドウを閉じます。
1. **[OK]** を選択して、**[選択されたアーティファクト]** ウィドウを閉じます。

## <a name="save-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの保存
Azure Resource Manager テンプレートでは、反復可能なデプロイを定義する宣言的な方法が提供されます。
次の手順では、作成される VM の Azure Resource Manager テンプレートを保存する方法について説明します。
保存した Azure Resource Manager テンプレートで、[Azure PowerShell を使用して新しい VM をデプロイ](../azure-resource-manager/templates/overview.md)できます。

1. **[仮想マシン]** ウィンドウで、**[Azure Resource Manager テンプレートの表示]** を選択します。
2. **[Azure Resource Manager テンプレートの表示]** ウィンドウで、テンプレート テキストを選択します。
3. 選択したテキストをクリップボードにコピーします。
4. **[OK]** を選択して、**[Azure Resource Manager テンプレートの表示]** ウィンドウを閉じます。
5. テキスト エディターを開きます。
6. クリップボードからテンプレート テキストを貼り付けます。
7. 後で使用するためにファイルを保存します。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>次のステップ
* VM が作成されたら、VM のウィンドウで **[接続]** を選択して VM に接続できます。
* [DevTest Labs VM のカスタム アーティファクトの作成](devtest-lab-artifact-author.md)方法を学習します。
* [DevTest Labs Azure Resource Manager のクイックスタート テンプレート ギャラリー](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)を検索します。
