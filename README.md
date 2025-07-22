Objetivo implementar un API REST usando ASP.NET Core Identity para manejar autenticación y autorización de usuarios.
Registrar usuarios, logear y refrescar token.


Paso 1. Crear Proyecto
dotnet new webapi -n AuthIdentityApi

Iniciar repositorio
git init
Agregar proyecto al repositorio remoto
git remote add origin https://github.com/AlejandroHCarrillo/AuthIdentity.git


Preparar el proyecto para usar Identity

Agregar los paquetes de nuguet

dotnet add package Microsoft.EntityFrameworkCore.SqlServer      (version 9.07)
dotnet add package Microsoft.EntityFrameworkCore.Tools          (Version 9.07)
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore

Para habilitar el swagger hay que agregar el paquete de nuguet
dotnet add package Swashbuckle.AspNetCore
y agregar estas lineas a la clase Program.cs:
´´´
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();
´´´

En la raiz del proyecto crear la clase ApplicationDBContext que hereda de IdentyDbContext

```csharp ```

En  la case program Agregar el servicio de Identity y configurar la cadena de conexión a la base de datos.
``` 
    builder.Services.AddDbContext<ApplicationDBContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));
```

En la console ejecutar el comando para crear la migración inicial
``` Add-Migration InitialCreate ```

Al ejecutar la migración se creará las clases que modelan la base de datos y las tablas necesarias para Identity.

Ahora vamos a crear la base de datos con el comando:
```powershell
Update-database
```

En la clase program agregamos los servicios Identity endpoints
```
builder.Services.AddIdentityApiEndpoints<IdentityUser>()
        .AddEntityFrameworkStores<ApplicationDBContext>();
```

Ahora agregamos el MapGroup 
```
app.MapGroup("/identity").MapIdentityApi<IdentityUser>();

```

Agregamos la autorización a los endpoints de la API
```
builder.Services.AddAuthorization();
```

Ahora protegemos el endpoint agrando la propiedad RequireAuthorization
```
app.MapGet("/weatherforecast", () =>
{ ... }).RequireAuthorization()
.WithName("GetWeatherForecast");
```

Ejecutamos y al tratar de acceder a la url https://localhost:7181/weatherforecast

Nos regresa el error 401 Unautorized

Entonces hay que registrar a un usuario nuevo

https://localhost:7181/register