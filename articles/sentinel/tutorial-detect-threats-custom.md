---
title: Azure Sentinel を使用して疑わしい脅威を検出するカスタム分析ルールを作成する | Microsoft Docs
description: このチュートリアルでは、Azure Sentinel で疑わしい脅威を検出するカスタム分析ルールを作成する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 18c11198f6b81e72e371b3ab06ed3a7330078c52
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72023773"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>チュートリアル:疑わしい脅威を検出するカスタム分析ルールを作成する

Azure Sentinel に [データ ソースを接続](quickstart-onboard.md) した後、環境全体で特定の条件を検索し、条件が一致したときにインシデントを生成するカスタム ルールを作成して、それらを調査できるようにすることができます。 このチュートリアルは、Azure Sentinel で脅威を検出するカスタム ルールを作成するために役立ちます。

このチュートリアルは、Azure Sentinel で脅威を検出するためのものです。
> [!div class="checklist"]
> * 分析ルールを作成する
> * 脅威への対応を自動化する

## <a name="create-custom-analytic-rules"></a>カスタム分析ルールを作成する

環境内で疑わしい脅威や異常の種類を検索するために役立つカスタム分析ルールを作成することができます。 このルールによって直ちに通知されるので、脅威のトリアージ、調査、および修復を行うことができます。

1. Azure portal の Azure Sentinel の下で **[Analytics]** を選択します。

1. 上部のメニュー バーで、 **[+ 作成]** を選択し、 **[スケジュール済みクエリ ルール]** を選択します。 これにより、 **[Custom rule creation]\(カスタム ルールの作成\)** ウィザードが開きます。

    ![スケジュール済みクエリを作成する](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. **[全般]** タブで、わかりやすい名前と説明を指定します。 必要に応じて、 **[アラートの重要度]** を設定します。 ルールを作成したら、有効にすることができます。これにより、ルールの作成が完了した直後に、ルールが実行されます。 無効として作成した場合、ルールは **[Active rules]\(アクティブなルール\)** タブに追加され、必要に応じてそこから有効にすることができます。

    ![カスタム分析ルールの作成を開始する](media/tutorial-detect-threats-custom/general-tab.png)

1. **[設定]** タブでクエリを直接作成するか、Log Analytics でクエリを作成してから **[検索クエリ]** フィールドに貼り付けるかのいずれかを行うことができます。 クエリを変更して構成すると、Azure Sentinel によって、右側にある **[結果のプレビュー]** ウィンドウでクエリの結果がシミュレートされます。 これにより、作成するアラートに対して特定の期間に生成されるデータの量を把握できます。 この量は、 **[クエリの実行間隔]** と **[Lookup data from the last]\(最新のものからデータを検索する\)** に設定した内容によって異なります。 平均して、アラートによってあまりにも頻繁にアラートがトリガーされることがわかった場合は、平均のベースラインを上回るように結果の数を増やすことができます。

   ![Azure Sentinel でクエリを作成する](media/tutorial-detect-threats-custom/settings-tab.png)

   異常な数のリソースが Azure アクティビティで作成されたときにアラートを発するサンプル クエリを次に示します。

    `AzureActivity
    \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    \| where ActivityStatus == "Succeeded"
    \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

   > [!NOTE]
   > クエリの長さは 1 から 10,000 文字にする必要があります。また、"search \*" または "union \*" を含めることはできません。

    1. **[Query scheduling]\(クエリのスケジュール\)** で次のパラメーターを設定します。

        1.  **[クエリの実行間隔]** を設定して、クエリが実行される頻度 (5 分間隔の頻度または 1 日に 1 回の頻度) を示す **[頻度]** を設定します。

        1.  クエリを実行するデータ量について時間枠を制御するように、 **[Lookup data from the last]\(最新のものからデータを検索する\)** を設定します。たとえば、60 分のデータに対し毎時間実行できます。

        1. 発生後に 1 回のみアラートを取得する場合は、Azure Sentinel を **[Stop running the query after alert is generated]\(アラートの生成後にクエリの実行を停止する\)** に設定できます。 クエリの実行を停止する期間 (最大 24 時間) を設定する必要があります。

    1. **[Alert trigger]** (アラートのトリガー) の下でアラートのトリガー条件を定義します。 **[エンティティ マッピング]** で、クエリの列を Azure Sentinel によって認識されるエンティティ フィールドにマップできます。 フィールドごとに、Log Analytics で作成したクエリで関連する列を適切なエンティティのフィールドにマップします。 各エンティティには、SID や GUID などの複数のフィールドが含まれています。 上のレベルのエンティティだけでなく、任意のフィールドに従ってエンティティをマップすることができます。

1.  **[Automate responses]\(対応の自動化\)** タブで、カスタム ルールによってアラートが生成されたときに自動的に実行するプレイブックを選択します。 プレイブックの作成と自動化の詳細については、 [脅威への対応](tutorial-respond-threats-playbook.md)に関する記事を参照してください。

    ![Azure Sentinel で脅威への対応を自動化する](media/tutorial-detect-threats-custom/response-automation-custom.png)

1. **[確認]** を選択して新しいアラート ルールのすべての設定を確認し、 **[Create to initialize your alert rule]\(アラート ルールを作成して初期化\)** を選択します。

   ![カスタム クエリを確認する](media/tutorial-detect-threats-custom/review-tab.png)

1.  アラートが作成されると、 **[Active rules]\(アクティブなルール\)** のテーブルにカスタム ルールが作成されます。 この一覧から、各ルールを有効にしたり、無効にしたり、削除したりできます。

1.  作成したアラート ルールの結果を表示するには、 **[インシデント]** ページに移動します。ここでは、トリアージ、 [インシデントの調査](tutorial-investigate-cases.md)、脅威の修復を行うことができます。


> [!NOTE]
> Azure Sentinel で生成されたアラートは、 [Microsoft Graph Security](https://aka.ms/securitygraphdocs) を通じて使用できます。 詳細については、 [Microsoft Graph のセキュリティ アラートのドキュメント](https://aka.ms/graphsecurityreferencebetadocs)を参照してください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Sentinel を使用して、脅威の検出を開始する方法について説明しました。

脅威への対応を自動化する方法については、「 [Azure Sentinel で脅威への自動対応を設定する](tutorial-respond-threats-playbook.md)」を参照してください。

