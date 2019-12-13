---
title: パイプライン アクティビティでの Azure Key Vault シークレットの使用
description: Data Factory パイプラインの実行中に、Azure Key Vault から格納済みの資格情報をフェッチして使用する方法について説明します。
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: 837d62784a56ad0f17471cca5a660819d4a83e12
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926756"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>パイプライン アクティビティでの Azure Key Vault シークレットの使用

資格情報またはシークレット値を Azure Key Vault に格納し、パイプラインの実行時にそれらを使用してアクティビティに渡すことができます。

## <a name="prerequisites"></a>前提条件

この機能は、データ ファクトリのマネージド ID に依存しています。  [データ ファクトリのマネージド ID](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) からのその動作方法について理解し、使用中のデータ ファクトリに ID が関連付けられていることを確認します。

## <a name="steps"></a>手順

1. データ ファクトリのプロパティを開き、マネージド ID アプリケーション ID の値をコピーします。

    ![マネージド ID の値](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. キー コンテナー アクセス ポリシーを開き、シークレットの取得および一覧表示のためのマネージド ID アクセス許可を追加します。

    ![キー コンテナー アクセス ポリシー](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![キー コンテナー アクセス ポリシー](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    **[追加]** をクリックし、 **[保存]** をクリックします。

3. キー コンテナー シークレットに移動し、シークレット識別子をコピーします。

    ![シークレット識別子](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    データ ファクトリ パイプラインの実行中に取得するシークレットの URI をメモしておきます。

4. データ ファクトリ パイプラインで、新しい Web アクティビティを追加し、次のように構成します。  

    |プロパティ  |値  |
    |---------|---------|
    |セキュリティで保護された出力     |True         |
    |URL     |[自分のシークレットの URI 値]?api-version=7.0         |
    |方法     |GET         |
    |認証     |MSI         |
    |リソース        |https://vault.azure.net       |

    ![Web アクティビティ](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > シークレットの URI の末尾に **? api-version = 7.0** を追加する必要があります。  

    > [!CAUTION]
    > シークレット値がプレーンテキストでログに記録されないようにするには、[セキュリティで保護された出力] オプションを true に設定します。  この値を使用するその他のアクティビティでは、[セキュリティで保護された入力] オプションが true に設定されている必要があります。

5. この値を別のアクティビティで使用するには、コード式 **@activity("web").output.value)** を使用します。

    ![コード式](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>次の手順

Azure Key Vault を使用して、データ ストアおよび計算のための資格情報を格納する方法については、「[Azure Key Vault への資格情報の格納](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)」を参照してください
