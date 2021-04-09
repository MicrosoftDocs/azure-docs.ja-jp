---
title: Azure DevTest Labs で Azure DevOps からイメージ ファクトリを実行する
description: この記事では、Azure DevOps (旧称 Visual Studio Team Services) からイメージ ファクトリを実行するために必要な、すべての準備について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: fa7050bae1ff8681e04b6ab38220be9eaf38a64a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85476140"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Azure DevOps からイメージ ファクトリを実行する
この記事では、Azure DevOps (旧称 Visual Studio Team Services) からイメージ ファクトリを実行するために必要な、すべての準備について説明します。

> [!NOTE]
> どのようなオーケストレーション エンジンでも機能します。 Azure DevOps は必須ではありません。 イメージ ファクトリは、Azure PowerShell スクリプトを使用して実行されるので、Windows タスク スケジューラや他の CI/CD システムを使用して、手動で実行できます。

## <a name="create-a-lab-for-the-image-factory"></a>イメージ ファクトリ用のラボの作成
イメージ ファクトリを設定する最初の手順は、Azure DevTest Labs でラボを作成することです。 このラボは、仮想マシンを作成してカスタム イメージを保存する、イメージ ファクトリのラボです。 このラボは、イメージ ファクトリ プロセス全体の一部と見なされます。 ラボを作成した後は、後で必要になるため、必ず名前を保存してください。

## <a name="scripts-and-templates"></a>スクリプトとテンプレート
チームのためにイメージ ファクトリを採用する際の次の手順は、使用可能なものを理解することです。 イメージ ファクトリ スクリプトとテンプレートは、[DevTest Labs GitHub リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)から公的に入手できます。 構成要素の概要を次に示します。

- イメージ ファクトリ。 これはルート フォルダーです。
    - 構成。 イメージ ファクトリへの入力
        - GoldenImages。 このフォルダーには、カスタム イメージの定義を表す JSON ファイルが含まれています。
        - Labs.json。 特定のカスタム イメージを受け取るためにチームがサインアップするファイルです。
- スクリプト。 イメージ ファクトリのエンジンです。

このセクションの記事では、これらのスクリプトとテンプレートについて詳しく説明します。

## <a name="create-an-azure-devops-team-project"></a>Azure DevOps チーム プロジェクトを作成する
Azure DevOps では、ソース コードを格納し、1 つの場所で Azure PowerShell を実行できます。 繰り返しの実行をスケジュールして、イメージを最新状態に維持することができます。 これには、問題の診断用に結果を記録するための優れた機能があります。  Azure DevOps の使用は要件ではありません。ただし、Azure に接続したり Azure PowerShell を実行したりできる任意のハーネス/エンジンを使用できます。

代わりに使用する既存の DevOps アカウントまたはプロジェクトがある場合、この手順をスキップします。

開始するには、Azure DevOps で無料アカウントを作成します。 [https://www.visualstudio.com/](https://www.visualstudio.com/ ) にアクセスし、**Azure DevOps** (以前の VSTS) の真下にある **[無償版の入手]** を選択します。 一意のアカウント名を選択し、Git を使用してコードを管理することを確実に選択する必要があります。 これが作成されたら、チーム プロジェクトに URL を保存します。 サンプル URL を次に示します: `https://<accountname>.visualstudio.com/MyFirstProject`。

## <a name="check-in-the-image-factory-to-git"></a>イメージ ファクトリの Git へのチェックイン
イメージ ファクトリのすべての PowerShell、テンプレート、構成は、[パブリックの DevTest Labs GitHub レポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)内にあります。 新しいチーム プロジェクトにコードを取り込む最も速い方法は、リポジトリをインポートすることです。 これにより、DevTest Labs リポジトリ全体をプルします (こうして追加のドキュメントやサンプルを取得できます)。

1. 前の手順で作成した Azure DevOps プロジェクトを参照してください (URL は **https:\//\<accountname>.visualstudio.com/MyFirstProject** などとなります)。
2. **[Import a Repository]\(リポジトリのインポート\)** を選択します。
3. DevTest Labs リポジトリの **クローン URL** を入力します: `https://github.com/Azure/azure-devtestlab`。
4. **[インポート]** を選択します。

    ![Git リポジトリのインポート](./media/set-up-devops-lab/import-git-repo.png)

必要なもの (イメージ ファクトリ ファイル) のみを厳密にチェックインする場合は、[ここ](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs)に記載されてる手順に従って Git リポジトリを複製し、**scripts/ImageFactory** ディレクトリ内にあるファイルのみをプッシュします。

## <a name="create-a-build-and-connect-to-azure"></a>ビルドの作成と Azure への接続
この時点で、Azure DevOps の Git リポジトリ内にソース ファイルは格納されています。 ここで、Azure PowerShell を実行するためのパイプラインを設定する必要があります。 これらの手順を実行するためのたくさんのオプションがあります。 この記事では簡潔さのためにビルド定義を使用していますが、DevOps ビルド、DevOps リリース (1 つまたは複数の環境)、Windows タスク スケジューラのような他の実行エンジン、または Azure PowerShell を実行できるその他のすべてのハーネスでも機能します。

> [!NOTE]
> 留意しておくべき 1 つの重要な点は、一部の PowerShell のファイルは、多くのカスタム イメージ (10 以上) を作成するときには、実行に長い時間がかかるということです。 無料でホストされる DevOps ビルド/リリース エージェントは、タイムアウトは 30 分であるので、多くのイメージのビルドを開始したら、無料でホストされるエージェントは使用できません。 このタイムアウトの課題はどのハーネスを使用する場合でも適用されるので、長時間実行の Azure PowerShell スクリプトの場合は、標準的なタイムアウトを延長できるということを前もって確認しておくのは良いことです。 Azure DevOps の場合は、有料でホストされるエージェントを使用するか、独自のビルド エージェントを使用することができます。

1. 開始するには、DevOps プロジェクトのホームページで **[ビルドのセットアップ]** を選択します。

    ![[ビルドのセットアップ] ボタン](./media/set-up-devops-lab/setup-build-button.png)
2. ビルドの **名前** を指定します (たとえば、"イメージのビルドと DevTest Labs への配信")。
3. **空の** ビルド定義を選択し、 **[適用]** を選択してビルドを作成します。
4. この段階で、ビルド エージェントに **[Hosted]\(ホステッド\)**  を選択できます。
5. ビルド定義の **[保存]** を実行します。

    ![ビルド定義](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>ビルド変数の構成
コマンド ライン パラメーターを簡素化するには、イメージ ファクトリを一連のビルド変数にするキー値をカプセル化します。 **[変数]** タブを選択すると、いくつかの既定の変数の一覧が表示されます。 Azure DevOps に入力する変数の一覧を次に示します。


| 変数名 | 値 | Notes |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | これは、リポジトリ内の **Configuration** フォルダーへのフルパスです。 上記のリポジトリ全体をインポートした場合、左側の値が正しいです。 そうでない場合、構成の場所を指すように更新します。 |
| DevTestLabName | MyImageFactory | イメージを生成するためのファクトリとして使用される Azure DevTest Labs のラボの名前です。 それがない場合は、作成します。 ラボが、サービス エンドポイントがアクセスするのと同じサブスクリプション内にあることを確認します。 |
| ImageRetention | 1 | 各タイプの保存に必要なイメージの数。 既定値は 1 に設定します。 |
| MachinePassword | ******* | 仮想マシンの組み込み管理者アカウントのパスワード。 これは一時的なアカウントであるので、安全であることを確認します。 安全な文字列であることを確認するには、右側の小さなロック アイコンを選択します。 |
| MachineUserName | ImageFactoryUser | 仮想マシンの組み込み管理者アカウント ユーザー名。 これは一時的なアカウントです。 |
| StandardTimeoutMinutes | 30 | 通常の Azure 操作を待つ必要があるタイムアウト。 |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | ラボが存在し、サービス エンドポイントがアクセスできるサブスクリプションの ID。 |
| VMSize | Standard_A3 | **作成** 手順に使用する仮想マシンのサイズ。 作成された VM は一時的なものです。 サイズは、[ラボに有効](devtest-lab-set-lab-policy.md)なものでなければなりません。 十分な[サブスクリプション コア クォータ](../azure-resource-manager/management/azure-subscription-service-limits.md)があることを確認します。

![ビルド変数](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Azure に接続する
次の手順では、サービス プリンシパルを設定します。 これは、Azure Active Directory 内の ID であり、これによりユーザーの代わりに Azure で DevOps ビルド エージェントが動作します。 設定するには、まず Azure PowerShell のビルド ステップを追加することから始めます。

1. **[タスクの追加]** を選択します。
2. **Azure PowerShell** を検索します。
3. 見つかった場合は、 **[追加]** を選択してタスクをビルドに追加します。 これを実行すると、追加されたタスクが左側に表示されます。

![PowerShell のセットアップ手順](./media/set-up-devops-lab/set-up-powershell-step.png)

サービス プリンシパルをセットアップする最も速い方法は、Azure DevOps にその実行を任せることです。

1. 先ほど追加した **タスク** を選択します。
2. **[Azure 接続の種類]** で、 **[Azure Resource Manager]** を選択します。
3. **[管理]** リンクを選択して、サービス プリンシパルを設定します。

詳細については、[このブログの投稿](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)を参照してください。 **[管理]** リンクを選択すると、Azure への接続を設定するための、DevOps の適切な場所が表示されます (ブログ投稿の 2 番目のスクリーン ショット)。 これを設定するときには、**Azure Resource Manager サービス エンドポイント** を必ず選択します。

## <a name="complete-the-build-task"></a>ビルド タスクを実行する
ビルド タスクを選択した場合、入力する必要があるすべての詳細が右側のウィンドウに表示されます。

1. 最初に、ビルド タスクの名前を付けます。**仮想マシンを作成します**。
2. **Azure Resource Manager** を選択して作成した **サービス プリンシパル** を選択します。
3. **サービス エンドポイント** を選択します。
4. **[スクリプト パス]** に、右側で **...(省略記号)** を選択します。
5. **MakeGoldenImageVMs.ps1** スクリプトに移動します。
6. スクリプトのパラメーターは、次のように表示されます。`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![ビルド定義を作成する](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>ビルドをキューに配置する
新しいビルドをキューに配置することで、すべてが正しく設定されていることを確認します。 ビルドの実行中に、[Azure portal](https://portal.azure.com) に切り替え、 **[All Virtual Machines]\(すべての仮想マシン\)** をイメージ ファクトリのラボで選択して、すべてが正しく動作することを確認します。 ラボに 3 つの仮想マシンが作成されていることが表示されます。

![ラボの VM](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>次のステップ
Azure DevTest Labs に基づいてイメージ ファクトリを設定する最初の手順が完了しました。 シリーズの次の記事では、一般化されてカスタム イメージに保存される VM について説明します。 次に、それらを他のすべてのラボに配布します。 シリーズの次の記事をご覧ください。「[カスタム イメージを保存して複数のラボに配布する](image-factory-save-distribute-custom-images.md)」。
