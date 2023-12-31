#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/runtime:6.0 AS base
WORKDIR /app

ENV RABBITMQ_WAIT_TIME=20
RUN echo "Waiting for RabbitMQ to start..." && sleep ${RABBITMQ_WAIT_TIME}

FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["Consumer/Consumer.csproj", "Consumer/"]
COPY ["Business/Business.csproj", "Business/"]
COPY ["Data/Data.csproj", "Data/"]
COPY ["Domain/Domain.csproj", "Domain/"]
RUN dotnet restore "./Consumer/./Consumer.csproj"
COPY . .
WORKDIR "/src/Consumer"
RUN dotnet build "./Consumer.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./Consumer.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Consumer.dll"]