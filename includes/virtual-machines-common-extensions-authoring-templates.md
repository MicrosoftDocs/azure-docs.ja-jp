## <a name="overview-of-azure-resource-manager-templates"></a>Azure リソース マネージャー テンプレートの概要
Azure Resource Manager テンプレートでは、リソース間の依存関係を定義することで、JSON 言語で Azure IaaS インフラストラクチャを宣言によって指定できます。 Azure Resource Manager テンプレートの概要については、次の記事をご覧ください。

[リソース グループの概要](../articles/azure-resource-manager/resource-group-overview.md)

## <a name="sample-template-snippet-for-vm-extensions"></a>VM 拡張機能のサンプル テンプレート スニペット
VM 拡張機能を Azure Resource Manager のテンプレートの一部としてデプロイするには、テンプレートで拡張機能の構成を宣言で指定する必要があります。
拡張機能の構成の指定形式を次に示します。

      {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "MyExtension",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
      "properties":
      {
      "publisher": "Publisher Namespace",
      "type": "extension Name",
      "typeHandlerVersion": "extension version",
      "settings": {
      // Extension specific configuration goes in here.
      }
      }
      }

上記からわかるように、拡張機能テンプレートには次の 2 つの主要な部分があります。

1. 拡張機能の名前、発行元、およびバージョン。
2. 拡張機能の構成。

## <a name="identifying-the-publisher-type-and-typehandlerversion-for-any-extension"></a>任意の拡張機能の発行元、種類、typeHandlerVersion の識別
Azure VM 拡張機能は、マイクロソフトと信頼されたサード パーティの発行元によって発行されています。それぞれの拡張機能は、その発行元、種類、typeHandlerVersion によって一意に識別されます。 これらは、次の方法で確認できます。  



<!--HONumber=Nov16_HO3-->


