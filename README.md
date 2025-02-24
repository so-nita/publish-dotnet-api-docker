# publish-dotnet-api-docker
# API Project

![Project Logo](https://via.placeholder.com/150)

## Overview
This project is a .NET 8 API that runs in a Docker container.

## Prerequisites
- [Docker](https://www.docker.com/get-started)
- [.NET 8 SDK](https://dotnet.microsoft.com/en-us/download/dotnet/8.0)

## Docker Setup

### 1. Clone the Repository
```sh
git clone https://github.com/your-repo/api-project.git
cd api-project
```

### 2. Dockerfile
Ensure your project contains the following `Dockerfile`:

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
USER app
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["Api.csproj", "."]
RUN dotnet restore "./././Api.csproj"
COPY . .
WORKDIR "/src/."
RUN dotnet build "./Api.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./Api.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Api.dll"]
```

### 3. Build Docker Image
Run the following command to build the Docker image:
```sh
docker build -t api .
```

### 4. Run the Docker Container
```sh
docker run -d -p 8081:80 --name api-container api
```
- `8081`: Host port
- `80`: Container port
- `api-container`: Container name

### 5. Verify the Running Container
```sh
docker ps
```

### 6. Access the API
Open your browser and go to:
```
http://localhost:8081
```

## Replace Placeholder Image
To update the project logo, replace `https://via.placeholder.com/150` with your actual image URL.

## License
This project is licensed under [MIT License](LICENSE).
