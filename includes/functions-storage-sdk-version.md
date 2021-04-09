---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 6a37850eb6536c5399d63144e60ea210fbc194d8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "77198422"
---
#### <a name="azure-storage-sdk-version-in-functions-1x"></a>Functions 1.x での Azure Storage SDK のバージョン

Functions 1.x では、Storage トリガーとバインドは、Azure Storage SDK のバージョン 7.2.1 ([WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet パッケージ) を使用します。 別のバージョンの Storage SDK を参照し、関数シグネチャで Storage SDK の型にバインドする場合は、Functions ランタイムがその型にバインドできないことを報告することがあります。 解決するには、プロジェクトで [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) を参照していることを確認します。
