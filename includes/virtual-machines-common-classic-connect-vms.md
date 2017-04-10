

![Virtual machines in a standalone cloud service](./media/virtual-machines-common-classic-connect-vms/CloudServiceExample.png)

仮想マシンを仮想ネットワークに配置する場合、負荷分散と可用性セットに使用するクラウド サービスの数を決めることができます。 さらに、オンプレミスのネットワークと同じように仮想マシンをサブネットにまとめ、仮想ネットワークをオンプレミスのネットワークに接続することもできます。 次に例を示します。

![Virtual machines in a virtual network](./media/virtual-machines-common-classic-connect-vms/VirtualNetworkExample.png)

仮想ネットワークは、Azure で仮想マシンを接続するためのお勧めの方法です。 また、アプリケーションの各階層を別々のクラウド サービスで構成することもお勧めします。 ただし、各サブスクリプションで割り当てられるクラウド サービスの最大数である 200 を超えないように、異なるアプリケーション層にあるいくつかの仮想マシンを同じクラウド サービスにまとめることが必要になる場合もあります。 これとその他の制限については、「 [Azure サブスクリプションとサービスの制限、クォータ、および制約](../articles/azure-subscription-service-limits.md)」をご覧ください。

## <a name="connect-vms-in-a-virtual-network"></a>仮想ネットワークでの VM の接続
仮想マシンを仮想ネットワークで接続する方法:

1. [Azure ポータル](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md)で仮想ネットワークを作成します。
2. デプロイメントで可用性セットと負荷分散用の設計を反映させるため、クラウド サービスのセットを作成します。 Azure クラシック ポータルで、各クラウド サービスについて **[新規]、[Compute]、[クラウド サービス]、[カスタム作成]** の順にクリックします。
3. 新しい仮想マシンを作成するには、それぞれについて **[新規]、[Compute]、[仮想マシン]、[ギャラリーから]** の順にクリックします。 VM の適切なクラウド サービスと仮想ネットワークを選択します。 仮想ネットワークに既に参加しているクラウド サービスがある場合、その名前が既定で選択されます。

![Selecting a cloud service for a virtual machine](./media/virtual-machines-common-classic-connect-vms/VMConfig1.png)

## <a name="connect-vms-in-a-standalone-cloud-service"></a>スタンドアロン クラウド サービスでの VM の接続
仮想マシンをスタンドアロンのクラウド サービスで接続するには:

1. [Azure クラシック ポータル](http://manage.windowsazure.com)でクラウド サービスを作成します。 **[新規]、[Compute]、[クラウド サービス]、[カスタム作成]** の順にクリックします。 また、仮想マシンを初めて作成するときに、デプロイメント用のクラウド サービスを作成することもできます。
2. 仮想マシンを作成するときは、前の手順で作成したクラウド サービスの名前を選択します。
   
   ![仮想マシンを既存のクラウド サービスに追加する](./media/virtual-machines-common-classic-connect-vms/Connect-VM-to-CS.png)



<!--HONumber=Nov16_HO3-->


