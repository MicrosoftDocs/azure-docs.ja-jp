


## <a name="using-vm-extensions"></a>VM 拡張機能の使用
Azure VM 拡張機能は、Azure VM 上での他のプログラムの動作の支援 (たとえば、 **WebDeployForVSDevTest** 拡張機能により Visual Studio は Azure VM に Web デプロイできるようになります)、または他のいくつかの動作をサポートするための VM の操作機能の提供 (たとえば、Azure CLI という PowerShell の VM アクセス拡張機能と REST クライアントを使用すると、Azure VM のリモート アクセス値をリセットまたは変更できます) を行う、動作や機能を実装します。

> [!IMPORTANT]
> サポートする機能別の拡張機能の完全な一覧については、[Azure VM 拡張機能とその機能](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。 各 VM 拡張機能は特定の機能をサポートするため、拡張機能を使用して実行可能なことと不可能なことはその拡張機能によって異なります。 したがって、VM を変更する前に、使用する VM 拡張機能についてのドキュメントを必ず読んでください。 VM 拡張機能の中には削除がサポートされていないものがあり、また別の拡張機能には VM の動作を根本から変更するように設定できるプロパティがあります。
> 
> 

最も一般的なタスクは次のとおりです。

1. 使用可能な拡張機能の検索
2. 読み込み済みの拡張機能の更新
3. 拡張機能の追加
4. 拡張機能の削除

## <a name="find-available-extensions"></a>使用可能な拡張機能の検索
以下を使用して、拡張機能とその詳細情報を見つけることができます。

* PowerShell
* Azure クロスプラットフォーム コマンド ライン インターフェイス (Azure CLI)
* サービス管理 REST API

### <a name="azure-powershell"></a>Azure PowerShell
一部の拡張機能には、PowerShell からの構成を行いやすくする固有の PowerShell コマンドレットがあります。また、以下のコマンドレットはすべての VM 拡張機能で動作します。

次のコマンドレットを使用すると、使用可能な拡張機能に関する情報を取得できます。

* Web ロール または worker ロールのインスタンスの場合、 [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) コマンドレットを使用できます。
* 仮想マシンのインスタンスの場合、 [Get-AzureVMAvailableExtension](https://msdn.microsoft.com/library/azure/dn722480.aspx) コマンドレットを使用できます。
  
   例として、次のコード例に PowerShell を使用して **IaaSDiagnostics** 拡張機能の情報を一覧表示する方法を示します。
  
      PS C:\> Get-AzureVMAvailableExtension -ExtensionName IaaSDiagnostics
  
      Publisher                   : Microsoft.Azure.Diagnostics
      ExtensionName               : IaaSDiagnostics
      Version                     : 1.2
      Label                       : Microsoft Monitoring Agent Diagnostics
      Description                 : Microsoft Monitoring Agent Extension
      PublicConfigurationSchema   :
      PrivateConfigurationSchema  :
      IsInternalExtension         : False
      SampleConfig                :
      ReplicationCompleted        : True
      Eula                        :
      PrivacyUri                  :
      HomepageUri                 :
      IsJsonExtension             : True
      DisallowMajorVersionUpgrade : False
      SupportedOS                 :
      PublishedDate               :
      CompanyName                 :

### <a name="azure-command-line-interface-azure-cli"></a>Azure コマンド ライン インターフェイス (Azure CLI)
一部の拡張機能 (Docker VM 拡張機能など) には、構成を行いやすくする固有の Azure CLI コマンドがあります。また、以下のコマンドレットはすべての VM 拡張機能で動作します。

**azure vm extension list** コマンドを使用すると利用可能な拡張機能についての情報を取得することができ、**-json** オプションを付けると 1 つ以上の拡張機能について得られた情報をすべて表示することができます。 拡張機能名を指定しない場合、このコマンドはすべての利用可能な拡張機能についての説明を JSON で返します。

たとえば、次のコード例では Azure CLI の **azure vm extension list** コマンドを使用して **IaaSDiagnostics** 拡張機能に関する情報を一覧表示する方法を示しており、**-json** オプションを付けて完全な情報を返すようにしています。

    $ azure vm extension list -n IaaSDiagnostics --json
    [
      {
        "publisher": "Microsoft.Azure.Diagnostics",
        "name": "IaaSDiagnostics",
        "version": "1.2",
        "label": "Microsoft Monitoring Agent Diagnostics",
        "description": "Microsoft Monitoring Agent Extension",
        "replicationCompleted": true,
        "isJsonExtension": true
      }
    ]



### <a name="service-management-rest-apis"></a>サービス管理 REST API
以下の REST API を使用すると、利用可能な拡張機能についての情報を取得できます。

* Web ロール または worker ロールのインスタンスの場合、 [List Available Extensions](https://msdn.microsoft.com/library/dn169559.aspx) 操作を使用できます。 [List Extension Versions](https://msdn.microsoft.com/library/dn495437.aspx)を使用すると、利用可能な拡張機能のバージョンを一覧表示することができます。
* 仮想マシンのインスタンスの場合、 [List Resource Extensions](https://msdn.microsoft.com/library/dn495441.aspx) 操作を使用できます。 [List Resource Extension Versions](https://msdn.microsoft.com/library/dn495440.aspx)を使用すると、利用可能な拡張機能のバージョンを一覧表示することができます。

## <a name="add-update-or-disable-extensions"></a>拡張機能の追加、更新、無効化
拡張機能は、インスタンスの作成時に追加するか、実行中のインスタンスに追加することができます。 拡張機能は更新、無効化、または削除できます。 これらのアクションは、Azure PowerShell またはサービス管理 REST API 操作を使用して行うことができます。 一部の拡張機能のインストールと設定にはパラメーターが必須です。 拡張機能ではパブリック パラメーターとプライベート パラメーターがサポートされます。

### <a name="azure-powershell"></a>Azure PowerShell
Azure PowerShell コマンドレットを使用するのが、最も簡単に拡張機能を追加および更新する方法です。 拡張機能コマンドレットを使用する場合、拡張機能の構成のほとんどが自動で実行されます。 場合によっては、プログラムを使用して拡張機能を追加する必要があります。 これを行う場合、拡張機能の構成を指定する必要があります。

次のコマンドレットを使用すると、拡張機能でパブリック パラメーターとプライベート パラメーターを構成する必要があるかどうかを知ることができます。

* Web ロール または worker ロールのインスタンスの場合、 **Get-AzureServiceAvailableExtension** コマンドレットを使用できます。
* 仮想マシンのインスタンスの場合、 **Get-AzureVMAvailableExtension** コマンドレットを使用できます。

### <a name="service-management-rest-apis"></a>サービス管理 REST API
利用可能な拡張機能の一覧を REST API を使用して取得すると、拡張機能の構成に関する情報が表示されます。 返される情報では、パラメーター情報がパブリック スキーマとプライベート スキーマによって表される場合があります。 パブリック パラメーターの値は、インスタンスに関するクエリで返されます。 プライベート パラメーターの値は返されません。

次の REST API を使用すると、拡張機能でパブリック パラメーターとプライベート パラメーターを構成する必要があるかどうかを知ることができます。

* Web ロール または worker ロール のインスタンスの場合、**PublicConfigurationSchema** 要素と **PrivateConfigurationSchema** 要素には、[List Available Extensions](https://msdn.microsoft.com/library/dn169559.aspx) 操作からの応答の情報が含まれます。
* 仮想マシンのインスタンスの場合、**PublicConfigurationSchema** 要素と **PrivateConfigurationSchema** 要素には、[List Resource Extensions](https://msdn.microsoft.com/library/dn495441.aspx) 操作からの応答の情報が含まれます。

> [!NOTE]
> 拡張機能では、JSON で定義された構成も使用できます。 これらの種類の拡張機能を使用する場合、 **SampleConfig** 要素だけが使用されます。
> 
> 

