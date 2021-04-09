---
title: Azure Automation State Configuration のために既存のサーバーから構成を作成する
description: この記事では、Azure Automation State Configuration のために既存のサーバーから構成を作成する方法について説明します。
keywords: DSC, PowerShell, 構成, セットアップ
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8f376fb383e50a39f0f12d45cf9b5ae47ad6fcbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86186505"
---
# <a name="create-configurations-from-existing-servers"></a>既存のサーバーから構成を作成する

> 適用先:Windows PowerShell 5.1

既存のサーバーから構成を作成するのは、困難な作業になることがあります。
*すべての* 設定ではなく、気になる設定だけが必要な場合があります。
それでも、構成を正常に適用するためには、どのような順序で設定を適用する必要があるかを知っている必要があります。

> [!NOTE]
> この記事では、オープン ソース コミュニティによって管理されているソリューションについて説明します。
> サポートは、Microsoft からではなく、GitHub コラボレーションの形式でのみ利用できます。

## <a name="community-project-reversedsc"></a>コミュニティ プロジェクト:ReverseDSC

SharePoint の頃から、この分野で利用するための [ReverseDSC](https://github.com/microsoft/reversedsc) というソリューションがコミュニティによって管理されてきました。

このソリューションは [SharePointDSC リソース](https://github.com/powershell/sharepointdsc)を基盤としており、既存の SharePoint サーバーからの[情報収集](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use)をオーケストレーションするように機能が拡張されています。
最新バージョンは、インクルードする情報のレベルを決定する複数の[抽出モード](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes)を備えています。

ソリューションを使用した結果、SharePointDSC 構成スクリプトで使用する[構成データ](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data)が生成されます。

データ ファイルが生成されたら、それらを [DSC 構成スクリプト](/powershell/scripting/dsc/overview/overview)と共に使用して MOF ファイルを生成し、[その MOF ファイルを Azure Automation にアップロードする](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation)ことができます。
次に、[オンプレミス](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines)または [Azure](./automation-dsc-onboarding.md#enable-azure-vms) のいずれかからサーバーを登録して、構成をプルします。

ReverseDSC を試すには、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/ReverseDSC/)にアクセスし、ソリューションをダウンロードするか、[Project Site] をクリックして[ドキュメント](https://github.com/Microsoft/sharepointDSC.reverse)を参照してください。

## <a name="next-steps"></a>次のステップ

- PowerShell DSC については、「[Windows PowerShell Desired State Configuration の概要](/powershell/scripting/dsc/overview/overview)」をご覧ください。
- PowerShell DSC リソースについては、「[DSC リソース](/powershell/scripting/dsc/resources/resources)」をご覧ください。
- Local Configuration Manager の構成の詳細については、「[ローカル構成マネージャーの構成](/powershell/scripting/dsc/managing-nodes/metaconfig)」をご覧ください。
