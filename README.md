![image](https://github.com/rajneeshprakashhajela/MicroservicesArchitecture/assets/43515480/0c89ec31-48b9-41d0-aab3-1e7781951c76)


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

