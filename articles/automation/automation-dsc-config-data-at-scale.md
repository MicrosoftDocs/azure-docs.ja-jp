---
title: 大規模な構成データ - Azure Automation
description: Azure Automation で状態構成のために大規模なデータ構成を行う方法について説明します。
keywords: DSC, PowerShell, 構成, セットアップ
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f6bb89370c85389d6c41306ed224d27d710cd7c8
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69559170"
---
# <a name="configuration-data-at-scale"></a>スケーリング対応の構成データ

> 適用対象:Windows PowerShell 5.1

数百台、数千台のサーバーを管理することは困難な場合があります。
最も困難な点は[構成データ](/powershell/dsc/configurations/configdata)を実際に管理することであるというフィードバックをお客様からいただいています。
場所、種類、環境などの論理構造間で情報を整理します。

> [!NOTE]
> この記事では、オープン ソース コミュニティによって管理されるソリューションについて説明します。
> サポートは、Microsoft からではなく、GitHub コラボレーションの形式でのみ利用できます。

## <a name="community-project-datum"></a>コミュニティ プロジェクト:Datum

この課題を解決するために、[Datum](https://github.com/gaelcolas/Datum) という名前のコミュニティ管理ソリューションが作成されました。
Datum は、他の構成管理プラットフォームからの優れたアイデアに基づいて構築されており、PowerShell DSC 用の同じ種類のソリューションを実装しています。
情報は、論理的なアイデアに基づいて[テキストファイルにまとめられます](https://github.com/gaelcolas/Datum#3-intended-usage)。
次に例を示します。

- グローバルに適用する必要がある設定
- 1 つの場所のすべてのサーバーに適用する必要がある設定
- すべてのデータベース サーバーに適用する必要がある設定
- 個別のサーバー設定

この情報は、ユーザーが希望するファイル形式 (JSON、Yaml、または PSD1) で編成されます。
その後、サーバーまたはサーバー ロールの 1 つのビューに各ファイルの[情報を統合する](https://github.com/gaelcolas/Datum#datum-tree)ことによって構成データ ファイルを生成するためのコマンドレットが提供されます。

データ ファイルが生成されたら、それらを [DSC 構成スクリプト](/powershell/dsc/configurations/write-compile-apply-configuration)と共に使用して MOF ファイルを生成し、[その MOF ファイルを Azure Automation にアップロードする](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)ことができます。
次に、[オンプレミス](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws)または [Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) でサーバーを登録して、構成をプルします。

Datum を試すには、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/datum/)にアクセスし、ソリューションをダウンロードするか、[Project Site] をクリックして[ドキュメント](https://github.com/gaelcolas/Datum#2-getting-started--concepts)を参照してください。

## <a name="next-steps"></a>次の手順

- [Windows PowerShell Desired State Configuration の概要](/powershell/dsc/overview/overview)
- [DSC リソース](/powershell/dsc/resources/resources)
- [ローカル構成マネージャーの構成](/powershell/dsc/managing-nodes/metaconfig)
