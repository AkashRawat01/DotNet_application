# Use the official .NET Core SDK image to build the app
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build_and_runtime
WORKDIR /src

# Copy the project files and restore dependencies
COPY ./cloud-assignment/ ./
RUN dotnet restore

# Build the project
RUN dotnet publish -c Release -o /app/out

WORKDIR /app/out

# Using the required aspnetcore-runtime-2.2.8
COPY ./cloud-assignment/aspnetcore-runtime-2.2.8-linux-x64.tar.gz .
RUN tar -xzf aspnetcore-runtime-2.2.8-linux-x64.tar.gz -C /usr/share/dotnet

# Expose the port the app runs on
EXPOSE 80

# Define the entry point for the container
ENTRYPOINT ["dotnet", "aspnet-core-dotnet-core.dll"]