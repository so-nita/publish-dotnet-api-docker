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

### 7. Upload image to Docker Hub
1. Create Repo in Docker Hub
   [sample image](https://private-user-images.githubusercontent.com/32194879/290172715-d0faa9c1-815f-4ba0-a90c-238c88a80a87.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NDAzNzM0MDcsIm5iZiI6MTc0MDM3MzEwNywicGF0aCI6Ii8zMjE5NDg3OS8yOTAxNzI3MTUtZDBmYWE5YzEtODE1Zi00YmEwLWE5MGMtMjM4Yzg4YTgwYTg3LnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNTAyMjQlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjUwMjI0VDA0NTgyN1omWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTIxOGNlYmQzMWNiNjBmNmUxZTllOGM1NzU2YzIwYWYyZDVjODVkNGI5YTZhMTQ4ZGNhNzA4NDQ1NzAwNzM2YjcmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0In0.SjYxLsG34T7j8TnqPMWlj04f-Frr-CJnMsGBS6YYIdU)
   
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
