---
title: Azure 診断監視 - Azure Attestation
description: Azure Attestation の Azure 診断監視
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d01e7817906927295591353b710afe2899aacdf1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726480"
---
# <a name="setting-up-diagnostics-with-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Azure Attestation のトラステッド プラットフォーム モジュール (TPM) エンドポイントで診断を設定する

Azure のアクティビティ ログとリソース ログを含む Azure の[プラットフォーム ログ](../azure-monitor/essentials/platform-logs-overview.md)では、Azure リソースとそれらが依存している Azure プラットフォームの詳細な診断情報と監査情報が提供されます。 [プラットフォーム メトリック](../azure-monitor/essentials/data-platform-metrics.md)は、既定で収集され、通常は Azure Monitor メトリック データベースに格納されます。 この記事では、プラットフォーム メトリックとプラットフォーム ログをさまざまな送信先に送信するための診断設定を作成して構成する方法について詳しく説明します。 

TPM エンドポイント サービスは、アクティビティを監視する目的に使用できます。診断設定を使用して有効にします。 PowerShell を使用して TPM サービス エンドポイントの [Azure Monitoring](../azure-monitor/overview.md) を設定するには、以下の手順に従ってください。 

Azure Attestation サービスを設定する。 

[Azure PowerShell を使用して Azure Attestation を設定する](./quickstart-powershell.md)

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```
アクティビティ ログは、ストレージ アカウントの [コンテナー] セクションにあります。 詳細については、[Azure リソースからリソース ログを収集し、Azure Monitor で分析する](../azure-monitor/essentials/tutorial-resource-logs.md)方法に関する記事を参照してください。
