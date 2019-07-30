---
title: API の HTTP 応答コード - QnA Maker
titleSuffix: Azure Cognitive Services
description: どのような HTTP 応答コードが QnA Maker API から返されるかを理解しましょう。 そのことが、あらゆるエラーの解決に役立ちます。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/20/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 0416b282a869536b0cea591ba39d7a3804697bac
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446534"
---
# <a name="qna-maker-api-http-response-codes"></a>QnA Maker API の HTTP 応答コード
[管理](https://go.microsoft.com/fwlink/?linkid=2092179)と予測 API は、HTTP 応答コードを返します。 応答メッセージには、要求に固有の情報が含まれていますが、HTTP 応答の状態コードは全般的なものです。 

### <a name="management"></a>管理

|コード|説明|
|:--|--|
|2xx|Success|
|400|"要求のパラメーターが正しくない" とは、必須パラメーターが見つからない、形式が正しくない、または大きすぎることを意味します|
|400|"要求の本文が正しくない" とは、JSON が見つからない、形式が正しくない、または大きすぎることを意味します|
|401|無効なキー|
|403|禁止されています - アカウントに適切なアクセス許可がありません|
|404|KB が存在しません|
|410|この API は非推奨であり、現在は利用できません|
