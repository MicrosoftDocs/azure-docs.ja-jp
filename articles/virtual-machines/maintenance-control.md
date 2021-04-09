---
title: Azure portal を使用した Azure 仮想マシンのメンテナンス コントロールの概要
description: メンテナンス コントロールを使用して、ご利用の Azure VM にメンテナンスを適用するタイミングを制御する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/19/2020
ms.author: cynthn
ms.openlocfilehash: 290a1e8da4e9b3e8eff171ab2d5837bfc9c381b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552424"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>メンテナンス コントロールによるプラットフォームの更新の管理 

メンテナンス コントロールを使用して、再起動が不要なプラットフォームの更新を管理します。 Azure は、信頼性、パフォーマンス、セキュリティを改善し、新機能を導入する目的で、インフラストラクチャを頻繁に更新しています。 ほとんどの更新は、ユーザーからは透過的に行われます。 ゲーム、メディア ストリーミング、金融取引などの一部のセンシティブなワークロードでは、数秒間であっても、メンテナンスのために VM がフリーズしたり切断したりすることが許されません。 メンテナンス コントロールが提供するオプションを使用すると、プラットフォームの更新を待機し、35 日間のローリング期間内にそれらの更新を適用できます。 

メンテナンス コントロールを使用すると、分離された VM や Azure 専用ホストに更新プログラムを適用するタイミングをユーザーが決定できます。

メンテナンス コントロールを使用すると、次のことができます。
- 更新プログラムを 1 つの更新プログラム パッケージにまとめる。
- 最大 35 日間待機して更新プログラムを適用する。 
- メンテナンス スケジュールを構成するか、[Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler)を使用して、プラットフォームの更新を自動化します。
- メンテナンス構成が、複数のサブスクリプションやリソース グループ全体で機能するようにする。 

## <a name="limitations"></a>制限事項

- VM は、[専用ホスト](./dedicated-hosts.md)上にあるか、[分離された VM サイズ](isolation.md)を使用して作成される必要があります。
- メンテナンス スケジュールが宣言されている場合、その期間は 2 時間以上であることが必要です。
- 35 日後に、更新プログラムが自動的に適用されます。
- ユーザーは、**リソース共同作成者** のアクセス権を持っている必要があります。

## <a name="management-options"></a>管理オプション

次のいずれかのオプションを使用して、メンテナンス構成を作成および管理できます。

- [Azure CLI](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Azure Portal](maintenance-control-portal.md)

Azure Functions のサンプルについては、「[メンテナンス コントロールと Azure Functions を使用したメンテナンス更新のスケジュール](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler)」を参照してください。

## <a name="next-steps"></a>次のステップ

詳細については、[メンテナンスと更新プログラム](maintenance-and-updates.md)に関するページを参照してください。
