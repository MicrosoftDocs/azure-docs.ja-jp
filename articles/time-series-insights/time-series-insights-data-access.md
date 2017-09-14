---
title: "Azure Time Series Insights のデータ アクセス ポリシー | Microsoft Docs"
description: "このチュートリアルでは、Time Series Insights のデータ アクセス ポリシーを管理する方法について学習します"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: omravi
ms.translationtype: Human Translation
ms.sourcegitcommit: 300958a69fc854cb8db02120a383a4cbbfcacd7b
ms.openlocfilehash: e975c6d8f217bc73948c0c046204b16b1a742bc7
ms.contentlocale: ja-jp
ms.lasthandoff: 05/20/2017

---

# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Azure Portal を使用して Time Series Insights 環境にデータ アクセスを許可する

Time Series Insights 環境には、2 種類の独立したアクセス ポリシーがあります。

* 管理アクセス ポリシー
* データ アクセス ポリシー

どちらのポリシーでも、特定の環境に対するさまざまなアクセス許可が Azure Active Directory プリンシパル (ユーザーおよびアプリ) に付与されます。 このプリンシパル (ユーザーおよびアプリ) は、その環境が含まれているサブスクリプションに関連付けられたアクティブなディレクトリ (つまり "Azure テナント") に属している必要があります。

管理アクセス ポリシーは、次のような、環境の構成に関連したアクセス許可を付与します。
*   環境、イベント ソース、参照データ セットの作成と削除。
*   データ アクセス ポリシーの管理。

データ アクセス ポリシーは、データ クエリの発行、環境内での参照データの操作、環境に関連付けられた保存クエリとパースペクティブの共有を実行するためのアクセス許可を付与します。

この 2 種類のポリシーにより、環境の管理へのアクセスと環境内のデータへのアクセスを明確に分離できます。 たとえば、環境の所有者または作成者がデータ アクセスから除外されるように環境を設定することが可能です。 また、環境からのデータの読み取りを許可されているユーザーとサービスに環境の構成へのアクセスを許可しないことも可能です。

## <a name="grant-data-access"></a>データ アクセスの許可
次の手順では、ユーザー プリンシパルにデータ アクセスを許可する方法を示します。

1.  [Azure Portal](https://portal.azure.com) にサインインします。
2.  Azure Portal の左側のメニューで [すべてのリソース] をクリックします。
3.  Time Series Insights 環境を選択します。

  ![Time Series Insights ソースの管理 - 環境](media/data-access/getstarted-grant-data-access1.png)

4.  [データ プレーン アクセス] を選択し、[追加] をクリックします。

  ![Time Series Insights ソースの管理 - 追加](media/data-access/getstarted-grant-data-access2.png)

5.  [ユーザーの選択] をクリックします。
6.  電子メール アドレスでユーザーを検索して選択します。
7.  [ユーザーの選択] ブレードの [選択] をクリックします。

  ![Time Series Insights ソースの管理 - ユーザーの選択](media/data-access/getstarted-grant-data-access3.png)

8.  [Select role (ロールの選択)] をクリックします。
9.  ユーザーに対して、参照データの変更に加え、保存クエリとパースペクティブを環境の他のユーザーと共有することを許可する場合は [共同作成者] を選択します。 それ以外の場合は [閲覧者] を選択し、環境内のデータへのクエリ実行と環境内の個人用 (共有されない) クエリの保存をユーザーに許可します。
10. [Select role (ロールの選択)] ブレードで [OK] をクリックします。

  ![Time Series Insights ソースの管理 - ロールの選択](media/data-access/getstarted-grant-data-access4.png)

11. [Select role (ユーザー ロールの選択)] ブレードで [OK] をクリックします。
12. 次のような結果が表示されます。

  ![Time Series Insights ソースの管理 - 結果](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>次のステップ

* [イベント ソースを作成する](time-series-insights-add-event-source.md)
* イベント ソースに[イベントを送信する](time-series-insights-send-events.md)
* [Time Series Insights ポータル](https://insights.timeseries.azure.com)で環境を表示する

