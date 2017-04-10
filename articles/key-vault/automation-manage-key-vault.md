---
title: "Azure Automation を使用した Azure Key Vault の管理 | Microsoft Docs"
description: "Azure Automation サービスを使用して Azure Key Vault を管理する方法について説明します。"
services: Key-Vault, automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 4e780762-19b6-4ca6-b894-ebb44c538f35
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: magoedte;csand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: dee39662472fe54776b591977f2b1ecb39d15b00


---
# <a name="managing-azure-key-vault-using-azure-automation"></a>Azure Automation を使用した Azure Key Vault の管理
このガイドでは、Azure Automation サービスと、このサービスを使用して Azure Key Vault でのキーとシークレットの管理を簡略化する方法について紹介します。

## <a name="what-is-azure-automation"></a>Azure Automation とは
[Azure Automation](../automation/automation-intro.md) は、プロセスの自動化と必要な状態の構成によってクラウド管理を簡略化するための Azure サービスです。 Azure Automation を使用して手動タスク、繰り返しタスク、実行時間の長いタスク、エラーが発生しやすいタスクを自動化し、信頼性と効率性を向上して組織のゴール達成までの時間を短縮できます。

Azure Automation は、ニーズに対応可能な信頼性と可用性の高いワークフロー実行エンジンを提供します。 Azure Automation では、サード パーティ製のシステムによって手動でプロセスを開始したり、必要なときに正確にタスクが起動されるようにスケジュールされた間隔でプロセスを開始できます。

Azure Automation でクラウド管理タスクを自動実行すれば、運用上のオーバーヘッドが削減され、IT と DevOps スタッフの負担が軽減されるため、ビジネス価値の向上にフォーカスすることができます。

## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>Azure Automation を Azure Key Vault の管理に役立てる方法
[Azure Key Vault コマンドレット](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4)および [Azure Classic Key Vault コマンドレット](https://msdn.microsoft.com/library/azure/dn868052.aspx)を使用することにより、Azure Automation で Key Vault を管理できます。 クラシック Key Vault を管理するための Azure モジュールは Azure Automation で自動的に使用でき、 [AzureRM-KeyVault モジュール](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) を Azure Automation にインポートできるので、サービス内で Key Vault 管理タスクの多くを実行できます。 Azure Automation 内のこれらのコマンドレットと別の Azure サービスのコマンドレットを組み合わせて、Azure サービスおよびサード パーティ システム全体の複雑なタスクを自動化することもできます。

Azure Key Vault コマンドレットを使用すると、次のタスクを実行できます。 

* キー コンテナーを作成して構成する
* キーを作成またはインポートする
* シークレットを作成または更新する
* キーの属性を更新する
* キーまたはシークレットを取得する
* キーまたはシークレットを削除する

PowerShell を使用して Key Vault を管理するいくつかの例を次に示します。  

* [Azure Key Vault - Step by Step (Azure Key Vault - ステップ バイ ステップ)](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [Setting Up and Configuring an Azure Key Vault (Azure Key Vault のセットアップと構成)](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)

## <a name="next-steps"></a>次のステップ
ここまで、Azure Automation の基本と Azure Automation を使用して Azure Key Vault を管理する方法について説明しました。Azure Automation の詳細については、次の各リンクを参照してください。

* Azure Automation の [作業開始のチュートリアル](../automation/automation-first-runbook-graphical.md)に関するページを参照してください。
* [Azure Key Vault の PowerShell スクリプト](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5)に関するページを参照してください。




<!--HONumber=Nov16_HO3-->


