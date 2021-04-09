---
title: Azure DevTest Labs でのサービスとしてのプラットフォーム (PaaS) サービスの使用
description: Azure DevTest Labs でサービスとしてのプラットフォーム (PasS) サービスを使用する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: eec37527386098174906dc2737d7b763241da3f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85478741"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Azure DevTest Labs でのサービスとしてのプラットフォーム (PaaS) サービスの使用
PaaS は、環境機能を使用して DevTest Labs でサポートされます。 DevTest Labs 内の環境は、Git リポジトリ内の事前構成済みの Azure Resource Manager テンプレートでサポートされます。 環境には、PaaS と IaaS の両方のリソースを含めることができます。 これらを使用すると、仮想マシン、データベース、仮想ネットワーク、Web アプリなど、連携するようにカスタマイズされている Azure リソースを含めることができる複雑なシステムを作成できます。 これらのテンプレートでは、ソース コード管理を使用して、環境の一貫したデプロイと管理の向上を実現できます。 

適切に設定されたシステムでは、次のシナリオが可能になります。 

- 開発者が独立した複数の環境を保有する
- さまざまな構成で非同期にテストする
- テンプレートを変更せずにステージングおよび運用パイプラインに統合する
- 開発マシンと環境の両方を同じラボ内に配置して、管理の容易さとコスト レポートを向上させる。  

DevTest Labs リソース プロバイダーがラボ ユーザーに代わってリソースを作成するため、PaaS リソースを使用できるようにラボ ユーザーに追加のアクセス許可を与える必要はありません。 次の画像は、ラボ内の環境としての Service Fabric クラスターを示しています。

![環境としての Service Fabric クラスター](./media/create-environment-service-fabric-cluster/cluster-created.png)

環境設定の詳細については、「[Azure Resource Manager テンプレートを使用してマルチ VM 環境と PaaS リソースを作成する](devtest-lab-create-environment-from-arm.md)」を参照してください。 DevTest Labs には、Azure Resource Manager テンプレートのパブリック リポジトリがあり、このテンプレートを使用すると、自分で外部 GitHub ソースに接続しなくても環境を作成できます。 パブリックな環境の詳細については、「[Azure DevTest Labs でのパブリックな環境の構成と使用](devtest-lab-configure-use-public-environments.md)」を参照してください。

大規模な組織の開発チームは通常、カスタマイズまたは分離されたテスト環境などの環境を提供します。 部署または部門内のすべてのチームが使用する環境がある場合もあります。 IT グループは、組織内のすべてのチームが使用できる環境を提供したい場合もあります。  

## <a name="customizations"></a>カスタマイズ

#### <a name="sandbox"></a>サンドボックス 
ラボ所有者は、ラボ環境をカスタマイズして、リソース グループ内のユーザーのロールを **閲覧者** から **共同作成者** に変更できます。 この機能は、**[ラボの設定]** ページのラボの **[構成とポリシー]** の下にあります。 このロールの変更により、ユーザーはその環境内でリソースを追加または削除できるようになります。 アクセスをさらに制限する場合は、Azure ポリシーを使用します。 この機能を使用すると、サブスクリプション レベルのアクセスがなくてもリソースまたは構成をカスタマイズできます。

#### <a name="custom-tokens"></a>カスタム トークン
リソース グループの外部にあり、テンプレートからアクセスできる環境に固有したカスタム ラボ情報がいくつかあります。 その一部を次に示します。 

- ラボ ネットワーク ID
- 場所
- Resource Manager テンプレート ファイルが保存されているストレージ アカウント。 
 
#### <a name="lab-virtual-network"></a>ラボ仮想ネットワーク
[ラボの仮想ネットワークへの環境の接続](connect-environment-lab-virtual-network.md)に関する記事では、`$(LabSubnetId)` トークンを使用するよう Resource Manager テンプレートを変更する方法が説明されています。 環境の作成時、`$(LabSubnetId)` トークンは、**[仮想マシンの作成時に使用]** オプションが **true** に設定されている最初のサブネット マークに置き換えられます。 これにより、以前に作成されたネットワークを環境で使用できるようになります。 ステージングや運用と同じ Resource Manager テンプレートをテストの環境で使用する場合は、Resource Manager テンプレート パラメーターの既定の値として `$(LabSubnetId)` を使用します。 

#### <a name="environment-storage-account"></a>環境のストレージ アカウント
DevTest Labs は、[入れ子になった Resource Manager テンプレート](../azure-resource-manager/templates/linked-templates.md)の使用をサポートしています。 [[テスト環境用に入れ子になった Azure Resource Manager テンプレートを展開する方法](deploy-nested-template-environments.md)に関する記事では、`_artifactsLocation` および `_artifactsLocationSasToken` トークンを使用して、Resource Manager テンプレートへの URI をメイン テンプレートと同じフォルダー内または入れ子になったフォルダー内に作成する方法が説明されています。 この 2 つのトークンの詳細については、[Azure Resource Manager のベスト プラクティス ガイド](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)の **デプロイの成果物** に関するセクションを参照してください。

## <a name="user-experience"></a>ユーザー エクスペリエンス

## <a name="developer"></a>開発者
開発者は、VM の作成に同じワークフローを使用して、特定の環境を作成します。 彼らは、環境とマシン イメージを選択し、テンプレートに必要な情報を入力します。 環境を所有している各開発者は、変更のデプロイと改良された内部ループ デバッグを行うことができます。 その環境は、最新のテンプレートを使用していつでも作成できます。  この機能は、環境を破棄して作成し直すことを可能にし、手動でシステムを作成したり障害テストから復旧したりすることによるダウンタイムを減らすのに役立ちます。  

### <a name="testing"></a>テスト
DevTest Labs 環境では、特定のコードと構成を非同期的に独立してテストすることが可能です。 一般的な方法では、個々 の pull request のコードを使用して環境を設定し、自動テストを開始します。 自動テストの実行が完了したら、特定の環境に対して手動テストを実行できます。 通常、このプロセスは、CI/CD パイプラインの一部として行われます。 

## <a name="management-experience"></a>管理エクスペリエンス

### <a name="cost-tracking"></a>コスト管理
コスト追跡機能には、全体的なコスト傾向の一部として、さまざまな環境内の Azure リソースが含まれます。 リソース別のコストでは、環境内の各種リソースが示されるのではなく、その環境が 1 つのコストとして表示されます。

### <a name="security"></a>セキュリティ
DevTest Labs を使用して適切に構成された Azure サブスクリプションでは、[ラボを介した Azure リソースへのアクセスのみを制限](devtest-lab-add-devtest-user.md)できます。 環境を使用した場合、ラボ所有者は、他の Azure リソースへのアクセスを許可しなくても、承認された構成の PaaS リソースへのアクセスをユーザーに許可できます。 ラボ ユーザーが環境をカスタマイズするシナリオでは、ラボ所有者が共同作成者アクセスを許可できます。 共同作成者アクセスにより、ラボ ユーザーは、マネージド リソース グループ内でのみ Azure リソースを追加または削除できます。 サブスクリプションへの共同作成者アクセスをユーザーに許可するよりも、追跡や管理が簡単になります。

### <a name="automation"></a>オートメーション
オートメーションは、大規模で効果的なエコシステムにとって重要なコンポーネントです。 サブスクリプションやラボをまたがって複数の環境の管理や追跡を処理するには、オートメーションが必要です。

### <a name="cicd-pipeline"></a>CI/CD パイプライン
DevTest Labs の PaaS サービスは、ラボによってアクセスが制御されるデプロイに重点を置いたことで、CI/CD パイプラインの向上に役立ちます。

## <a name="next-steps"></a>次のステップ
環境の詳細については、次の記事を参照してください。 

- 
- [Azure Resource Manager テンプレートを使用してマルチ VM 環境と PaaS リソースを作成する](devtest-lab-create-environment-from-arm.md)
- [Azure DevTest Labs でパブリック環境を構成して使用する](devtest-lab-configure-use-public-environments.md)
- [Azure DevTest Labs で Service Fabric クラスターがある環境を作成する](create-environment-service-fabric-cluster.md)
- [Azure DevTest Labs のラボの仮想ネットワークに環境を接続する](connect-environment-lab-virtual-network.md)
- [環境を Azure DevOps CI/CD パイプラインに統合する](integrate-environments-devops-pipeline.md)
 





