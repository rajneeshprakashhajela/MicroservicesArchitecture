System Design Blueprint: The Ultimate Guide
https://blog.devgenius.io/system-design-blueprint-the-ultimate-guide-e27b914bf8f1
![image](https://github.com/rajneeshprakashhajela/MicroservicesArchitecture/assets/43515480/ca005316-12f2-41d2-b95e-f63ea31063a1)


![image](https://github.com/rajneeshprakashhajela/MicroservicesArchitecture/assets/43515480/a41880dc-744a-4232-a940-3d30bf9041b6)

![image](https://github.com/rajneeshprakashhajela/MicroservicesArchitecture/assets/43515480/39160b01-e0bb-4e9d-b534-21f9e99a1e15)
![image](https://github.com/rajneeshprakashhajela/MicroservicesArchitecture/assets/43515480/785b167f-9e7b-4f0f-a4f0-3a98798a7322)

![image](https://github.com/rajneeshprakashhajela/MicroservicesArchitecture/assets/43515480/0c89ec31-48b9-41d0-aab3-1e7781951c76)
![image](https://github.com/rajneeshprakashhajela/MicroservicesArchitecture/assets/43515480/609c6a81-2a75-4843-8675-c78d13605e39)
![image](https://github.com/rajneeshprakashhajela/MicroservicesArchitecture/assets/43515480/c3a38801-f224-44a6-bcb1-db6f0d14b265)
![image](https://github.com/rajneeshprakashhajela/MicroservicesArchitecture/assets/43515480/c162f666-fd30-4332-b393-5b4979befddb)
![image](https://github.com/rajneeshprakashhajela/MicroservicesArchitecture/assets/43515480/c6a1308b-5287-471b-864a-786837cacacb)


using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
namespace WebApplication5.Controllers
{
    public class AccountController : Controller
    {
        [Authorize(Roles = "admin,user")]
        public IActionResult Index()
        {
            return View();
        }

        [Authorize(Roles = "admin,user")]
        public IActionResult About()
        {
            return View();
        }
        [Authorize(Roles = "user")]
        public IActionResult Contact()
        {
            return View();
        }

        public IActionResult Login()
        {
            return View();
        }
        [HttpPost]
        public IActionResult PostLogin(string username, string password)
        {
            if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            {
                return RedirectToAction("Index");
            }
            ClaimsIdentity? identity = null;
            bool isAuthenticated = false;
            if (username == "admin" && password == "sa")
            {
                identity = new ClaimsIdentity(new[]
                {
                    new Claim(ClaimTypes.Name, username),
                    new Claim(ClaimTypes.Role, "Admin")
                },
                  CookieAuthenticationDefaults.AuthenticationScheme);
                isAuthenticated = true;
            }
            if (username == "demo" && password == "sa")
            {
                identity = new ClaimsIdentity(new[]
                {
                    new Claim(ClaimTypes.Name, username),
                    new Claim(ClaimTypes.Role, "User")
                },
                  CookieAuthenticationDefaults.AuthenticationScheme);
                isAuthenticated = true;
            }
            if (isAuthenticated)
            {
                var principal = new ClaimsPrincipal(identity);
                var login = HttpContext.SignInAsync(CookieAuthenticationDefaults.AuthenticationScheme, principal);
                return RedirectToAction("Index", "Home");
            }


            return View();
        }

    }
}

==============

@model IEnumerable<WebApplication5.Models.Company>

@{
    ViewBag.Title = "Index";
    Layout = "~/Views/Shared/_Layout.cshtml";
}

<h2>Index</h2>

<p>
    @Html.ActionLink("Create New", "Create")
</p>
<table class="table">
    <tr>
        <th>
            @Html.DisplayNameFor(model => model.Id)
        </th>
        <th>
            @Html.DisplayNameFor(model => model.Address)
        </th>
        <th></th>
    </tr>

@foreach (var item in Model) {
    <tr>
        <td>
            @Html.DisplayFor(modelItem => item.Id)
        </td>
        <td>
            @Html.DisplayFor(modelItem => item.Name)
        </td>
        <td>
            @Html.ActionLink("Edit", "Edit", new { id=item.Id }) |
            @Html.ActionLink("Details", "Details", new { id=item.Name  }) |
        </td>
    </tr>
}

</table>

======

using Dapper;
using WebApplication5.Data.Migrations;
using WebApplication5.Models;

namespace WebApplication5.Repository
{
    public class CompanyRepository : ICompanyRepository
    {
        private readonly DapperContext _context;
        public CompanyRepository(DapperContext context)
        {
            _context = context;
        }
        public async Task<IEnumerable<Company>> GetCompanies1()
        {
            var query = "SELECT * FROM Companies";
            using (var connection = _context.CreateConnection())
            {
                var companies = await connection.QueryAsync<Company>(query);
                return companies.ToList();
            }
        }
        public async Task<IEnumerable<Company>> GetCompanies()
        {
            var baseAddress = new Uri("https://localhost:7276/api/Companies");
            using (var handler = new HttpClientHandler { UseCookies = false })
            using (var client = new HttpClient(handler) { BaseAddress = baseAddress })
            {
                var message = new HttpRequestMessage(HttpMethod.Get, baseAddress);
                var result = await client.SendAsync(message);
                // return result;
            }

            var query = "SELECT * FROM Companies";
            using (var connection = _context.CreateConnection())
            {
                var companies = await connection.QueryAsync<Company>(query);
                return companies.ToList();
            }
        }
    }
}

    

