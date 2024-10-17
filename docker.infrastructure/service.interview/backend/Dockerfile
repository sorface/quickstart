FROM ubuntu as source_code

WORKDIR /source

RUN apt-get update && \
    apt-get install -y git && \
    git clone https://github.com/sorface/interview.git

FROM mcr.microsoft.com/dotnet/aspnet:7.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build

WORKDIR /src

COPY --from=source_code /source/interview/Backend/ .

RUN dotnet restore "Interview.Backend/Interview.Backend.csproj"

WORKDIR "/src/Interview.Backend"

RUN dotnet build "Interview.Backend.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "Interview.Backend.csproj" -c Release -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Interview.Backend.dll"]
