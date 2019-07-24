---
title: インクルード ファイル
description: インクルード ファイル
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: c779147e464a592d45da8a9a2d8e812320dc23e8
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631074"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> データは、既定の [Apache Avro](https://avro.apache.org/) 形式または JSON (プレビュー) のいずれかで BLOB ストレージに書き込むことができます。 
>    
> JSON 形式にエンコードする機能は、IoT Hub が提供されているすべてのリージョンでプレビュー段階です (米国東部、米国西部、西ヨーロッパを除く)。 エンコード形式は、BLOB ストレージ エンドポイントの構成時にのみ設定できます。 既に設定されているエンドポイントの形式は変更できません。 JSON エンコードを使用する場合は、メッセージのシステム プロパティで contentType を JSON に設定し、contentEncoding を UTF-8 に設定する必要があります。 
>
> BLOB ストレージ エンドポイントの使用に関する詳細については、[BLOB ストレージへのルーティングに関するガイダンス](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-blob-storage)を参照してください。
>