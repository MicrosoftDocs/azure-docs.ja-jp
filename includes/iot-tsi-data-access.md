---
title: インクルード ファイル
description: インクルード ファイル
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: include
ms.date: 08/20/2018
ms.author: anshan
ms.custom: include file
ms.openlocfilehash: c9daa86bf36b260001d9969385b9e8a98a8ac0cf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58125367"
---
## <a name="grant-data-access"></a>データ アクセスの許可

次の手順に従って、ユーザー プリンシパルにデータ アクセスを許可します。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. Time Series Insights 環境を検索します。 **[検索]** ボックスに「**Time Series**」と入力します。 検索結果の **[Time Series Environment]\(Time Series 環境\)** を選択します。 

3. リストから Time Series Insights 環境を選択します。

4. **[データ アクセス ポリシー]** を選択し、**[+ 追加]** をクリックします。
    ![Time Series Insights ソースの管理 - 環境](media/iot-tsi-data-access/getstarted-grant-data-access1.png)

5. **[ユーザーの選択]** を選択します。  ユーザー名または電子メール アドレスを検索して追加するユーザーを探します。 **[選択]** をクリックして選択内容を確定します。 

    ![Time Series Insights ソースの管理 - 追加](media/iot-tsi-data-access/getstarted-grant-data-access2.png)

6. **[ロールの選択]** を選択します。 ユーザーに割り当てる適切なアクセス ロールを選択します。
   - ユーザーに対して、参照データの変更に加え、保存クエリとパースペクティブを環境の他のユーザーと共有することを許可する場合は **[共同作成者]** を選択します。 
   - それ以外の場合は **[閲覧者]** を選択し、環境内のデータへのクエリ実行と環境内の個人用 (共有されない) クエリの保存をユーザーに許可します。

     **[OK]** を選び、ロールの選択を確定します。

     ![Time Series Insights ソースの管理 - ユーザーの選択](media/iot-tsi-data-access/getstarted-grant-data-access3.png)

7. **[ユーザー ロールの選択]** ページで **[OK]** を選択します。

    ![Time Series Insights ソースの管理 - ロールの選択](media/iot-tsi-data-access/getstarted-grant-data-access4.png)

8. **[データ アクセス ポリシー]** ページには、各ユーザーのユーザーとロールが一覧表示されます。

    ![Time Series Insights ソースの管理 - 結果](media/iot-tsi-data-access/getstarted-grant-data-access5.png)