---
title: Azure Stack 用検証レポート | Microsoft Docs
description: Azure Stack 適合性チェッカー レポートを使用して、検証結果を確認します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: 7a6d2b417d7c17ea343ce3019a4ba05cf87b2219
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58800986"
---
# <a name="azure-stack-validation-report"></a>Azure Stack 検証レポート

"*Azure Stack 適合性チェッカー*" ツールを使用して、Azure Stack 環境のデプロイとサービスをサポートする検証を実行します。 このツールでは、結果が .json レポート ファイルに書き込まれます。 レポートには、Azure Stack デプロイの前提条件の状態に関する詳細データと概要データが表示されます。 また、既存の Azure Stack デプロイのシークレット ローテーションについての情報も表示されます。  

## <a name="where-to-find-the-report"></a>レポートの場所

ツールを実行すると、結果のログが **AzsReadinessCheckerReport.json** に出力されます。 また、ツールにより、**AzsReadinessChecker.log** という名前のログも作成されます。 これらのファイルの場所は、PowerShell に検証結果と共に表示されます。

![run-validation](./media/azure-stack-validation-report/validation.png)

両方のファイルに、同じコンピューター上で実行された以降の検証チェックの結果が保持されます。 たとえば、ツールを実行して証明書を検証した後、再度実行して Azure ID を検証し、3 回目に実行では登録を検証することができます。 この 3 回の検証の結果はすべて、結果として生成される .json レポートで確認できます。  

既定では、両方のファイルが **C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json** に書き込まれます。  

- 別のレポートの場所を指定するには、コマンド ラインの末尾に `-OutputPath <path>` パラメーターを使用します。
- ツールの以前の実行に関する情報を **AzsReadinessCheckerReport.json** からクリアするには、コマンド ラインの末尾に `-CleanReport` パラメーターを使用します。

## <a name="view-the-report"></a>レポートを表示する

PowerShell でレポートを表示するには、レポートへのパスを `-ReportPath` の値として指定します。 このコマンドにより、レポートのコンテンツが表示されます。また、結果がまだ出力されていない検証が特定されます。

たとえば、レポートがある場所で開かれている PowerShell プロンプトからレポートを表示するには、次のコマンドを実行します。

```shell
Read-AzsReadinessReport -ReportPath .\AzsReadinessReport.json
```

出力は次の例のようになります。

```shell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation results not available.

############### Registration Validation Summary ###############

Azure Registration Validation results not available.

############### Azure Identity Results ###############

Test                          : ServiceAdministrator
Result                        : OK
AAD Service Admin             : admin@contoso.onmicrosoft.com
Azure Environment             : AzureCloud
Azure Active Directory Tenant : contoso.onmicrosoft.com
Error Details                 : 

############### Azure Identity Validation Summary ###############

Azure Identity Validation found no errors or warnings.

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.

############### AzsReadiness Job Summary ###############

Index             : 0
Operations        : 
StartTime         : 2018/10/22 14:24:16
EndTime           : 2018/10/22 14:24:19
Duration          : 3
PSBoundParameters :
```

## <a name="view-the-report-summary"></a>レポートの概要を表示する

レポートの概要を表示するには、PowerShell コマンドの末尾に `-summary` パラメーターを追加します。 例: 

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -summary
```

概要には、結果がない検証が表示され、完了した検証が成功したか失敗したかが示されます。 出力は次の例のようになります。

```shell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation found no errors or warnings.

############### Registration Validation Summary ###############

Registration Validation found no errors or warnings.

############### Azure Identity Validation Summary ###############

Azure Identity Validation found no errors or warnings.

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.
```

## <a name="view-a-filtered-report"></a>フィルター処理されたレポートを表示する

1 種類の検証でフィルター処理されたレポートを表示するには、**-ReportSections** パラメーターを使用して、次のいずれかの値を指定します。

- 証明書
- AzureRegistration
- AzureIdentity
- Graph
- ADFS
- [ジョブ]
- All  

たとえば、証明書のレポート概要のみを表示するには、次の PowerShell コマンド ラインを使用します。

```powershell
Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -ReportSections Certificate – Summary
```
