


このトピックでは、次の操作について説明します。

* Azure の仮想マシン (VM) をプロビジョニングしているときに、スクリプトやデータを挿入する。
* Windows や Linux 用にデータを取得する。
* いくつかのシステムで利用できる特殊なツールを使用して、自動的にカスタム データを検出し、操作する。

> [!NOTE]
> この記事では、Azure サービス管理 API で作成されたカスタム データを、VM を使用して挿入する方法について説明します。 Azure リソース管理 API を使用する方法については、「 [サンプル テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata)」をご覧ください。
> 
> 

## <a name="injecting-custom-data-into-your-azure-virtual-machine"></a>Azure 仮想マシンにカスタム データを挿入する
この機能は、現在、 [Azure コマンド ライン インターフェイス](https://github.com/Azure/azure-xplat-cli)でのみサポートされています。 ここでは、データを含む `custom-data.txt` ファイルを作成し、プロビジョニング時に VM にそれを挿入します。 `azure vm create` コマンドのオプションはすべて使用できますが、以下はきわめて基本的な方法の 1 つです。

```
    azure vm create <vmname> <vmimage> <username> <password> \  
    --location "West US" --ssh 22 \  
    --custom-data ./custom-data.txt  
```


## <a name="using-custom-data-in-the-virtual-machine"></a>仮想マシンでカスタム データを使用する
* Azure VM が Windows ベース VM の場合、カスタム データのファイルは `%SYSTEMDRIVE%\AzureData\CustomData.bin`に保存されます。 このファイルは、ローカル コンピューターから新しい VM に転送するために Base64 でエンコードされますが、自動的にデコードされるため、直ちに開いて使用できます。
  
  > [!NOTE]
  > このファイルが既に存在している場合には、上書きされます。 ディレクトリのセキュリティは、**[システム: フル コントロール]** と **[管理者: フル コントロール]** に設定されます。
  > 
  > 
* Azure VM が Linux ベースの VM の場合、カスタム データのファイルは、ディストリビューションに基づき、次の場所に置かれます。 データが base64 でエンコードされていれば、最初にデータをデコードする必要があります。
  
  * `/var/lib/waagent/ovf-env.xml`
  * `/var/lib/waagent/CustomData`
  * `/var/lib/cloud/instance/user-data.txt` 

## <a name="cloud-init-on-azure"></a>Azure での cloud-Init
Azure VM が Ubuntu イメージまたは CoreOS イメージから作成されている場合は、CustomData を使用して cloud-config を cloud-init に送信できます。 また、カスタム データ ファイルがスクリプトの場合は、cloud-init でスクリプトを実行できます。

### <a name="ubuntu-cloud-images"></a>Ubuntu Cloud Image
ほとんどの Azure Linux イメージでは、"/etc/waagent.conf" を編集して、一時的なリソース ディスクとスワップ ファイルを構成します。 詳細については、[Azure Linux エージェント ユーザー ガイド](../articles/virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)をご覧ください。

ただし、Ubuntu Cloud Image では、cloud-init を使用して、リソース ディスク ("一時" ディスク) とスワップ パーティションを構成する必要があります。 詳細については、Ubuntu Wiki の [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions)をご覧ください。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps-using-cloud-init"></a>次のステップ: cloud-init を使用する
詳細については、 [「cloud-init documentation for Ubuntu (Ubuntu 用 cloud-init のドキュメント)」](https://help.ubuntu.com/community/CloudInit)を参照してください。

<!--Link references-->
[ロールの追加](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Azure コマンド ライン インターフェイス](https://github.com/Azure/azure-xplat-cli)

