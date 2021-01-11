---
title: Azure Attestation ワークフロー
description: Azure Attestation のワークフロー。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 240b27f897d8e7a34026701cf7fdc844eb9d4086
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89237108"
---
# <a name="workflow"></a>ワークフロー

Microsoft Azure Attestation は、エンクレーブから証拠を受け取り、それを Azure セキュリティ ベースラインおよび構成可能なポリシーに照らして評価します。 検証に成功すると、エンクレーブの信頼性を裏付ける構成証明トークンが Azure Attestation によって生成されます。

Azure Attestation のワークフローに関係するアクターは次のとおりです。

- **証明書利用者**: Azure Attestation を利用してエンクレーブの信頼性を検証するコンポーネントです。 
- **クライアント**: エンクレーブから情報を収集して Azure Attestation に要求を送信するコンポーネントです。 
- **Azure Attestation**: クライアントからエンクレーブの証拠を受け取ってそれを検証し、構成証明トークンをクライアントに返すコンポーネントです。


## <a name="enclave-validation-work-flow"></a>エンクレーブ検証ワークフロー

Azure Attestation を使用した標準的な SGX エンクレーブ構成証明ワークフローの大まかなステップは次のとおりです。

1. クライアントがエンクレーブから証拠を収集します。 証拠とは、エンクレーブ環境に関する情報とエンクレーブ内で実行されているクライアント ライブラリに関する情報です。
1. クライアントは、Azure Attestation のインスタンスを参照する URI を把握しています。 クライアントが Azure AD に対して認証を行って、アクセス トークンを取得します。
1. Azure Attestation に対し、クライアントがアクセス トークンと共に証拠を送信します。 プロバイダーに送信される厳密な情報は、エンクレーブのタイプによって異なります。
1. Azure Attestation が、送信された情報を検証し、それを構成済みのポリシーに照らして評価します。 検証に成功した場合、Azure Attestation が構成証明トークンを発行してクライアントに返します。 このステップに失敗した場合、Azure Attestation からクライアントにエラーがレポートされます。 
1. クライアントが証明書利用者に構成証明トークンを送信します。 証明書利用者が Azure Attestation の公開キー メタデータ エンドポイントを呼び出して署名証明書を取得します。 その後、証明書利用者は、構成証明トークンの署名を検証してエンクレーブの信頼性を保証します。 

![エンクレーブ検証フロー](./media/validation-flow.png)


## <a name="next-steps"></a>次のステップ
- [構成証明ポリシーを作成して署名する方法](author-sign-policy.md)
- [PowerShell を使用して Azure Attestation を設定する](quickstart-powershell.md)
