---
title: "Log Analytics の IIS ログ | Microsoft Docs"
description: "インターネット インフォメーション サービス (IIS) は、Log Analytics によって収集されるログ ファイルにユーザーの利用状況を格納します。  この記事では、IIS ログの収集を構成する方法と OMS リポジトリに作成されるレコードの詳細について説明します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: c6e2ecebf6cd1b246c155c158d12d4d83bd1feda
ms.openlocfilehash: bda2da933accb769bae4c9b420ae330014fc2ba0
ms.lasthandoff: 02/27/2017


---
# <a name="iis-logs-in-log-analytics"></a>Log Analytics の IIS ログ
インターネット インフォメーション サービス (IIS) は、Log Analytics によって収集されるログ ファイルにユーザーの利用状況を格納します。  

![IIS ログ](media/log-analytics-data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>IIS ログの構成
Log Analytics は IIS によって作成されたログ ファイルからエントリを収集するため、[ログを収集するように IIS を構成](https://technet.microsoft.com/library/hh831775.aspx)する必要があります。

Log Analytics でサポートされるのは、W3C 形式で保存された IIS ログ ファイルのみです。カスタム フィールドや IIS 詳細ログはサポートされません。  
NCSA 形式や IIS のネイティブ形式のログは、Log Analytics によって収集されません。

Log Analytics の IIS ログは、[Log Analytics の [設定] の [データ] メニュー](log-analytics-data-sources.md#configuring-data-sources)から構成します。  必要な構成は、 **[Collect W3C format IIS log files]**(W3C 形式の IIS ログ ファイルを収集する) を選択することのみです。

IIS ログの収集を有効にする場合、各サーバーに IIS ログ ロールオーバー設定を構成することをお勧めします。

## <a name="data-collection"></a>データ収集
Log Analytics は約 15 分おきに接続されたソースから IIS ログのエントリを収集します。  エージェントは、収集元の場所を各イベント ログに記録します。  エージェントがオフラインになった場合、Log Analytics は中止した箇所からイベントの収集を再開します。オフラインの間に作成されたイベントも収集されます。

## <a name="iis-log-record-properties"></a>IIS ログ レコードのプロパティ
IIS ログ レコードの型は **W3CIISLog** になり、次の表に示すプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| Computer |イベント収集元のコンピューターの名前。 |
| cIP |クライアントの IP アドレス。 |
| csMethod |要求のメソッド (GET、POST など) |
| csReferer |現在のサイトに到達するまでにユーザーがリンクをフォローした元のサイト |
| csUserAgent |クライアントのブラウザーの種類。 |
| csUserName |サーバーにアクセスした認証されたユーザーの名前。 匿名ユーザーはハイフンで示されます。 |
| csUriStem |要求のターゲット (Web ページなど) |
| csUriQuery |クライアントが実行を試行していたクエリ (存在する場合) |
| ManagementGroupName |Operations Manager エージェントの管理グループの名前。  その他のエージェントの場合、これは AOI-\<workspace ID\> です。 |
| RemoteIPCountry |クライアントの IP アドレスの国。 |
| RemoteIPLatitude |クライアントの IP アドレスの緯度。 |
| RemoteIPLongitude |クライアントの IP アドレスの経度。 |
| scStatus |HTTP 状態コード。 |
| scSubStatus |副状態エラー コード。 |
| scWin32Status |Windows 状態コード。 |
| sIP |Web サーバーの IP アドレス。 |
| SourceSystem |OpsMgr |
| sPort |クライアントが接続されているサーバー上のポート。 |
| sSiteName |IIS サイトの名前。 |
| TimeGenerated |エントリがログに記録された日付と時刻。 |
| TimeTaken |要求の処理にかかった時間 (ミリ秒) |

## <a name="log-searches-with-iis-logs"></a>IIS ログのログ検索
次の表は、IIS ログ レコードを取得するログ クエリのさまざまな例をまとめたものです。

| クエリ | Description |
|:--- |:--- |
| Type=W3CIISLog |IIS ログのすべてのレコード。 |
| Type=W3CIISLog scStatus=500 |戻り状態が 500 であるすべての IIS ログ レコード。 |
| Type=W3CIISLog &#124; Measure count() by cIP |クライアントの IP アドレス別の IIS ログ エントリの数。 |
| Type=W3CIISLog csHost="www.contoso.com" &#124; Measure count() by csUriStem |ホスト www.contoso.com の URL 別の IIS ログ エントリの数。 |
| Type=W3CIISLog &#124; Measure Sum(csBytes) by Computer &#124; top 500000 |各 IIS コンピューターによって受信された合計バイト数。 |

## <a name="next-steps"></a>次のステップ
* 分析のために別の [データ ソース](log-analytics-data-sources.md) を収集するように Log Analytics を構成します。
* [ログ検索](log-analytics-log-searches.md) について学習し、データ ソースとソリューションから収集されたデータを分析します。
* IIS ログで検出された重要な状態を事前に通知するように、Log Analytics のアラートを構成します。


