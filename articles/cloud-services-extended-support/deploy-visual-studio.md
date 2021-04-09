---
title: Cloud Services の使用 (延長サポート) (プレビュー)
description: Azure Resource Manager と Visual Studio を使用して、Azure クラウド サービスを作成およびデプロイする方法について説明します
author: ghogen
ms.service: cloud-services-extended-support
manager: jillfra
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: tutorial
ms.date: 10/5/2020
ms.author: ghogen
ms.openlocfilehash: 80aa160c53b278137467dba2afa41384c7c4f378
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101722672"
---
# <a name="create-and-deploy-a-azure-cloud-service-extended-support-using-visual-studio"></a>Visual Studio を使用して Azure クラウド サービス (延長サポート) を作成およびデプロイする

[Visual Studio 2019 バージョン 16.9](https://visualstudio.microsoft.com/vs/preview/) (現在プレビュー段階) からは、Azure Resource Manager (ARM) を使用してクラウド サービスを操作できるようになっています。これにより、Azure リソースのメンテナンスと管理が大幅に簡素化、最新化されます。 これは、Cloud Services (延長サポート) と呼ばれる新しい Azure サービスによって実現されます。 既存のクラウド サービスを Cloud Services (延長サポート) に発行することができます。 この Azure サービスの詳細については、[Cloud Services (延長サポート) に関するドキュメント](overview.md)を参照してください。

> [!IMPORTANT]
> Cloud Services (延長サポート) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="register-the-feature-for-your-subscription"></a>サブスクリプションに機能を登録する
Cloud Services (延長サポート) は現在、プレビュー段階です。 次のようにして、サブスクリプションに機能を登録します。

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```
詳細については、[Cloud Services (延長サポート) のデプロイの前提条件](deploy-prerequisite.md)に関する記事を参照してください。

## <a name="create-a-project"></a>プロジェクトの作成

Visual Studio には、**Azure クラウド サービス (延長サポート)** という名前の、延長サポート付きの Azure クラウド サービスを作成できるプロジェクト テンプレートが用意されています。 クラウド サービスは、シンプルな汎用 Azure サービスです。 プロジェクトを作成したら、Visual Studio でクラウド サービスを構成し、デバッグして、Azure にデプロイできます。

### <a name="to-create-an-azure-cloud-service-extended-support-project-in-visual-studio"></a>Visual Studio で Azure クラウド サービス (延長サポート) プロジェクトを作成するには

このセクションでは、Visual Studio で 1 つ以上の Web ロールを使用して Azure クラウド サービス プロジェクトを作成する手順について説明します。

1. スタート ウィンドウで、**[新しいプロジェクトの作成]** を選択します。

1. 検索ボックスに「*クラウド*」と入力し、 **[Azure Cloud Service (extended support)]\(Azure クラウドサービス (延長サポート)\)** を選択します。

   ![延長サポート付きの新しい Azure クラウド サービス](./media/choose-project-template.png)

1. プロジェクト名を設定し、**[作成]** を選択します。

   ![プロジェクト名を設定します。](./media/configure-new-project.png)

1. **[新しい Microsoft Azure クラウド サービス]** ダイアログで、追加するロールを選択し、右矢印ボタンをクリックしてロールをソリューションに追加します。

    ![新しい Azure クラウド サービス ロールを選択する](./media/choose-roles.png)

1. 追加したロールの名前を変更するには、**[新しい Microsoft Azure クラウド サービス]** ダイアログでそのロールの上にマウス ポインターを置き、コンテキスト メニューの **[名前の変更]** を選択します。 ロールを追加した後に、(**ソリューション エクスプローラー** で) ソリューション内でロールの名前を変更することもできます。

    ![Azure クラウド サービス ロールの名前を変更する](./media/new-cloud-service-rename.png)

Visual Studio の Azure プロジェクトは、ソリューション内のロール プロジェクトに関連付けられています。 また、プロジェクトには、*サービス定義ファイル* と *サービス構成ファイル* が含まれます。

- **サービス定義ファイル** - 必要なロール、エンドポイント、仮想マシンのサイズなど、アプリケーションのランタイム設定を定義します。
- **サービス構成ファイル** - 実行されるロールのインスタンス数とロールに定義されている設定の値を構成します。

これらのファイルの詳細については、[Visual Studio を使用した Azure クラウド サービスのロールの構成](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service)に関する記事をご覧ください。

## <a name="publish-a-cloud-service"></a>クラウド サービスの発行

1. Visual Studio で Azure クラウド サービス プロジェクトを開くか、新たに作成します。

1. **ソリューション エクスプローラー** でそのプロジェクトを右クリックし、コンテキスト メニューの **[発行]** を選択します。

   ![サインイン ページ](./media/publish-step-1.png)

1. **アカウント** - アカウント ドロップダウン リストでアカウントを選択するか、**[アカウントの追加]** を選択します。

1. **[サブスクリプションの選択]** - デプロイに使用するサブスクリプションを選択します。 Cloud Services (延長サポート) のデプロイに使用するサブスクリプションには、ロールベースのアクセス制御 (RBAC) を使用して所有者または共同作成者のロールが割り当てられている必要があります。 ご自分のサブスクリプションにこれらのロールのいずれも含まれていない場合は、「[ロールの割り当てを追加する手順](../role-based-access-control/role-assignments-steps.md)」を参照して、先に進む前にこれを追加してしてください。

1. **[次へ]** を選択して、 **[設定]** ページに移動します。

   ![[共通設定]](./media/publish-settings.png)

1. **[クラウド サービス]** - ドロップダウンを使用して既存のクラウド サービスを選択するか、 **[新規作成]** を選択してクラウド サービスを作成します。 クラウド サービスごとに、データ センターがかっこ内に表示されます。 クラウド サービスのデータ センターの場所は、ストレージ アカウントのデータ センターと同じ場所にすることをお勧めします。

   新しいクラウド サービスを作成することにした場合は、 **[Create Cloud Service (extended support)]\(クラウド サービスの作成 (延長サポート)\)** ダイアログが表示されます。 クラウド サービスに使用する場所とリソース グループを指定します。

   ![延長サポート付きのクラウド サービスを作成する](./media/extended-support-dialog.png)

1. **[ビルド構成]** - **[デバッグ]** または **[リリース]** を選択します。

1. **[サービス構成]** - **[クラウド]** または **[ローカル]** を選択します。

1. **[ストレージ アカウント]** - このデプロイに使用するストレージ アカウントを選択するか、 **[新規作成]** を選択してストレージ アカウントを作成します。 ストレージ アカウントごとに、リージョンがかっこ内に表示されます。 ストレージ アカウントのデータ センターの場所は、クラウド サービスのデータ センターと同じ場所 ([共通設定]) にすることをお勧めします。

   Azure ストレージ アカウントには、アプリケーション デプロイのパッケージが格納されます。 アプリケーションのデプロイ後、パッケージはストレージ アカウントから削除されます。

1. **[キー コンテナー]** - このクラウド サービスのシークレットを格納するキー コンテナーを指定します。 これは、リモート デスクトップが有効になっているか、構成に証明書が追加されている場合に有効になります。

1. **[すべてのロールのリモート デスクトップを有効にする]** - サービスにリモート接続できるようにする場合は、このオプションを選択します。 資格情報を指定するように求められます。

   ![リモート デスクトップの設定](./media/remote-desktop-configuration.png)

1. **[次へ]** を選択して、 **[診断設定]** ページに移動します。

   ![診断設定](./media/diagnostics-settings.png)

   診断を使用して、Azure クラウド サービス (または Azure 仮想マシン) のトラブルシューティングを行うことができます。 診断については、「[Azure クラウド サービスおよび仮想マシン用の診断の構成](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)」をご覧ください。 Application Insights については、「[Application Insights とは何か?](../azure-monitor/app/app-insights-overview.md)」をご覧ください。

1. **[次へ]** を選択して **[概要]** ページに移動します。

   ![まとめ](./media/publish-summary.png)

1. **[ターゲット プロファイル]** - 選択した設定から発行プロファイルを作成できます。 たとえば、テスト環境用と運用環境用に 1 つずつプロファイルを作成できます。 このプロファイルを保存するには、**[保存]** アイコンをクリックします。 ウィザードでプロファイルが作成され、Visual Studio プロジェクトに保存されます。 プロファイル名を変更するには、**[ターゲット プロファイル]** の一覧を開き、[**管理…]** を選択します。

   > [!Note]
   > 発行プロファイルが Visual Studio のソリューション エクスプローラーに表示され、プロファイル設定が .azurePubxml という拡張子を持つファイルに書き込まれます。 設定は、XML タグの属性として保存されます。

1. プロジェクトのデプロイのすべての設定を構成したら、ダイアログの下部にある **[発行]** をクリックします。 Visual Studio の **[Azure アクティビティ ログ]** 出力ウィンドウでプロセスの状態を監視できます。

お疲れさまでした。 これで、延長サポート クラウド サービス プロジェクトが Azure に発行されました。 同じ設定を使用して再度発行する場合は、発行プロファイルを再利用するか、この手順を繰り返して新しいプロファイルを作成します。

## <a name="clean-up-azure-resources"></a>Azure リソースをクリーンアップする

このチュートリアルに従って作成した Azure リソースをクリーンアップするには、[Azure portal](https://portal.azure.com) にアクセスして **[リソース グループ]** を選択し、サービスの作成に使用したリソース グループを探して開き、 **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

**[発行]** 画面の **[構成]** ボタンを使用して、継続的インテグレーション (CI) を設定します。 詳細については、「[Azure Pipelines のドキュメント](/azure/devops/pipelines)」を参照してください。