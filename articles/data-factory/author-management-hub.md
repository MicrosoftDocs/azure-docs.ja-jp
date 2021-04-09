---
title: 管理ハブ
description: Azure Data Factory 管理ハブでの接続、ソース管理の構成、およびグローバル作成のプロパティの管理
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.date: 02/01/2021
ms.openlocfilehash: b4b9ecef84f8ffcc82107299ad6603466380d1c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371500"
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

管理ハブの Git 構成設定で、Git に関連するすべての情報を表示および編集することができます。 

最後に発行されたコミット情報も一覧表示され、複数の環境を対象にして最後に発行またはデプロイされた正確なコミットを理解するのに役立ちます。 また、運用環境で修正プログラムを実行する場合にも役立ちます。

詳細については、「[Azure Data Factory でのソース管理](source-control.md)」を参照してください。

![Git リポジトリの管理](media/author-management-hub/management-hub-git.png)

### <a name="parameterization-template"></a>パラメーター化テンプレート

コラボレーション ブランチから発行するときに生成される Resource Manager テンプレート パラメーターをオーバーライドするため、カスタム パラメーター ファイルを生成または編集することができます。 詳細については、[Resource Manager テンプレートでカスタム パラメーターを使用する](continuous-integration-deployment.md#use-custom-parameters-with-the-resource-manager-template)方法を参照してください。 パラメーター化テンプレートは、Git リポジトリで作業している場合にのみ使用できます。 *arm-template-parameters-definition.json* ファイルが作業ブランチに存在しない場合は、既定のテンプレートを編集すると生成されます。

![カスタム パラメーターの管理](media/author-management-hub/management-hub-custom-parameters.png)

## <a name="manage-authoring"></a>オーサリングの管理

### <a name="triggers"></a>トリガー

トリガーは、パイプラインの実行をいつ開始するかを決定します。 現在、トリガーは、ウォール クロック スケジュールに基づくことも、定期的な間隔で実行することも、イベントに依存することもできます。 詳細については、「[トリガー実行](concepts-pipeline-execution-triggers.md#trigger-execution)」を参照してください。 管理ハブでは、トリガーの現在の状態を作成、編集、削除、または表示できます。

![トリガーの現在の状態を作成、編集、削除、または表示する場所を示すスクリーンショット。](media/author-management-hub/management-hub-triggers.png)

### <a name="global-parameters"></a>グローバル パラメーター

グローバル パラメーターは、任意の式のパイプラインで使用できるデータ ファクトリ全体での定数です。 詳細については、[グローバル パラメーター](author-global-parameters.md)に関するページを参照してください。

![グローバル パラメーターを作成する](media/author-global-parameters/create-global-parameter-3.png)

## <a name="next-steps"></a>次のステップ

ADF で [Git リポジトリを構成する](source-control.md)方法を学習します


