### 🧠 **WHAT YOU’LL LEARN (the road to mastery)**

We’ll break this down into digestible **milestones**:

1. **Understand the Problem** – Why do we need Django Tenants?
2. **Conceptual Basics** – What is multi-tenancy? What are tenants?
3. **Choose a Tenant Library** – `django-tenants` vs `django-tenant-schemas`
4. **Setup a Django Project with Tenants**
5. **Create Shared Apps and Tenant-Specific Apps**
6. **Tenant-aware Routing and Middleware**
7. **How Tenant Databases Work (Schema-based vs DB-based)**
8. **Creating and Managing Tenants**
9. **Deploying Tenant-Based Applications**
10. **Advanced Usage: Custom Domains, Signals, Caching, Testing**
11. **Security, Scaling, and Gotchas**



## 🎯 Milestone 1: Why Django Tenants?

Imagine you’re building a SaaS (like a CRM, or email marketing tool), and companies sign up to use your platform.

Each company should:

* Have its **own data** (e.g., customers, workflows)
* **Not see other companies’ data**
* Still use **the same codebase**

So:

* You **don’t** want one massive database with messy cross-company filtering.
* You want true isolation: secure, performant, clean.

That’s where **multi-tenancy** comes in.

---

## 🎯 Milestone 2: What is Multi-Tenancy?

In simple terms:

* A **Tenant** is a client/customer using your system.
* **Multi-tenancy** means serving multiple clients from one app.
* Each client gets:

  * Their own isolated data
  * Same features / UI
  * Custom domain or subdomain (e.g. `client1.myapp.com`, `client2.myapp.com`)

🧠 There are two types of multi-tenancy:

1. **Schema-based (PostgreSQL)** – Each tenant has its own schema in the same DB
2. **Database-based** – Each tenant has its own DB

We’ll focus on **schema-based**, using the powerful `django-tenants` library.

---

## 🎯 Milestone 3: Choosing the Right Tool

Two major libraries:

| Library                 | Schema Support | DB Support | Notes                   |
| ----------------------- | -------------- | ---------- | ----------------------- |
| `django-tenants`        | ✅ Yes          | ❌ No       | Active, PostgreSQL only |
| `django-tenant-schemas` | ✅ Yes          | ❌ No       | Older, less maintained  |

🔧 We’ll use `django-tenants`.

---

## 🎯 Milestone 4: Let’s Build It – From Scratch

### 🛠️ Step 1: Setup Project

```bash
django-admin startproject tenant_project
cd tenant_project
python -m venv venv && source venv/bin/activate
pip install django psycopg2 django-tenants
```

### 🛠️ Step 2: Add to `INSTALLED_APPS`

In `settings.py`:

```python
INSTALLED_APPS = [
    'django_tenants',  # must be first
    'customers',       # our tenant model
    'django.contrib.contenttypes',
    ...
]
```

### 🛠️ Step 3: Tell Django About Multi-Tenancy

```python
TENANT_MODEL = "customers.Client"  # Where the tenant lives
TENANT_DOMAIN_MODEL = "customers.Domain"  # For domain/subdomain routing

DATABASE_ROUTERS = (
    'django_tenants.routers.TenantSyncRouter',
)

DATABASES = {
    'default': {
        'ENGINE': 'django_tenants.postgresql_backend',
        'NAME': 'tenants',
        'USER': 'postgres',
        'PASSWORD': '',
        'HOST': 'localhost',
        'PORT': '5432',
    }
}
```

---

## 🎯 Milestone 5: Define Your Tenant Model

### `customers/models.py`

```python
from django_tenants.models import TenantMixin, DomainMixin
from django.db import models

class Client(TenantMixin):
    name = models.CharField(max_length=100)
    paid_until = models.DateField()
    on_trial = models.BooleanField()
    auto_create_schema = True  # Automatically create schema on save

class Domain(DomainMixin):
    pass
```

Run migrations:

```bash
python manage.py makemigrations
python manage.py migrate_schemas --shared
```

---

## 🎯 Milestone 6: Shared vs Tenant Apps

In `settings.py`:

```python
SHARED_APPS = (
    'django_tenants', 'customers',
    'django.contrib.contenttypes',
    'django.contrib.auth',
    'django.contrib.admin',
)

TENANT_APPS = (
    'myapp',
    'django.contrib.contenttypes',
)

INSTALLED_APPS = list(SHARED_APPS) + list(app for app in TENANT_APPS if app not in SHARED_APPS)
```

---

## 🎯 Milestone 7: Create a Tenant

```python
from customers.models import Client, Domain

tenant = Client(
    schema_name='tenant1',
    name='Client One',
    paid_until='2030-12-05',
    on_trial=True
)
tenant.save()

domain = Domain()
domain.domain = 'client1.localhost'
domain.tenant = tenant
domain.is_primary = True
domain.save()
```

Then visit: `http://client1.localhost:8000`

> You’ll need to set up `client1.localhost` to point to `127.0.0.1` (via `/etc/hosts` or `dnsmasq`).

---

## 🎯 Milestone 8: Tenant Routing

`django-tenants` uses middleware to figure out the current tenant based on domain.

In `settings.py`:

```python
MIDDLEWARE = [
    'django_tenants.middleware.main.TenantMainMiddleware',
    ...
]
```

---

## 🎯 Milestone 9: Admin, User Auth, Routing

You can:

* Run the admin for each tenant (`client1.localhost/admin`)
* Handle user login/registration per tenant
* Store tenant-specific models (Orders, Campaigns, etc.) in tenant schemas

---

## 🎯 Milestone 10: Advanced Features

* Custom domain routing
* Tenant-aware signals
* Caching per tenant
* Switching tenants manually (for jobs/scripts)
* Public/shared app usage (like global notifications or audit logs)

---

## 🎯 Milestone 11: Deploying and Scaling

* Always use **PostgreSQL**
* Automate tenant creation (via signup or admin)
* Use **nginx + gunicorn + subdomain routing**
* Use **pg\_dump** or schema cloning for backups

---

## 🚀 Summary Cheatsheet

| Concept     | Example                  |
| ----------- | ------------------------ |
| Tenant      | `Client` model           |
| Domain      | `client1.localhost`      |
| Schema      | `tenant1`                |
| Shared Apps | Used across all tenants  |
| Tenant Apps | Data isolated per tenant |

---

### ✅ Next Steps (Your Learning Plan)

1. ✅ Build a working demo with 2 tenants (subdomains)
2. ✅ Add models (like Products, Customers) and test isolation
3. ✅ Setup admin for each tenant
4. ✅ Add tenant creation via signup form
5. ✅ Deploy to cloud (e.g. Render, Railway, GCP with Nginx config)
6. ✅ Study caching, signals, custom routing

