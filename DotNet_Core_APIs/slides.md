layout: true
<header>
  <p class="left">Effingham Software Developer Group</p>
  <p class="right">@rebelwebdev</p>
</header>

---

class: title, middle, center
# Dot Net Core Rest APIs
## Ryan Condron

---
# Why Dot Net Core?

- Async Programming
- Cross Platform (easily develop and deploy to multiple platforms)
- Docker Support
- Shared Code

---
# Why not Ruby or Javascript?
- Threading & more control
- Shared Code among mobile (Xamarin), Desktop, and API
- C# is a mainstream language.

---
class: center, middle
# What is an API

An API is an resource for other applications to use to interact wirh your
application. Today's example is to make data calls to database using REST and
Entity Framework Core

---
# HTTP Response Codes
- 200 - Ok() -  Response returned Ok()
- 400 - BadRequest() - Inproper format, etc.
- 401 - Unauthorized() - User does not have permission
- 422 - UnprocessableEntity() - Validation Fails

---
# HTTP Headers
- HTTP Headers are used to return the proper format i.e. JSON, XML
- Also used for Authorization
- Other options can be implemented via HEADERS.

---
# Dependency Injection
- Keeps logic looslely coupled
- Allows application to create objects and not defining them.
- Build into  DotNet  Core by default via IServiceCollection in Microsoft.Extensions.DependencyInjection;

```cs
  private readonly ICustomerRepository _repository;
  private readonly CustomerSupervisor _supervisor;

  public CustomersController(ICustomerRepository repository,
      CustomerSupervisor supervisor)
  {
      _repository = repository;
      _supervisor = supervisor;
  }
```
---
# Sample Get Request

- Data  Retrieval
- No Data Processing
- Can Have URL parameters to Retrieve specific records

```cs
[HttpGet("{id}")]
public async Task<IActionResult> Get(long id)
{
    Customer customers = await _repository.GetByIdAsync(id);
    return Ok(customers);
}
```
---
# Sample Post Request
- Process or save data
- Typically No URL Parameters

```cs
[HttpPost]
public async Task<IActionResult> Post([FromBody] CustomerViewModel vm)
{
    if (ModelState.IsValid)
    {
        Customer customer = await _supervisor.AddAsync(vm);
        return Ok(customer);
    }

    return UnprocessableEntity();
}
```

---
# Sample Patch Request
- Updating Database
- Has URL Parameter

```cs
[HttpPatch("{id}")]
public async Task<IActionResult> Patch(long id,
    [FromBody] CustomerViewModel vm)
{
    if(ModelState.IsValid)
    {
        Customer customer = await _supervisor.UpdateAsync(vm);
        return Ok(vm);
    }

    return UnprocessableEntity();
}
```
---
# Sample Delete Request
- Used to remove records
- Has URL Parameter

```cs
[HttpDelete("{id}")]
public async Task<IActionResult> Delete(long id)
{
    await _supervisor.DestroyAsync(id);
    return Ok(true);
}
```
---
# Keep you domain logic separate

When developing domain logic for your application use DotNet Standard as much as
possible, this will let you get the max use out of your assemblies.

By keeping you domain logic out of the API you can build other products as well,
including other APIs, WPF Applications, Xamarin Apps, etc.
---
class: center, middle
# Demo Time
