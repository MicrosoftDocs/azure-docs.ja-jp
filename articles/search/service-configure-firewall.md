---
title: Azure Cognitive Search サービス用に IP ファイアウォールを構成する
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search サービスへのアクセスを制限するための IP 制御ポリシーを構成します。
manager: nitinme
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: de34c2921c7829cb6d7e7354a1ebcff44271efd3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545549"
---
# <a name="configure-ip-firewall-for-azure-cognitive-search"></a>Azure Cognitive Search 用に IP ファイアウォールを構成する

Azure Cognitive Search では、受信ファイアウォールをサポートするための IP 規則がサポートされています。 このモデルには、Azure 仮想ネットワーク セキュリティ グループにある IP 規則と同様に、検索サービス用の追加のセキュリティ層が用意されています。 これらの IP 規則を使用して、承認された一連のマシンやクラウド サービスからのみアクセスできるように検索サービスを構成することができます。 ただし、検索サービスに格納されているデータに、これらの承認された一連のマシンやサービスからアクセスするには、呼び出し側が有効な承認トークンを提示する必要がある点は変わりません。

IP 規則は、この記事で説明されているように Azure portal で設定できます。 また、[Management REST API バージョン 2020-03-13](/rest/api/searchmanagement/)、[Azure PowerShell](/powershell/module/az.search)、または [Azure CLI](/cli/azure/search) を使用することもできます。

## <a name="configure-an-ip-firewall-using-the-azure-portal"></a><a id="configure-ip-policy"></a> Azure portal を使用して IP ファイアウォールを構成する

Azure portal で IP アクセス制御ポリシーを設定するには、Azure Cognitive Search サービスのページに移動し、ナビゲーション メニューの **[ネットワーク]** を選択します。 エンドポイント ネットワーク接続は **[パブリック]** である必要があります。 接続が **[プライベート]** に設定されている場合は、プライベート エンドポイント経由でのみ検索サービスにアクセスできます。

![Azure portal で IP ファイアウォールを構成する方法を示すスクリーンショット](./media/service-configure-firewall/azure-portal-firewall.png)

Azure portal では、CIDR 形式で IP アドレスと IP アドレス範囲を指定できます。 CIDR 表記の例として、8.8.8.0/24 があります。これは、8.8.8.0 から 8.8.8.255 の範囲の IP を表しています。

> [!NOTE]
> Azure Cognitive Search サービスに対して IP アクセス制御ポリシーを有効にした後は、許可された IP アドレス範囲リストに含まれていないマシンからのデータ プレーンへの要求はすべて拒否されます。 IP 規則が構成されている場合は、Azure portal の一部の機能が無効になります。 サービスレベル情報を表示して管理することはできますが、インデックスデータやサービス内のさまざまなコンポーネント (インデックス、インデクサー、スキルセットの定義など) へのポータル アクセスは、セキュリティ上の理由で制限されています。 ポータルの代わりに、[VS Code 拡張機能](https://aka.ms/vscode-search)を使用して、サービス内のさまざまなコンポーネントを操作することもできます。

### <a name="requests-from-your-current-ip"></a>現在ご利用の IP からの要求

開発が簡単になるように、Azure portal ではクライアント マシンの IP を識別して許可リストに追加することができます。 それにより、マシンで実行されているアプリは、Azure Cognitive Search サービスにアクセスすることができます。

ポータルでは、クライアントの IP アドレスが自動的に検出されます。 それは、ご利用のマシンまたはネットワーク ゲートウェイのクライアント IP アドレスの場合があります。 運用環境にご利用のワークロードを移行する前に、この IP アドレスを必ず削除してください。

現在の IP を IP リストに追加するには、 **[クライアント IP アドレスを追加する]** をオンにしてください。 次に、 **[保存]** を選択します。

![現在の IP を許可するようにファイアウォール設定を構成する方法を示すスクリーンショット](./media/service-configure-firewall/enable-current-ip.png)

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>IP アクセス制御ポリシーに関する問題のトラブルシューティング

次のオプションを使用して IP アクセス制御ポリシーに関する問題のトラブルシューティングを行うことができます。

### <a name="azure-portal"></a>Azure portal

Azure Cognitive Search サービスに対して IP アクセス制御ポリシーを有効にすると、Azure portal を含め、許可された IP アドレス範囲リストに含まれていないマシンからの要求はすべてブロックされます。  サービスレベル情報を表示して管理することはできますが、インデックスデータやサービス内のさまざまなコンポーネント (インデックス、インデクサー、スキルセットの定義など) へのポータル アクセスは、セキュリティ上の理由で制限されています。 

### <a name="sdks"></a>SDK

許可リストに含まれていないマシンから SDK を使用して Azure Cognitive Search サービスにアクセスすると、**403 Forbidden** という一般的な応答が返され、詳しい情報は示されません。 ご利用のアカウントの許可 IP リストを確認し、検索サービス用に正しい構成が更新されていることを確認してください。

## <a name="next-steps"></a>次のステップ

Private Link を使用した検索サービスへのアクセスの詳細については、次の記事を参照してください。

* [Azure Cognitive Search に安全な接続を行うためのプライベート エンドポイントを作成する](service-create-private-endpoint.md)