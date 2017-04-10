

コンピューティング リソースをスケール アウトしたりスケール インしたりする必要のあるアプリケーションでは、複数の障害ドメインと更新ドメインに対してスケール操作が暗黙的にバランシングされます。 VM スケール セットの概要については、最近の [Azure ブログでの発表](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/)を参照してください。

以下のビデオで、VM スケール セットが詳しく解説されています。

* [Mark Russinovich が語る Azure スケール セット](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  
* [仮想マシン スケール セットを Guy Bowerman が解説](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-vm-scale-sets"></a>VM スケール セットの作成と管理
VM スケール セットは、個々の Azure リソース マネージャー VM と同様に、JSON テンプレートと [REST API](https://msdn.microsoft.com/library/mt589023.aspx) を使用して定義およびデプロイできます。 そのため、Azure リソース マネージャーの標準的なデプロイ方法を利用することができます。 テンプレートの詳細については、「 [Azure リソース マネージャーのテンプレートの作成](../articles/resource-group-authoring-templates.md)」をご覧ください。

VM スケール セットのサンプル テンプレートは、Azure Quickstart templates GitHub リポジトリから入手できます。

[https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) - タイトルに *vmss* が含まれるテンプレートを探してください。

これらのテンプレートの詳細ページには、ポータルのデプロイ機能にリンクしているボタンが表示されます。 VM スケール セットをデプロイするには、このボタンをクリックし、ポータルで必要なすべてのパラメーターを指定します。 リソースで大文字がサポートされているかどうかが不明な場合は、常に小文字のパラメーター値を使用する方が安全です。 以下のサイトで、VM スケール セット テンプレートについてやさしく解説したビデオをご覧いただけます。

[VM スケール セット テンプレートの解説](https://channel9.msdn.com/Blogs/Azure/VM-Scale-Set-Template-Dissection/player)

## <a name="scaling-a-vm-scale-set-out-and-in"></a>VM スケール セットのスケール アウトとスケール イン
VM スケール セット内の仮想マシンの数を増減するには、単に *capacity* プロパティを変更し、テンプレートを再デプロイします。 このように単純なので、Azure 自動スケールでサポートされていないカスタム スケール イベントを定義する場合に、簡単に独自のカスタム スケール層を記述できます。

容量を変更するためにテンプレートを再デプロイする場合は、SKU と更新された容量だけが含まれている、非常に小さいテンプレートを定義できます。 その例は、 [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)にあります。

自動的にサイズを増減するスケール セットの作成手順については、「 [仮想マシン スケール セットでのマシンの自動スケール](../articles/virtual-machines/windows/vmss-powershell-creating.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="monitoring-your-vm-scale-set"></a>VM スケール セットの監視
現時点では、 [Azure リソース エクスプローラー](https://resources.azure.com) を使用して VM スケール セットを表示するようお勧めします。 VM スケール セットは、Microsoft.Compute の下のリソースなので、このサイトから以下のリンクを展開すると表示できます。

    subscriptions -> your subscription -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> your VM scale set -> etc.

## <a name="vm-scale-set-scenarios"></a>VM スケール セットのシナリオ
このセクションでは、標準的な VM スケール セットのシナリオをいくつか紹介します。 一部の高レベルの Azure サービス (Batch、Service Fabric、Azure Container Service など) も、これらのシナリオを使用します。

* **VM スケール セット インスタンスへの RDP/SSH** - VM スケール セットは VNET の中に作成され、その中の個々の VM にはパブリック IP アドレスが割り当てられていません。 これは、望ましいことです。通常は、コンピューティング グリッド内のすべてのステートレス リソースに個別の IP アドレスを割り当てるための費用や管理オーバーヘッドを避けたいためです。また、これらの VM には VNET 内の他のリソース (ロード バランサー、スタンドアロン仮想マシンなど、パブリック IP アドレスを持つリソース) から簡単に接続できます。
* **NAT 規則を使用した VM への接続** - パブリック IP アドレスを作成し、ロード バランサーに割り当て、IP アドレス上のポートを VM スケール セット内の VM 上のポートにマッピングする受信 NAT 規則を定義することができます。 例:
  
   Public IP->Port 50000 -> vmss\_0->Port 22  Public IP->Port 50001 -> vmss\_1->Port 22  Public IP->Port 50002-> vmss\_2->Port 22
  
   1 つのパブリック IP を使用しているスケール セット内のすべての VM への SSH 接続を NAT 規則で有効にする VM スケール セットの作成例は、 [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)
  
   同じことを RDP と Windows で行う例は、 [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)
* **"Jumpbox" を使用して VM に接続** - VM スケール セットとスタンドアロン VM を同じ VNET 内に作成する場合、スタンドアロン VM と VM スケール セット VM は、VNET/サブネットによって定義された内部 IP アドレスを使用して、互いに接続することができます。 パブリック IP アドレスを作成し、スタンドアロン VM に割り当てる場合は、スタンドアロン VM に RDP または SSH 接続して、そのコンピューターから VM スケール セット インスタンスに接続できます。 これでお気付きかもしれませんが、単純な VM スケール セットは、本質的に、既定の構成のパブリック IP アドレスを持つ単純なスタンドアロン VM よりも安全です。
  
   この方法の例として、次のテンプレートは、VM のスケール セット ベースのクラスターを管理するスタンドアロン マスター VM で構成される単純な Mesos クラスターを作成します: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)
* **VM スケール セット インスタンスに対するラウンド ロビン方式の負荷分散** - "ラウンド ロビン" 方式を使用して VM のコンピューティング クラスターに作業を割り当てる場合は、それに応じた負荷分散規則で Azure ロード バランサーを構成できます。 また、指定されたプロトコル、間隔、および要求パスでポートを ping して、アプリケーションが実行されていることを確認するためのプローブを定義することもできます。
  
   以下に、IIS Web サーバーを実行している VM の VM スケール セットを作成し、各 VM が受け取る負荷を分散するロード バランサーを使用する例を示します。 この例では、各 VM の特定の URL を ping するために、HTTP プロトコルも使用します: [https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json)。Microsoft.Network/loadBalancers というリソースの種類と、virtualMachineScaleSet の networkProfile および extensionProfile を見てください。
* **PaaS クラスター マネージャーでのコンピューティング クラスターとしての VM スケール セットのデプロイ** - VM スケール セットは、次世代の worker ロールとして説明されることがあります。 有効な説明ですが、スケール セットの機能を PaaS v1 worker ロール機能と混同させる危険性があります。 ある意味で、VM スケール セットは、真の "worker ロール" または worker リソースを提供します。これにより、プラットフォーム/ランタムに依存せず、カスタマイズ可能で、Azure リソース マネージャー IaaS に統合された、汎用コンピューティング リソースを利用できます。
  
   PaaS v1 worker ロールは、プラットフォーム/ランタイムのサポートの面では制限がありますが (Windows プラットフォーム イメージのみ)、VIP スワップ、構成可能なアップグレード設定、ランタイム/アプリ デプロイ固有の設定などのサービスも提供しています。これらのサービスは、VM スケール セットでは "*まだ*" 利用できないか、Service Fabric などの他の高レベル PaaS サービスによって提供されます。 このような点を踏まえておけば、VM スケール セットは PaaS をサポートするインフラストラクチャであると見なすことができます。 つまり、 Service Fabric のような PaaS ソリューションや Mesos のようなクラスター マネージャーを、スケーラブルなコンピューティング層として VM スケール セット上に構築できます。
  
   以下に、スタンドアロン VM と同じ VNET 内に VM スケール セットをデプロイする Mesos クラスターの例を示します。 スタンドアロン VM は、Mesos マスターであり、VM スケール セットはスレーブ ノードのセットを表します: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)。 将来のバージョンの [Azure Container Service](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) では、VM スケール セットに基づいて、このシナリオの、より複雑で強化されたバージョンがデプロイされます。

## <a name="vm-scale-set-performance-and-scale-guidance"></a>VM スケール セットのパフォーマンスとスケールのガイダンス
* パブリック プレビュー期間中は、複数の VM スケール セット内に 500 個を超える VM を同時に作成しないでください。
* ストレージ アカウントあたり 40 個以下の VM を計画します。
* 各ストレージ アカウント名の最初の文字は、できるだけ重複しないようにします。  [Azure のクイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates/) のサンプル VMSS テンプレートには、その方法の例が紹介されています。
* カスタム VM を使用している場合は、1 つのストレージ アカウントで、VM スケール セットあたり 40 個以下の VM を計画します。  VM スケール セットのデプロイを開始する前に、イメージをストレージ アカウントに事前コピーする必要があります。 詳細については、FAQ を参照してください。
* VNET あたり 2,048 個以下の VM を計画します。  この制限は、今後緩和されます。
* 作成できる VM の数は、どのリージョンでも、コンピューティング コア クォータによって制限されます。 計算クォータの制限を緩和するには、現時点でクラウド サービスや IaaS v1 で使用するためのコアの上限が高い場合でも、カスタマー サポートへの連絡が必要である場合があります。 クォータを照会するには、Azure CLI コマンドの場合は *azure vm list-usage*、PowerShell コマンドの場合は *Get-AzureRmVMUsage* (1.0 以前のバージョンの PowerShell を使用している場合は *Get-AzureVMUsage*) を実行します。

## <a name="vm-scale-set-frequently-asked-questions"></a>VM スケール セットに関してよく寄せられる質問
**Q.** VM スケール セットには何個の VM を設定できますか?

**A.** 複数のストレージ アカウントに分散できるプラットフォーム イメージを使用する場合は、100 個です。 カスタム イメージを使用する場合は、最大で 40 個です。プレビュー期間中は、カスタム イメージが 1 つのストレージ アカウントに制限されているためです。

**Q** VM スケール セットには、他にどのようなリソース制限がありますか?

**A.** プレビュー期間中は、リージョンごとに複数のスケール セットで 500 個を超える VM を作成できないという制限があります。 既存の [Azure サブスクリプション サービス制限](../articles/azure-subscription-service-limits.md) が適用されます。

**Q.** VM スケール セット内でデータ ディスクはサポートされていますか?

**A.** 最初のリリースではされていません。 データを格納するためのオプションは、次のとおりです。

* Azure ファイル (SMB 共有ドライブ)
* OS ドライブ
* 一時ドライブ (ローカルで、Azure Storage にサポートされない)
* 外部データ サービス (リモート データベース、Azure テーブル、Azure BLOB など)

**Q.** VM スケール セットは、どの Azure リージョンでサポートされていますか?

**A.** Azure リソース マネージャーがサポートされているすべてのリージョンで、VM スケール セットがサポートされています。

**Q.** カスタム イメージを使用して VM スケール セットを作成するにはどうすればよいですか?

**A.** vhdContainers プロパティを空白のままにします。たとえば、次のようにします。

    "storageProfile": {
        "osDisk": {
            "name": "vmssosdisk",
            "caching": "ReadOnly",
            "createOption": "FromImage",
            "image": {
                "uri": [https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
            },
            "osType": "Windows"
        }
    },


**Q.** VM スケール セット容量を 20 から 15 に減らすと、どの VM が削除されますか?

**A.** 可用性を最大限に高めるために仮想マシンは、すべてのアップグレード ドメインと障害ドメインのスケール セットから均等に削除されます。

**Q.** その後、容量を 15 から 18 に増やす場合はどうなるでしょうか?

**A.** 18 に増やすと、15、16、17 のインデックスを持つ VM が作成されます。 どちらの場合も、VM は FD と UD 間に分散されます。

**Q.** VM スケール セットで複数の拡張機能を使用する場合、実行順序を強制できますか?

**A.** 直接的にではありませんが、customScript 拡張機能の場合は、スクリプトで他の拡張機能が完了するまで待機できます (たとえば、拡張機能ログを監視することによって。[https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh) を参照)。

**Q.** VM スケール セットは、Azure 可用性セットと連携できますか?

**A.** はい。 VM スケール セットは、3 つの FD と 5 つの UD を持つ、暗黙的な可用性セットです。 virtualMachineProfile の下のものは、何も構成する必要はありません。 今後のリリースでは、VM スケール セットが複数のテナントにまたがる可能性がありますが、現時点では 1 つの可用性セットです。

