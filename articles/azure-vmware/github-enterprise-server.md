---
title: Azure VMware Solution のプライベート クラウドに GitHub Enterprise Server を設定する
description: Azure VMware Solution のプライベート クラウドに GitHub Enterprise Server を設定する方法について説明します。
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 59a76c3976f6fcda88423b7b78344f2abed1ea84
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382023"
---
# <a name="set-up-github-enterprise-server-on-your-azure-vmware-solution-private-cloud"></a>Azure VMware Solution のプライベート クラウドに GitHub Enterprise Server を設定する

この記事では、Azure VMware Solution のプライベート クラウドに [GitHub.com](https://github.com/) の "オンプレミス" バージョンである GitHub Enterprise Server を設定する手順について説明します。 ここで取り上げるシナリオは、GitHub Actions で 1 分あたり最大 25 個のジョブを実行する、最大 3,000 人の開発者にサービスを提供できる GitHub Enterprise Server インスタンスです。 これには GitHub Actions などの "*プレビュー*" 機能 (本書の執筆時点) の設定が含まれます。 特定のニーズに合わせて設定をカスタマイズするには、「[VMware への GitHub Enterprise Server のインストール](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations)」に記載されている要件をご確認ください。

## <a name="before-you-begin"></a>開始する前に

GitHub Enterprise Server には有効なライセンス キーが必要です。 [評価版ライセンス](https://enterprise.github.com/trial)にサインアップできます。 統合によって GitHub Enterprise Server の機能を拡張する場合は、接続クライアント 5 人分の開発者ライセンスを無料でご利用いただくことができます。 [GitHub のパートナー プログラム](https://partner.github.com/)を通じてこのライセンスにお申し込みください。

## <a name="installing-github-enterprise-server-on-vmware"></a>VMware への GitHub Enterprise Server のインストール

VMware ESXi/vSphere (OVA) 用の [GitHub Enterprise Server の最新リリース](https://enterprise.github.com/releases/2.19.0/download)をダウンロードし、ダウンロードした [OVA テンプレートをデプロイ](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html)します。

:::image type="content" source="media/github-enterprise-server/github-options.png" alt-text="GitHub をオンプレミスまたはクラウドで実行することを選択します。":::  

:::image type="content" source="media/github-enterprise-server/deploy-ova-template.png" alt-text="OVA テンプレートをデプロイします。":::  

新しい仮想マシンに、GitHubEnterpriseServer などの分かりやすい名前を付けます。 インスタンスをアップグレードするとリリースの詳細が古くなるため、この詳細を VM 名に含める必要はありません。 ここではすべての既定値を選択し (これらの詳細はすぐに編集します)、OVA がインポートされるまで待ちます。

インポートしたら、必要に応じて[ハードウェア構成を調整します](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#creating-the-github-enterprise-server-instance)。 このシナリオの例では、次の構成が必要です。

| リソース | 標準セットアップ | 標準セットアップ + "ベータ機能" (Actions) |
| --- | --- | --- |
| vCPU 数 | 4 | 8 |
| メモリ | 32 GB | 61 GB |
| ストレージの接続 | 250 GB | 300 GB |
| ルート ストレージ | 200 GB | 200 GB |

ただし、実際のニーズは異なる場合があります。 「[VMware への GitHub Enterprise Server のインストール](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations)」のハードウェアの考慮事項に関するガイダンスをご覧ください。 また、「[VMware の CPU リソースまたはメモリ リソースの追加](https://docs.github.com/en/enterprise/admin/enterprise-management/increasing-cpu-or-memory-resources#adding-cpu-or-memory-resources-for-vmware)」を参照して、状況に応じてハードウェア構成をカスタマイズしてください。

## <a name="configuring-the-github-enterprise-server-instance"></a>GitHub Enterprise Server インスタンスの構成

:::image type="content" source="media/github-enterprise-server/install-github-enterprise.png" alt-text="GitHub Enterprise をインストールします。":::  

新しくプロビジョニングされた仮想マシン (VM) の電源がオンになったら、[ブラウザーを使用して構成](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#configuring-the-github-enterprise-server-instance)します。 ライセンス ファイルをアップロードし、管理コンソールのパスワードを設定する必要があります。 このパスワードは安全な場所に書き留めてください。

:::image type="content" source="media/github-enterprise-server/ssh-access.png" alt-text="SSH 経由で管理シェルにアクセスします。":::    

少なくとも次の手順を実行することをお勧めします。

1. 管理コンソールに公開 SSH キーをアップロードして、[SSH 経由で管理シェルにアクセス](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)できるようにします。 

2. [お使いのインスタンスの TLS を構成して](https://docs.github.com/en/enterprise/admin/configuration/configuring-tls)、信頼された証明機関によって署名された証明書を使用できるようにします。

:::image type="content" source="media/github-enterprise-server/configuring-your-instance.png" alt-text="インスタンスを構成します。":::

設定を適用します。  インスタンスが再起動している間、次の手順の「**GitHub Actions 用の BLOB ストレージの構成**」に進むことができます。

:::image type="content" source="media/github-enterprise-server/create-admin-account.png" alt-text="管理者アカウントを作成します。":::

インスタンスが再起動したら、インスタンスの新しい管理者アカウントを作成できます。 このユーザーのパスワードもメモしておいてください。

### <a name="other-configuration-steps"></a>その他の構成手順

実稼働環境で使用するためにインスタンスを強化するには、次のオプションのセットアップ手順を実行することをお勧めします。

1. 次に対する保護のために[高可用性](https://help.github.com/enterprise/admin/guides/installation/configuring-github-enterprise-for-high-availability/)を構成します。

    - ソフトウェアのクラッシュ (OS またはアプリケーション レベル)
    - ハードウェア障害 (ストレージ、CPU、RAM など)
    - 仮想化ホスト システムの障害
    - ネットワークの論理的または物理的な切断

2. [バックアップ ユーティリティ](https://github.com/github/backup-utils)[を構成して](https://docs.github.com/en/enterprise/admin/configuration/configuring-backups-on-your-appliance)、プライマリ インスタンスとは別の可用性でホストされる、ディザスター リカバリーのためのバージョン管理されたスナップショットを用意します。
3. 有効な TLS 証明書を使用して[サブドメインの分離を設定し](https://docs.github.com/en/enterprise/admin/configuration/enabling-subdomain-isolation)、クロスサイト スクリプティングやその他の関連する脆弱性を軽減します。

## <a name="configuring-blob-storage-for-github-actions"></a>GitHub Actions 用の BLOB ストレージの構成

> [!NOTE]
> GitHub Actions は、[GitHub Enterprise Server リリース 2.22 の制限付きのベータ版が現在提供されています](https://docs.github.com/en/enterprise/admin/github-actions)。

GitHub Enterprise Server で GitHub Actions を有効にするには(現在は "ベータ" 機能として利用可能)、外部 BLOB ストレージが必要です。 この外部 BLOB ストレージは、成果物とログを格納するために Actions によって使用されます。 GitHub Enterprise Server での Actions では、[記憶域プロバイダーとして Azure Blob Storage (およびその他) がサポート](https://docs.github.com/en/enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage#about-external-storage-requirements)されます。 次に、[BlobStorage](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts) というストレージ アカウントの種類を使用して、新しい Azure ストレージ アカウントをプロビジョニングします。

:::image type="content" source="media/github-enterprise-server/storage-account.png" alt-text="Azure Blob Storage アカウントをプロビジョニングします。":::

新しい BlobStorage リソースのデプロイが完了したら、([アクセス キー] の下にある) 接続文字列をコピーしてメモします。 この文字列は、このあとすぐに必要になります。

## <a name="setting-up-the-github-actions-runner"></a>GitHub Actions ランナーの設定

> [!NOTE]
> GitHub Actions は、[GitHub Enterprise Server リリース 2.22 の制限付きのベータ版が現在提供されています](https://docs.github.com/en/enterprise/admin/github-actions)。

この時点で、管理者アカウントが作成された、GitHub Enterprise Server のインスタンスが実行されている必要があります。 また、GitHub Actions の永続化に使用する外部 BLOB ストレージも必要です。

次に、GitHub Actions を実行するための場所を作成しましょう。ここでも、Azure VMware Solution を使用します。

まず、クラスターに新しい VM をプロビジョニングしてみましょう。 ここでの VM は、[最新リリースの Ubuntu Server](http://releases.ubuntu.com/20.04.1/) に基づくようにします。

:::image type="content" source="media/github-enterprise-server/provision-new-vm.png" alt-text="新しい VM をプロビジョニングする。":::

:::image type="content" source="media/github-enterprise-server/provision-new-vm-2.png" alt-text="新しい VM をプロビジョニングする手順 2。":::

VM が作成されたら、電源を入れて SSH 経由で接続します。

次に、GitHub Actions ワークフローからジョブを実行する [Actions ランナー](https://github.com/actions/runner) アプリケーションをインストールします。 [リリース ページ](https://github.com/actions/runner/releases) で、または次のクイック スクリプトを実行して、Actions ランナーの最新の Linux x64 リリースを特定し、ダウンロードします。 このスクリプトでは、curl と [jq](https://stedolan.github.io/jq/) の両方が VM 上に存在している必要があります。

`LATEST\_RELEASE\_ASSET\_URL=$( curl https://api.github.com/repos/actions/runner/releases/latest | \`

`  jq -r '.assets | .[] | select(.name | match("actions-runner-linux-arm64")) | .url' )`

`DOWNLOAD\_URL=$( curl $LATEST\_RELEASE\_ASSET\_URL | \`

`  jq -r '.browser\_download\_url' )`

`curl -OL $DOWNLOAD\_URL`

これで、お使いの VM のローカルにファイル actions-runner-linux-arm64-\*.tar.gz が作成されました。 この tar をローカルに抽出します。

`tar xzf actions-runner-linux-arm64-\*.tar.gz`

この抽出によって、`config.sh` や `run.sh` スクリプトなど、いくつかのファイルがローカルにアンパックされます。これについては、このあとすぐに説明します。

## <a name="enabling-github-actions"></a>GitHub Actions の有効化

> [!NOTE]
> GitHub Actions は、[GitHub Enterprise Server リリース 2.22 の制限付きのベータ版が現在提供されています](https://docs.github.com/en/enterprise/admin/github-actions)。

あともう少しです。 GitHub Enterprise Server インスタンスで GitHub Actions を構成し、有効にしてみましょう。 [GitHub Enterprise Server インスタンスの管理シェルに SSH 経由でアクセスし](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)、次のコマンドを実行する必要があります。

`# set an environment variable containing your Blob storage connection string`

`export CONNECTION\_STRING="<your connection string from the blob storage step>"`

`# configure actions storage`

`ghe-config secrets.actions.storage.blob-provider azure`

`ghe-config secrets.actions.storage.azure.connection-string "$CONNECTION\_STRING"`

`# apply these settings`

`ghe-config-apply`

`# execute a precheck, this install additional software required by Actions on GitHub Enterprise Server`

`ghe-actions-precheck -p azure -cs "$CONNECTION\_STRING"`

`# enable actions, and re-apply the config`

`ghe-config app.actions.enabled true`

`ghe-config-apply`

次の実行:

`ghe-actions-check -s blob`

次のような出力結果が表示されます。"Blob Storage is healthy"。

GitHub Actions が構成されたので、ユーザーに対して有効にします。 GitHub Enterprise Server インスタンスに管理者としてサインインし、![ロケット アイコン](media/github-enterprise-server/rocket-icon.png) (各ページの右上にあります) を選択します。 左側のサイドバーで、 **[Enterprise overview]** を選択してから、 **[Policies]** 、 **[Actions]** の順に選択し、**すべての組織に対してアクションを有効にする** オプションを選択します。

次に、 **[Self-hosted runners]** タブでランナーを構成します。 **[Add new]** を選択し、ドロップダウンから **[New runner]** を選択します。

次のページに、実行する一連のコマンドが表示されます。ここで必要なのは、ランナーを **構成する** ためのコマンドをコピーすることだけです。以下に例を示します。

`./config.sh --url https://10.1.1.26/enterprises/octo-org --token AAAAAA5RHF34QLYBDCHWLJC7L73MA`

`config.sh` コマンドをコピーし、お使いの Actions ランナーのセッション (以前に作成したもの) に貼り付けます。

:::image type="content" source="media/github-enterprise-server/actions-runner.png" alt-text="Actions ランナー。":::

run.sh コマンドを使用して、ランナーを "*実行*" します。

:::image type="content" source="media/github-enterprise-server/run-runner.png" alt-text="ランナーを実行する。":::

このランナーを企業内の組織で使用できるようにするには、その組織のアクセス権を編集します。

:::image type="content" source="media/github-enterprise-server/edit-runner-access.png" alt-text="ランナーのアクセス権を編集する。":::

ここでは、すべての組織で使用できるようにしますが、組織のサブセットや、さらには特定のリポジトリだけにアクセスを制限することもできます。

## <a name="optional-configuring-github-connect"></a>(省略可能) GitHub Connect の構成

この手順は省略可能ですが、GitHub.com で提供されているオープンソース アクションを使用する予定の場合にお勧めします。 これにより、これらの再利用可能なアクションをワークフロー内で参照することによって、他の人の作業に基づいて構築できます。

GitHub Connect を有効にするには、「[GitHub Connect を使用した GitHub.com アクションへの自動アクセスを有効にする](https://docs.github.com/en/enterprise/admin/github-actions/enabling-automatic-access-to-githubcom-actions-using-github-connect)」の手順に従います。

GitHub Connect が有効になったら、 **[Server can use actions from GitHub.com in workflow runs]** オプションを選択します。

:::image type="content" source="media/github-enterprise-server/enable-using-actions.png" alt-text="GitHub.com のアクションをワークフロー実行で使用できるようにする。":::

## <a name="setting-up-and-running-your-first-workflow"></a>最初のワークフローの設定と実行

Actions と GitHub Connect が設定されたので、このすべての作業を有効に利用してみましょう。 次に示すのは、優れた [octokit/request-action](https://github.com/octokit/request-action) を参照し、GitHub Actions で動作する GitHub API を使用した対話によって GitHub を "スクリプト化" できるワーフクローの例です。

この基本的なワークフローでは、`octokit/request-action` を使い、API を使用して GitHub で問題を開きます。

:::image type="content" source="media/github-enterprise-server/workflow-example.png" alt-text="ワークフローの例。":::

>[!NOTE]
>GitHub.com はアクションをホストしますが、GitHub Enterprise Server 上で実行される場合は、GitHub Enterprise Server API を "*自動的に*" 使用します。

GitHub Connect を有効にしないことを選択した場合は、次の代替ワークフローを使用できます。

:::image type="content" source="media/github-enterprise-server/workflow-example-2.png" alt-text="代替のワークフローの例。":::

お使いのインスタンスのリポジトリに移動し、上記のワークフローを次のように追加します。`.github/workflows/hello-world.yml`

:::image type="content" source="media/github-enterprise-server/workflow-example-3.png" alt-text="別のワークフローの例。":::

リポジトリの **[Actions]** タブで、ワークフローが実行されるまで待ちます。

:::image type="content" source="media/github-enterprise-server/executed-example-workflow.png" alt-text="実行されるワークフローの例。":::

また、ランナーによって処理されていることを監視することもできます。

:::image type="content" source="media/github-enterprise-server/workflow-processed-by-runner.png" alt-text="ランナーによって処理されたワークフロー。":::

すべてが正常に実行されると、リポジトリに "Hello world" という新しい問題が表示されます。

:::image type="content" source="media/github-enterprise-server/example-in-repo.png" alt-text="リポジトリの例。":::

おめでとうございます。 Azure VMware Solution のプライベート クラウドで実行されている GitHub Enterprise Server 上の最初のアクション ワークフローを完了しました。

この記事では、Azure VMware Solution のプライベート クラウド上に、GitHub.com の自己ホスト型に相当する GitHub Enterprise Server の新しいインスタンスを設定しました。 このインスタンスには、GitHub Actions のサポートが含まれており、ログと成果物を永続化するために Azure Blob Storage を使用します。 しかしながら、ここまでは、GitHub Actions でできることをほんの少し学んだだけです。 [GitHub の Marketplace](https://github.com/marketplace) で Actions の一覧を確認したり、[独自に作成](https://docs.github.com/en/actions/creating-actions)したりしてください。

## <a name="next-steps"></a>次のステップ

Azure VMware Solution のプライベート クラウドでの GitHub Enterprise Server のセットアップについて理解したら、次の事項の学習に進むことができます。 

- [GitHub Actions の概要](https://docs.github.com/en/actions)。
- [ベータ プログラムに参加する](https://resources.github.com/beta-signup/)。
- [GitHub Enterprise Server の管理](https://githubtraining.github.io/admin-training/#/00_getting_started)。
