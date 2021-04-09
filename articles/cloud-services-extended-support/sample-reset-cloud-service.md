---
title: Azure PowerShell の例 - Azure クラウド サービス (延長サポート) をリセットする
description: Azure クラウド サービス (延長サポート) のデプロイをリセットするサンプル スクリプト
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 5c43d61b1e7cd98674eab4c6d857cc1114a06013
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99475322"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>Azure クラウド サービス (延長サポート) をリセットする 
ここに挙げたサンプルでは、Azure クラウド サービス (延長サポート) の既存のデプロイをリセットする際のさまざまな方法を紹介します。

## <a name="reimage-role-instances-of-cloud-service"></a>クラウド サービスのロール インスタンスを再イメージ化する
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
このコマンドは、ContosOrg というリソース グループに所属する ContosoCS というクラウド サービスの 2 つのロール インスタンス (ContosoFrontEnd_IN_0 と ContosoBackEnd_IN_1) を再イメージ化するものです。

## <a name="reimage-all-roles-of-cloud-service"></a>クラウド サービスのロールをすべて再イメージ化する
```powershell
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
このコマンドは、ContosOrg というリソース グループに所属する ContosoCS というクラウド サービスのすべてのロール インスタンスを再イメージ化するものです。

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>クラウド サービスの単一のロール インスタンスを再イメージ化する
```powershell
Invoke-AzCloudServiceRoleInstanceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0"
```
このコマンドは、ContosOrg というリソース グループに所属する ContosoCS というクラウド サービスの ContosoFrontEnd_IN_0 というロール インスタンスを再イメージ化するものです。

## <a name="rebuild-role-instances-of-cloud-service"></a>クラウド サービスのロール インスタンスをリビルドする
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
このコマンドは、ContosOrg というリソース グループに所属する ContosoCS というクラウド サービスの 2 つのロール インスタンス (ContosoFrontEnd_IN_0 と ContosoBackEnd_IN_1) をリビルドするものです。

## <a name="rebuild-all-roles-of-cloud-service"></a>クラウド サービスのロールをすべてリビルドする
```powershell
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
このコマンドは、ContosOrg というリソース グループに所属する ContosoCS というクラウド サービスのすべてのロール インスタンスをリビルドするものです。

## <a name="restart-role-instances-of-cloud-service"></a>クラウド サービスのロール インスタンスを再起動する
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
このコマンドは、ContosOrg というリソース グループに所属する ContosoCS というクラウド サービスの 2 つのロール インスタンス (ContosoFrontEnd_IN_0 と ContosoBackEnd_IN_1) を再起動するものです。

## <a name="restart-all-roles-of-cloud-service"></a>クラウド サービスのロールをすべて再起動する
```powershell
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
このコマンドは、ContosOrg というリソース グループに所属する ContosoCS というクラウド サービスのすべてのロール インスタンスを再起動するものです。

## <a name="next-steps"></a>次のステップ

- Azure Cloud Services (延長サポート) の詳細については、[Azure Cloud Services (延長サポート) の概要](overview.md)に関するページを参照してください。
- [Cloud Services (延長サポート) のサンプル リポジトリ](https://github.com/Azure-Samples/cloud-services-extended-support)を確認します。
