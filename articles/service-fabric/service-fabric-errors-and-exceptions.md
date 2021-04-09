---
title: 一般的にスローされる FabricClient 例外
description: FabricClient API でアプリケーションやクラスターの管理操作を実行しているときにスローされる可能性のある一般的な例外とエラーについて説明します。
author: georgewallace
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: gwallace
ms.openlocfilehash: 7f3c3e072a3a2e4f7723f84b2c70ba0d0ddb9d03
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "86258831"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>FabricClient API の操作時に発生する一般的な例外とエラー
Service Fabric のアプリケーションやサービス、クラスターに対する管理タスクは、クラスター アドミニストレーターやアプリケーション管理者が [FabricClient](/dotnet/api/system.fabric.fabricclient) API を使って実行できます。 管理タスクの例としては、アプリケーションのデプロイ、アップグレード、削除、クラスターの正常性のチェック、サービスのテストといった作業が挙げられます。 アプリケーション開発者やクラスター アドミニストレーターは、FabricClient API を使用して、Service Fabric クラスターや Service Fabric アプリケーションを管理するためのツールを開発できます。

FabricClient を使用して実行できる操作の種類は多岐にわたります。  FabricClient のメソッドは、誤った入力やランタイム エラー、一過性のインフラストラクチャの問題などに起因するエラーによって例外をスローします。  特定のメソッドでどのような例外がスローされるかについては、API リファレンス ドキュメントを参照してください。 ただし、数ある [FabricClient](/dotnet/api/system.fabric.fabricclient) API に共通してスローされる例外もいくつかあります。 以下の表は、各種の FabricClient API に共通する例外を一覧にしたものです。

| 例外 | スローされるタイミング |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](/dotnet/api/system.fabric.fabricobjectclosedexception) |[FabricClient](/dotnet/api/system.fabric.fabricclient) オブジェクトがクローズ済みであるとき。 使用中の [FabricClient](/dotnet/api/system.fabric.fabricclient) オブジェクトを破棄したうえで、新しい [FabricClient](/dotnet/api/system.fabric.fabricclient) オブジェクトをインスタンス化してください。 |
| [System.TimeoutException](/dotnet/core/api/system.timeoutexception) |操作がタイムアウトしたとき。操作の完了前に MaxOperationTimeout に達すると、[OperationTimedOut](/dotnet/api/system.fabric.fabricerrorcode) が返されます。 |
| [System.UnauthorizedAccessException](/dotnet/core/api/system.unauthorizedaccessexception) |操作のアクセス チェックに失敗したとき。 E_ACCESSDENIED が返されます。 |
| [System.Fabric.FabricException](/dotnet/api/system.fabric.fabricexception) |操作の実行中にランタイム エラーが発生したとき。 FabricClient のいずれのメソッドも、[FabricException](/dotnet/api/system.fabric.fabricexception) をスローする可能性があります。例外の正確な原因は [ErrorCode](/dotnet/api/system.fabric.fabricexception.errorcode) プロパティによって示されます。 エラー コードは、[FabricErrorCode](/dotnet/api/system.fabric.fabricerrorcode) 列挙体として定義されています。 |
| [System.Fabric.FabricTransientException](/dotnet/api/system.fabric.fabrictransientexception) |なんらかの一過性のエラー状態が原因で操作に失敗したとき。 たとえばレプリカのクォーラムが一時的に到達不能状態に陥ったことが原因で操作に失敗する可能性があります。 一過性の例外によって失敗した操作は、再試行することができます。 |

[FabricException](/dotnet/api/system.fabric.fabricexception) で返される可能性のある一般的な [FabricErrorCode](/dotnet/api/system.fabric.fabricerrorcode) エラーを次に示します。

| エラー | 条件 |
| --- |:--- |
| CommunicationError |操作の失敗の原因は通信エラーです。操作をやり直してください。 |
| InvalidCredentialType |資格情報の種類が無効です。 |
| InvalidX509FindType |X509FindType が無効です。 |
| InvalidX509StoreLocation |X509 ストアの場所が無効です。 |
| InvalidX509StoreName |X509 ストアの名前が無効です。 |
| InvalidX509Thumbprint |X509 証明書の拇印の文字列が無効です。 |
| InvalidProtectionLevel |保護レベルが無効です。 |
| InvalidX509Store |X509 証明書ストアを開くことができません。 |
| InvalidSubjectName |サブジェクト名が無効です。 |
| InvalidAllowedCommonNameList |共通名リスト文字列の形式が無効です。 コンマ区切りで指定する必要があります。 |
