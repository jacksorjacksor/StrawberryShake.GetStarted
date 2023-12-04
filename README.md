# GET STARTED
URL: https://chillicream.com/docs/strawberryshake/v13/get-started

Terminal:
```bash
dotnet new tool-manifest
dotnet tool install StrawberryShake.Tools
dotnet new sln -n Demo
dotnet new blazorwasm -n Demo
dotnet sln add ./Demo
dotnet add Demo package StrawberryShake.Blazor
dotnet graphql init https://demo.chillicream.com/graphql/ -n CryptoClient -p ./Demo
```

Update .graphqlrc.json [add namespace]:
```json
{
  "schema": "schema.graphql",
  "documents": "**/*.graphql",
  "extensions": {
    "strawberryShake": {
      "name": "CryptoClient",
      "namespace": "Demo.GraphQL",
      "url": "https://demo.chillicream.com/graphql/",
      "records": {
        "inputs": false,
        "entities": false
      },
      "transportProfiles": [
        {
          "default": "Http",
          "subscription": "WebSocket"
        }
      ]
    }
  }
}
```

Create query doc `GetSessions.graphql`
```json
query GetAssets {
  assets {
    nodes {
      name
      price {
        lastPrice
      }
    }
  }
}
```

Build:
`dotnet build`

Add to `Program.cs`:
```csharp
builder.Services
    .AddCryptoClient()
    .ConfigureHttpClient(client => client.BaseAddress = new Uri("https://demo.chillicream.com/graphql"));
```

Add to `_Imports.razor`:
```csharp
@using Demo.GraphQL
@using Demo.GraphQL.Components
@using StrawberryShake
```

Update `index.html`:

```csharp
@page "/"

<UseGetAssets Context="result">
    <ChildContent>
        <ul>
        @foreach (var item in result.Assets!.Nodes!)
        {
            <li>@item.Name (@item.Price.LastPrice)</li>
        }
        </ul>
    </ChildContent>
    <ErrorContent>
        Something went wrong ...<br />
        @result.First().Message
    </ErrorContent>
    <LoadingContent>
        Loading ...
    </LoadingContent>
</UseGetAssets>
```

Run with 
`dotnet watch --project ./Demo`