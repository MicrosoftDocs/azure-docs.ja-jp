---
title: Azure Automation State Configuration で大規模なデータ構成を行う
description: この記事では、Azure Automation State Configuration で大規模なデータ構成を行う方法について説明します。
keywords: DSC, PowerShell, 構成, セットアップ
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bc55a4c5ab20cac041a00a0f924b207eb256ae8b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86186522"
---
# <a name="configure-data-at-scale-for-azure-automation-state-configuration"></a>Azure Automation State Configuration で大規模なデータ構成を行う

> 適用先:Windows PowerShell 5.1

数百台、数千台のサーバーを管理することは困難な場合があります。
最も困難な点は[構成データ](/powershell/scripting/dsc/configurations/configdata)を実際に管理することであるというフィードバックをお客様からいただいています。
場所、種類、環境などの論理構造間で情報を整理します。

> [!NOTE]
> この記事では、オープン ソース コミュニティによって管理されているソリューションについて説明します。
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

データ ファイルが生成されたら、それらを [DSC 構成スクリプト](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)と共に使用して MOF ファイルを生成し、[その MOF ファイルを Azure Automation にアップロードする](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation)ことができます。
次に、[オンプレミス](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines)または [Azure](./automation-dsc-onboarding.md#enable-azure-vms) のいずれかからサーバーを登録して、構成をプルします。

Datum を試すには、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/datum/)にアクセスし、ソリューションをダウンロードするか、[Project Site] をクリックして[ドキュメント](https://github.com/gaelcolas/Datum#2-getting-started--concepts)を参照してください。

## <a name="next-steps"></a>次のステップ

- PowerShell DSC については、「[Windows PowerShell Desired State Configuration の概要](/powershell/scripting/dsc/overview/overview)」をご覧ください。
- PowerShell DSC リソースについては、「[DSC リソース](/powershell/scripting/dsc/resources/resources)」をご覧ください。
- Local Configuration Manager の構成の詳細については、「[ローカル構成マネージャーの構成](/powershell/scripting/dsc/managing-nodes/metaconfig)」をご覧ください。
