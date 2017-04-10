---
title: "Power BI Embedded の新機能"
description: "Power BI Embedded の新機能に関する最新情報を入手できます。"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 2794ae98-b9a7-45df-b6e1-962a395b91fa
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/11/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 07c53a5d6b1881a4c207a2aefed9fcede0fa069e
ms.lasthandoff: 03/14/2017

---
# <a name="whats-new-in-power-bi-embedded"></a>Power BI Embedded の新機能

**Power BI Embedded** の更新は定期的にリリースされます。 ただし、すべてのリリースにユーザー向けの新機能が含まれているわけではありません。一部のリリースでは、バックエンド サービス機能に重点を置いています。 ここでは、ユーザー向けの新機能について説明します。 必ず頻繁に確認してください。

## <a name="march-2017"></a>2017 年 3 月

<iframe width="640" height="360" src="https://www.youtube.com/embed/ibuN4DzCl5c?showinfo=0" frameborder="0" allowfullscreen></iframe>

**セルフサービス機能**

* [新しいレポートの作成](power-bi-embedded-create-report-from-dataset.md)
* [レポートに名前を付けて保存する](power-bi-embedded-save-reports.md)
* 読み取りモード、編集モード、新規作成モードでのレポートの埋め込み 
* [レポートの編集モードと読み取りモードの切り替え](power-bi-embedded-toggle-mode.md)

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

* [Power BI Embedded と R を使用した高度な分析](https://powerbi.microsoft.com/blog/r-in-pbie/)

## <a name="august-31st-2016"></a>2016 年 8 月 31 日
このリリースの新機能は次のとおりです。

* すべての新しい JavaScript SDK で [高度なフィルタリングと ページ ナビゲーション](power-bi-embedded-interact-with-reports.md)がサポートされます。
* Power BI Embedded がカナダ中部データセンターでサポートされるようになりました。 [データセンターのステータス](https://azure.microsoft.com/status/)に関するページをご覧ください。

## <a name="july-11th-2016"></a>2016 年 7 月 11 日
このリリースの新機能は次のとおりです。

* **お知らせ:** Power BI Embedded サービスがプレビューではなくなり、一般公開 (GA) されました。  
* すべての REST API が **/beta** から **/v1.0** に移行されました。
* .NET および JavaScript SDK が **v1.0**用に更新されました。
* API キーを使用して、Power BI API 呼び出しを直接認証できるようになりました。 埋め込みに必要なのはアプリ トークンだけです。 この一環として、プロビジョニング用トークンと開発用トークンは、v1.0 API では使用されなくなりましたが、2016 年 12 月 30 日までベータ版で引き続き機能します。 詳細については、「 [Power BI Embedded での認証と承認](power-bi-embedded-app-token-flow.md)」をご覧ください。
* 行レベルのセキュリティ (RLS) でアプリ トークンと埋め込みレポートがサポートされます。 詳細については、「 [Power BI Embedded の行レベルのセキュリティ](power-bi-embedded-rls.md)」をご覧ください。
* サンプル アプリケーションが更新され、すべての **v1.0** API 呼び出しに対応しました。
* Power BI Embedded で Azure SDK、PowerShell、CLI がサポートされます。
* 視覚化データを **.csv**にエクスポートできるようになりました。
* Microsoft Azure と同じすべての言語/ロケールで Power BI Embedded がサポートされるようになりました。 詳細については、[Azure の言語](http://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx)に関するページをご覧ください。


