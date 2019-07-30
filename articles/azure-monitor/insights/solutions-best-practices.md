---
title: Azure の管理ソリューションのベスト プラクティス | Microsoft Docs
description: ''
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: a4f982f6265d1c8cab2ae666b9d6e2e33beb5064
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672932"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Azure の管理ソリューションの作成に関するベスト プラクティス (プレビュー)
> [!NOTE]
> 本記事は、現在プレビュー段階である Azure の管理ソリューションの作成に関する暫定版ドキュメントです。 本記事で説明するスキーマは、変更されることがあります。  

この記事では、Azure の[管理ソリューション ファイル作成](solutions-solution-file.md)のベスト プラクティスを紹介します。  この情報は、追加のベスト プラクティスに合わせて更新されます。

## <a name="data-sources"></a>データ ソース
- データソースは、[Resource Manager テンプレートで構成](../../azure-monitor/platform/template-workspace-configuration.md)できますが、ソリューション ファイルには含めないでください。  現在データソースの構成はべき等ではなく、ユーザーのワークスペースにある既存のソリューションが、上書きされる可能性があるためです。<br><br>たとえば、アプリケーション イベント ログの警告イベントおよびエラー イベントが必要なソリューションがあるとします。  これをソリューションのデータソースとして指定し、さらに、これがユーザーによってワークスペースで構成されていると、情報イベントを削除してしまう可能性があります。  すべてのイベントを含めると、ユーザーのワークスペースで収集の対象となる情報イベントが多くなりすぎることがあります。

- 標準的なデータ ソースのいずれかのデータがソリューションに必要な場合は、前提条件としてこれを定義する必要があります。  顧客がデータ ソースを独自に構成する必要があることをドキュメントに記述します。  
- [データ フロー確認](../../azure-monitor/platform/view-designer-tiles.md)メッセージをソリューションのすべてのビューに追加して、必要なデータを収集するために構成する必要がある、データソースに関する指示をユーザーに提供します。  このメッセージは、必要なデータが見つからないときに、ビューのタイルに表示されます。


## <a name="runbooks"></a>Runbook
- スケジュールに従って実行する必要があるソリューションで、Runbook ごとに [Automation スケジュール](../../automation/automation-schedules.md)を追加します。
- [IngestionAPI モジュール](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5)を、Log Analytics レポジトリへのデータ書き込みを行う、Runbook によって使用されるソリューションに挿入します。  このリソースを[参照](solutions-solution-file.md#solution-resource)するようにソリューションを構成して、ソリューションが削除されてもリソースが保持されるようにします。  これにより、複数のソリューションがモジュールを共有できます。
- [Automation 変数](../../automation/automation-schedules.md)を使用して、ユーザーが後で変更できる値をソリューションに指定します。  ソリューションで変数を使用するように構成されていても、その値は引き続き変更できます。

## <a name="views"></a>ビュー
- すべてのソリューションに、ユーザーのポータルに表示されている 1 つのビューを含める必要があります。  ビューでは複数の[視覚化パーツ](../../azure-monitor/platform/view-designer-parts.md)を使って、さまざまなデータセットを表すことができます。
- [データ フロー確認](../../azure-monitor/platform/view-designer-tiles.md)メッセージをソリューションのすべてのビューに追加して、必要なデータを収集するために構成する必要がある、データソースに関する指示をユーザーに提供します。
- ビューを[含める](solutions-solution-file.md#solution-resource)ようにソリューションを構成することで、ソリューションを削除したときに、ビューも一緒に削除されるようにします。

## <a name="alerts"></a>アラート
- ソリューション ファイルで受信者リストをパラメーターとして定義し、ユーザーがソリューションをインストールしたときに、定義できるようにします。
- アラート ルールを[参照](solutions-solution-file.md#solution-resource)するようにソリューションを構成して、ユーザーがその構成を変更できるようにします。  受信者リストやアラートのしきい値を変更したり、アラート ルールを無効にしたりできます。 


## <a name="next-steps"></a>次の手順
* [管理ソリューションの設計および構築](solutions-creating.md)の基本的なプロセスを順を追って確認する。
* [ソリューション ファイルの作成](solutions-solution-file.md)方法を確認する。
* 管理ソリューションに、[保存した検索とアラートを追加する](solutions-resources-searches-alerts.md)。
* 管理ソリューションに[ビューを追加する](solutions-resources-views.md)。
* 管理ソリューションに [Automation Runbook とその他のリソースを追加する](solutions-resources-automation.md)。

