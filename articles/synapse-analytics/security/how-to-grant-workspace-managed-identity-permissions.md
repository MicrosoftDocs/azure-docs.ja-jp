---
title: Synapse ワークスペースのマネージド ID にアクセス許可を付与する
description: Azure Synapse ワークスペースのマネージド ID のアクセス許可を構成する方法について説明する記事。
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 46fe27205a2c30d6cb2319bf620c6fe1ee34c31e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96458999"
---
# <a name="grant-permissions-to-workspace-managed-identity"></a>ワークスペースのマネージド ID にアクセス許可を付与する

この記事では、Azure Synapse ワークスペースのマネージド ID にアクセス許可を付与する方法について説明します。 そして、アクセス許可によって、Azure portal からワークスペース内の専用 SQL プールや ADLS Gen2 ストレージ アカウントにアクセスできるようになります。

>[!NOTE]
>このドキュメントの残りの部分では、このワークスペース マネージド ID をマネージド ID と呼びます。

## <a name="grant-managed-identity-permissions-to-the-dedicated-sql-pool"></a>マネージド ID に専用 SQL プールへのアクセス許可を付与する

マネージド ID によって、ワークスペース内の専用 SQL プールへのアクセス許可が付与されます。 アクセス許可が付与されていると、専用 SQL プール関連のアクティビティを実行するパイプラインを調整できます。 Azure portal を使用して Azure Synapse ワークスペースを作成するときに、専用 SQL プールに対する CONTROL アクセス許可をマネージド ID に付与できます。

Azure Synapse ワークスペースの作成時に、 **[セキュリティ]** を選択します。 次に、 **[Allow pipelines (running as workspace's system assigned identity) to access SQL pools]\((ワークスペースのシステム割り当て ID として実行されている) パイプラインに SQL プールへのアクセスを許可する\)** を選択します。

![専用 SQL プールに対する CONTROL アクセス許可](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>マネージド ID に ADLS Gen2 ストレージ アカウントへのアクセス許可を付与する

Azure Synapse ワークスペースの作成には、ADLS Gen2 ストレージ アカウントが必要です。 Azure Synapse ワークスペースで Spark プールを正常に起動するには、Azure Synapse マネージド ID に、このストレージ アカウントに対する *ストレージ BLOB データ共同作成者* ロールが必要です。 Azure Synapse のパイプライン オーケストレーションでも、このロールが役立ちます。

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>ワークスペースの作成中にマネージド ID にアクセス許可を付与する

Azure portal を使用して Azure Synapse ワークスペースが作成された後、Azure Synapse では、ストレージ BLOB データ共同作成者ロールをマネージド ID に付与しようとします。 ADLS Gen2 ストレージ アカウントの詳細は、 **[基本]** タブで入力します。

![ワークスペース作成フローの [基本] タブ](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

**[アカウント名]** と **[ファイルシステム名]** で ADLS Gen2 ストレージ アカウントとファイルシステムをそれぞれ選択します。

![ADLS Gen2 ストレージ アカウントの詳細の入力](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

ワークスペース作成者が ADLS Gen2 ストレージ アカウントの **所有者** でもある場合、Azure Synapse では、"*ストレージ BLOB データ共同作成者*" ロールをマネージド ID に割り当てます。 入力したストレージ アカウントの詳細の下に、次のメッセージが表示されます。

![ストレージ BLOB データ共同作成者の割り当てに成功](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

ワークスペース作成者が ADLS Gen2 ストレージ アカウントの所有者でない場合、Azure Synapse では、*ストレージ BLOB データ共同作成者* ロールをマネージド ID に割り当てません。 ストレージ アカウントの詳細の下に表示されるメッセージは、*ストレージ BLOB データ共同作成者* ロールをマネージド ID に付与するための十分なアクセス許可がないことをワークスペース作成者に通知します。

![ストレージ BLOB データ共同作成者の割り当てに失敗](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

メッセージに示されているように、*ストレージ BLOB データ共同作成者* がマネージド ID に割り当てられていない限り、Spark プールを作成することはできません。

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>ワークスペースの作成後にマネージド ID にアクセス許可を付与する

ワークスペースの作成中に、"*ストレージ BLOB データ共同作成者*" をマネージド ID に割り当てていない場合は、ADLS Gen2 ストレージ アカウントの **所有者** が手動でそのロールを ID に割り当てます。 次の手順は、手動での割り当てを行う場合に役立ちます。

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>手順 1:Azure portal で ADLS Gen2 ストレージ アカウントに移動する

Azure portal で、ADLS Gen2 ストレージ アカウントを開き、左側のナビゲーションから **[概要]** を選択します。 *ストレージ BLOB データ共同作成者* ロールは、コンテナーまたはファイルシステム レベルで割り当てる必要があります。 **[コンテナー]** を選択します。  
![ADLS Gen2 ストレージ アカウントの概要](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>手順 2:コンテナーを選択する

マネージド ID には、ワークスペースの作成時に指定したコンテナー (ファイルシステム) へのデータ アクセスが必要です。 このコンテナーまたはファイルシステムは Azure portal で確認できます。 Azure portal で Azure Synapse ワークスペースを開き、左側のナビゲーションから **[概要]** タブを選択します。
![ADLS Gen2 ストレージ アカウントのコンテナー](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


それと同じコンテナーまたはファイルシステムを選択して、*ストレージ BLOB データ共同作成者* ロールをマネージド ID に付与します。
![選択する必要があるコンテナーまたはファイル システムを示すスクリーンショット。](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>手順 3:アクセス制御に移動する

**[アクセス制御 (IAM)]** を選択します。

![[アクセス制御 (IAM)]](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>手順 4:新しいロールの割り当てを追加する

**[+ 追加]** を選択します。

![新しいロールの割り当てを追加する](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-azure-role"></a>手順 5:Azure ロールを選択する

**[ストレージ BLOB データ共同作成者]** ロールを選択します。

![Azure ロールを選択する](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>手順 6:Azure AD セキュリティ プリンシパルを選択する

**[アクセスの割り当て先]** ドロップダウンで **[Azure AD のユーザー、グループ、サービス プリンシパル]** を選択します。

![Azure AD セキュリティ プリンシパルを選択する](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>手順 7:マネージド ID を検索する

マネージド ID の名前は、ワークスペース名でもあります。 マネージド ID を検索するには、 **[選択]** に Azure Synapse ワークスペース名を入力します。 マネージド ID が表示されます。

![マネージド ID を検索する](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>手順 8:マネージド ID を選択する

**[選択したメンバー]** に対してマネージド ID を選択します。 **[保存]** を選択して、ロールの割り当てを追加します。

![マネージド ID を選択する](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>手順 9:ストレージ BLOB データ共同作成者ロールがマネージド ID に割り当てられていることを確認する

**[アクセス制御 (IAM)]** を選択してから、 **[ロールの割り当て]** を選択します。

![ロールの割り当てを確認する](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

**[ストレージ BLOB データ共同作成者]** セクションの下に、*ストレージ BLOB データ共同作成者* ロールが割り当てられているマネージド ID が表示されているのを確認できます。 
![ADLS Gen2 ストレージ アカウントのコンテナーの選択](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>次のステップ

[ワークスペースのマネージド ID](./synapse-workspace-managed-identity.md) の詳細を確認する
