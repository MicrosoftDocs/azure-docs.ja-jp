---
title: "Desired State Configuration の Resource Manager テンプレート | Microsoft Docs"
description: "Resource Manager テンプレートによる Azure の Desired State Configuration の定義の例とトラブルシューティング"
services: virtual-machines-windows
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
keywords: 
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 09/15/2016
ms.author: zachal
translationtype: Human Translation
ms.sourcegitcommit: 119a6c3e5a5764beb7445774a04621f49573a23a
ms.openlocfilehash: e9baa32f6ae21b2446447ab9a6d9dcba1168905d


---
# <a name="windows-vmss-and-desired-state-configuration-with-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用した Windows VMSS および Desired State Configuration
この記事では、[Desired State Configuration 拡張機能ハンドラー](virtual-machines-windows-extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)の Resource Manager テンプレートについて説明します。 

## <a name="template-example-for-a-windows-vm"></a>Windows VM のテンプレートの例
以下のスニペットは、テンプレートの Resource セクションに含まれます。

```json
            "name": "Microsoft.Powershell.DSC",
            "type": "extensions",
             "location": "[resourceGroup().location]",
             "apiVersion": "2015-06-15",
             "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "dscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }

```

## <a name="template-example-for-windows-vmss"></a>Windows VMSS のテンプレートの例
VMSS ノードには、"VirtualMachineProfile" を備えた "properties" セクション、"extensionProfile" 属性があります。 DSC は "extensions" の下に追加します。 

```json
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": true,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="detailed-settings-information"></a>設定情報の詳細
次のスキーマは、Azure Resource Manager テンプレートの Azure DSC 拡張機能の settings 部分のものです。

```json

"settings": {
  "wmfVersion": "latest",
  "configuration": {
    "url": "http://validURLToConfigLocation",
    "script": "ConfigurationScript.ps1",
    "function": "ConfigurationFunction"
  },
  "configurationArguments": {
    "argument1": "Value1",
    "argument2": "Value2"
  },
  "configurationData": {
    "url": "https://foo.psd1"
  },
  "privacy": {
    "dataCollection": "enable"
  },
  "advancedOptions": {
    "downloadMappings": {
      "customWmfLocation": "http://myWMFlocation"
    }
  } 
},
"protectedSettings": {
  "configurationArguments": {
    "parameterOfTypePSCredential1": {
      "userName": "UsernameValue1",
      "password": "PasswordValue1"
    },
    "parameterOfTypePSCredential2": {
      "userName": "UsernameValue2",
      "password": "PasswordValue2"
    }
  },
  "configurationUrlSasToken": "?g!bber1sht0k3n",
  "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}

```

## <a name="details"></a>詳細
| プロパティ名 | 型 | Description |
| --- | --- | --- |
| settings.wmfVersion |string |VM にインストールする Windows Management Framework のバージョンを指定します。 このプロパティを 'latest' に設定すると、WMF の最新バージョンがインストールされます。 現在このプロパティに設定できる値は、 **'4.0'、'5.0'、'5.0PP'、'latest'**のみです。 これらの設定できる値は更新される可能性があります。 既定値は 'latest' です。 |
| settings.configuration.url |string |DSC 構成 zip ファイルのダウンロード元の URL の場所を指定します。 指定した URL にアクセスのための SAS トークンが必要な場合、protectedSettings.configurationUrlSasToken プロパティに SAS トークンの値を設定する必要があります。 settings.configuration.script または settings.configuration.function を定義する場合、このプロパティは必須です。 |
| settings.configuration.script |string |DSC 構成の定義を含むスクリプトのファイル名を指定します。 このスクリプトは、configuration.url プロパティで指定した URL からダウンロードされた zip ファイルのルート フォルダーに含まれている必要があります。 settings.configuration.url または settings.configuration.script を定義する場合、このプロパティは必須です。 |
| settings.configuration.function |string |DSC 構成の名前を指定します。 名前が指定された構成は、configuration.script で定義したスクリプト内に含まれている必要があります。 settings.configuration.url または settings.configuration.functiont を定義する場合、このプロパティは必須です。 |
| settings.configurationArguments |コレクション |DSC 構成に渡すパラメーターを定義します。 このプロパティは暗号化されません。 |
| settings.configurationData.url |string |DSC 構成の入力として使用する構成データ (.psd1) ファイルのダウンロード元の URL を指定します。 指定した URL にアクセスのための SAS トークンが必要な場合、protectedSettings.configurationDataUrlSasToken プロパティに SAS トークンの値を設定する必要があります。 |
| settings.privacy.dataEnabled |string |テレメトリの収集を有効または無効にします。 このプロパティに指定できる値は、 **'Enable'、'Disable'、''、または $null**のみです。 このプロパティを空または null にした場合は、テレメトリが有効になります。 既定値は '' です。 [詳細](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) |
| settings.advancedOptions.downloadMappings |コレクション |WMF のダウンロード元になる別の場所を定義します。 [詳細](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) |
| protectedSettings.configurationArguments |コレクション |DSC 構成に渡すパラメーターを定義します。 このプロパティは暗号化されます。 |
| protectedSettings.configurationUrlSasToken |string |configuration.url で定義した URL にアクセスするための SAS トークンを指定します。 このプロパティは暗号化されます。 |
| protectedSettings.configurationDataUrlSasToken |string |ConfigurationData.url で定義した URL にアクセスするための SAS トークンを指定します。 このプロパティは暗号化されます。 |

## <a name="settings-vs-protectedsettings"></a>Settings と ProtectedSettings
すべての設定は、VM の設定テキスト ファイルに保存されます。
'settings' の下にあるプロパティは、設定テキスト ファイルで暗号化されないため、パブリック プロパティです。
'protectedSettings' の下にあるプロパティは、証明書で暗号化されるため、VM のこのファイルにプレーンテキストで表示されません。

構成に資格情報が必要な場合、protectedSettings に含めることができます。

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
               "userName": "UsernameValue1",
               "password": "PasswordValue1"
        }
    }
}
```

## <a name="example"></a>例
次の例は、[DSC 拡張機能ハンドラーの概要ページ](virtual-machines-windows-extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)の「使用の開始」セクションから派生しています。
この例では、コマンドレットの代わりに Resource Manager テンプレートを使用して拡張機能をデプロイします。 "IisInstall.ps1" の構成を保存し、.ZIP ファイル内に配置してから、アクセス可能な URL にファイルをアップロードします。 この例では、Azure Blob Storage を使用しますが、.ZIP ファイルを任意の場所からダウンロードすることもできます。

Azure Resource Manager テンプレートでは、次のコードにより、正しいファイルをダウンロードして適切な PowerShell 関数を実行するように VM に対して指示をします。

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
    } 
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="updating-from-the-previous-format"></a>以前の形式からの更新
以前の形式の設定 (ModulesUrl、ConfigurationFunction、SasToken、または Properties の各パブリック プロパティを含む) は、自動的に現在の形式に対応し、以前と同じように動作します。

次のスキーマで、以前の設定スキーマの例を示します。

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties":  {
        "ParameterToConfigurationFunction1":  "Value1",
        "ParameterToConfigurationFunction2":  "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1" 
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": { 
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

以前の形式と現在の形式の対応関係を次に示します。

| プロパティ名 | 対応する以前のスキーマ |
| --- | --- |
| settings.wmfVersion |settings.wmfVersion |
| settings.configuration.url |settings.ModulesUrl |
| settings.configuration.script |settings.ConfigurationFunction の最初の部分 ('\\\\' の前) |
| settings.configuration.function |settings.ConfigurationFunction の 2 番目の部分 ('\\\\' の後) |
| settings.configurationArguments |settings.Properties |
| settings.configurationData.url |protectedSettings.DataBlobUri (SAS トークンを使用しない) |
| settings.privacy.dataEnabled |settings.privacy.dataEnabled |
| settings.advancedOptions.downloadMappings |settings.advancedOptions.downloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |settings.SasToken |
| protectedSettings.configurationDataUrlSasToken |protectedSettings.DataBlobUri の SAS トークン |

## <a name="troubleshooting---error-code-1100"></a>トラブルシューティング - エラー コード 1100
エラー コード 1100 は、DSC 拡張機能に対するユーザー入力に問題があることを示します。
これらのエラーのテキストは変数であり、変更される可能性があります。
直面する可能性のあるいくつかのエラーとそのエラーの修正方法について説明します。

### <a name="invalid-values"></a>無効な値
"Privacy.dataCollection is '{0}'. The only possible values are '', 'Enable', and 'Disable' (Privacy.dataCollection は '{0}' です。指定できる値は ''、'Enable'、および 'Disable' のみです)" "WmfVersion is '{0}'. Only possible values are … and 'latest' (WmfVersion は '{0}' です。指定できる値は …  および 'latest' のみです)"

問題点: 指定した値が許可されていません。

解決策: 無効な値を有効な値に変更してください。 「詳細」セクションの表を参照してください。

### <a name="invalid-url"></a>無効な URL
"ConfigurationData.url is '{0}'.This is not a valid URL (ConfigurationData.url は '{0}' です。 これは有効な URL ではありません)" "DataBlobUri is '{0}'.This is not a valid URL (DataBlobUri は '{0}' です。 これは有効な URL ではありません)" "Configuration.url is '{0}'.This is not a valid URL (Configuration.url は '{0}' です。 これは有効な URL ではありません)"

問題点: 指定した URL が無効です。

解決策: 指定した URL すべてを確認してください。 拡張機能がリモート マシンにアクセスできるように、すべての URL が有効な場所に解決されていることを確認します。

### <a name="invalid-configurationargument-type"></a>無効な ConfigurationArgument の型
"Invalid configurationArguments type {0} (無効な configurationArguments の型 {0})"

問題点: ConfigurationArguments プロパティがハッシュテーブル オブジェクトに解決できません。 

解決策: ConfigurationArguments プロパティをハッシュテーブルにしてください。 前の例に示されている形式に従います。 引用符、コンマ、および中かっこに注意します。

### <a name="duplicate-configurationarguments"></a>ConfigurationArguments の重複
"Found duplicate arguments '{0}' in both public and protected configurationArguments (パブリックと保護対象の両方の configurationArguments で重複する引数 '{0}' が見つかりました)"

問題点: パブリック設定の ConfigurationArguments と保護された設定の ConfigurationArguments に同じ名前のプロパティが含まれています。

解決策: 重複するプロパティのいずれかを削除してください。

### <a name="missing-properties"></a>不足しているプロパティ
"Configuration.function requires that configuration.url or configuration.module is specified (Configuration.function には、configuration.url または configuration.module の指定が必要です)"

"Configuration.url requires that configuration.script is specified (Configuration.url には configuration.script の指定が必要です)"

"Configuration.script requires that configuration.url is specified (Configuration.script には configuration.url の指定が必要です)"

"Configuration.url requires that configuration.function is specified (Configuration.url には configuration.function の指定が必要です)"

"ConfigurationUrlSasToken requires that configuration.url is specified (ConfigurationUrlSasToken には configuration.url の指定が必要です)"

"ConfigurationDataUrlSasToken requires that configurationData.url is specified (ConfigurationDataUrlSasToken には configurationData.url の指定が必要です)"

問題点: 定義したプロパティには、不足している別のプロパティが必要です。

解決策: 

* 不足しているプロパティを指定します。
* 不足しているプロパティを必要とするプロパティを削除します。

## <a name="next-steps"></a>次のステップ
DSC と仮想マシン スケール セットについて、「 [仮想マシン スケール セットと Azure DSC 拡張機能の使用](../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md)

[DSC による安全な資格情報管理](virtual-machines-windows-extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)の詳細を確認してください。 

Azure DSC 拡張機能ハンドラーの詳細については、「 [Azure Desired State Configuration 拡張機能ハンドラーの概要](virtual-machines-windows-extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。 

PowerShell DSC の詳細については、 [PowerShell ドキュメント センター](https://msdn.microsoft.com/powershell/dsc/overview)を参照してください。 




<!--HONumber=Feb17_HO3-->


