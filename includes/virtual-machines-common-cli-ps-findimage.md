

## <a name="azure-cli-20"></a>Azure CLI 2.0

[Azure CLI 2.0 をインストール](https://docs.microsoft.com/cli/azure/install-az-cli2)すると、`az vm image list` コマンドを使用して、一般的な VM イメージのキャッシュ リストを確認できます。 たとえば、`az vm image list -o table` コマンドの例は次のようになります。

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               14.04.4-LTS         Canonical:UbuntuServer:14.04.4-LTS:latest                       UbuntuLTS            latest
CentOS         OpenLogic               7.2                 OpenLogic:CentOS:7.2:latest                                     CentOS               latest
openSUSE       SUSE                    13.2                SUSE:openSUSE:13.2:latest                                       openSUSE             latest
RHEL           RedHat                  7.2                 RedHat:RHEL:7.2:latest                                          RHEL                 latest
SLES           SUSE                    12-SP1              SUSE:SLES:12-SP1:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

### <a name="finding-all-current-images"></a>現在のイメージをすべて検索する

現在のすべてのイメージの一覧を取得するには、`az vm image list` コマンドを `--all` オプションと組み合わせて使用します。 Azure CLI 1.0 のコマンドとは異なり、`az vm image list --all` コマンドは**westus** にあるすべてのイメージを (特定の `--location` 引数を指定しない限り) 既定で返すため、`--all` コマンドが完了するまで少し時間がかかります。 対話形式で調査する場合は、`az vm image list --all > allImages.json` を使用すると、Azure で現在利用可能なすべてのイメージの一覧が取得され、ローカルで使用するためのファイルとして保存されます。 

既に念頭に置いているものがある場合は、複数のオプションの中から&1; つを指定して、検索を特定の場所、プラン、発行元、または SKU に絞り込むことができます。 場所を指定しない場合は、**westus** の値が返されます。

### <a name="find-specific-images"></a>特定のイメージを検索する

特定の情報を検索するには、[JMESPATH クエリ フィルター](https://docs.microsoft.com/cli/azure/query-az-cli2)で `az vm image list` を使用します。 たとえば、次の例では **Debian** で利用可能な **SKU** が表示されます (`--all` スイッチがない場合、一般的なイメージのローカル キャッシュのみが検索されることに注意してください)。

```azurecli
az vm image list --query '[?contains(offer,`Debian`)]' -o table --all
```

出力は次のようになります。 
```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
  Sku  Publisher    Offer    Urn                       Version    UrnAlias
-----  -----------  -------  ------------------------  ---------  ----------
    8  credativ     Debian   credativ:Debian:8:latest  latest     Debian

<list shortened for the example>
```

デプロイ先がわかる場合は、`az vm image list-skus`、`az vm image list-offers`、`az vm image list-publishers` の各コマンドで一般的なイメージの検索結果を使用して、必要なイメージや、デプロイ可能な場所を正確に検索できます。 たとえば、前述の例で `credativ` に Debian のプランがあることがわかっている場合は、`--location` などのオプションを使って必要なイメージを正確に検索できます。 次の例では、**westeurope** にある Debian 8 イメージを検索します。

```azurecli 
az vm image show -l westeurope -f debian -p credativ --skus 8 --version 8.0.201701180
```

出力は次のようになります。

```json
{
  "dataDiskImages": [],
  "id": "/Subscriptions/<guid>/Providers/Microsoft.Compute/Locations/westeurope/Publishers/credativ/ArtifactTypes/VMImage/Offers/debian/Skus/8/Versions/8.0.201701180",
  "location": "westeurope",
  "name": "8.0.201701180",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

## <a name="azure-cli-10"></a>Azure CLI 1.0 

> [!NOTE]
> この記事では、Azure Resource Manager デプロイメント モデルをサポートする Azure CLI 1.0 または Azure PowerShell を使用して、仮想マシン イメージの入手場所に移動し、イメージを選択する方法について説明します。 前提条件として、Resource Manager モードに変更してください。 Azure CLI を使用している場合、次のように入力してこのモードに入ります`azure config mode arm`。 
> 

イメージを検索する最も簡単な方法は、`azure vm image list` コマンドを呼び出し、場所、発行元の名称 (大文字と小文字は区別されません)、プラン (わかっている場合) を渡すことです。 たとえば、次のリストは、"Canonical" が "UbuntuServer" プランの発行元であることを知っている場合の短い例にすぎません。多数のリストは非常に長くなります。

```azurecli
azure vm image list westus canonical ubuntuserver
info:    Executing command vm image list
warn:    The parameter --sku if specified will be ignored
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         Sku                OS     Version          Location  Urn
data:    ---------  ------------  -----------------  -----  ---------------  --------  --------------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607220  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607220
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607230  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607230
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607240  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607240
```

**Urn** 列は、`azure vm quick-create` に渡されるフォームです。

ただし、多くの場合、何が使用可能かをまだ知りません。 この場合、`azure vm image list-publishers` コマンドを使用してデータ センターの場所をプロンプトで指定することで、イメージの場所に移動できます。 たとえば、以下は米国西部の場所にあるすべてのイメージ発行元をリストしたものです (場所の引数は、標準の場所名を小文字化し、スペースを削除して渡します)

```azurecli
azure vm image list-publishers
info:    Executing command vm image list-publishers
Location: westus
+ Getting virtual machine and/or extension image publishers (Location: "westus")
data:    Publisher                                       Location
data:    ----------------------------------------------  --------
data:    a10networks                                     westus  
data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
data:    alertlogic                                      westus  
data:    AlertLogic.Extension                            westus  
```

このリストはかなり長い場合があるため、前述の例のリストは抜粋です。 たとえば、「Canonical 」が、実際に米国西部の場所にあるイメージ発行元であることに気付いたとします。 これで、次の例のように、 `azure vm image list-offers` を呼び出して、プロンプトが出されたときに場所と発行元を渡すことで、プランを検索できます。

```azurecli
azure vm image list-offers
info:    Executing command vm image list-offers
Location: westus
Publisher: canonical
+ Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
data:    Publisher  Offer                      Location
data:    ---------  -------------------------  --------
data:    canonical  Ubuntu15.04Snappy          westus
data:    canonical  Ubuntu15.04SnappyDocker    westus
data:    canonical  UbunturollingSnappy        westus
data:    canonical  UbuntuServer               westus
data:    canonical  Ubuntu_Snappy_Core         westus
data:    canonical  Ubuntu_Snappy_Core_Docker  westus
info:    vm image list-offers command OK
```

これで、米国西部のリージョンで、Canonical が Azure で **UbuntuServer** プランを発行していることが分かりました。 どんな SKU でしょうか。 それらの値を取得するには、`azure vm image list-skus` を呼び出し、検出した場所、発行元、プランを指定してプロンプトに応答します。

```azurecli
azure vm image list-skus
info:    Executing command vm image list-skus
Location: westus
Publisher: canonical
Offer: ubuntuserver
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         sku                Location
data:    ---------  ------------  -----------------  --------
data:    canonical  ubuntuserver  12.04.2-LTS        westus
data:    canonical  ubuntuserver  12.04.3-LTS        westus
data:    canonical  ubuntuserver  12.04.4-LTS        westus
data:    canonical  ubuntuserver  12.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  12.04.5-LTS        westus
data:    canonical  ubuntuserver  12.10              westus
data:    canonical  ubuntuserver  14.04-beta         westus
data:    canonical  ubuntuserver  14.04.0-LTS        westus
data:    canonical  ubuntuserver  14.04.1-LTS        westus
data:    canonical  ubuntuserver  14.04.2-LTS        westus
data:    canonical  ubuntuserver  14.04.3-LTS        westus
data:    canonical  ubuntuserver  14.04.4-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.4-LTS        westus
data:    canonical  ubuntuserver  14.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.5-LTS        westus
data:    canonical  ubuntuserver  14.10              westus
data:    canonical  ubuntuserver  14.10-beta         westus
data:    canonical  ubuntuserver  14.10-DAILY        westus
data:    canonical  ubuntuserver  15.04              westus
data:    canonical  ubuntuserver  15.04-beta         westus
data:    canonical  ubuntuserver  15.04-DAILY        westus
data:    canonical  ubuntuserver  15.10              westus
data:    canonical  ubuntuserver  15.10-alpha        westus
data:    canonical  ubuntuserver  15.10-beta         westus
data:    canonical  ubuntuserver  15.10-DAILY        westus
data:    canonical  ubuntuserver  16.04-alpha        westus
data:    canonical  ubuntuserver  16.04-beta         westus
data:    canonical  ubuntuserver  16.04.0-DAILY-LTS  westus
data:    canonical  ubuntuserver  16.04.0-LTS        westus
data:    canonical  ubuntuserver  16.10-DAILY        westus
info:    vm image list-skus command OK
```

この情報を使用すると、元の呼び出しを先頭で呼び出すことによって、必要なイメージを正確に検索できます。

```azurecli
azure vm image list westus canonical ubuntuserver 16.04.0-LTS
info:    Executing command vm image list
+ Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "16.04.0-LTS" Location:"westus")
data:    Publisher  Offer         Sku          OS     Version          Location  Urn
data:    ---------  ------------  -----------  -----  ---------------  --------  --------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
info:    vm image list command OK
```

これで、使用するイメージを正確に選択できます。 検出した URN 情報を使用して仮想マシンを素早く作成する方法や、その URN 情報のあるテンプレートを使用する方法については、「 [Azure リソース マネージャーでの Azure CLI for Mac, Linux, and Windows の使用](../articles/xplat-cli-azure-resource-manager.md)」をご覧ください。

## <a name="powershell"></a>PowerShell
> [!NOTE]
> [最新の Azure PowerShell](/powershell/azureps-cmdlets-docs)をインストールして構成します。 バージョン 1.0 より前の Azure PowerShell を使用する場合でも以下のコマンドを使用できますが、初めに `Switch-AzureMode AzureResourceManager`を実行する必要があります。 
> 
> 

Azure リソース マネージャーを使用して新しい仮想マシンを作成するとき、場合によっては、以下のイメージ プロパティの組み合わせによりイメージを指定する必要があります。

* 発行元
* プラン
* SKU

たとえば、これらの値は、 `Set-AzureRMVMSourceImage` PowerShell コマンドレットや、作成する仮想マシンの種類を指定する必要のあるリソース グループ テンプレート ファイルで必要となります。

これらの値を判別する必要がある場合は、以下の方法で、イメージをナビゲートしてそれらの値を判別できます。

1. イメージの発行元を一覧表示する。
2. 指定された発行元について、そのプランを一覧表示する。
3. 指定されたプランについて、その SKU を一覧表示する。

最初に、次のコマンドを使用して発行元を一覧表示します。

```powershell
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

選択した発行元の名前を入力して、次のコマンドを実行します。

```powershell
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

選択したプランの名前を入力して、次のコマンドを実行します。

```powershell
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

`Get-AzureRMVMImageSku` コマンドの表示から、新しい仮想マシンのイメージを指定するために必要なすべての情報が得られます。

完全な例を次に示します。

```powershell
PS C:\> $locName="West US"
PS C:\> Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

PublisherName
-------------
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

"MicrosoftWindowsServer" が発行元の場合:

```powershell
PS C:\> $pubName="MicrosoftWindowsServer"
PS C:\> Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer

Offer
-----
WindowsServer
```

"WindowsServer" プランの場合:

```powershell
PS C:\> $offerName="WindowsServer"
PS C:\> Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

Skus
----
2008-R2-SP1
2012-Datacenter
2012-R2-Datacenter
2016-Nano-Server-Technical-Previe
2016-Technical-Preview-with-Conta
Windows-Server-Technical-Preview
```

この一覧から選択した SKU の名前をコピーすれば、 `Set-AzureRMVMSourceImage` PowerShell コマンドレットやリソース グループ テンプレートに必要なすべての情報が得られます。

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/