1. [Azure Portal](https://portal.azure.com) にサインインします。

2. 左上の **[新規] > [コンピューティング] > [Windows Server 2016 Datacenter]** をクリックします。

    ![ポータルで Azure VM イメージに移動する](./media/virtual-machines-common-portal-create-fqdn/marketplace-new.png)

3. [Windows Server 2016 Datacenter] で、クラシック デプロイ モデルを選択します。 [作成] をクリックします。

    ![ポータルで使用できる Azure VM イメージを示すスクリーンショット](./media/virtual-machines-common-portal-create-fqdn/deployment-classic-model.png)

## <a name="1-basics-blade"></a>1.[基本] ブレード

[基本] ブレードでは、仮想マシンの管理情報を入力する必要があります。

1. 仮想マシンの**名前**を入力します。 この例では、_HeroVM_ が仮想マシンの名前です。 名前は 1 ～ 15 文字とし、特殊文字を含めることはできません。

2. VM にローカル アカウントを作成する際に使用する**ユーザー名**と強力な**パスワード**を入力します。 VM へのサインインと VM の管理にはローカル アカウントを使用します。 この例では、_azureuser_ がユーザー名です。

 パスワードは、8 ～ 123 文字で指定する必要があります。また、1 つの小文字、1 つの大文字、1 つの数字、1 つの特殊文字という複雑さの 4 要件のうち、3 つを満たしている必要があります。 [ユーザー名とパスワードの要件](../articles/virtual-machines/windows/faq.md)に関するページで詳細を確認してください。

3. **サブスクリプション**は省略可能です。 一般的な設定の 1 つは "従量料金制" です。

4. 既存の**リソース グループ**を選択するか、新しいリソース グループの名前を入力します。 この例では、_HeroVMRG_ がリソース グループの名前です。

5. VM を実行する Azure データセンターの**場所**を選択します。 この例では、**米国東部** が場所です。

6. 完了したら、**[次へ]** をクリックして次のブレードに進みます。

    ![Screenshot that shows the settings on the Basics blade for configuring an Azure VM](./media/virtual-machines-common-portal-create-fqdn/basics-blade-classic.png)

## <a name="2-size-blade"></a>2.[サイズ] ブレード

[サイズ] ブレードには、VM の構成の詳細が示されます。OS、プロセッサ数、ディスク ストレージの種類、毎月の推定利用コストなどのさまざまな選択肢が表示されます。  

VM サイズを選択し、**[選択]** をクリックして続行します。 この例では、_DS1_\__V2 Standard_ が VM サイズです。

  ![Screenshot of the Size blade that shows the Azure VM sizes that you can select](./media/virtual-machines-common-portal-create-fqdn/vm-size-classic.png)


## <a name="3-settings-blade"></a>手順 3.[設定] ブレード

[設定] ブレードでは、ストレージとネットワークのオプションを指定します。 既定の設定をそのまま使用できます。 必要に応じて適切なエントリが作成されます。

Azure Premium Storage をサポートする仮想マシン サイズを選択した場合は、[ディスクの種類] の [Premium (SSD)] を選択することで、Premium Storage を試用できます。

変更が済んだら **[OK]**をクリックします。

## <a name="4-summary-blade"></a>4.[概要] ブレード

[概要] ブレードには、前の各ブレードで指定した設定が表示されます。 イメージを作成する準備が完了したら、**[OK]** をクリックします。

 ![仮想マシンの指定した設定を示す [概要] ブレードのレポート](./media/virtual-machines-common-portal-create-fqdn/summary-blade-classic.png)

仮想マシンが作成されると、ポータルの **[すべてのリソース]** に新しい仮想マシンが表示され、ダッシュボードに仮想マシンのタイルが表示されます。 対応するクラウド サービスとストレージ アカウントも作成され、表示されます。 仮想マシンとクラウド サービスが自動的に起動し、その状態がいずれも " **実行中**" として表示されます。

 ![VM エージェントと仮想マシンのエンドポイントを構成します](./media/virtual-machines-common-portal-create-fqdn/portal-with-new-vm.png)
