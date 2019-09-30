---
title: 既存のサーバーから構成を作成する - Azure Automation
description: Azure Automation のために既存のサーバーから構成を作成する方法について説明します。
keywords: dsc, powershell, 構成, セットアップ
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b8c39ba6c12d43da1b2311ae4d7d85dd13946f25
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69559207"
---
# <a name="create-configurations-from-existing-servers"></a>既存のサーバーから構成を作成する

> 適用対象:Windows PowerShell 5.1

既存のサーバーから構成を作成するのは、困難な作業になることがあります。
*すべての*設定ではなく、気になる設定だけが必要な場合があります。
それでも、構成を正常に適用するためには、どのような順序で設定を適用する必要があるかを知っている必要があります。

> [!NOTE]
> この記事では、オープン ソース コミュニティによって管理されているソリューションについて説明します。
> サポートは、マイクロソフトからではなく、GitHub コラボレーションの形式でのみ利用できます。

## <a name="community-project-reversedsc"></a>コミュニティ プロジェクト:ReverseDSC

SharePoint の頃から、この分野で利用するための [ReverseDSC](https://github.com/microsoft/reversedsc) というソリューションがコミュニティによって管理されてきました。

このソリューションは [SharePointDSC リソース](https://github.com/powershell/sharepointdsc)を基盤としており、既存の SharePoint サーバーからの[情報収集](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use)をオーケストレーションするように機能が拡張されています。
最新バージョンは、インクルードする情報のレベルを決定する複数の[抽出モード](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes)を備えています。

ソリューションを使用した結果、SharePointDSC 構成スクリプトで使用する[構成データ](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data)が生成されます。

データ ファイルが生成されたら、それらを [DSC 構成スクリプト](/powershell/dsc/overview/overview)と共に使用して MOF ファイルを生成し、[その MOF ファイルを Azure Automation にアップロードする](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)ことができます。
次に、[オンプレミス](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws)または [Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) のいずれかからサーバーを登録して、構成をプルします。

ReverseDSC を試すには、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/ReverseDSC/)にアクセスし、ソリューションをダウンロードするか、[Project Site] をクリックして[ドキュメント](https://github.com/Microsoft/sharepointDSC.reverse)を参照してください。

## <a name="next-steps"></a>次の手順

- [Windows PowerShell Desired State Configuration の概要](/powershell/dsc/overview/overview)
- [DSC リソース](/powershell/dsc/resources/resources)
- [ローカル構成マネージャーの構成](/powershell/dsc/managing-nodes/metaconfig)
