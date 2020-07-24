FROM mcr.microsoft.com/dotnet/core/aspnet:3.1-buster-slim AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443
FROM node:10.15-alpine AS client 
ARG skip_client_build=false 
WORKDIR /app 
COPY ClientApp . 
RUN [[ ${skip_client_build} = true ]] && echo "Skipping npm install" || npm install 
RUN [[ ${skip_client_build} = true ]] && mkdir dist || npm run-script build

FROM mcr.microsoft.com/dotnet/core/sdk:3.1-buster AS build
WORKDIR /src
COPY ["DevSecOps.csproj", "DevSecOps.csprojJrTech.Angular.Docker/"]
RUN dotnet restore "DevSecOps.csproj"
COPY . .
WORKDIR "/src/DevSecOps.csproj"
RUN dotnet build "DevSecOps.csproj" -c Release -o /app/build
FROM build AS publish
RUN dotnet publish "DevSecOps.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
COPY --from=client /app/dist /app/dist
ENTRYPOINT ["dotnet", "DevSecOps.dll"]