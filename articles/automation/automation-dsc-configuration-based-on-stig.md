---
title: Azure Automation State Configuration の STIG に基づくデータの構成
description: この記事では、Azure Automation State Configuration の DoD STIG に基づいてデータを構成する方法について説明します。
keywords: DSC, PowerShell, 構成, セットアップ
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dab0cd7f7d660808b4ed7a91318baad55f80928c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87015138"
---
# <a name="configure-data-based-on-security-technical-information-guide-stig"></a>セキュリティ技術情報ガイド (STIG) に基づいてデータを構成する

> 適用先:Windows PowerShell 5.1

構成コンテンツを初めて作成することは困難である可能性があります。
多くの場合、目標は、業界の推奨事項に沿っていることが望ましい "ベースライン" に従って、サーバーの構成を自動化することです。

> [!NOTE]
> この記事では、オープン ソース コミュニティによって管理されているソリューションについて説明します。
> サポートは、Microsoft からではなく、GitHub コラボレーションの形式でのみ利用できます。

## <a name="community-project-powerstig"></a>コミュニティ プロジェクト:PowerSTIG

[PowerSTIG](https://github.com/microsoft/powerstig) という名前のコミュニティ プロジェクトでは、STIG (セキュリティ技術実装ガイド) に関して提供された[公開情報](https://public.cyber.mil/stigs/)に基づく DSC コンテンツを生成することで、この問題を解決することを目標としています。

ベースラインの処理は、簡単そうに聞こえますが、なかなか大変です。
多くの組織では、ルールに対する[例外を文書化](https://github.com/microsoft/powerstig#powerstigdata)し、そのデータを大規模に管理する必要があります。
PowerSTIG では、設定の全範囲を 1 つの大きなファイルで処理するのではなく、構成の各領域に対応する[複合リソース](https://github.com/microsoft/powerstig#powerstigdsc)を提供することによって、この問題に取り組んでいます。

構成が生成されたら、それらを [DSC 構成スクリプト](/powershell/scripting/dsc/configurations/configurations)を使用して MOF ファイルを生成し、[その MOF ファイルを Azure Automation にアップロードする](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation)ことができます。
次に、[オンプレミス](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines)または [Azure](./automation-dsc-onboarding.md#enable-azure-vms) のいずれかからサーバーを登録して、構成をプルします。

PowerSTIG を試すには、[PowerShell ギャラリー](https://www.powershellgallery.com)にアクセスし、ソリューションをダウンロードするか、[Project Site]\(プロジェクト サイト\) をクリックして[ドキュメント](https://github.com/microsoft/powerstig)を参照してください。

## <a name="next-steps"></a>次のステップ

- PowerShell DSC については、「[Windows PowerShell Desired State Configuration の概要](/powershell/scripting/dsc/overview/overview)」をご覧ください。
- PowerShell DSC リソースについては、「[DSC リソース](/powershell/scripting/dsc/resources/resources)」をご覧ください。
- Local Configuration Manager の構成の詳細については、「[ローカル構成マネージャーの構成](/powershell/scripting/dsc/managing-nodes/metaconfig)」をご覧ください。
