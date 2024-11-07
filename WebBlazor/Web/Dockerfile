# Usamos una imagen base de ASP.NET Runtime para ejecutar la aplicación en el entorno de producción
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app                
# Establece el directorio de trabajo en /app dentro de la imagen
EXPOSE 80                  
# Expone el puerto 80 para permitir conexiones HTTP
ENV ASPNETCORE_URLS=http://*:80
# Segunda etapa: configuración del entorno de compilación con el SDK de .NET
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src                
# Establece el directorio de trabajo en /src

# Copia el archivo del proyecto (.csproj) desde el host al contenedor
COPY ["Web/Web.csproj", "Web/"]

# Restaura las dependencias y paquetes especificados en el archivo de proyecto (.csproj)
RUN dotnet restore "Web/Web.csproj"

# Copia todo el contenido de la carpeta actual al contenedor
COPY .. .

# Cambia el directorio de trabajo al de la aplicación específica
WORKDIR "/src/Web"

# Compila el proyecto en modo de lanzamiento (Release) y genera los archivos en /app/build
RUN dotnet build "Web.csproj" -c Release -o /app/build

# Tercera etapa: publica los archivos necesarios para ejecutar la aplicación
FROM build AS publish
# Publica el proyecto en modo Release y guarda los archivos en /app/publish
RUN dotnet publish "Web.csproj" -c Release -o /app/publish

# Etapa final: crea una imagen ligera con solo los archivos necesarios para la ejecución
FROM base AS final
WORKDIR /app               
# Establece el directorio de trabajo en /app
COPY --from=publish /app/publish .  
# Copia los archivos publicados desde la etapa de compilación
RUN ln -fs /usr/share/zoneinfo/America/Lima/etc/localtime
RUN dpkg-reconfigure --frontend nointeractive tzdata
# Define el punto de entrada de la aplicación, especificando el archivo .dll a ejecutar
ENTRYPOINT ["dotnet", "Web.dll"]
