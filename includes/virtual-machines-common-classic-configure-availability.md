


可用性セットは、メンテナンス時などのシステム停止時に仮想マシンを利用するのに役立ちます。 同じように構成された 2 つ以上の仮想マシンを同じ可用性セットに割り当てることによって、仮想マシンが実行するアプリケーションやサービスの可用性を維持するために必要な冗長性が実現されます。 このしくみの詳細については、[仮想マシンの可用性管理][Manage the availability of virtual machines]に関する記事を参照してください。

アプリケーションをいつでも利用できるようにするだけでなく、効率的に実行できるようにするためには、可用性セットと負荷分散エンドポイントの両方を使用することをお勧めします。 負荷分散されたエンドポイントの詳細については、[Azure インフラストラクチャ サービスの負荷分散][Load balancing for Azure infrastructure services]に関する記事を参照してください。

クラシック仮想マシンを可用性セットに追加するには、次の 2 つのオプションがあります。

* [オプション 1: 仮想マシンと可用性セットを同時に作成する][Option 1: Create a virtual machine and an availability set at the same time]。 この後で、新しい仮想マシンを作成する場合には、ここで作成した可用性セットに追加します。
* [オプション 2: 既存の仮想マシンを可用性セットに追加する][Option 2: Add an existing virtual machine to an availability set]。

> [!NOTE]
> クラシック モデルで複数の仮想マシンを同じ可用性セットに割り当てる場合は、それらの仮想マシンが同じクラウド サービスに属している必要があります。
> 
> 

## <a id="createset"> </a>オプション 1: 仮想マシンと可用性セットを同時に作成する
Azure ポータルを使用する方法と、Azure PowerShell コマンドを使用する方法があります。

Azure ポータルを使用するには。

1. まだサインインしていない場合は、 [Azure ポータル](https://portal.azure.com)にサインインします。
2. ハブ メニューで **[+新規]**、**[仮想マシン]** の順にクリックします。
   
    ![イメージ テキスト](./media/virtual-machines-common-classic-configure-availability/ChooseVMImage.png)
3. 使用する Marketplace 仮想マシン イメージを選択します。 Linux または Windows の仮想マシンを作成することができます。
4. 選択した仮想マシンについて、デプロイメント モデルが **[クラシック]** に設定されていることを確認し、**[作成]** をクリックします。
   
    ![イメージ テキスト](./media/virtual-machines-common-classic-configure-availability/ChooseClassicModel.png)
5. 仮想マシン名と、ユーザー名とパスワード (Windows コンピューターの場合) または SSH 公開キー (Linux コンピューターの場合) を入力します。 
6. VM のサイズを選び、 **[選択]** をクリックして続行します。
7. **[オプションの構成]、[可用性セット]** の順に選択し、仮想マシンを追加する可用性セットを選択します。
   
    ![イメージ テキスト](./media/virtual-machines-common-classic-configure-availability/ChooseAvailabilitySet.png) 
8. 構成設定を確認します。 完了したら、 **[作成]**をクリックします。
9. Azure が仮想マシンを作成している間の進捗状況は、ハブ メニューの **[仮想マシン]** で追跡できます。

Azure PowerShell コマンドを使用して Azure 仮想マシンを作成し、新規または既存の可用性セットに追加する方法については、[Azure PowerShell を使用した Windows ベースの仮想マシンの作成および事前構成](../articles/virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)に関するページを参照してください。

## <a id="addmachine"> </a>オプション 2: 既存の仮想マシンを可用性セットに追加する
Azure ポータルでは、既存のクラシック仮想マシンを可用性セットに追加することも、既存の仮想マシン用に新しい可用性セットを作成することもできます  (同じ可用性セット内の仮想マシンは同じクラウド サービスに属する必要がある点に留意してください)。いずれの場合も、手順はほとんど同じです。 Azure PowerShell では、既存の仮想マシンを既存の可用性セットに追加できます。

1. まだサインインしていない場合は、 [Azure ポータル](https://portal.azure.com)にサインインします。
2. ハブ メニューで **[仮想マシン (クラシック)]**をクリックします。
   
    ![イメージ テキスト](./media/virtual-machines-common-classic-configure-availability/ChooseClassicVM.png)
3. 仮想マシンの一覧から、既存の可用性セットに追加する仮想マシンの名前を選択します。
4. 仮想マシンの **[設定]** から **[可用性セット]** を選択します。
   
    ![イメージ テキスト](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetSettings.png)
5. 仮想マシンを追加する可用性セットを選択します。 仮想マシンは、可用性セットと同じクラウド サービスに属している必要があります。
   
    ![イメージ テキスト](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetPicker.png)
6. **[保存]**をクリックします。

Azure PowerShell コマンドを使用するには、管理者レベルの Azure PowerShell セッションを開き、次のコマンドを実行します。 プレース ホルダー (&lt;VmCloudServiceName&gt; など) は、< > を含む引用符内のすべてを正しい名前に置き換えます。

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

> [!NOTE]
> 可用性セットに仮想マシンを追加する手順を完了するには、仮想マシンの再起動が必要になる場合があります。
> 
> 

<!-- LINKS -->
[Option 1: Create a virtual machine and an availability set at the same time]: #createset
[Option 2: Add an existing virtual machine to an availability set]: #addmachine

[Load balancing for Azure infrastructure services]: ../articles/virtual-machines/virtual-machines-linux-load-balance.md
[Manage the availability of virtual machines]:../articles/virtual-machines/linux/manage-availability.md

[Create a virtual machine running Windows]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md
[Virtual Network overview]: ../articles/virtual-network/virtual-networks-overview.md

