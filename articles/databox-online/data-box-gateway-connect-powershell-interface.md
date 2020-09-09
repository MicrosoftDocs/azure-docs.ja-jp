---
title: Windows PowerShell を使用して Azure Data Box Gateway デバイスへの接続と管理を行う
description: Data Box Gateway に Windows PowerShell インターフェイス経由で接続し、管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: c071d372ba90d29806fd8a44909e2c803a8d3fa4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84339282"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Azure Data Box Gateway デバイスを Windows PowerShell 経由で管理する

Azure Data Box Gateway ソリューションでは、ネットワーク経由で Azure にデータを送信できます。 この記事では、Data Box Gateway デバイスについての構成と管理のタスクをいくつか説明します。 Azure portal、ローカル Web UI、または Windows PowerShell インターフェイスを使用してデバイスを管理できます。

この記事では、PowerShell インターフェイスを使用して行うタスクに重点を置いて説明します。

この記事には、次の手順が含まれています。

- PowerShell インターフェイスに接続する
- サポート パッケージを作成する
- 証明書のアップロード
- 非 DHCP 環境で起動する
- デバイス情報を表示する

## <a name="connect-to-the-powershell-interface"></a>PowerShell インターフェイスに接続する

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>サポート パッケージを作成する

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>証明書のアップロード

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>非 DHCP 環境で起動する

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>デバイス情報を表示する

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>次のステップ

- Azure portal で [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) を配置する。
