---
title: インクルード ファイル
description: インクルード ファイル
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.custom: include file
ms.openlocfilehash: e1512db9bea2aa3eb1a56045a97b3402ac62d84e
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949026"
---
次の表では、Azure Digital Twins で使用可能なロールを説明しています。

| **ロール** | **説明** | **識別子** |
| --- | --- | --- |
| スペース管理者 | 指定のスペースとその下にあるすべてのノードに対する *CREATE*、*READ*、*UPDATE*、および *DELETE* アクセス許可。 グローバル アクセス許可。 | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| ユーザー管理者| ユーザーとユーザー関連オブジェクトに対する *CREATE*、*READ*、*UPDATE*、および *DELETE* アクセス許可。 スペースに対する *READ* アクセス許可。 | dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| デバイス管理者 | デバイスとデバイス関連オブジェクトに対する *CREATE*、*READ*、*UPDATE*、および *DELETE* アクセス許可。 スペースに対する *READ* アクセス許可。 | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| キー管理者 | アクセス キーに対する *CREATE*、*READ*、*UPDATE*、および *DELETE* アクセス許可。 スペースに対する *READ* アクセス許可。 | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| トークン管理者 |  アクセス キーに対する *READ* および *UPDATE* アクセス許可。 スペースに対する *READ* アクセス許可。 | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| User |  スペース、センサー、ユーザー、それに対応する関連オブジェクトに対する *READ* アクセス許可。 | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| サポート スペシャリスト |  アクセス キーを除くすべてのものに対する *READ* アクセス許可。 | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| デバイス インストーラー | デバイス、センサー、それに対応する関連オブジェクトに対する *READ* および *UPDATE* アクセス許可。 スペースに対する *READ* アクセス許可。 | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| ゲートウェイ デバイス | センサーに対する *CREATE* アクセス許可。 デバイス、センサー、それに対応する関連オブジェクトに対する *READ* アクセス許可。 | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |