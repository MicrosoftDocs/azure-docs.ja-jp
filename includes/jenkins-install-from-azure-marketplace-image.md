---
description: インクルード ファイル
author: tomarcher
manager: rloutlaw
ms.service: multiple
ms.workload: web
ms.devlang: na
ms.topic: include
ms.date: 03/12/2018
ms.author: tarcher
ms.custom: Jenkins
ms.openlocfilehash: 5439de30b02b0ce05853c8112f9e29239743ef98
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181795"
---
1. ブラウザーで、[Jenkins 用の Azure Marketplace イメージ](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview)を開きます。

1. **[今すぐ入手する]** を選択します。

    ![[今すぐ入手する] を選択して、Jenkins Marketplace イメージのインストール プロセスを開始する。](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. 料金の詳細と使用条件についての情報を確認した後、 **[続行]** を選択します。

    ![Jenkins Marketplace イメージの料金と使用条件の情報。](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. **[作成]** を選択して、Azure ポータルで Jenkins サーバーを構成します。 

    ![Jenkins Marketplace イメージをインストールする。](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. **[基本]** タブで、次の値を指定します。

   - **[Name]\(名前\)** - 「`Jenkins`」と入力します。
   - **ユーザー名** - Jenkins が実行される仮想マシンへのサインイン時に使用するユーザー名を入力します。 ユーザー名は、[特定の要件](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm)を満たしている必要があります。
   - **認証の種類** - **[SSH 公開キー]** を選択します。
   - **SSH 公開キー** - RSA 公開キーをコピーして、1 行形式 (`ssh-rsa` で始まります) または複数行の PEM 形式で貼り付けます。 SSH キーは、Linux と macOS では ssh-keygen を、Windows では PuTTYGen を使用して生成できます。 SSH キーと Azure の詳細については、「[Azure 上の Windows で SSH キーを使用する方法](/azure/virtual-machines/linux/ssh-from-windows)」を参照してください。
   - **サブスクリプション**: Jenkins をインストールする Azure サブスクリプションを選択します。
   - **リソース グループ**: **[新規作成]** を選択し、Jenkins のインストールを構成するリソースのコレクションの論理コンテナーとして機能するリソース グループの名前を入力します。
   - **場所**: **[米国東部]** を選択します。

     ![[基本] タブに Jenkins の認証とリソース グループの情報を入力する。](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. **[OK]** を選択して、 **[追加設定]** タブに進みます。 

1. **[追加設定]** タブで、次の値を指定します。

   - **サイズ**: Jenkins 仮想マシンの適切なサイズ オプションを選択します。
   - **VM ディスクの種類**: Jenkins 仮想マシンで許可されるストレージ ディスクの種類を示すために、[HDD] \(ハード ディスク ドライブ) または [SSD] \(ソリッドステート ドライブ) のどちらかを指定します。
   - **仮想ネットワーク** - (省略可能) **[仮想ネットワーク]** を選択して既定の設定を変更します。
   - **サブネット** - **[サブネット]** を選択し、情報を確認し、 **[OK]** を選択します。
   - **パブリック IP アドレス**: IP アドレス名の既定値は、前のページで指定した Jenkins 名にサフィックス-IP が付いたものになります。 この既定値を変更するオプションを選択できます。
   - **ドメイン名ラベル**: Jenkins 仮想マシンの完全修飾 URL の値を指定します。
   - **Jenkins リリースの種類** - オプションから必要なリリースの種類を選択します (`LTS`、`Weekly build`、または`Azure Verified`)。 `LTS` オプションと `Weekly build` オプションについては、「[Jenkins LTS Release Line](https://jenkins.io/download/lts/)」(Jenkins LTS リリース ライン) に説明があります。 `Azure Verified` オプションは、Azure での実行が検証されている [Jenkins LTS バージョン](https://jenkins.io/download/lts/) を参照します。 
   - **JDK の種類** - インストールする JDK。 既定値は、OpenJDK のテスト済み、認定済みのビルドである Zulu です。

     ![[設定] タブで、Jenkins の仮想マシン設定を入力する。](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. **[OK]** を選択して、 **[統合設定]** タブに進みます。

1. **[統合設定]** タブで、次の値を指定します。

    - **サービス プリンシパル** - サービス プリンシパルは、Azure で認証されるための資格情報として Jenkins に追加されます。 `Auto` は、プリンシパルが MSI (管理対象サービス ID) によって作成されることを意味します。 `Manual` は、プリンシパルを自分で作成する必要があることを意味します。 
        - **アプリケーション ID** と**シークレット** - **[サービス プリンシパル]** オプションで `Manual` オプションを選択した場合は、サービス プリンシパルの `Application ID` と `Secret` を指定する必要があります。 [サービス プリンシパルを作成する](/cli/azure/create-an-azure-service-principal-azure-cli)ときは、既定のロールが**共同作成者**であることに注意してください。これは Azure リソースを操作するのに十分なロールです。
    - **[Enable Cloud Agents]\(クラウド エージェントの有効化\)** - エージェントの既定のクラウド テンプレートを指定します。`ACI` は Azure コンテナー インスタンスを、`VM` は仮想マシンを参照します。 クラウド エージェントを有効にしない場合は、`No` を指定できます。

1. **[OK]** を選択して、 **[概要]** タブに進みます。

1. **[概要]** タブが表示されれば、入力した情報は検証されています。 「**検証に成功しました** 」というメッセージが (タブの上部に) 表示されたら、 **[OK]** をクリックします。 

     ![[概要] タブが表示され、選択したオプションが検証される。](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. **[作成]** タブが表示されたら、 **[作成]** を選択して Jenkins 仮想マシンを作成します。 サーバーの準備が完了すると、Azure ポータルに通知が表示されます。

     ![Jenkins の準備が完了したことを示す通知。](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)
