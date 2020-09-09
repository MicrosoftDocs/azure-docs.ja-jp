---
title: 管理ハブ
description: Azure Data Factory 管理ハブでの接続、ソース管理の構成、およびグローバル作成のプロパティの管理
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.date: 06/02/2020
ms.openlocfilehash: bba49014fd6b082ea015da259402a8e87bf4afff
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87851990"
---
# <a name="management-hub-in-azure-data-factory"></a>Azure Data Factory の管理ハブ

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory UX の *[管理]* タブによってアクセスされる管理ハブは、データ ファクトリのグローバル管理アクションをホストするポータルです。 ここでは、データ ストアと外部コンピューティングへの接続、ソース管理の構成、およびトリガーの設定を管理できます。

## <a name="manage-connections"></a>接続の管理

### <a name="linked-services"></a>リンクされたサービス

リンクされたサービスは、外部のデータ ストアやコンピューティング環境に接続するための Azure Data Factory の接続情報を定義します。 詳細については、[リンクされたサービスの概念](concepts-linked-services.md)に関するページを参照してください。 リンクされたサービスの作成、編集、および削除は、管理ハブで行います。

![リンクされたサービスの管理](media/author-management-hub/management-hub-linked-services.png)

### <a name="integration-runtimes"></a>統合ランタイム

統合ランタイムは、異なるネットワーク環境間でデータ統合機能を提供するために Azure Data Factory によって使用されるコンピューティング インフラストラクチャです。 詳細については、[統合ランタイムの概念](concepts-integration-runtime.md)に関するページを参照してください。 管理ハブでは、統合ランタイムを作成、削除、および監視できます。

![統合ランタイムの管理](media/author-management-hub/management-hub-integration-runtime.png)

## <a name="manage-source-control"></a>ソース管理の管理

### <a name="git-configuration"></a>Git 構成

管理ハブで、構成されている Git リポジトリの設定を表示および編集します。 詳細については、「[Azure Data Factory でのソース管理](source-control.md)」を参照してください。

![Git リポジトリの管理](media/author-management-hub/management-hub-git.png)

### <a name="parameterization-template"></a>パラメーター化テンプレート

コラボレーション ブランチから発行するときに生成される Resource Manager テンプレート パラメーターをオーバーライドするため、カスタム パラメーター ファイルを生成または編集することができます。 詳細については、[Resource Manager テンプレートでカスタム パラメーターを使用する](continuous-integration-deployment.md#use-custom-parameters-with-the-resource-manager-template)方法を参照してください。 パラメーター化テンプレートは、Git リポジトリで作業している場合にのみ使用できます。 *arm-template-parameters-definition.json* ファイルが作業ブランチに存在しない場合は、既定のテンプレートを編集すると生成されます。

![カスタム パラメーターの管理](media/author-management-hub/management-hub-custom-parameters.png)

## <a name="manage-authoring"></a>オーサリングの管理

### <a name="triggers"></a>トリガー

トリガーは、パイプラインの実行をいつ開始するかを決定します。 現在、トリガーは、ウォール クロック スケジュールに基づくことも、定期的な間隔で実行することも、イベントに依存することもできます。 詳細については、「[トリガー実行](concepts-pipeline-execution-triggers.md#trigger-execution)」を参照してください。 管理ハブでは、トリガーの現在の状態を作成、編集、削除、または表示できます。

![カスタム パラメーターの管理](media/author-management-hub/management-hub-triggers.png)

### <a name="global-parameters"></a>グローバル パラメーター

グローバル パラメーターは、任意の式のパイプラインで使用できるデータ ファクトリ全体での定数です。 詳細については、[グローバル パラメーター](author-global-parameters.md)に関するページを参照してください。

![グローバル パラメーターを作成する](media/author-global-parameters/create-global-parameter-3.png)

## <a name="next-steps"></a>次のステップ

ADF で [Git リポジトリを構成する](source-control.md)方法を学習します


