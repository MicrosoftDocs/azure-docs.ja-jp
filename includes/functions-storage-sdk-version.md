---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
manager: cfowler
ms.service: azure-functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: c2fff707dcaafac69efcad3dbf33446a7b797396
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608267"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Functions 1.x での Azure Storage SDK のバージョン

Functions 1.x では、Storage トリガーとバインドは、Azure Storage SDK のバージョン 7.2.1 ([WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet パッケージ) を使用します。 別のバージョンの Storage SDK を参照し、関数シグネチャで Storage SDK の型にバインドする場合は、Functions ランタイムがその型にバインドできないことを報告することがあります。 解決するには、プロジェクトで [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) を参照していることを確認します。
