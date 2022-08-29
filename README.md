using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace EmployeeService.Dats
{
    [Table("Departments")]
    public class Department
    {
        [Key, DatabaseGenerated(DatabaseGeneratedOption.Identity)]
        public int Id { get; set; }

        [Colum(TypeName = "nvarchar(128)")]
        public int Description { get; set; }

        [InverseProperty(nameof(Employee.Department))]
        public virtual ICollection<Employee> Employees { get; set; } = new HashSet<Employee>();
    }
}

using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace EmployeeService.Dats
{
    [Table("Employees")]
    public class Employee 
    {

        [Column]
        [StringLength(128)]
        public int Description { get; set; }

        [Key, DatabaseGenerated(DatabaseGeneratedOption.Identity)]
        public int Id { get; set; } 

        [ForeignKey(nameof(Department))]
        public int DepartmentId { get; set; }

        [ForeignKey(nameof(EmployeeType))]
        public int EmployeeTypeId { get; set; } 

        [Column]
        [StringLength(255)]
        public int FirstName { get; set; }

        [Column]
        [StringLength(255)]
        public int Surname { get; set; }

        [Column]
        [StringLength(255)]
        public int Patronymic { get; set; } 

        [Column(TypeName = "money")]
        public int Salary { get; set; } 

        public virtual EmployeeType EmployeeType { get; set; }

        public virtual Department Department { get; set; }
    } 
}

using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace EmployeeService.Dats
{
    class EmployeeServiceDbContext : DbContext
    {
        public DbSet<Employee> Employees {get; set;}

        public DbSet<Department> Departments {get; set;}

        public DbSet<EmployeeType> EmployeeTypes { get; set; }


public EmployeeServiceDbContext(DbContextOptions options) : base(options)
        {

        }
    }
}

using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace EmployeeService.Dats
{
    [Table("EmployeeTypes")]
    public class EmployeeType
    {
        [Key, DatabaseGenerated(DatabaseGeneratedOption.Identity)]
        public int Id { get; set; }

        [Column]
        [StringLength(128)]
        public int Description { get; set; }

        [InverseProperty(nameof(Employee.EmployeeType))]
        public virtual ICollection<Employee> Employees { get; set; } = new HashSet<Employee>();

    }
}

using EmployeeService.Dats;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Options;
using Timesheets.Models.Options;
using Timesheets.Models;
using Timesheets.Models.Request;
using Timesheets.Services;

namespace Timesheets.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class EmployeeTypeController : ControllerBase
    {
        private readonly ILogger<EmployeeTypeController> _logger;
        private readonly IEmployeeTypeRepository _employeeTypeRepository;
        private readonly IOptions<LoggerOptions> _loggerOptions;

        public EmployeeTypeController(ILogger<EmployeeTypeController> logger, IOptions<LoggerOptions> loggerOptions, IEmployeeTypeRepository employeeTypeRepository)
        {
            _logger = logger;
            _employeeTypeRepository = employeeTypeRepository;
            _loggerOptions = loggerOptions;
        }

        //[HttpDelete("employee-types/delete")]
        //public ActionResult DeleteEmployeeType(int id)
        //{
        //    _logger.LogInformation("EmployeeType delete");
        //    var log = _loggerOptions.Value.Path;

        //    _employeeTypeRepository.Delete(id);
        //    return Ok();
        //}

        //[HttpPost("employee-types/create")]
        //public ActionResult<int> CreateEmployeeType(string description)
        //{
        //    _logger.LogInformation("EmployeeType add");
        //    var log = _loggerOptions.Value.Path;

        //    return Ok(_employeeTypeRepository.Create(new Data.EmployeeType
        //    {
        //        Description = description,
        //    }));
        //}

        //[HttpGet("employee-types/get/all")]
        //public ActionResult<IList<EmployeeTypeDto>> GetAllEmployeeType()
        //{
        //    _logger.LogInformation("EmployeeType getall");
        //    var log = _loggerOptions.Value.Path;

        //    return Ok(_employeeTypeRepository.GetAll().Select(employeeType => new EmployeeTypeDto
        //    {
        //        Id = employeeType.Id,
        //        Description = employeeType.Description,

        //    }).ToList());
        //}

        [HttpGet("get/{id}")]
        public ActionResult<EmployeeTypeDto> GetByIdEmployeeType([FromRoute] int id)
        {
            _logger.LogInformation("EmployeeType get");
            var log = _loggerOptions.Value.Path;

            var employee = _employeeTypeRepository.GetById(id);

            return Ok(new EmployeeTypeDto
            {
                Id = employee.Id,
                Description = employee.Description,
            });
        }
    }
}

using EmployeeService.Dats;
using System;
using System.Collections;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Timesheets.Models;

namespace Timesheets.Services.Impl
{
    public class EmployeeTypeRepository : IEmployeeTypeRepository
    {
        private readonly EmployeeServiceDbContext _context;

        public EmployeeTypeRepository(EmployeeServiceDbContext context)
        {
            _context = _context;
        }


        public int Create(EmployeeType data)
        {
            _context.EmployeeTypes.Add(data);
            _context.SaveChanges();
            return data.Id;
        }

        public void Delete(int id)
        {
            EmployeeType employeeType = GetById(id);
            if (employeeType == null)
                throw new Exception("EmployeeType not found");

            _context.EmployeeTypes.Remove(GetById);
            _context.SaveChanges(true);
            return data.Id;
        }

        public IList GetAll<EmployeeType>()
        {
            _context.EmployeeTypes.ToList();
        }

        public EmployeeType GetById(int id)
        {
            return _context.EmployeeTypes.FirstOrDefault(et => et.Id == id);
        }

        public void Update(EmployeeType data)
        {

            if (data == null)
                throw new Exception("EmployeeType is null.");
            EmployeeTypeRepository employeeType = GetById(data.Id);
            employeeType.Description = data.Description;
            _context.SaveChanges(true);
        }
    }
}

{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*",
  "Settings": {
    "Logger": {
      "Path": "C:/Logs/EmployeeService"
    }
  },
  "DatabaseOptions": {
    "ConnectionString": "data source=DESKTOP-6DH400P\\SQLEXPRESS;initial catalog=EmployeeDatabase;User Id=EmployeeDatabaseUser;Password=12345;MultipleActiveResultSets=True;App=EntityFramework"
  }
}


using Timesheets.Services.Impl;
using Timesheets.Services.lmpl;
using Timesheets.Services;
using Timesheets.Models.Options;
using Microsoft.AspNetCore.HttpLogging;
using Microsoft.Extensions.Hosting;
using NLog.Web;

namespace Timesheets
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var builder = WebApplication.CreateBuilder(args);

            // Add services to the container.

            #region Configure EF DBContext Service (EmployeeDatabase Database)

            builder.Services.AddDbContext<EmployeeServiceDbContext>(options =>
            {
                options.UseSqlServer(builder.Configuration["Settings:DatabaseOptions:ConnectionString"]);
            });

            #endregion

            builder.Services.AddScoped<IEmployeeRepository, EmployeeRepository>();
            builder.Services.AddScoped<IEmployeeTypeRepository, EmployeeTypeRepository>();
            builder.Services.AddScoped<IDepartmentRepository, DepartmentRepository>();

            #region Configure Options

            builder.Services.Configure<LoggerOptions>(options =>
              builder.Configuration.GetSection("Settings:Logger").Bind(options)
            );

            builder.Services.AddHttpLogging(logging =>
            {
                logging.LoggingFields = HttpLoggingFields.All | HttpLoggingFields.RequestQuery;
                logging.RequestBodyLogLimit = 4096;
                logging.ResponseBodyLogLimit = 4096;
                logging.RequestHeaders.Add("Authorization");
                logging.RequestHeaders.Add("X-Real-IP");
                logging.RequestHeaders.Add("X-Forwarded-For");
            });

            builder.Host.ConfigureLogging(logging =>
            {
                logging.ClearProviders();
                logging.AddConsole();

            }).UseNLog(new NLogAspNetCoreOptions() { RemoveLoggerFactoryFilter = true });
            #endregion

            builder.Services.AddControllers();
            // Learn more about configuring Swagger/OpenAPI at https://aka.ms/aspnetcore/swashbuckle
            builder.Services.AddEndpointsApiExplorer();
            builder.Services.AddSwaggerGen();

            var app = builder.Build();

            // Configure the HTTP request pipeline.
            if (app.Environment.IsDevelopment())
            {
                app.UseSwagger();
                app.UseSwaggerUI();
            }

           
            app.UseAuthorization();

            app.UseHttpLogging();

            app.MapControllers();

            app.Run();
        }
    }
}
