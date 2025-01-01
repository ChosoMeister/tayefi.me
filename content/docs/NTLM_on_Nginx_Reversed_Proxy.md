+++
date = '2025-01-01T13:53:10+03:30'
draft = false
title = 'احراز هویت NTLM با استفاده از Nginx Reversed Proxy'
+++

<style>
  body {
    direction: rtl;
    text-align: right;
  }
</style>

<div dir="rtl">

## مقدمه

احراز هویت NTLM یک پروتکل احراز هویت چالش-پاسخ است که توسط مایکروسافت توسعه یافته و معمولاً در شبکه‌های ویندوزی استفاده می‌شود. این پروتکل از روش‌های مختلفی برای احراز هویت، از جمله هش رمز عبور، استفاده می‌کند و به کاربران اجازه می‌دهد تا به منابع شبکه دسترسی پیدا کنند \[1\]. **Nginx Plus** نسخه تجاری Nginx است \[2\] که یک وب سرور قدرتمند و پرکاربرد است و می‌تواند به عنوان یک پروکسی معکوس برای برنامه‌های وب استفاده شود. با استفاده از **Nginx Reversed Proxy**، می‌توانید ترافیک ورودی را به سرورهای backend هدایت کنید و از مزایای مختلفی مانند افزایش امنیت، بهبود عملکرد و مدیریت ترافیک بهره‌مند شوید.

> **نکته**: NTLM یک پروتکل قدیمی است و ممکن است در برابر حملات آسیب‌پذیر باشد \[1\]. بنابراین، در صورت امکان، بهتر است از پروتکل‌های احراز هویت جدیدتر و امن‌تر مانند **Kerberos** استفاده کنید.

---

## روش‌های پیاده‌سازی احراز هویت NTLM با Nginx Reversed Proxy

### 1. استفاده از Nginx Plus

**NGINX Plus** نسخه تجاری Nginx است که از ماژول `ntlm` پشتیبانی می‌کند \[2\]. این ماژول به شما امکان می‌دهد تا به راحتی احراز هویت NTLM را در Nginx Reversed Proxy پیکربندی کنید. برای استفاده از این ماژول، کافی است دستور `ntlm` را در بلوک `upstream` پیکربندی Nginx اضافه کنید. به عنوان مثال:

```nginx
upstream http_backend {
  server 127.0.0.1:8080;
  ntlm;
}
```

> توجه داشته باشید که Nginx Plus یک نسخه تجاری است و برای استفاده از آن باید هزینه پرداخت کنید \[2\].

---

### 2. استفاده از ماژول‌های شخص ثالث

اگر از Nginx Plus استفاده نمی‌کنید، می‌توانید از ماژول‌های شخص ثالث برای پیاده‌سازی احراز هویت NTLM استفاده کنید. یکی از این ماژول‌ها، `nginx-ntlm-module` است که توسط Gabriel Hodoroaga توسعه یافته است \[2\]. این ماژول عملکرد مشابهی با ماژول `ntlm` در Nginx Plus ارائه می‌دهد.

برای استفاده از این ماژول، باید آن را از سورس کد کامپایل کنید و به Nginx اضافه کنید \[3\]. پس از نصب، می‌توانید دستور `ntlm` را در بلوک `upstream` پیکربندی Nginx اضافه کنید.

> توجه داشته باشید که استفاده از ماژول‌های شخص ثالث ممکن است با خطراتی همراه باشد و باید با دقت انجام شود \[3\].

---

### 3. استفاده از Lua

یکی دیگر از روش‌های پیاده‌سازی احراز هویت NTLM با Nginx Reversed Proxy، استفاده از **Lua** است. Lua یک زبان برنامه‌نویسی سبک و قدرتمند است \[4\] که می‌تواند برای پیاده‌سازی قابلیت‌های مختلف در Nginx استفاده شود \[4\]. برای پیاده‌سازی احراز هویت NTLM با Lua، می‌توانید از کتابخانه `lua-resty-ntlm` استفاده کنید \[5\]. این کتابخانه به شما امکان می‌دهد تا به راحتی احراز هویت NTLM را در Nginx Reversed Proxy پیاده‌سازی کنید.

#### مراحل کلی:

1. **نصب OpenResty**  
   برای استفاده از lua-resty-ntlm، ابتدا باید OpenResty را نصب کنید. OpenResty یک توزیع Nginx است که شامل LuaJIT و ماژول‌های Lua مختلف است. برای نصب OpenResty در ویندوز، می‌توانید مراحل زیر را دنبال کنید \[7\]:

   1. به وب‌سایت OpenResty بروید و به بخش "Download" بروید.
   2. نسخه ویندوز را که با معماری سیستم شما (32 بیتی یا 64 بیتی) مطابقت دارد، انتخاب کنید.
   3. بسته نصب (`.exe`) را برای OpenResty دانلود کنید.
   4. بسته نصب را اجرا کنید و مراحل نصب را دنبال کنید.
   5. مطمئن شوید که کامپوننت "**Luarocks**" را برای پشتیبانی از مدیریت بسته Lua انتخاب کرده‌اید.

2. **نصب LuaRocks**  
   LuaRocks یک مدیر بسته برای Lua است که به شما امکان می‌دهد تا به راحتی کتابخانه‌های Lua را نصب کنید. برای نصب LuaRocks، می‌توانید از دستور زیر استفاده کنید \[6\]:

   ```bash
   luarocks install lua-resty-ntlm
   ```

3. **پیکربندی lua-resty-ntlm**  
   برای پیکربندی lua-resty-ntlm، می‌توانید از کد Lua زیر در بلوک `access_by_lua_block` پیکربندی Nginx استفاده کنید \[8\]:

   ```lua
   access_by_lua_block {
     local cache = ngx.shared.ntlm_cache
     require('ntlm').negotiate("ldap://domain.net:389", cache, 10)
   }
   ```

---

### 4. پیکربندی IIS با ARR

یکی دیگر از روش‌های پیاده‌سازی احراز هویت NTLM با استفاده از پروکسی معکوس، پیکربندی **IIS با ARR (Application Request Routing)** است \[9\]. در این روش، IIS به عنوان پروکسی معکوس عمل می‌کند و احراز هویت NTLM را انجام می‌دهد. برای پیکربندی IIS با ARR، باید Windows Authentication را به صورت global فعال کنید و یک authorization rule برای اجازه دادن به کاربرانی که می‌خواهید از طریق پروکسی معکوس احراز هویت شوند، پیکربندی کنید.

---

### 5. استفاده از Apache با mod_auth_sspi

**Apache** یک وب سرور دیگر است که می‌تواند به عنوان پروکسی معکوس استفاده شود. برای پیاده‌سازی احراز هویت NTLM در Apache، می‌توانید از ماژول `mod_auth_sspi` استفاده کنید \[9\]. این ماژول به شما امکان می‌دهد تا احراز هویت NTLM را در Apache پیکربندی کنید.

---

### 6. استفاده از YARP با HttpClient سفارشی

**YARP (Yet Another Reverse Proxy)** یک پروکسی معکوس دیگر است که می‌توانید برای پیاده‌سازی احراز هویت NTLM استفاده کنید \[10\]. برای انجام این کار، می‌توانید YARP را با یک HttpClient سفارشی پیکربندی کنید که اعتبارنامه‌های NTLM را به سرور backend ارسال می‌کند.

---

## محدودیت‌ها

استفاده از lua-resty-ntlm با محدودیت‌هایی همراه است. به عنوان مثال، این کد هدرها و بدنه HTTP را تغییر نمی‌دهد، بنابراین اگر location شما در OpenResty سرور NTLM-aware را تقلید نکند، این روش کار نخواهد کرد \[11\]. همچنین، این کد از timeout برای عملیات خواندن سوکت استفاده می‌کند، بنابراین هر درخواستی نمی‌تواند در زمان کمتری از این timeout انجام شود \[11\].

---

## عیب‌یابی مشکلات احراز هویت NTLM با Nginx Reversed Proxy

در صورت بروز مشکل در احراز هویت NTLM با Nginx Reversed Proxy، می‌توانید از روش‌های زیر برای عیب‌یابی استفاده کنید:

1. **فعال کردن لاگ‌ها**:  
   با فعال کردن لاگ‌های Nginx، می‌توانید اطلاعات بیشتری در مورد خطاهای رخ داده در حین احراز هویت NTLM به دست آورید \[1\].

2. **استفاده از ابزارهای شبکه**:  
   با استفاده از ابزارهایی مانند **Wireshark**، می‌توانید ترافیک شبکه را بررسی کنید و مشکلات مربوط به احراز هویت NTLM را شناسایی کنید. برای استفاده از Wireshark، باید آن را نصب کنید و سپس ترافیک شبکه را capture کنید. پس از capture کردن ترافیک، می‌توانید آن را برای یافتن خطاهای مربوط به احراز هویت NTLM تجزیه و تحلیل کنید \[1\].

3. **بررسی پیکربندی**:  
   مطمئن شوید که پیکربندی Nginx Reversed Proxy به درستی انجام شده است و تمام پارامترهای لازم برای احراز هویت NTLM تنظیم شده‌اند \[1\].

> عیب‌یابی مشکلات احراز هویت NTLM ممکن است پیچیده باشد و نیاز به دانش فنی داشته باشد \[1\].

---

## بهترین روش‌ها برای پیاده‌سازی احراز هویت NTLM با Nginx Reversed Proxy

پیاده‌سازی احراز هویت NTLM باید با توجه به نیازهای امنیتی و عملکردی برنامه وب انجام شود \[8\]. در زیر به برخی از بهترین روش‌ها برای پیاده‌سازی احراز هویت NTLM با Nginx Reversed Proxy اشاره شده است:

1. **استفاده از SSL**  
   برای افزایش امنیت، از **SSL** برای رمزگذاری ترافیک بین کلاینت و Nginx Reversed Proxy استفاده کنید \[12\]. با استفاده از SSL، می‌توانید از استراق سمع و دستکاری داده‌ها در حین انتقال جلوگیری کنید.

2. **استفاده از Keepalive**  
   برای بهبود عملکرد، از **Keepalive** برای حفظ اتصالات TCP بین Nginx Reversed Proxy و سرورهای backend استفاده کنید \[8\]. با استفاده از Keepalive، می‌توانید از ایجاد و بستن مکرر اتصالات TCP جلوگیری کنید و در نتیجه عملکرد برنامه وب را بهبود بخشید.

3. **استفاده از Cache**  
   برای کاهش بار سرورهای backend، از **Cache** برای ذخیره پاسخ‌های سرور استفاده کنید \[8\]. با استفاده از Cache، می‌توانید تعداد درخواست‌هایی که به سرورهای backend ارسال می‌شود را کاهش دهید و در نتیجه عملکرد برنامه وب را بهبود بخشید.

---

## نتیجه‌گیری

در این مقاله، نحوه پیکربندی **Nginx Reversed Proxy** برای احراز هویت NTLM را بررسی کردیم. با استفاده از روش‌های مختلفی که در این مقاله توضیح داده شد، می‌توانید به راحتی احراز هویت NTLM را در Nginx Reversed Proxy پیاده‌سازی کنید و از مزایای مختلفی مانند افزایش امنیت، بهبود عملکرد و مدیریت ترافیک بهره‌مند شوید.

هر روش پیاده‌سازی احراز هویت NTLM مزایا و معایب خاص خود را دارد. به عنوان مثال، استفاده از **Nginx Plus** ساده‌ترین روش است، اما نیاز به خرید لایسنس دارد. استفاده از ماژول‌های شخص ثالث رایگان است، اما ممکن است با خطراتی همراه باشد. استفاده از Lua انعطاف‌پذیری بیشتری را ارائه می‌دهد، اما نیاز به دانش برنامه‌نویسی Lua دارد.

در نهایت، انتخاب روش مناسب برای پیاده‌سازی احراز هویت NTLM به نیازهای خاص شما بستگی دارد.

---

## Works cited

1. Solved: Reverse Proxy to work with NTLM authentication - Experts Exchange, accessed December 30, 2024, [https://www.experts-exchange.com/questions/28068656/Reverse-Proxy-to-work-with-NTLM-authentication.html](https://www.experts-exchange.com/questions/28068656/Reverse-Proxy-to-work-with-NTLM-authentication.html)
2. How to use nginx to proxy to a host requiring NTLM authentication? - Server Fault, accessed December 30, 2024, [https://serverfault.com/questions/799267/how-to-use-nginx-to-proxy-to-a-host-requiring-ntlm-authentication](https://serverfault.com/questions/799267/how-to-use-nginx-to-proxy-to-a-host-requiring-ntlm-authentication)
3. A nginx module to allow proxying requests with NTLM Authentication. - GitHub, accessed December 30, 2024, [https://github.com/gabihodoroaga/nginx-ntlm-module](https://github.com/gabihodoroaga/nginx-ntlm-module)
4. Lua | NGINX Documentation, accessed December 30, 2024, [https://docs.nginx.com/nginx/admin-guide/dynamic-modules/lua/](https://docs.nginx.com/nginx/admin-guide/dynamic-modules/lua/)
5. gosp/lua-resty-ntlm: nginx ntlm module implemented by lua - GitHub, accessed December 30, 2024, [https://github.com/gosp/lua-resty-ntlm](https://github.com/gosp/lua-resty-ntlm)
6. lua-resty-ntlm - LuaRocks, accessed December 30, 2024, [https://luarocks.org/modules/gosp/lua-resty-ntlm](https://luarocks.org/modules/gosp/lua-resty-ntlm)
7. Nginx Lua Module, accessed December 30, 2024, [https://nginxtutorials.com/nginx-lua-module/](https://nginxtutorials.com/nginx-lua-module/)
8. session - NGINX Extras Documentation, accessed December 30, 2024, [https://nginx-extras.getpagespeed.com/lua/session/](https://nginx-extras.getpagespeed.com/lua/session/)
9. Authenticate NTLM at reverse proxy in front of unauthenticated web servers, accessed December 30, 2024, [https://serverfault.com/questions/922725/authenticate-ntlm-at-reverse-proxy-in-front-of-unauthenticated-web-servers](https://serverfault.com/questions/922725/authenticate-ntlm-at-reverse-proxy-in-front-of-unauthenticated-web-servers)
10. Support NTLM Authentication · Issue #166 · microsoft/reverse-proxy - GitHub, accessed December 30, 2024, [https://github.com/microsoft/reverse-proxy/issues/166](https://github.com/microsoft/reverse-proxy/issues/166)
11. nginx/openresty reverse proxy ntlm support - GitHub Gist, accessed December 30, 2024, [https://gist.github.com/pohmelie/d1f3ae729472aa652177c2f954bbee08](https://gist.github.com/pohmelie/d1f3ae729472aa652177c2f954bbee08)
12. K000134604: Configure NTLM on NGINX Plus - MyF5 | Support, accessed December 30, 2024, [https://my.f5.com/manage/s/article/K000134604](https://my.f5.com/manage/s/article/K000134604)

</div>
