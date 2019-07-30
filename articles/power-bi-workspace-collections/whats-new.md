---
title: Power BI ワークスペース コレクションの新機能
description: Power BI ワークスペース コレクションの新機能を紹介します。
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 448c0997068fae0637c7015eed96fafbaf5b3442
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672341"
---
# <a name="whats-new-in-power-bi-workspace-collections"></a>Power BI ワークスペース コレクションの新機能

**Power BI ワークスペース コレクション**の更新は定期的にリリースされます。 ただし、すべてのリリースにユーザー向けの新機能が含まれているわけではありません。一部のリリースでは、バックエンド サービス機能に重点を置いています。 ここでは、ユーザー向けの新機能について説明します。

> [!IMPORTANT]
> Power BI ワークスペース コレクションは非推奨となっており、2018 年 6 月または契約に定める日までに限り利用できます。 アプリケーションの中断を避けるため、Power BI Embedded への移行をご検討ください。 Power BI Embedded にデータを移行する方法については、[Power BI ワークスペース コレクション コンテンツを Power BI Embedded に移行する方法](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)に関するページを参照してください。

## <a name="march-2017"></a>2017 年 3 月

**セルフサービス機能**

* [新しいレポートの作成](create-report-from-dataset.md)
* [レポートに名前を付けて保存する](save-reports.md)
* 読み取りモード、編集モード、新規作成モードでのレポートの埋め込み 
* [レポートの編集モードと読み取りモードの切り替え](toggle-mode.md)

**REST API を使用したデータ接続性**

* [データセットの作成](https://msdn.microsoft.com/library/azure/mt778875.aspx)
* データのプッシュ 

**管理 API**

* レポートとデータセットの複製
* 異なるデータセットへのレポートのバインド

**サンプル**

* 更新された [JavaScript によるレポートの埋め込みのサンプル](https://microsoft.github.io/PowerBI-JavaScript/demo)

## <a name="december-2016"></a>2016 年 12 月

* [新しい JavaScript 埋め込みサンプル](https://microsoft.github.io/PowerBI-JavaScript/demo/)

## <a name="october-2016"></a>2016 年 10 月

* [Power BI ワークスペース コレクションと R を使った高度な分析](https://powerbi.microsoft.com/blog/r-in-pbie/)

## <a name="august-31-2016"></a>2016 年 8 月 31 日
このリリースの新機能は次のとおりです。

* すべての新しい JavaScript SDK で [高度なフィルタリングと ページ ナビゲーション](interact-with-reports.md)がサポートされます。
* Power BI ワークスペース コレクションがカナダ中部のデータセンターでサポートされるようになりました。 [データセンターのステータス](https://azure.microsoft.com/status/)に関するページをご覧ください。

## <a name="july-11-2016"></a>2016 年 7 月 11 日
このリリースの新機能は次のとおりです。

* **お知らせ:** Power BI ワークスペース コレクション サービスがプレビュー段階から GA (一般公開) に移行しました。  
* すべての REST API が **/beta** から **/v1.0** に移行されました。
* .NET および JavaScript SDK が **v1.0**用に更新されました。
* API キーを使用して、Power BI API 呼び出しを直接認証できるようになりました。 埋め込みに必要なのはアプリ トークンだけです。 この一環として、プロビジョニング用トークンと開発用トークンは、v1.0 API では非推奨となりましたが、2016 年 12 月 30 日までベータ版で引き続き機能します。 詳細については、「[Power BI ワークスペース コレクションでの認証と承認](app-token-flow.md)」を参照してください。
* 行レベルのセキュリティ (RLS) でアプリ トークンと埋め込みレポートがサポートされます。 詳細については、「[Power BI ワークスペース コレクションでの行レベルのセキュリティ](row-level-security.md)」を参照してください。
* サンプル アプリケーションが更新され、すべての **v1.0** API 呼び出しに対応しました。
* Power BI ワークスペース コレクションで Azure SDK、PowerShell、CLI がサポートされます。
* 視覚化データを **.csv**にエクスポートできるようになりました。
* Power BI ワークスペース コレクションが Microsoft Azure と同じ言語/ロケールでサポートされるようになりました。 詳細については、[Azure の言語](https://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx)に関するページをご覧ください。

