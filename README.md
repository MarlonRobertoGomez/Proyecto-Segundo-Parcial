# Proyecto-Segundo-Parcial

Install-Package Microsoft.EntityFrameworkCore.Sqlite

public class Producto
{
    public int Id { get; set; }
    public string Nombre { get; set; }
    public decimal Precio { get; set; }
    public int CategoriaId { get; set; }
    public Categoria Categoria { get; set; }
}
public class Categoria
{
    public int Id { get; set; }
    public string Nombre { get; set; }
    public List<Producto> Productos { get; set; }
}

public class ApplicationDbContext : DbContext
{
    public DbSet<Producto> Productos { get; set; }
    public DbSet<Categoria> Categorias { get; set; }

    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    { }
}

services.AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlite("Data Source=miBaseDeDatos.db"));

Add-Migration Inicial
Update-Database

@page
@model MiProyecto.Pages.ProductosModel

<form method="post">
    <div>
        <label>Nombre</label>
        <input type="text" asp-for="Producto.Nombre" required />
    </div>
    <div>
        <label>Precio</label>
        <input type="number" asp-for="Producto.Precio" required />
    </div>
    <button type="submit">Guardar</button>
</form>

public class ProductosModel : PageModel
{
    private readonly ApplicationDbContext _context;
    public Producto Producto { get; set; }

    public ProductosModel(ApplicationDbContext context)
    {
        _context = context;
    }

    public void OnGet()
    {
        // Obtener lista de productos
        Producto = _context.Productos.FirstOrDefault();
    }

    public IActionResult OnPost()
    {
        if (!ModelState.IsValid)
        {
            return Page();
        }

        _context.Productos.Add(Producto);
        _context.SaveChanges();

        return RedirectToPage("/Index");
    }
}

<div>
    <button onclick="location.href='@Url.Page("/Productos")'">Gestionar Productos</button>
    <button onclick="location.href='@Url.Page("/Categorias")'">Gestionar Categorías</button>
    <!-- Otros botones -->
</div>

public class Producto
{
    public int Id { get; set; }
    
    [Required]
    [StringLength(100)]
    public string Nombre { get; set; }

    [Range(0.01, 10000)]
    public decimal Precio { get; set; }
}

try
{
    _context.Productos.Add(Producto);
    _context.SaveChanges();
}
catch (Exception ex)
{
    // Maneja el error
    ModelState.AddModelError("", "Ocurrió un error al guardar el producto.");
}

public decimal CalcularDescuento(decimal precio)
{
    return precio * 0.10m; // 10% de descuento
}

var productosConDescuento = _context.Productos
                                     .Where(p => p.Precio > 100)
                                     .Select(p => new { p.Nombre, Descuento = CalcularDescuento(p.Precio) })
                                     .ToList();

