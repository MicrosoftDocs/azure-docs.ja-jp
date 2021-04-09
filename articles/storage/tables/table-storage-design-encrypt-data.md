---
title: Azure ストレージ テーブルのデータを暗号化する | Microsoft Docs
description: Azure Storage のテーブル データの暗号化について説明します。 .NET Azure Storage クライアント ライブラリを使用すると、挿入操作や置換操作で文字列エンティティを暗号化できます。
services: storage
author: tamram
ms.author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/11/2018
ms.subservice: tables
ms.openlocfilehash: b921be718bfeb5eb95d4a802fb4d2a8cdd0946c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88236779"
---
# <a name="encrypt-table-data"></a>テーブル データを暗号化する
.NET Azure Storage クライアント ライブラリでは、挿入および置換操作の文字列エンティティ プロパティの暗号化がサポートされます。 暗号化された文字列はバイナリ プロパティとしてサービスで保存され、復号化された後、解読された後、文字列に再度変換されます。    

テーブルの場合、暗号化ポリシーに加え、ユーザーは暗号化するプロパティを指定する必要があります。 これを実行するには、[EncryptProperty] 属性を指定するか (TableEntity から派生した POCO エンティティ用)、または要求オプションで暗号化リゾルバーを指定します。 暗号化リゾルバーは、パーティション キー、行キー、プロパティ名を取得するデリゲートで、プロパティを暗号化するかどうかを示すブール値を返します。 暗号化時、クライアント ライブラリはこの情報を使用して、ネットワークへの書き込み時にプロパティを暗号化するかどうかを決定します。 また、デリゲートは、プロパティの暗号化方法に関するロジックを使用する可能性にも備えます。 (X の場合、プロパティ A を暗号化し、それ以外の場合はプロパティ A および B を暗号化するなど。)エンティティの読み込み中、またはクエリの実行中は、この情報を指定する必要はありません。

## <a name="merge-support"></a>マージのサポート

現在、マージはサポートされていません。 別のキーを使用してプロパティのサブセットが以前に暗号化されている場合、新しいプロパティをマージしたり、メタデータを更新したりするとデータ損失が発生します。 マージでは、追加のサービス呼び出しをして既存のエンティティをサービスから読み込むか、プロパティごとに新しいキーを使用する必要があります。いずれの方法も、パフォーマンス上の理由でお勧めできません。     

テーブル データの暗号化については、「 [Microsoft Azure Storage のクライアント側の暗号化と Azure Key Vault](../common/storage-client-side-encryption.md)」を参照してください。  

## <a name="next-steps"></a>次のステップ

- [テーブルの設計パターン](table-storage-design-patterns.md)
- [リレーションシップのモデル化](table-storage-design-modeling.md)
- [リレーションシップのモデル化](table-storage-design-modeling.md)
- [データの変更に対応した設計](table-storage-design-for-modification.md)
