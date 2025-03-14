# dotnet new

## New Soltution
`dotnet new sln`

### Add project to solution
`dotnet sln todo.slnx add **/*.csproj` (linux/unix)

`dotnet sln todo.slnx add (ls -r **/*.csproj)` (powershell)

dotnet sln YabaAuthorizationServer.sln add (ls -r **/*.csproj)

## New Project
`dotnet new [template] -n [project_name] -f [framewok.version] -o [folder_name]`

dotnet new webapi -n Yaba.AuthorizationServer.Api -f net8.0 -o src/Yaba.AuthorizationServer.Api
dotnet new auth0webapi -n Auth0.Api -f net8.0 

## Run Application

`dotnet run --project [path]`

dotnet run --project src/Yaba.AuthorizationServer.Api/Yaba.AuthorizationServer.Api.csproj
dotnet run --project Auth0.Api/Auth0.Api.csproj

# Dica

## Setar variável de ambiente permanente? no powershell

`setx ASPNETCORE_ENVIRONMENT "staging"`

# Dotnet

# Ef Core

## Add Migration

`dotnet ef migrations add`


## Remove Migration


## Update database

`dotnet ef database update`

`dotnet ef database update -- --environment Development`


## Script Migration From and To

`dotnet ef migrations script <From> <To>`



# Net Framework and EF 6

## Entity Framework 6

