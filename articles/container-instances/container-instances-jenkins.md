---
title: Azure Container Instances を Jenkins ビルド エージェントとして使用する
description: Azure Container Instances を Jenkins ビルド エージェントとして使用する方法を説明します。
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 08/31/2018
ms.author: danlep
ms.openlocfilehash: 2687a64bfd952888086862d929a3e5869aee5a4f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58003424"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Azure Container Instances を Jenkins ビルド エージェントとして使用する

Azure Container Instances (ACI) は、コンテナー化ワークロードを実行するためのバースト対応のオンデマンド分離環境を提供します。 これらの特性により、ACI は大規模な環境で Jenkins ビルド ジョブを実行するための優れたプラットフォームを作成します。 この記事では、ビルド ターゲットとして ACI で事前に構成されている Jenkins サーバーの展開と使用の手順について説明します。

Azure Container Instances について詳しくは、[Azure Container Instances][about-aci] に関する記事をご覧ください。

## <a name="deploy-a-jenkins-server"></a>Jenkins サーバーを展開する

1. Azure Portal で **[リソースの作成]** を選択し、**Jenkins** を検索します。 **Microsoft** が発行している Jenkins を選択し、**[作成]** を選択します。

2. **[基本]** フォームに次の情報を入力した後、**[OK]** を選びます。

   - **[名前]**:Jenkins デプロイの名前を入力します。
   - **[ユーザー名]**: Jenkins 仮想マシンの管理ユーザーの名前を入力します。
   - **[認証の種類]**: 認証には SSH 公開キーをお勧めします。 このオプションを選んだ場合は、Jenkins 仮想マシンへのログインに使う SSH 公開キーを貼り付けます。
   - **サブスクリプション**:Azure サブスクリプションを選択します。
   - **[リソース グループ]**:リソース グループを作成するか、既存のリソース グループを選択します。
   - **[場所]**:Jenkins サーバーの場所を選びます。

   ![Jenkins ポータル展開の基本設定](./media/container-instances-jenkins/jenkins-portal-01.png)

3. **[追加設定]** フォームに、次の項目を入力します。

   - **[サイズ]**:Jenkins 仮想マシンの適切なサイズ オプションを選びます。
   - **VM ディスクの種類**: Jenkins サーバーの **HDD** (ハード ディスク ドライブ) または **SSD** (ソリッドステート ドライブ) を指定します。
   - **仮想ネットワーク**:既定の設定を変更する場合、矢印を選びます。
   - **サブネット**:矢印を選び、情報を確認して、**[OK]** を選びます。
   - **[パブリック IP アドレス]**: 矢印を選んでパブリック IP アドレスにカスタムの名前を付け、SKU を構成して、割り当て方法を設定します。
   - **[ドメイン名ラベル]**: 値を指定して Jenkins 仮想マシンの完全修飾 URL を作成します。
   - **[Jenkins のリリースの種類]**: 次のオプションから必要なリリースの種類を選択します。(**[LTS]**、**[Weekly build]\(週次ビルド\)**、または **[Azure Verified]\(Azure 確認済み\)**)。

   ![Jenkins ポータル展開の追加設定](./media/container-instances-jenkins/jenkins-portal-02.png)

4. サービス プリンシパル統合では、**[Auto(MSI)]\(自動 (MSI)\)** を選んで、[Azure リソースのマネージド ID][managed-identities-azure-resources] が Jenkins インスタンスの認証 ID を自動的に作成するようにします。 独自のサービス プリンシパル資格情報を提供するには **[手動]** を選びます。

5. クラウド エージェントは、Jenkins ビルド ジョブのクラウドベース プラットフォームを構成します。 この記事では、**[ACI]** を選びます。 ACI クラウド エージェントでは、各 Jenkins ビルド ジョブはコンテナー インスタンス内で実行されます。

   ![Jenkins ポータル展開のクラウド統合の設定](./media/container-instances-jenkins/jenkins-portal-03.png)

6. 統合の設定が完了したら、**[OK]** を選び、検証の概要でもう一度 **[OK]** を選びます。 **[使用条件]** の概要で **[作成]** を選びます。 Jenkins サーバーのデプロイには数分かかります。

## <a name="configure-jenkins"></a>Jenkins を構成する

1. Azure portal で、Jenkins リソース グループを参照し、Jenkins 仮想マシンを選んで、DNS 名をメモします。

   ![Jenkins 仮想マシンについての詳細での DNS 名](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Jenkins VM の DNS 名を参照し、返される SSH 文字列をコピーします。

   ![SSH 文字列を使用した Jenkins のログイン手順](./media/container-instances-jenkins/jenkins-portal-04.png)

3. 開発システム上でターミナル セッションを開き、前の手順の SSH 文字列を貼り付けます。 Jenkins サーバーを展開するときに指定したユーザー名で `username` を更新します。

4. セッションが接続された後、次のコマンドを実行して、初期管理者パスワードを取得します。

   ```
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. SSH セッションとトンネルを実行したままにして、ブラウザーで `http://localhost:8080` に移動します。 ボックスに初期管理者パスワードを貼り付けて、**[続行]** を選びます。

   ![管理者パスワード入力ボックスが表ｊしあれた "Jenkins ロック解除" 画面](./media/container-instances-jenkins/jenkins-portal-05.png)

6. **[Install suggested plugins]\(推奨されるプラグインのインストール\)** を選択し、推奨されるすべての Jenkins プラグインをインストールします。

   !["推奨されるプラグインのインストール" が選択された "Jenkins カスタマイズ" 画面](./media/container-instances-jenkins/jenkins-portal-06.png)

7. 管理者ユーザー アカウントを作成します。 このアカウントは、Jenkins インスタンスへのログインと操作に使われます。

   ![資格情報が入力された "初期管理者ユーザー作成" 画面](./media/container-instances-jenkins/jenkins-portal-07.png)

8. **[Save and Finish]\(保存して終了\)** を選び、**[Start using Jenkins]\(Jenkins の使用を開始\)** を選んで構成を完了します。

Jenkins が構成され、コードのビルドとデプロイの準備が完了します。 この例では、単純な Java アプリケーションを使用して、Azure Container Instances 上での Jenkins のビルドを示します。

## <a name="create-a-build-job"></a>ビルド ジョブを作成する

Jenkins のビルド ジョブが作成され、Azure コンテナー インスタンス上で Jenkins のビルドをデモできるようになりました。

1. **[新しい項目]** を選択し、ビルド プロジェクトに **aci-demo** などの名前を付け、**[Freestyle project]\(Freestyle プロジェクト\)** を選択し、**[OK]** を選択します。

   ![ビルド ジョブの名前のボックスと、プロジェクトの種類の一覧](./media/container-instances-jenkins/jenkins-new-job.png)

2. **[全般]** で、**[Restrict where this project can be run]\(このプロジェクトを実行できる場所を制限する\)** が選択されていることを確認します。 ラベル式に「**linux**」と入力します。 この構成により、このビルド ジョブが ACI クラウド上で実行されます。

   ![構成の詳細画表示された [全般] タブ](./media/container-instances-jenkins/jenkins-job-01.png)

3. **[ビルド]** で **[ビルド ステップの追加]** を選択し、**[シェルの実行]** を選択します。 コマンドとして `echo "aci-demo"` を入力します。

   ![ビルド ステップが選ばれた [ビルド] タブ](./media/container-instances-jenkins/jenkins-job-02.png)

5. **[保存]** を選択します。

## <a name="run-the-build-job"></a>ビルド ジョブの実行

ビルド ジョブをテストし、ビルド プラットフォームとしての Azure コンテナー インスタンスを確認するには、ビルドを手動で開始します。

1. **[Build Now]\(今すぐビルド\)** を選択してビルド ジョブを開始します。 ジョブが開始するまで､数分かかります｡ 次の図ような状態が表示されます。

   ![ジョブの状態が表示された "ビルド履歴" 情報](./media/container-instances-jenkins/jenkins-job-status.png)

2. ジョブの実行中に、Azure Portal を開き、Jenkins リソース グループを参照します。 コンテナー インスタンスが作成されています。 Jenkins ジョブはこのインスタンス内で実行されています。

   ![リソース グループ内のコンテナー インスタンス](./media/container-instances-jenkins/jenkins-aci.png)

3. Jenkins が Jenkins 実行子の数 (既定値は 2) より多くのジョブを実行すると、複数のコンテナー インスタンスが作成されます。

   ![新しく作成されたコンテナー インスタンス](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. すべてのビルド ジョブが完了すると、コンテナー インスタンスは削除されます。

   ![コンテナー インスタンスが削除されたリソース グループ](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Jenkins プラグインのトラブルシューティング

Jenkins プラグインでバグが発生した場合は、[Jenkins JIRA](https://issues.jenkins-ci.org/) で特定のコンポーネントについて問題を報告してください。

## <a name="next-steps"></a>次の手順

Azure 上の Jenkins について詳しくは、「[Azure と Jenkins][jenkins-azure]」をご覧ください。

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-identities-azure-resources]: ../active-directory/managed-identities-azure-resources/overview.md