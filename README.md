# .Net Core CSharp - Pagination

## Base class pagination
```csharp
public abstract class PagedResultBase
{
    public int CurrentPage { get; set; }
    public int PageCount { get; set; }
    public int PageSize { get; set; }
    public int RowCount { get; set; }
    public int FirstRowOnPage => (CurrentPage - 1) * PageSize + 1;
    public int LastRowOnPage => Math.Min(CurrentPage * PageSize, RowCount);
}
```
## Class pagination
```csharp
public class PagedResult<T> : PagedResultBase where T : class
{
    public IList<T> Results { get; set; }
    public PagedResult()
    {
        Results = new List<T>();
    }
}
```
## Extencion metod to pagination
```csharp
public static async Task<PagedResult<T>> GetPaged<T>(this IQueryable<T> query, int page, int pageSize) where T : class
{
    var result = new PagedResult<T>
    {
        CurrentPage = page,
        PageSize = pageSize,
        RowCount = query.AsNoTracking().Count()
    };
    var pageCount = (double)result.RowCount / pageSize;
    result.PageCount = (int)Math.Ceiling(pageCount);
    var skip = (page - 1) * pageSize;
    result.Results = query.AsNoTracking().Skip(skip).Take(pageSize).ToList();
    return await Task.FromResult(result);
}
```
## Usage
```csharp
var result = await dbSet.AsQueryable().GetPaged(page, pageSize);
```
	
