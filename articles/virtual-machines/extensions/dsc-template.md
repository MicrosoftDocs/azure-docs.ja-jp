---
title: Azure Resource Manager テンプレートを使用した Desired State Configuration 拡張機能
description: Resource Manager テンプレートによる Azure の Desired State Configuration (DSC) 拡張機能の定義について説明します。
services: virtual-machines
author: mgoedtel
tags: azure-resource-manager
keywords: dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/09/2021
ms.author: magoedte
ms.openlocfilehash: 6d22e93fe8bd36e24520108bc310d5b08c25120c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560023"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用した Desired State Configuration 拡張機能

この記事では、[Desired State Configuration (DSC) 拡張機能ハンドラー](dsc-overview.md)の Azure Resource Manager テンプレートについて説明します。 多くの例では、Azure Automation を使用してオンボードするために、**RegistrationURL** (String として指定) と **RegistrationKey** ([PSCredential](/dotnet/api/system.management.automation.pscredential) として指定) を使用しています。 それらの値の取得の詳細については、「[Azure Automation State Configuration による管理のためのマシンのオンボード - セキュリティで保護された登録](../../automation/automation-dsc-onboarding.md#enable-machines-securely-using-registration)」を参照してください。

> [!NOTE]
> スキーマの例に若干の違いがある可能性があります。 スキーマの変更は 2016 年 10 月 のリリースで発生しました。 詳しくは、[以前の形式からの更新](#update-from-a-previous-format)に関する記事をご覧ください。

## <a name="template-example-for-a-windows-vm"></a>Windows VM のテンプレートの例

以下のスニペットは、テンプレートの **Resource** セクションに含まれます。
DSC 拡張機能は、既定の拡張機能プロパティを継承します。
詳しくは、「[VirtualMachineExtension class (VirtualMachineExtension クラス)](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension)」をご覧ください。

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/Microsoft.Powershell.DSC')]",
  "apiVersion": "2018-06-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "protectedSettings": {
      "Items": {
        "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
      }
    },
    "settings": {
      "Properties": [
        {
          "Name": "RegistrationKey",
          "Value": {
            "UserName": "PLACEHOLDER_DONOTUSE",
            "Password": "PrivateSettingsRef:registrationKeyPrivate"
          },
          "TypeName": "System.Management.Automation.PSCredential"
        },
        {
          "Name": "RegistrationUrl",
          "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
          "TypeName": "System.String"
        },
        {
          "Name": "NodeConfigurationName",
          "Value": "[parameters('nodeConfigurationName')]",
          "TypeName": "System.String"
        }
      ]
    }
  }
}
```

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Windows 仮想マシン スケール セット用のテンプレート例

仮想マシン スケール セット ノードには、**VirtualMachineProfile** を備えた **properties** セクション、extensionProfile 属性があります。
**[拡張機能]** の下に DSC 拡張機能の詳細を追加します。

DSC 拡張機能は、既定の拡張機能プロパティを継承します。
詳しくは、「[VirtualMachineScaleSetExtension class (VirtualMachineScaleSetExtension クラス)](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension)」をご覧ください。

```json
"extensionProfile": {
    "extensions": [
      {
        "name": "Microsoft.Powershell.DSC",
        "properties": {
          "publisher": "Microsoft.Powershell",
          "type": "DSC",
          "typeHandlerVersion": "2.77",
          "autoUpgradeMinorVersion": true,
          "protectedSettings": {
            "Items": {
              "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
            }
          },
          "settings": {
            "Properties": [
              {
                "Name": "RegistrationKey",
                "Value": {
                  "UserName": "PLACEHOLDER_DONOTUSE",
                  "Password": "PrivateSettingsRef:registrationKeyPrivate"
                },
                "TypeName": "System.Management.Automation.PSCredential"
              },
              {
                "Name": "RegistrationUrl",
                "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
                "TypeName": "System.String"
              },
              {
                "Name": "NodeConfigurationName",
                "Value": "[parameters('nodeConfigurationName')]",
                "TypeName": "System.String"
              }
            ]
          }
        }
      }
    ]
  }
```

## <a name="detailed-settings-information"></a>設定情報の詳細

次のスキーマは、Resource Manager テンプレートの Azure DSC 拡張機能の **settings** セクションで使用します。

既定の構成スクリプトで使用可能な引数の一覧については、「[既定の構成スクリプト](#default-configuration-script)」をご覧ください。

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

| プロパティ名 | Type | 説明 |
| --- | --- | --- |
| settings.wmfVersion |string |VM にインストールする Windows Management Framework (WMF) のバージョンを指定します。 このプロパティを **latest** に設定すると、WMF の最新バージョンがインストールされます。 現在、このプロパティに設定できる値は、**4.0**、**5.0**、**5.1**、**latest** のみです。 これらの設定できる値は更新される可能性があります。 既定値は **latest** です。 |
| settings.configuration.url |string |DSC 構成 .zip ファイルのダウンロード元の URL の場所を指定します。 指定した URL にアクセスのための SAS トークンが必要な場合は、**protectedSettings.configurationUrlSasToken** プロパティに SAS トークンの値を設定します。 **settings.configuration.script** または **settings.configuration.function** を定義する場合、このプロパティは必須です。 これらのプロパティの値を指定しない場合、Location Configuration Manager (LCM) のメタデータを設定する既定の構成スクリプトが拡張機能から呼び出され、引数が指定されます。 |
| settings.configuration.script |string |DSC 構成の定義を含むスクリプトのファイル名を指定します。 このスクリプトは、**settings.configuration.url** プロパティで指定した URL からダウンロードされた .zip ファイルのルート フォルダーに含まれている必要があります。 **settings.configuration.url** または **settings.configuration.script** を定義する場合、このプロパティは必須です。 これらのプロパティの値を指定しない場合、LCM のメタデータを設定する既定の構成スクリプトが拡張機能から呼び出され、引数が指定されます。 |
| settings.configuration.function |string |DSC 構成の名前を指定します。 名前が指定された構成は、**settings.configuration.script** によって定義されたスクリプト内に含まれている必要があります。 **settings.configuration.url** または **settings.configuration.function** を定義する場合、このプロパティは必須です。 これらのプロパティの値を指定しない場合、LCM のメタデータを設定する既定の構成スクリプトが拡張機能から呼び出され、引数が指定されます。 |
| settings.configurationArguments |コレクション |DSC 構成に渡すパラメーターを定義します。 このプロパティは暗号化されません。 |
| settings.configurationData.url |string |DSC 構成の入力として使用する構成データ (.psd1) ファイルのダウンロード元の URL を指定します。 指定した URL にアクセスのための SAS トークンが必要な場合は、**protectedSettings.configurationDataUrlSasToken** プロパティに SAS トークンの値を設定します。 |
| settings.privacy.dataCollection |string |テレメトリの収集を有効または無効にします。 このプロパティに指定できる値は、**Enable**、**Disable**、 **''** 、または **$null** のみです。 このプロパティを空または null にした場合は、テレメトリが有効になります。 既定値は **''** です。 詳しくは、「[Azure DSC extension data collection (Azure DSC 拡張機能のデータ収集)](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/)」をご覧ください。 |
| settings.advancedOptions.downloadMappings |コレクション |WMF のダウンロード元になる別の場所を定義します。 詳しくは、「[Azure DSC extension 2.8 and how to map downloads of the extension dependencies to your own location (Azure DSC 拡張機能 2.8 と、拡張機能の依存関係のダウンロードを独自の場所にマップする方法)](https://devblogs.microsoft.com/powershell/azure-dsc-extension-2-8-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location/)」をご覧ください。 |
| protectedSettings.configurationArguments |コレクション |DSC 構成に渡すパラメーターを定義します。 このプロパティは暗号化されます。 |
| protectedSettings.configurationUrlSasToken |string |**settings.configuration.url** で定義された URL へのアクセスに使用する SAS トークンを指定します。 このプロパティは暗号化されます。 |
| protectedSettings.configurationDataUrlSasToken |string |**settings.configurationData.url** で定義された URL へのアクセスに使用する SAS トークンを指定します。 このプロパティは暗号化されます。 |

## <a name="default-configuration-script"></a>既定の構成スクリプト

次の値について詳しくは、[ローカル構成マネージャーの基本設定](/powershell/scripting/dsc/managing-nodes/metaConfig#basic-settings)に関するページをご覧ください。
DSC 拡張機能の既定の構成スクリプトは、次の表に記載されている LCM プロパティの構成にのみ使用できます。

| プロパティ名 | Type | 説明 |
| --- | --- | --- |
| protectedSettings.configurationArguments.RegistrationKey |PSCredential |必須のプロパティです。 Azure Automation サービスに登録するためにノードで使用するキーを、PowerShell 資格情報オブジェクトのパスワードとして指定します。 この値は、Automation アカウントに対して **listkeys** メソッドを使用することで、自動的に検出できます。  [例](#example-using-referenced-azure-automation-registration-values)を参照してください。 |
| settings.configurationArguments.RegistrationUrl |string |必須のプロパティです。 ノードが登録を試みる Automation エンドポイントの URL を指定します。 この値は、Automation アカウントに対して **reference** メソッドを使用することで、自動的に検出できます。 |
| settings.configurationArguments.NodeConfigurationName |string |必須のプロパティです。 ノードに割り当てる Automation アカウントのノード構成を指定します。 |
| settings.configurationArguments.ConfigurationMode |string |LCM のモードを指定します。 有効なオプションには、**ApplyOnly**、**ApplyandMonitor**、および **ApplyandAutoCorrect** があります。  既定値は **ApplyandMonitor** です。 |
| settings.configurationArguments.RefreshFrequencyMins | uint32 | LCM が Automation アカウントを使用して更新プログラムの確認を試みる頻度を指定します。  既定値は **30** です。  最小値は **15** です。 |
| settings.configurationArguments.ConfigurationModeFrequencyMins | uint32 | LCM が現在の構成を検証する頻度を指定します。 既定値は **15** です。 最小値は **15** です。 |
| settings.configurationArguments.RebootNodeIfNeeded | boolean | DSC 操作で要求した場合に、ノードが自動的に再起動されてよいかどうかを指定します。 既定値は **false** です。 |
| settings.configurationArguments.ActionAfterReboot | string | 構成を適用したときの再起動後の動作を指定します。 有効なオプションは、**ContinueConfiguration** と **StopConfiguration** です。 既定値は **ContinueConfiguration** です。 |
| settings.configurationArguments.AllowModuleOverwrite | boolean | LCM がノード上の既存のモジュールを上書きするかどうかを指定します。 既定値は **false** です。 |

## <a name="settings-vs-protectedsettings"></a>settings と protectedSettings

すべての設定は、VM の設定テキスト ファイルに保存されます。
**settings** の下に記載されているプロパティはパブリック プロパティです。
パブリック プロパティは、設定のテキスト ファイル内で暗号化されません。
**protectedSettings** の下に記載されたプロパティは証明書で暗号化されるため、VM 上の設定ファイルにプレーンテキストで表示されません。

構成に資格情報が必要な場合は、**protectedSettings** に資格情報を含めることができます。

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

## <a name="example-configuration-script"></a>構成スクリプトの例

次の例は、DSC 拡張機能の既定の動作を示したものです (LCM にメタデータの設定を提供し、Automation DSC サービスに登録します)。
構成引数は必須です。
構成引数は、LCM のメタデータを設定するために既定の構成スクリプトに渡されます。

```json
"settings": {
    "configurationArguments": {
        "RegistrationUrl" : "[parameters('registrationUrl1')]",
        "NodeConfigurationName" : "nodeConfigurationNameValue1"
    }
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "registrationKey"
        }
    }
}
```

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Azure Storage での構成スクリプトの使用例

次の例は、[DSC 拡張機能ハンドラーの概要](dsc-overview.md)に関する記事から引用したものです。
この例では、コマンドレットの代わりに Resource Manager テンプレートを使用して拡張機能をデプロイします。
IisInstall.ps1 の構成を保存し、.zip ファイル (例: `iisinstall.zip`) 内に配置してから、アクセス可能な URL にファイルをアップロードします。
この例では、Azure Blob Storage を使用しますが、.zip ファイルを任意の場所からダウンロードすることもできます。

Resource Manager テンプレートでは、次のコードにより、正しいファイルをダウンロードして適切な PowerShell 関数を実行するように VM に対して指示をします。

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/iisinstall.zip",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="example-using-referenced-azure-automation-registration-values"></a>参照されている Azure Automation 登録の値の使用例

次の例では、Azure Automation アカウントのプロパティを参照し、**listkeys** メソッドを使用して主キー (0) を取得することで、**RegistrationUrl** と **RegistrationKey** を取得します。  この例では、パラメーター **automationAccountName** および **NodeConfigName** がテンプレートに指定されています。

```json
"settings": {
    "RegistrationUrl" : "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
    "NodeConfigurationName" : "[parameters('NodeConfigName')]"
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-01-15').Keys[0].value]"
        }
    }
}
```

## <a name="update-from-a-previous-format"></a>以前の形式からの更新

拡張機能の以前の形式の設定 (**ModulesUrl**、**ModuleSource**、**ModuleVersion**、**ConfigurationFunction**、**SasToken**、または **Properties** の各パブリック プロパティを含むもの) は、自動的に拡張機能の現在の形式に対応します。
これらは以前と同じように動作します。

次のスキーマは、以前の設定スキーマの例を示したものです。

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties": {
        "ParameterToConfigurationFunction1": "Value1",
        "ParameterToConfigurationFunction2": "Value2",
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

| 現在のプロパティ名 | 対応する以前のスキーマ |
| --- | --- |
| settings.wmfVersion |settings.wmfVersion |
| settings.configuration.url |settings.ModulesUrl |
| settings.configuration.script |settings.ConfigurationFunction の最初の部分 (\\\\ の前) |
| settings.configuration.function |settings.ConfigurationFunction の 2 番目の部分 (\\\\ の後) |
| settings.configuration.module.name | settings.ModuleSource |
| settings.configuration.module.version | settings.ModuleVersion |
| settings.configurationArguments |settings.Properties |
| settings.configurationData.url |protectedSettings.DataBlobUri (SAS トークンを使用しない) |
| settings.privacy.dataCollection |settings.Privacy.dataCollection |
| settings.advancedOptions.downloadMappings |settings.advancedOptions.downloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |settings.SasToken |
| protectedSettings.configurationDataUrlSasToken |protectedSettings.DataBlobUri の SAS トークン |

## <a name="troubleshooting"></a>トラブルシューティング

直面する可能性のあるいくつかのエラーとそのエラーの修正方法について説明します。

### <a name="invalid-values"></a>無効な値

"Privacy.dataCollection is '{0}'.
The only possible values are '', 'Enable', and 'Disable' (Privacy.dataCollection は '{0}' です。指定できる値は ''、'Enable'、および 'Disable' のみです)"
"WmfVersion is '{0}'.
Only possible values are … and 'latest' (WmfVersion は '{0}' です。指定できる値は … および 'latest' のみです)"

**問題点**:指定した値が許可されていません。

**解決策**:無効な値を有効な値に変更してください。
詳しくは、「[詳細](#details)」の表をご覧ください。

### <a name="invalid-url"></a>無効な URL

"ConfigurationData.url is '{0}'. This is not a valid URL. (Configuration.url は '{0}' です。これは有効な URL ではありません。)" "DataBlobUri is '{0}'. これは有効な URL ではありません)" "Configuration.url is '{0}'.This is not a valid URL (Configuration.url は '{0}' です。 これは有効な URL ではありません)"

**問題点**:指定した URL が無効です。

**解決策**:指定した URL すべてを確認してください。
拡張機能がリモート マシンにアクセスできるように、すべての URL が有効な場所に解決されていることを確認します。

### <a name="invalid-registrationkey-type"></a>無効な RegistrationKey の型

"Invalid type for parameter RegistrationKey of type PSCredential. (型 PSCredential のパラメーター RegistrationKey の無効な型。)"

**問題点**:protectedSettings.configurationArguments の *RegistrationKey* の値は、PSCredential 以外の型としては指定できません。

**解決策**:RegistrationKey の protectedSettings.configurationArguments エントリを、次の形式を使用して PSCredential 型に変更してください。

```json
"configurationArguments": {
    "RegistrationKey": {
        "userName": "NOT_USED",
        "Password": "RegistrationKey"
    }
}
```

### <a name="invalid-configurationargument-type"></a>無効な ConfigurationArgument の型

"Invalid configurationArguments type {0} (無効な configurationArguments の型 {0})"

**問題点**:*ConfigurationArguments* プロパティが **ハッシュ テーブル** オブジェクトに解決できません。

**解決策**:*ConfigurationArguments* プロパティを **ハッシュ テーブル** にしてください。
前の例に示されている形式に従います。 引用符、コンマ、および中かっこに注意します。

### <a name="duplicate-configurationarguments"></a>ConfigurationArguments の重複

"Found duplicate arguments '{0}' in both public and protected configurationArguments (パブリックと保護対象の両方の configurationArguments で重複する引数 '{0}' が見つかりました)"

**問題点**:パブリック設定の *ConfigurationArguments* と保護された設定の *ConfigurationArguments* に同じ名前のプロパティが含まれています。

**解決策**:重複するプロパティのいずれかを削除してください。

### <a name="missing-properties"></a>不足しているプロパティ

"settings.Configuration.function requires that settings.configuration.url or settings.configuration.module is specified (settings.Configuration.function には、settings.configuration.url または settings.configuration.module の指定が必要です)"

"settings.Configuration.url requires that settings.configuration.script is specified (settings.Configuration.url には settings.configuration.script の指定が必要です)"

"settings.Configuration.script requires that settings.configuration.url is specified (settings.Configuration.script には settings.configuration.url の指定が必要です)"

"settings.Configuration.url requires that settings.configuration.function is specified (settings.Configuration.url には settings.configuration.function の指定が必要です)"

"protectedSettings.ConfigurationUrlSasToken requires that settings.configuration.url is specified (protectedSettings.ConfigurationUrlSasToken には settings.configuration.url の指定が必要です)"

"protectedSettings.ConfigurationDataUrlSasToken requires that settings.configurationData.url is specified (protectedSettings.ConfigurationDataUrlSasToken には settings.configurationData.url の指定が必要です)"

**問題点**:定義したプロパティには、不足している別のプロパティが必要です。

**解決策**:

- 不足しているプロパティを指定します。
- 不足しているプロパティを必要とするプロパティを削除します。

## <a name="next-steps"></a>次のステップ

- [仮想マシン スケール セットと Azure DSC 拡張機能の使用](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md)について知る。
- [DSC による安全な資格情報管理](dsc-credentials.md)の詳細を確認する。
- [Azure DSC 拡張機能ハンドラーの概要](dsc-overview.md)を確認する。
- PowerShell DSC の詳細については、[PowerShell ドキュメント センター](/powershell/scripting/dsc/overview/overview)を参照してください。
