# 概要
## [Azure Resource Manager とは](resource-group-overview.md)
## [リソース プロバイダーと種類](resource-manager-supported-services.md)
## [Resource Manager デプロイメントとクラシック デプロイメント](resource-manager-deployment-model.md)
## [サブスクリプションのガバナンス](resource-manager-subscription-governance.md)
## [マネージ アプリケーション](managed-application-overview.md)

# 作業開始
## [テンプレートを作成してデプロイする](resource-manager-create-first-template.md)
## [テンプレート用の VS Code 拡張機能](resource-manager-vscode-extension.md)
## [Visual Studio と Resource Manager](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# サンプル
## [コード サンプル](https://azure.microsoft.com/en-us/resources/samples/?service=azure-resource-manager)
## PowerShell
### [テンプレートのデプロイ](resource-manager-samples-powershell-deploy.md)

## Azure CLI
### [テンプレートのデプロイ](resource-manager-samples-cli-deploy.md)

# 方法
## テンプレートの作成
### [テンプレート セクション](resource-group-authoring-templates.md)
### [テンプレートのベスト プラクティス](resource-manager-template-best-practices.md)
### [他のテンプレートにリンクする](resource-group-linked-templates.md)
### [リソース間の依存関係を定義する](resource-group-define-dependencies.md)
### [複数のインスタンスの作成](resource-group-create-multiple.md)
### [場所を設定する](resource-manager-template-location.md)
### [タグを割り当てる](resource-manager-template-tags.md)
### [子リソースの名前と種類を設定する](resource-manager-template-child-resource.md)
### [リソースを更新する](resource-manager-update.md)
### [パラメーターにオブジェクトを使用する](resource-manager-objects-as-parameters.md)
### [リンク済みテンプレート間の状態を共有する](best-practices-resource-manager-state.md)
### [テンプレートの設計パターン](best-practices-resource-manager-design-templates.md)


## デプロイ
### PowerShell
#### [テンプレートのデプロイ](resource-group-template-deploy.md)
#### [SAS トークンを使用したプライベート テンプレートのデプロイ](resource-manager-powershell-sas-token.md)
#### [テンプレートをエクスポートして再デプロイする](resource-manager-export-template-powershell.md)
### Azure CLI
#### [テンプレートのデプロイ](resource-group-template-deploy-cli.md)
#### [SAS トークンを使用したプライベート テンプレートのデプロイ](resource-manager-cli-sas-token.md)
#### [テンプレートをエクスポートして再デプロイする](resource-manager-export-template-cli.md)
### ポータル
#### [リソースのデプロイ](resource-group-template-deploy-portal.md)
#### [テンプレートをエクスポートする](resource-manager-export-template.md)
### [REST API](resource-group-template-deploy-rest.md)
### [リソース グループ間のデプロイ](resource-manager-cross-resource-group-deployment.md)
### [Visual Studio Team Services を使用した継続的インテグレーション](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [デプロイ時にセキュリティで保護された値を渡す](resource-manager-keyvault-parameter.md)

## 管理
### [PowerShell](powershell-azure-resource-manager.md)
### [Azure CLI](xplat-cli-azure-resource-manager.md)
### [ポータル](resource-group-portal.md)
### [REST API](resource-manager-rest-api.md)
### [タグを使用してリソースを整理する](resource-group-using-tags.md)
### [リソースを新しいグループまたはサブスクリプションに移動する](resource-group-move-resources.md)
### [ガバナンスの例](resource-manager-subscription-examples.md)

## アクセスを制御する
### サービス プリンシパルの作成
#### [PowerShell](resource-group-authenticate-service-principal.md)
#### [Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Azure CLI 1.0](resource-group-authenticate-service-principal-cli.md)
#### [ポータル](resource-group-create-service-principal-portal.md)
### [サブスクリプションにアクセスするための認証 API](resource-manager-api-authentication.md)
### [リソースのロック](resource-group-lock-resources.md)

## リソース ポリシーの設定
### [リソース ポリシーとは](resource-manager-policy.md)
### [ポータルを使用してポリシーを割り当てる](resource-manager-policy-portal.md)
### [スクリプトを使用してポリシーを割り当てる](resource-manager-policy-create-assign.md)
### 例
#### [タグ](resource-manager-policy-tags.md)
#### [名前付け規則](resource-manager-policy-naming-convention.md)
#### [ネットワーク](resource-manager-policy-network.md)
#### [Storage](resource-manager-policy-storage.md)
#### [Linux VM](../virtual-machines/linux/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Windows VM](../virtual-machines/windows/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)

## マネージ アプリケーションの使用
### [サービス カタログ アプリケーションの発行](managed-application-publishing.md)
### [サービス カタログ アプリケーションの使用](managed-application-consumption.md)
### [マーケットプレース アプリケーションの発行](managed-application-author-marketplace.md)
### [マーケットプレース アプリケーションの使用](managed-application-consume-marketplace.md)
### [UI 定義を作成する](managed-application-createuidefinition-overview.md)

## Audit
### [アクティビティ ログを表示する](resource-group-audit.md)
### [デプロイ操作の表示](resource-manager-deployment-operations.md)

## トラブルシューティング
### [一般的なデプロイのエラー](resource-manager-common-deployment-errors.md)
### [デプロイ エラーについて](resource-manager-troubleshoot-tips.md)
### [RequestDisallowedByPolicy エラー](resource-manager-policy-requestdisallowedbypolicy-error.md)
### 仮想マシンのデプロイのエラー
#### [Linux](../virtual-machines/linux/troubleshoot-deploy-vm.md)
#### [Windows](../virtual-machines/windows/troubleshoot-deploy-vm.md)

# リファレンス
## [テンプレートの形式](/azure/templates/)
## [テンプレート関数](resource-group-template-functions.md)
### [配列とオブジェクトの関数](resource-group-template-functions-array.md)
### [比較関数](resource-group-template-functions-comparison.md)
### [デプロイ関数](resource-group-template-functions-deployment.md)
### [論理関数](resource-group-template-functions-logical.md)
### [数値関数](resource-group-template-functions-numeric.md)
### [リソース関数](resource-group-template-functions-resource.md)
### [文字列関数](resource-group-template-functions-string.md)
## [UI 定義関数](managed-application-createuidefinition-functions.md)
## [UI 定義要素](managed-application-createuidefinition-elements.md)
### [Microsoft.Common.DropDown](managed-application-microsoft-common-dropdown.md)
### [Microsoft.Common.FileUpload](managed-application-microsoft-common-fileupload.md)
### [Microsoft.Common.OptionsGroup](managed-application-microsoft-common-optionsgroup.md)
### [Microsoft.Common.PasswordBox](managed-application-microsoft-common-passwordbox.md)
### [Microsoft.Common.Section](managed-application-microsoft-common-section.md)
### [Microsoft.Common.TextBox](managed-application-microsoft-common-textbox.md)
### [Microsoft.Compute.CredentialsCombo](managed-application-microsoft-compute-credentialscombo.md)
### [Microsoft.Compute.SizeSelector](managed-application-microsoft-compute-sizeselector.md)
### [Microsoft.Compute.UserNameTextBox](managed-application-microsoft-compute-usernametextbox.md)
### [Microsoft.Network.PublicIpAddressCombo](managed-application-microsoft-network-publicipaddresscombo.md)
### [Microsoft.Network.VirtualNetworkCombo](managed-application-microsoft-network-virtualnetworkcombo.md)
### [Microsoft.Storage.MultiStorageAccountCombo](managed-application-microsoft-storage-multistorageaccountcombo.md)
### [Microsoft.Storage.StorageAccountSelector](managed-application-microsoft-storage-storageaccountselector.md)
## [PowerShell](/powershell/module/azurerm.resources)
## [Azure CLI](/cli/azure/resource)
## [.NET](/dotnet/api/microsoft.azure.management.resourcemanager)
## [Java](/java/api/com.microsoft.azure.management.resources)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagement.html)
## [REST ()](/rest/api/resources/)

# リソース
## [Azure のロードマップ](https://azure.microsoft.com/roadmap/?category=monitoring-management)
## [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)
## [サービスの更新情報](https://azure.microsoft.com/updates/?product=azure-resource-manager)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [スロットル リクエスト](resource-manager-request-limits.md)
## [非同期操作の追跡](resource-manager-async-operations.md)
## [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)
