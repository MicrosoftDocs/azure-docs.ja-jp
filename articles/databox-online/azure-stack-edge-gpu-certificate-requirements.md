---
title: Azure Stack Edge Pro での証明書の要件とトラブルシューティング | Microsoft Docs
description: Azure Stack Edge Pro デバイスでの証明書の要件と証明書エラーのトラブルシューティングについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 9184a3f429804ac383f137de49c5391e2e1db80f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439240"
---
# <a name="certificate-requirements"></a>証明書の要件

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge Pro デバイスに証明書をインストールする前に満たす必要がある証明書の要件について説明します。 要件は、PFX 証明書、発行機関、証明書のサブジェクト名とサブジェクトの別名、およびサポートされている証明書アルゴリズムに関連しています。

## <a name="certificate-issuing-authority"></a>証明書発行機関

証明書の発行要件は次のとおりです。

* 証明書は、内部の証明機関または公的証明機関のどちらかから発行されている必要があります。

* 自己署名証明書の使用はサポートされていません。

* 証明書の *Issued to:* フィールドは、ルート CA 証明書を除き、*Issued by:* フィールドと同じにしないでください。


## <a name="certificate-algorithms"></a>証明書アルゴリズム

証明書アルゴリズムには次の要件が必要です。

* 証明書では、RSA キー アルゴリズムを使用する必要があります。

* Microsoft RSA/Schannel Cryptographic Provider を使用した RSA 証明書のみがサポートされます。

* 証明書の署名アルゴリズムを SHA1 にすることはできません。

* 最小キー サイズは 4096 です。

## <a name="certificate-subject-name-and-subject-alternative-name"></a>証明書のサブジェクト名とサブジェクトの別名

証明書には、次のサブジェクト名とサブジェクトの別名の要件が必要です。

* 証明書のサブジェクトの別名 (SAN) フィールドですべての名前空間をカバーする 1 つの証明書を使用するか、 または、名前空間ごとに個別の証明書を使用できます。 どちらの方法でも、必要な場合にはエンドポイントに対してワイルドカードを使用する必要があります (バイナリ ラージ オブジェクト (BLOB) など)。

* サブジェクト名 (サブジェクト名の共通名) が、確実にサブジェクトの別名の拡張子のサブジェクトの別名の一部であるようにします。

* 証明書の SAN フィールド内のすべての名前空間をカバーする 1 つのワイルドカード証明書を使用できます。

* エンドポイント証明書を作成するときは、次の表を使用します。

    |Type |サブジェクト名 (SN)  |サブジェクトの別名 (SAN)  |サブジェクト名の例 |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |BLOB ストレージ|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |ローカル UI| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
    |両方のエンドポイントに対するマルチ SAN の単一の証明書|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |
    |Node|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
    |VPN|`AzureStackEdgeVPNCertificate.<DnsDomain>`<br><br> * AzureStackEdgeVPNCertificate はハードコーディングされています。  | `*.<DnsDomain>`<br><br>`<AzureStackVPN>.<DnsDomain>` | `edgevpncertificate.microsoftdatabox.com`|
    
## <a name="pfx-certificate"></a>PFX 証明書

Azure Stack Edge Pro デバイスにインストールされている PFX 証明書は、次の要件を満たしている必要があります。

* SSL 機関から証明書を取得する場合は、証明書の完全な署名チェーンを取得するようにしてください。

* PFX 証明書をエクスポートするときは、必ず **[Include all certificates in the chain, if possible]\(可能であれば、チェーンにすべての証明書を含める\)** オプションを選択してください。

* Azure Stack Edge Pro には公開キーと秘密キーの両方が必要であるため、エンドポイント、ローカル UI、ノード、VPN、および Wi-Fi には PFX 証明書を使用します。 秘密キーにはローカル コンピューターのキー属性が設定されている必要があります。

* 証明書の PFX 暗号化は、3 DES になっている必要があります。 これは、Windows 10 クライアントまたは Windows Server 2016 証明書ストアからエクスポートするときに使用される既定の暗号化です。 3DES に関する詳細については、[トリプル DES](https://en.wikipedia.org/wiki/Triple_DES) を参照してください。

* 証明書 PFX ファイルの *Key Usage* フィールドには、*Digital Signature* と *KeyEncipherment* の有効な値が含まれている必要があります。

* 証明書の PFX ファイルの *Enhanced Key Usage* フィールドには、*Server Authentication (1.3.6.1.5.5.7.3.1)* と *Client Authentication (1.3.6.1.5.5.7.3.2)* の値が含まれている必要があります。

* Azure Stack 適合性チェッカー ツールを使用している場合、デプロイの時点で、すべての証明書 PFX ファイルのパスワードが同じである必要があります。 詳細については、「[Microsoft Azure Stack Hub 適合性チェッカー ツールを使用して Azure Stack Edge Pro の証明書を作成する](azure-stack-edge-gpu-create-certificates-tool.md)」を参照してください。

* 証明書 PFX のパスワードは、複雑なパスワードにする必要があります。 このパスワードはデプロイ パラメーターとして使用するため、メモしておいてください。

* Microsoft RSA/Schannel Cryptographic Provider を使用した RSA 証明書のみを使用してください。

詳細については、[秘密キーを使用した PFX 証明書のエクスポート](azure-stack-edge-gpu-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)に関するセクションを参照してください。

## <a name="next-steps"></a>次のステップ

[Azure Stack Edge Pro で証明書を使用する](azure-stack-edge-gpu-manage-certificates.md)

[Microsoft Azure Stack Hub 適合性チェッカー ツールを使用して Azure Stack Edge Pro の証明書を作成する](azure-stack-edge-gpu-create-certificates-tool.md)

[秘密キーを使用して PFX 証明書をエクスポートする](azure-stack-edge-gpu-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)

[証明書エラーのトラブルシューティング](azure-stack-edge-gpu-certificate-troubleshooting.md)