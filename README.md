
# Desafio dio-ASP.NET Minimals APIs    



Para criar um projeto completo utilizando **ASP.NET Minimal API** em **C#**, siga as etapas abaixo. Vou descrever o processo de criação de um projeto simples que inclui operações básicas de um CRUD (Create, Read, Update, Delete).



### Estrutura do Projeto

1. **Criação do Projeto**
2. **Configuração do `Program.cs`**
3. **Definição do Modelo**
4. **Criação do Repositório**
5. **Configuração das Rotas**
6. **Execução do Projeto**



### 1. Criação do Projeto

Abra o terminal ou o prompt de comando e execute o seguinte comando para criar um novo projeto:

bash



```bash
dotnet new web -n MinimalApiExample
```

### 2. Configuração do `Program.cs`

Abra o arquivo `Program.cs` e configure o projeto:

csharp



```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

var builder = WebApplication.CreateBuilder(args);

// Adiciona serviços ao contêiner.
builder.Services.AddSingleton<IRepository<TodoItem>, TodoRepository>();

var app = builder.Build();

// Configura as rotas
app.MapGet("/todos", async (IRepository<TodoItem> repository) =>
{
    return await repository.GetAllAsync();
});

app.MapGet("/todos/{id}", async (int id, IRepository<TodoItem> repository) =>
{
    var todo = await repository.GetByIdAsync(id);
    return todo is not null ? Results.Ok(todo) : Results.NotFound();
});

app.MapPost("/todos", async (TodoItem todo, IRepository<TodoItem> repository) =>
{
    await repository.AddAsync(todo);
    return Results.Created($"/todos/{todo.Id}", todo);
});

app.MapPut("/todos/{id}", async (int id, TodoItem todo, IRepository<TodoItem> repository) =>
{
    if (id != todo.Id) return Results.BadRequest();

    await repository.UpdateAsync(todo);
    return Results.NoContent();
});

app.MapDelete("/todos/{id}", async (int id, IRepository<TodoItem> repository) =>
{
    await repository.DeleteAsync(id);
    return Results.NoContent();
});

app.Run();
```

### 3. Definição do Modelo

Crie uma nova classe chamada `TodoItem.cs`:

csharp



```csharp
public class TodoItem
{
    public int Id { get; set; }
    public string Title { get; set; }
    public bool IsCompleted { get; set; }
}
```

### 4. Criação do Repositório

Crie uma interface chamada `IRepository.cs`:

csharp



```csharp
using System.Collections.Generic;
using System.Threading.Tasks;

public interface IRepository<T>
{
    Task<IEnumerable<T>> GetAllAsync();
    Task<T> GetByIdAsync(int id);
    Task AddAsync(T item);
    Task UpdateAsync(T item);
    Task DeleteAsync(int id);
}
```

Crie uma classe chamada `TodoRepository.cs`:

csharp



```csharp
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

public class TodoRepository : IRepository<TodoItem>
{
    private readonly List<TodoItem> _todos = new();

    public Task<IEnumerable<TodoItem>> GetAllAsync() => Task.FromResult(_todos.AsEnumerable());

    public Task<TodoItem> GetByIdAsync(int id) => Task.FromResult(_todos.FirstOrDefault(t => t.Id == id));

    public Task AddAsync(TodoItem item)
    {
        item.Id = _todos.Count + 1; // Simples lógica para atribuir ID
        _todos.Add(item);
        return Task.CompletedTask;
    }

    public Task UpdateAsync(TodoItem item)
    {
        var index = _todos.FindIndex(t => t.Id == item.Id);
        if (index != -1)
        {
            _todos[index] = item;
        }
        return Task.CompletedTask;
    }

    public Task DeleteAsync(int id)
    {
        var item = _todos.FirstOrDefault(t => t.Id == id);
        if (item != null)
        {
            _todos.Remove(item);
        }
        return Task.CompletedTask;
    }
}
```

### 5. Configuração das Rotas

As rotas já foram configuradas no `Program.cs`. As rotas implementadas permitem realizar operações de CRUD.

### 6. Execução do Projeto

Para executar o projeto, use o seguinte comando no terminal:

bash

```bash
dotnet run --project MinimalApiExample
```

 Acessar a API em `http://localhost:5000/todos` e testar as operações de CRUD usando ferramentas como **Postman** ou **curl**.

### Conclusão

Você agora tem um projeto básico de **ASP.NET Minimal API** funcional que pode ser expandido conforme necessário. Sinta-se à vontade para adicionar mais funcionalidades, como autenticação, persistência em banco de dados, etc. 
