# publish-dotnet-api-docker
# API Project

![Project Logo](https://via.placeholder.com/150)

## Overview
This project is a .NET 8 API that runs in a Docker container.

## Prerequisites
- ![Docker](https://github.com/user-attachments/assets/12e8c3ae-2d72-4b39-95a9-62e51a2d16ff)
- ![.NET 8 SDK](https://github.com/user-attachments/assets/0ff10159-bffc-4124-b48b-799573ee6e82)

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

### 7. Upload image to Docker Hub
1. Create Repo in Docker Hub
   ![sample image](https://github-production-user-asset-6210df.s3.amazonaws.com/130118661/416116255-f5e3c214-48e0-42f8-b96f-5477c7b9c9ce.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAVCODYLSA53PQK4ZA%2F20250224%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20250224T084853Z&X-Amz-Expires=300&X-Amz-Signature=efd9c6b5f7594b45cdcef4a15f065e626b39aee8f4339d966b949a971f1428e8&X-Amz-SignedHeaders=host)
   
2. We can Push images to this repo with the following commands:
```sh
docker tag api sonita2/my-second-repo:latest
```
then 
```sh
docker push sonita2/my-second-repo:latest
```

3. Pull the image on another machine (optional)
```sh
docker pull sonita2/my-second-repo:latest
```
   Then, you can run the container with:
```sh
docker run -d -p 8081:80 --name api-container sonita2/my-second-repo:latest
```


## License
This project is licensed under [MIT License](LICENSE).
