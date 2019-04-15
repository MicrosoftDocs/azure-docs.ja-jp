---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 5f1774e02a34b60a8a6e936b62905a0a27ccfb85
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891007"
---
# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Azure の仮想マシンでインフラストラクチャ自動化ツールを使用する
Azure の仮想マシン (VM) を一貫した方法で大規模に作成および管理するには、一般的に、何らかの形で自動化することが必要です。 Azure インフラストラクチャのデプロイと管理のライフサイクル全体を自動化できるツールやソリューションはたくさんあります。 この記事では、Azure で使用できるインフラストラクチャ自動化ツールの一部を紹介します。 これらのツールは、一般的に、次のいずれかのアプローチに適しています。

- VM の構成を自動化する
    - ツールには、[Ansible](#ansible)、[Chef](#chef)、[Puppet](#puppet) があります。
    - VM のカスタマイズに特化したツールには、Linux VM 用の[cloud-init](#cloud-init)、[PowerShell Desired State Configuration (DSC)](#powershell-dsc)、すべての Azure VM に使用できる [Azure カスタム スクリプト拡張機能](#azure-custom-script-extension)があります。
 
- インフラストラクチャの管理を自動化する
    - ツールには、カスタム VM イメージの作成を自動化するための [Packer](#packer) や、インフラストラクチャの構築プロセスを自動化するための [Terraform](#terraform) があります。
    - [Azure Automation](#azure-automation) は、Azure とオンプレミス インフラストラクチャでアクションを実行できます。

- アプリケーションのデプロイと配信を自動化する
    - サンプルには、[Azure DevOps Services](#azure-devops-services) や [Jenkins](#jenkins) が含まれます。

## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) は、構成の管理、VM の作成、またはアプリケーションのデプロイ用の自動化エンジンです。 Ansible では、ターゲット マシンを認証および管理するために、通常は SSH キーによる、エージェントレス モデルを使用します。 構成タスクはプレイブックで定義し、特定のタスクを実行するために利用できるさまざまな Ansible モジュールが用意されています。 詳細については、「[How Ansible works (Ansible のしくみ)](https://www.ansible.com/how-ansible-works)」を参照してください。

以下の項目について説明します。

- [Azure で使用するために Linux で Ansible をインストールおよび構成する](../articles/virtual-machines/linux/ansible-install-configure.md)。
- [Linux 仮想マシンを作成する](../articles/virtual-machines/linux/ansible-create-vm.md)。
- [Linux 仮想マシンを管理する](../articles/virtual-machines/linux/ansible-manage-linux-vm.md)。


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) は、インフラストラクチャを構成、デプロイ、管理する方法を定義するのに役立つ自動化プラットフォームです。 追加コンポーネントには、インフラストラクチャではなくアプリケーションのライフサイクル自動化のための Chef Habitat のほか、セキュリティとポリシー要件へのコンプライアンスの自動化に役立つ Chef InSpec があります。 Chef Client はターゲット マシンにインストールし、1 つ以上の中央 Chef Server で構成を保存および管理します。 詳細は、「[An Overview of Chef (シェフの概要)](https://docs.chef.io/chef_overview.html)」を参照してください。

以下の項目について説明します。

- [Azure Marketplace から Chef Automate をデプロイする](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview)。
- [Windows に Chef をインストールし、Azure VM を作成する](../articles/virtual-machines/windows/chef-automation.md)。


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) は、アプリケーションの配信とデプロイのプロセスを処理する、エンタープライズ対応の自動化プラットフォームです。 エージェントは、ターゲット マシンにインストールし、Azure インフラストラクチャと VM の目的の構成を定義したマニフェストを Puppet Master が実行できるようにします。 Puppet は、Jenkins や GitHub などの他のソリューションと統合して、DevOps ワークフローを強化できます。 詳細については、「[How Puppet works (Puppet のしくみ)](https://puppet.com/product/how-puppet-works)」を参照してください。

以下の項目について説明します。

- [Azure Marketplace から Puppet をデプロイする](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2017-2?tab=Overview)。


## <a name="cloud-init"></a>cloud-init
[cloud-Init](https://cloudinit.readthedocs.io) は、Linux VM を初回起動時にカスタマイズするために広く使用されているアプローチです。 cloud-init を使って、パッケージをインストールしてファイルを書き込んだり、ユーザーとセキュリティを構成したりすることができます。 cloud-init は初回起動プロセスの間に呼び出されるので、構成を適用するために追加の手順や必要なエージェントはありません。  `#cloud-config` ファイルの形式を正しく設定する方法について詳しくは、[cloud-init のドキュメント サイト](http://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)をご覧ください。  `#cloud-config`  ファイルは、base64 でエンコードされたテキスト ファイルです。

cloud-init はディストリビューション全体でも有効です。 たとえば、パッケージをインストールするときに **apt-get install** や **yum install** は使用しません。 代わりに、cloud-init ではインストールするパッケージの一覧をユーザーが定義できます。 cloud-init によって、選択したディストリビューションに対してネイティブのパッケージ管理ツールが自動的に使用されます。

 Microsoft は、動作保証済み Linux ディストリビューションのパートナーと協力して、cloud-init 対応のイメージを Azure Marketplace で利用できるようにする作業を行っています。 これらのイメージでは、cloud-init のデプロイと構成が、VM および仮想マシン スケール セット とシームレスに動作するようになります。 次の表は、現時点において Azure プラットフォームで利用できる cloud-init 対応イメージの概要を示したものです。

| Publisher | プラン | SKU | バージョン | cloud-init 対応
|:--- |:--- |:--- |:--- |:--- 
|Canonical |UbuntuServer |16.04 LTS |latest |はい | 
|Canonical |UbuntuServer |14.04.5-LTS |latest |はい |
|CoreOS |CoreOS |安定版 |latest |はい |
|OpenLogic |CentOS |7-CI |latest |preview |
|RedHat |RHEL |7-RAW-CI |latest |preview |

Azure の cloud-init について詳しくは、次のページをご覧ください。

- [Azure での Linux 仮想マシンに対する cloud-init のサポート](../articles/virtual-machines/linux/using-cloud-init.md)
- [cloud-init を使用した VM 構成の自動化に関するチュートリアル](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md)


## <a name="powershell-dsc"></a>PowerShell DSC
[PowerShell Desired State Configuration (DSC)](https://msdn.microsoft.com/powershell/dsc/overview) は、ターゲット マシンの構成を定義するための管理プラットフォームです。 DSC は、[Open Management Infrastructure (OMI) サーバー](https://collaboration.opengroup.org/omi/)を介して Linux で使用することもできます。

DSC 構成では、マシンにインストールするものと、ホストを構成する方法を定義します。 Local Configuration Manager (LCM) エンジンは、プッシュされた構成に基づいて要求されたアクションを処理する各ターゲット ノードで実行されます。 プル サーバーは、中央ホストで実行され、DSC 構成と関連リソースを格納する Web サービスです。 プル サーバーは、各ターゲット ホスト上の LCM エンジンと通信して、必要な構成を提供し、コンプライアンスについて報告します。

以下の項目について説明します。

- [基本的な DSC 構成を作成する](https://msdn.microsoft.com/powershell/dsc/quickstarts/website-quickstart)。
- [DSC プル サーバーを構成する](https://msdn.microsoft.com/powershell/dsc/pullserver)。
- [Linux 用の DSC を使用する](https://msdn.microsoft.com/powershell/dsc/lnxgettingstarted)。


## <a name="azure-custom-script-extension"></a>Azure カスタム スクリプト拡張機能
[Linux](../articles/virtual-machines/linux/extensions-customscript.md) または [Windows](../articles/virtual-machines/windows/extensions-customscript.md) 用の Azure カスタム スクリプト拡張機能は、Azure VM でスクリプトをダウンロードして実行します。 拡張機能は、VM を作成するときや、VM の使用中にいつでも使用できます。 

スクリプトは、Azure Storage または GitHub リポジトリなどの公開されている場所からダウンロードできます。 カスタム スクリプト拡張機能を使用すると、ソース VM で実行されるスクリプトを任意の言語で作成できます。 これらのスクリプトを使用して、アプリケーションをインストールしたり、必要に応じて VM を構成したりすることができます。 資格情報をセキュリティで保護するために、パスワードなどの機密情報を保護された構成に格納することができます。 これらの資格情報は、VM 内でのみ復号化されます。

以下の項目について説明します。

- [Azure CLI で Linux VM を作成し、カスタム スクリプト拡張機能を使用する](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json)。
- [Azure PowerShell で Windows VM を作成し、カスタム スクリプト拡張機能を使用する](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json)。


## <a name="packer"></a>Packer
[Packer](https://www.packer.io) は、Azure でカスタム VM イメージを作成するときのビルド プロセスを自動化します。 Packer を使用して、OS を定義し、特定のニーズに合わせて VM をカスタマイズする構成後スクリプトを実行します。 構成後、VM は管理対象ディスク イメージとしてキャプチャされます。 Packer は、ソース VM、ネットワーク、ストレージ リソースを作成し、構成スクリプトを実行して、VM イメージを作成するプロセスを自動化します。

以下の項目について説明します。

- [Packer を使用して、Azure で Linux VM イメージを作成する](../articles/virtual-machines/linux/build-image-with-packer.md)。
- [Packer を使用して、Azure で Windows VM イメージを作成する](../articles/virtual-machines/windows/build-image-with-packer.md)。


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) は、単一のテンプレート形式言語 (HashiCorp 構成言語 (HCL)) で、Azure インフラストラクチャ全体を定義して作成できる自動化ツールです。 Terraform を使用して、特定のアプリケーション ソリューションのネットワーク、ストレージ、および VM リソースを作成するプロセスを自動化するテンプレートを定義します。 他のプラットフォーム用の既存の Terraform テンプレートを Azure で使用することで、Azure Resource Manager テンプレートに変換せずに、一貫性を確保し、インフラストラクチャのデプロイを簡略化することができます。

以下の項目について説明します。

- [Azure で Terraform をインストールして構成する](../articles/virtual-machines/linux/terraform-install-configure.md)。
- [Terraform を使用して Azure インフラストラクチャを作成する](../articles/virtual-machines/linux/terraform-create-complete-vm.md)。


## <a name="azure-automation"></a>Azure Automation
[Azure Automation](https://azure.microsoft.com/services/automation/) では、Runbook を使用して、ターゲットの VM で一連のタスクを処理します。 Azure Automation は、インフラストラクチャを作成するためではなく、既存の VM を管理するために使用します。 Azure Automation は、Linux と Windows の両方の VM のほか、Hybrid Runbook Worker を使用してオンサイトの仮想マシンまたは物理マシンで実行できます。 Runbook は、GitHub などのソース管理リポジトリに格納することができます。 これらの Runbook は、手動で、または定義されたスケジュールに基づいて実行できます。

また、Azure Automation には、特定の VM セットの構成方法の定義を作成できる Desired State Configuration (DSC) サービスも用意されています。 DSC により、必要な構成が適用され、VM の一貫性が維持されます。 Azure Automation DSC は、Windows マシンと Linux マシンの両方で動作します。

以下の項目について説明します。

- [PowerShell Runbook を作成する](../articles/automation/automation-first-runbook-textual-powershell.md)。
- [Hybrid Runbook Worker を使用して、オンプレミスのリソースを管理する](../articles/automation/automation-hybrid-runbook-worker.md)。
- [Azure Automation DSC を使用する](../articles/automation/automation-dsc-getting-started.md)。


## <a name="azure-devops-services"></a>Azure DevOps Services
[Azure DevOps Services](https://www.visualstudio.com/team-services/) は、コードの共有と追跡、自動ビルドの使用、完全な継続的インテグレーションと開発 (CI/CD) パイプラインの作成に役立つ一連のツールです。 Azure DevOps Services は、Visual Studio や他のエディターと統合して、簡単に使用できるようにします。 Azure DevOps Services では、Azure VM を作成して構成した後、VM にコードをデプロイすることもできます。

各項目の詳細情報

- [Azure DevOps Services](https://docs.microsoft.com/azure/devops/user-guide/index?view=vsts)。


## <a name="jenkins"></a>Jenkins
[Jenkins](https://www.jenkins.io) は、アプリケーションのデプロイとテスト、コード配信用の自動化されたパイプラインの作成に役立つ継続的インテグレーション サーバーです。 Jenkins のコア プラットフォームを拡張するプラグインは多数あります。また、webhook を介して他のさまざまな製品やソリューションと統合することもできます。 Azure VM に手動で Jenkins をインストールしたり、Docker コンテナー内から Jenkins を実行したり、事前に構築された Azure Marketplace イメージを使用したりできます。

以下の項目について説明します。

- [Jenkins、GitHub、および Docker を使用して、Azure 内の Linux VM に開発インフラストラクチャを作成する](../articles/virtual-machines/linux/tutorial-jenkins-github-docker-cicd.md)。


## <a name="next-steps"></a>次の手順
Azure でインフラストラクチャ自動化ツールを使用する方法はたくさんあります。 ニーズや環境に最適なソリューションを自由に使用できます。 Azure に組み込まれているいくつかのツールを使用したり、試したりするには、[Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) または [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) VM のカスタマイズを自動化する方法を参照してください。
