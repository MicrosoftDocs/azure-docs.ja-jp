---
title: "Stream Analytics の Data Lake Store 出力 | Microsoft Docs"
description: "Stream Analytics ジョブで Azure Data Lake Store の認証と承認を構成する"
keywords: 
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: ea5baafa-0054-4c70-973a-6a3a8c6eaffc
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 2b4a10c77ae02ac0e9eeecf6d7d6ade6e4c33115
ms.openlocfilehash: 5d0a86742e4b238b94855464b2528db32b80526b
ms.lasthandoff: 01/25/2017


---
# <a name="stream-analytics-data-lake-store-output"></a>Stream Analytics の Data Lake Store 出力
Stream Analytics ジョブは、 [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)など、いくつかの出力方法をサポートしています。 Azure Data Lake Store は、ビッグ データの分析ワークロードに対応するエンタープライズ規模のハイパースケール リポジトリです。 Data Lake Store を使用すると、運用分析や調査分析を目的として任意のサイズ、種類、取り込み速度のデータを格納できます。

## <a name="authorize-a-data-lake-store-account"></a>Data Lake Store アカウントを承認する
1. Microsoft Azure 管理ポータルで出力として Data Lake Store を選択すると、既存の Data Lake Store の使用を承認するか、Azure クラシック ポータルで Data Lake Store へのアクセス権を要求するように求められます。
   
   ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.png)  
   
2. Data Lake Store へのアクセス権を既に持っている場合、[今すぐ承認] をクリックすると、"承認にリダイレクトしています..." というページが短時間表示されます。 このページは自動的に閉じられ、Data Lake Store 出力を構成できるページが表示されます。

Data Lake Store にサインアップしていない場合は、[今すぐサインアップ] リンクをクリックして要求を開始するか、[開始手順](../data-lake-store/data-lake-store-get-started-portal.md)に関するページの説明に従ってください。

## <a name="configure-the-data-lake-store-output-properties"></a>Data Lake Store 出力のプロパティを構成する
Data Lake Store アカウントが認証されたら、Data Lake Store 出力のプロパティを構成できます。 次の表は、Data Lake Store 出力を構成するためのプロパティ名とその説明の一覧です。

<table>
<tbody>
<tr>
<td><B>プロパティ名</B></td>
<td><B>説明</B></td>
</tr>
<tr>
<td>出力のエイリアス</td>
<td>クエリの出力をこの Data Lake Store に出力するためにクエリで使用されるわかりやすい名前です。</td>
</tr>
<tr>
<td>Data Lake Store アカウント</td>
<td>出力を送信するストレージ アカウントの名前。 ポータルにログインしたユーザーがアクセス権を持っている Data Lake Store アカウントのドロップダウン リストが表示されます。</td>
</tr>
<tr>
<td>パス プレフィックスのパターン [<I>省略可能</I>]</td>
<td>指定した Data Lake Store アカウント内のファイルを書き込むために使用するファイル パス。 <BR>{date}、{time}<BR>例 1: folder1/logs/{date}/{time}<BR>例 2: folder1/logs/{date}</td>
</tr>
<tr>
<td>日付形式 [<I>省略可能</I>]</td>
<td>日付トークンがプレフィックス パスで使用されている場合、ファイルを編成する日付形式を選択できます。 例: YYYY/MM/DD</td>
</tr>
<tr>
<td>時刻形式 [<I>省略可能</I>]</td>
<td>時刻トークンがプレフィックス パスで使用されている場合、ファイルを編成する時刻形式を指定します。 現在唯一サポートされている値は HH です。</td>
</tr>
<tr>
<td>イベントのシリアル化の形式</td>
<td>出力データのシリアル化形式。 JSON、CSV、Avro がサポートされています。</td>
</tr>
<tr>
<td>エンコード</td>
<td>CSV または JSON 形式の場合、エンコードを指定する必要があります。 現在のところ、UTF-8 が、唯一サポートされているエンコード形式です。</td>
</tr>
<tr>
<td>区切り記号</td>
<td>CSV のシリアル化のみに適用されます。 Stream Analytics は、CSV データをシリアル化するために、一般的な区切り記号をサポートしています。 サポートしている値は、コンマ、セミコロン、スペース、タブ、および縦棒です。</td>
</tr>
<tr>
<td>形式</td>
<td>JSON のシリアル化のみに適用されます。 行区切りを指定すると、各 JSON オブジェクトを新しい行で区切ることで、出力が書式設定されます。 配列を指定すると、出力が JSON オブジェクトの配列として書式設定されます。</td>
</tr>
</tbody>
</table>

## <a name="renew-data-lake-store-authorization"></a>Data Lake Store の承認を更新する
現在、Data Lake Store 出力のあるすべてのジョブについて、90 日おきに認証トークンを手動で更新する必要があるという制限事項があります。 また、ジョブが作成されてから、または最後の認証以降にパスワードが変わっている場合、Data Lake Store アカウントを再認証する必要もあります。 この問題の症状として、ジョブ出力がなく、再承認が必要であることを示すエラーが操作ログに記録されます。

この問題を解決するには、実行中のジョブを停止し、Data Lake Store 出力に移動します。 [承認の更新] リンクをクリックすると、"承認にリダイレクトしています..." というページが短時間表示されます。 処理が成功すると、このページは自動的に閉じられ、"承認が正常に更新されました" と表示されます。 ページの下部にある [保存] をクリックする必要があります。[最後に停止した時刻] からジョブを再開して継続することで、データの損失を防ぐことができます。

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.png)


