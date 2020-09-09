---
title: リライアブル コレクションのローカル バックアップの読み取りと更新
description: Azure Service Fabric のバックアップ エクスプローラーを使用して、ローカルのリライアブル コレクションのバックアップを読み取り、更新します。
author: athinanthny
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: atsenthi
ms.openlocfilehash: 1da70c37c8a6ed93e7abe1b5d329e808c592e43a
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86034749"
---
# <a name="read-and-update-a-reliable-collections-backup-by-using-backup-explorer"></a>バックアップ エクスプローラーを使用したリライアブル コレクションのローカル バックアップの読み取りと更新

Azure Service Fabric バックアップ エクスプローラーは、Service Fabric リライアブル コレクションのデータが壊れている場合にデータを修正するのに役立ちます。 データの現在の状態は、アプリケーションに取り込まれたバグ、またはライブ クラスターで作成された間違ったエントリによって破損している可能性があります。

バックアップ エクスプローラーを使用すると、次のタスクを実行できます。
-   コレクションのメタデータを照会します。
-   読み込まれたバックアップのコレクション内の現在の状態とそのエントリを表示します。
-   最後のチェックポイント以降に実行されたトランザクションを一覧表示します。
-   コレクション内のエントリを追加、更新、または削除することによって、コレクションを更新します。
-   更新された状態を使用して、新しいバックアップを作成します。

> [!NOTE]
> Service Fabric バックアップ エクスプローラーで現在サポートされているのは、バックアップ内のリライアブル コレクションの表示と編集だけです。
>

## <a name="access-the-backup"></a>バックアップへのアクセス

Service Fabric バックアップ エクスプローラーは、バックアップ内のリライアブル コレクションを表示または更新するために、次のいずれかの方法で使用でき ます。
-   **Binary**:NuGet パッケージを使用して、リライアブル コレクションを表示および変更します。
-   **HTTP/REST**:HTTP ベースの REST サーバーを使用して、リライアブル コレクションを表示および変更します。
-   **bkpctl**:Service Fabric バックアップ エクスプローラーのコマンド ライン インターフェイス (CLI) を使用して、リライアブル コレクションのバックアップを表示および変更します。

バックアップ エクスプローラーには、上級ユーザー用の C# ライブラリが用意されています。 アプリケーションでライブラリを直接使用して、顧客が既存のステートフル サービスで状態マネージャーを操作するのと同じように、リライアブル コレクションを操作できます。 基本的なユーザーと基本的なユース ケース向けに、エクスプローラーにはバックアップを検査するための API を公開するスタンドアロン REST サーバーもあります。 Bkpctl CLI ツールは、REST API 上で動作し、Python に基づいています。 CLI ツールを使用すると、バックアップの読み取りと更新を行うことができます。また、コマンド ラインを使用して新しいバックアップを作成することもできます。

詳細については、[Service Fabric バックアップ エクスプローラー](https://github.com/microsoft/service-fabric-backup-explorer)の GitHub リポジトリを参照してください。 リポジトリには、ソースとリリースの情報とセットアップ手順が含まれています。

また、リポジトリをローカルにビルドし、バックアップに対して作業することもできます。
 
バックアップ エクスプローラーの NuGet (Microsoft.ServiceFabric.ReliableCollectionBackup.Parser) は、[nuget.org](https://www.nuget.org/) で使用できるようになる予定です。 

## <a name="next-steps"></a>次のステップ

* [Azure Service Fabric ステートフル サービスのリライアブル コレクション](service-fabric-reliable-services-reliable-collections.md)の詳細を見る。
* [Service Fabric のベスト プラクティス](service-fabric-best-practices-overview.md)を確認する。
