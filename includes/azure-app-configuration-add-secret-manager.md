---
ms.openlocfilehash: b1be5e903994f0a2c4a3d457f0c2bb5572a889c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98663031"
---
## <a name="add-secret-manager"></a>シークレット マネージャーを追加する

シークレット マネージャーというツールは、開発作業の機密データをプロジェクト ツリーの外部に格納します。 これにより、ソース コード内のアプリ シークレットが偶発的に共有されるのを防止できます。 ASP.NET Core プロジェクトでシークレット マネージャーを使用できるようにするには、次の手順を実行します。

#### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

プロジェクトのルート ディレクトリに移動し、次のコマンドを実行して、プロジェクトのシークレット ストレージを有効にします。

```dotnetcli
dotnet user-secrets init
```

GUID を含んだ `UserSecretsId` 要素が *.csproj* ファイルに追加されます。

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>net5.0</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

プロジェクトのルート ディレクトリに移動し、次のコマンドを実行して、プロジェクトのシークレット ストレージを有効にします。

```dotnetcli
dotnet user-secrets init
```

GUID を含んだ `UserSecretsId` 要素が *.csproj* ファイルに追加されます。

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

1. *.csproj* ファイルを開きます。

1. 次のように `UserSecretsId` 要素を *.csproj* ファイルに追加します。 同じ GUID を使用することも、この値を独自の値に置き換えることもできます。

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    
        <PropertyGroup>
            <TargetFramework>netcoreapp2.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    
        <ItemGroup>
            <PackageReference Include="Microsoft.AspNetCore.App" />
            <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
        </ItemGroup>
    
    </Project>
    ```
    
1. *.csproj* ファイルを保存します。

---

> [!TIP]
> シークレット マネージャーの詳細については、「[ASP.NET Core での開発におけるアプリ シークレットの安全な保存](/aspnet/core/security/app-secrets)」を参照してください。
