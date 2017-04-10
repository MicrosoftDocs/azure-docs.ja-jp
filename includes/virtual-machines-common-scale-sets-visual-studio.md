

この記事では、Visual Studio の "リソース グループの配置" を使用して Azure 仮想マシン スケール セットをデプロイする方法について説明します。

[Azure 仮想マシン スケール セット](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) は、自動スケールと負荷分散のためのオプションを簡単に組み込んで、同様の仮想マシンを一元的にデプロイし、管理するための Azure コンピューティング リソースです。 VM スケール セットのプロビジョニングとデプロイは、 [Azure リソース マネージャー (ARM) テンプレート](https://github.com/Azure/azure-quickstart-templates)を使って行うことができます。 ARM テンプレートは、Azure CLI、PowerShell、REST を使ってデプロイできるほか、Visual Studio から直接デプロイすることもできます。 Visual Studio には、"Azure リソース グループの配置" プロジェクトの一環としてデプロイできるサンプル テンプレート一式が用意されています。

Azure リソース グループとしてデプロイすることによって、関連する一連の Azure リソースを集約し、1 回のデプロイ操作で発行することができます。 詳細については、「 [Visual Studio での Azure リソース グループの作成とデプロイ](../articles/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)」を参照してください。

## <a name="pre-requisites"></a>前提条件
Visual Studio で VM スケール セットをデプロイするには、次のものが必要です。

* Visual Studio 2013 または 2015
* Azure SDK 2.7 または 2.8

注: 以降の手順は、Visual Studio 2015 と [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)を使用していることを前提としています。

## <a name="creating-a-project"></a>プロジェクトの作成
1. Visual Studio 2015 で **[ファイル]、[新規作成]、[プロジェクト]**の順に選択し、新しいプロジェクトを作成します。
   
    ![File New][file_new]
2. **[Visual C#] の [Cloud]** で **[Azure Resource Manager]** を選択し、ARM テンプレートをデプロイするためのプロジェクトを作成します。
   
    ![Create Project][create_project]
3. テンプレートの一覧から、Linux または Windows の仮想マシン スケール セット テンプレートを選択します。
   
   ![Select Template][select_Template]
4. プロジェクトの作成後、PowerShell デプロイ スクリプト、Azure リソース マネージャー テンプレート、仮想マシン スケール セットのパラメーター ファイルが表示されます。
   
    ![Solution Explorer][solution_explorer]

## <a name="customize-your-project"></a>プロジェクトのカスタマイズ
これで、アプリケーションのニーズに応じてテンプレートを編集してカスタマイズできます。たとえば、VM 拡張機能のプロパティを追加したり、負荷分散の規則を編集したりできます。 既定では、自動スケール規則を簡単に追加できる AzureDiagnostics 拡張機能をデプロイするように VM スケール セット テンプレートが構成されています。 また、パブリック IP アドレスでロード バランサーがデプロイされ、SSH (Linux) または RDP (Windows) で VM インスタンスに接続できるように受信 NAT 規則が設定されます。フロントエンド ポートの範囲の開始は 50000 です。つまり Linux の場合、パブリック IP アドレス (またはドメイン名) のポート 50000 に SSH で接続すると、スケール セットにおける最初の VM のポート 22 にルーティングされます。 同様に、ポート 50001 に接続すると、2 つ目の VM のポート 22 にルーティングされます。

 Visual Studio でテンプレートを編集するときは、JSON アウトラインを使用してパラメーター、変数、リソースを整理することをお勧めします。 スキーマを認識させることによって、デプロイするテンプレートのエラーを事前に Visual Studio で検出することができます。

![JSON Explorer][json_explorer]

## <a name="deploy-the-project"></a>プロジェクトをデプロイする
1. ARM テンプレートを Azure にデプロイして VM スケール セット リソースを作成します。 プロジェクト ノードを右クリックして **[配置]、[新しい配置]**の順に選択します。
   
    ![Deploy Template][5deploy_Template]
2. [リソース グループに配置する] ダイアログで該当するサブスクリプションを選択します。
   
    ![Deploy Template][6deploy_Template]
3. ここから、テンプレートのデプロイ先となる新しい Azure リソース グループを作成することもできます。
   
    ![New Resource Group][new_resource]
4. 次に、**[パラメーターの編集]** ボタンを選択して、テンプレートに渡すパラメーターを入力します。デプロイを作成するには、所定の値 (OS のユーザー名とパスワードなど) を入力する必要があります。
   
    ![Edit Parameters][edit_parameters]
5. **[配置]**をクリックします。 **出力** ウィンドウにデプロイの進行状況が表示されます。 ここで実行されているのは **Deploy-AzureResourceGroup.ps1** スクリプトの処理です。
   
   ![Output Window][output_window]

## <a name="exploring-your-vm-scale-set"></a>VM スケール セットについて
デプロイが完了すると、Visual Studio の **クラウド エクスプローラー** で新しい VM スケール セットを確認できます (一覧を最新の情報に更新してください)。 アプリケーションの開発中に、Visual Studio からクラウド エクスプローラーを使って Azure のリソースを管理できます。 VM スケール セットは、Azure ポータルや Azure リソース エクスプローラーで表示することもできます。

![Cloud Explorer][cloud_explorer]

 ポータルの優れている点は、Azure のインフラストラクチャを Web ブラウザーを使って視覚的に管理できることです。一方、Azure リソース エクスプローラーの利点は、Azure のリソースを手軽に調査し、デバッグできることです。"インスタンス ビュー" を観察したり、着目しているリソースの PowerShell コマンドを表示したりすることができます。 VM スケール セットはプレビュー版ですが、VM スケール セットに関する大半の情報は、リソース エクスプローラーで表示することができます。

## <a name="next-steps"></a>次のステップ
Visual Studio を使って VM スケール セットを正常にデプロイしたら、実際のアプリケーションの要件に応じてプロジェクトをさらにカスタマイズすることができます。 たとえば、Insights リソースを追加したり、スタンドアロン VM などのインフラストラクチャをテンプレートに追加したり、カスタム スクリプト拡張機能を使ってアプリケーションをデプロイしたりすることによって、自動スケールをセットアップすることが考えられます。 サンプル テンプレートは、 [Azure クイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates) GitHub リポジトリから入手できます (「vmss」で検索してください)。

[file_new]: ./media/virtual-machines-common-scale-sets-visual-studio/1-FileNew.png
[create_project]: ./media/virtual-machines-common-scale-sets-visual-studio/2-CreateProject.png
[select_Template]: ./media/virtual-machines-common-scale-sets-visual-studio/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machines-common-scale-sets-visual-studio/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machines-common-scale-sets-visual-studio/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machines-common-scale-sets-visual-studio/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machines-common-scale-sets-visual-studio/6-DeployTemplate.png
[new_resource]: ./media/virtual-machines-common-scale-sets-visual-studio/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machines-common-scale-sets-visual-studio/8-EditParameter.png
[output_window]: ./media/virtual-machines-common-scale-sets-visual-studio/9-Output.png
[cloud_explorer]: ./media/virtual-machines-common-scale-sets-visual-studio/12-CloudExplorer.png

<!--HONumber=Jan17_HO3-->


