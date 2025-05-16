# 📘 Django ORM Complete Cheat Sheet – Beginner to Advanced

This comprehensive cheat sheet covers the Django Object-Relational Mapping (ORM) system, from basic CRUD operations to advanced querying techniques, optimizations, and best practices. It’s designed for beginners and advanced developers alike, providing clear examples and detailed explanations.

---

## 🧱 1. Creating Records

```python
# Method 1: Create and save in one line
Post.objects.create(title="Hello Django", content="Welcome to Django ORM", author=author, published=True)

# Method 2: Instantiate and save manually
post = Post(title="New Post", content="Exploring ORM", author=author)
post.save()

# Method 3: Bulk create multiple records
Post.objects.bulk_create([
    Post(title="Post 1", content="Content 1", author=author),
    Post(title="Post 2", content="Content 2", author=author),
])
```

**Explanation:**
- `create()`: Combines instantiation and saving in a single database query.
- `save()`: Allows modifications before saving, useful for complex logic.
- `bulk_create()`: Efficiently creates multiple records in one query, bypassing individual `.save()` calls.

---

## 🔍 2. Reading Records

```python
# Retrieve all records
Post.objects.all()

# Get a single record by primary key
Post.objects.get(id=1)

# Get first or last record
Post.objects.first()
Post.objects.last()

# Safe retrieval (returns None if not found)
Post.objects.filter(id=1).first()

# Check if record exists
Post.objects.filter(id=1).exists()
```

**Explanation:**
- `get()`: Retrieves exactly one record; raises `DoesNotExist` if none found or `MultipleObjectsReturned` if multiple found.
- `filter().first()`: Safer alternative to `get()`, returns `None` if no records match.
- `exists()`: Efficiently checks for record existence without fetching data.

---

## 🔎 3. Filtering Records

```python
# Basic filtering
Post.objects.filter(title="Django Guide")
Post.objects.filter(published=True)

# Advanced lookups
Post.objects.filter(title__icontains="django")  # Case-insensitive
Post.objects.filter(id__gt=5)                  # Greater than
Post.objects.filter(id__in=[1, 2, 3])          # In a list
Post.objects.filter(author__isnull=True)       # Null check
Post.objects.filter(created_at__year=2024)     # Date field lookup
```

**Explanation:**
- Double underscores (`__`) enable field lookups like `exact`, `icontains`, `gt`, etc.
- Lookups can be chained for precise filtering.

---

## 🚫 4. Excluding Records

```python
# Exclude records matching condition
Post.objects.exclude(published=True)
Post.objects.exclude(id__in=[1, 2, 3])
```

**Explanation:**
- `exclude()`: Returns records that **do not** match the specified conditions, complementing `filter()`.

---

## ⚙ 5. Combining Filters with Q Objects

```python
from django.db.models import Q

# OR condition
Post.objects.filter(Q(title__icontains="python") | Q(title__icontains="django"))

# AND condition
Post.objects.filter(Q(author__name="Alice") & Q(published=True))

# NOT condition
Post.objects.filter(~Q(published=True))

# Complex example
Post.objects.filter(Q(title__icontains="guide") | (Q(published=True) & Q(author__name="Bob")))
```

**Explanation:**
- **What are Q Objects?**: `Q` objects encapsulate query conditions, allowing complex logical combinations that standard `filter()` cannot handle. They wrap field lookups (e.g., `title__icontains`) and can be combined using operators.
- **When to Use**: Use `Q` objects for queries requiring OR (`|`), AND (`&`), or NOT (`~`) logic, such as filtering records that match multiple criteria or excluding specific conditions. They’re essential for dynamic queries where conditions are built programmatically (e.g., based on user input).
- **Example Use Case**: Building a search feature where posts must match either a title keyword or a tag, but only if published.

---

## 📊 6. Ordering Results

```python
# Order by single field
Post.objects.order_by("created_at")    # Ascending
Post.objects.order_by("-created_at")   # Descending

# Order by multiple fields
Post.objects.order_by("author__name", "-title")

# Random ordering
Post.objects.order_by("?")
```

**Explanation:**
- Use `-` for descending order.
- Supports related fields via `__` (e.g., `author__name`).
- `?` orders results randomly (performance may vary by database).

---

## 🔏 7. Limiting and Slicing Results

```python
# First 5 records
Post.objects.all()[:5]

# Records 11 to 20 (pagination)
Post.objects.all()[10:20]

# Single record by offset
Post.objects.all()[0]
```

**Explanation:**
- QuerySet slicing mimics Python list slicing, ideal for pagination.
- Avoid negative indexing (`[-1]`), as it’s not supported by most databases.

---

## 🔄 8. Updating Records

```python
# Update single record
post = Post.objects.get(id=1)
post.title = "Updated Title"
post.save()

# Bulk update multiple records
Post.objects.filter(published=False).update(published=True, updated_at=timezone.now())

# Increment a field
from django.db.models import F
Post.objects.filter(id=1).update(views=F("views") + 1)
```

**Explanation:**
- `save()`: Updates a single instance, triggers model signals.
- `update()`: Performs efficient batch updates directly in the database, skips signals.
- `F()`: References field values for atomic updates (e.g., incrementing counters).

---

## ❌ 9. Deleting Records

```python
# Delete single record
Post.objects.get(id=1).delete()

# Delete multiple records
Post.objects.filter(published=False).delete()

# Delete all records
Post.objects.all().delete()
```

**Explanation:**
- `delete()`: Removes records from the database.
- Triggers `pre_delete` and `post_delete` signals unless using `QuerySet.delete()`.

---

## 🧠 10. Common Field Lookups

| Lookup          | Description                          | Example                          |
|-----------------|--------------------------------------|----------------------------------|
| `exact`         | Exact match                          | `title__exact="Hello"`           |
| `iexact`        | Case-insensitive exact match         | `title__iexact="hello"`          |
| `contains`      | Case-sensitive substring match       | `title__contains="Django"`       |
| `icontains`     | Case-insensitive substring match     | `title__icontains="django"`      |
| `in`            | Value in list                        | `id__in=[1, 2, 3]`               |
| `gt`            | Greater than                         | `id__gt=10`                      |
| `gte`           | Greater than or equal to             | `id__gte=10`                     |
| `lt`            | Less than                            | `id__lt=5`                       |
| `lte`           | Less than or equal to                | `id__lte=5`                      |
| `range`         | Between two values                   | `id__range=(1, 5)`               |
| `isnull`        | Check for NULL                       | `author__isnull=True`            |
| `startswith`    | Starts with string                   | `title__startswith="H"`          |
| `istartswith`   | Case-insensitive starts with         | `title__istartswith="h"`         |
| `endswith`      | Ends with string                     | `title__endswith="g"`            |
| `iendswith`     | Case-insensitive ends with           | `title__iendswith="g"`           |
| `year`          | Year part of DateField               | `created_at__year=2024`          |
| `month`         | Month part of DateField              | `created_at__month=5`            |
| `day`           | Day part of DateField                | `created_at__day=16`             |
| `regex`         | Regular expression match             | `title__regex=r'^Hello.*$'`      |
| `iregex`        | Case-insensitive regex match         | `title__iregex=r'^hello.*$'`     |

**Detailed Comparison Lookups:**
- `gt`: Strictly greater than the given value.
- `gte`: Greater than or equal to the given value.
- `lt`: Strictly less than the given value.
- `lte`: Less than or equal to the given value.

**Date/Time Lookups:**
- Applicable to `DateField`, `DateTimeField`, and `TimeField`.
- Examples: `created_at__year`, `created_at__month`, `created_at__day`.

---

## 🔗 11. Filtering Across Relationships

```python
# Filter by related model fields
Post.objects.filter(author__name="Alice")
Post.objects.filter(tags__name="django")

# Reverse relation lookup
Author.objects.filter(post__title__icontains="guide")

# Many-to-many filtering
Post.objects.filter(tags__name__in=["python", "django"])
```

**Explanation:**
- Use `__` to traverse ForeignKey, OneToOne, or ManyToMany relationships.
- Reverse lookups access related objects (e.g., `post` from `Author`).

---

## 🚫 12. Selecting Specific Fields

```python
# Fetch only specified fields
Post.objects.only("title", "author")

# Return dictionaries of field values
Post.objects.values("title", "created_at")

# Return tuples or flat list
Post.objects.values_list("title", flat=True)

# Defer fields (lazy loading)
Post.objects.defer("content")
```

**Explanation:**
- `only()`: Fetches specified fields, defers others (useful for performance).
- `values()`: Returns dictionaries, ideal for serialization.
- `values_list()`: Returns tuples or flat lists, useful for simple data extraction.
- `defer()`: Excludes fields from initial query, loads them only when accessed.

---

## 📊 13. Aggregation Functions

```python
from django.db.models import Count, Avg, Sum, Min, Max

# Count total records
Post.objects.aggregate(total=Count("id"))

# Average, sum, min, max
Post.objects.aggregate(
    avg_views=Avg("views"),
    total_views=Sum("views"),
    min_views=Min("views"),
    max_views=Max("views")
)

# Group by with annotations
Post.objects.values("author__name").annotate(post_count=Count("id"))
```

**Explanation:**
- Aggregations compute summary statistics over a queryset.
- Use `values()` before `annotate()` for grouping (similar to SQL `GROUP BY`).

---

## 🧪 14. Annotation (Per-Row Calculations)

```python
from django.db.models import Count, Case, When, Value, IntegerField

# Count related objects per record
Post.objects.annotate(comment_count=Count("comments"))

# Conditional annotations
Post.objects.annotate(
    status=Case(
        When(published=True, then=Value(1)),
        When(published=False, then=Value(0)),
        output_field=IntegerField(),
    )
)
```

**Explanation:**
- **What is Annotation?**: Annotation adds a computed field to each object in a QuerySet, calculated at the database level. It’s like adding a temporary column to your query results, specific to each row.
- **When to Use**: Use annotations to compute per-object values, such as counting related objects (e.g., number of comments per post), performing conditional logic (e.g., categorizing posts), or deriving new data (e.g., string concatenation). Annotations are ideal when you need to enrich query results without post-processing in Python.
- **Example Use Case**: Displaying a list of posts with their comment counts or marking posts as “popular” based on view thresholds.
- **Key Points**:
  - Annotations are evaluated in the database, reducing Python-side computation.
  - Use with aggregation functions (`Count`, `Sum`) or expressions (`Case`, `When`).
  - Annotations can be referenced in subsequent filters or ordering.

---

## 🚀 15. Optimization with Related Queries

```python
# Optimize ForeignKey/OneToOne lookups
Post.objects.select_related("author")

# Optimize ManyToMany/reverse relation lookups
Post.objects.prefetch_related("tags")

# Combine optimizations
Post.objects.select_related("author").prefetch_related("tags")
```

**Explanation:**
- **What is select_related?**: `select_related()` optimizes queries by performing a SQL JOIN to fetch related objects (ForeignKey or OneToOneField) in a single database query. It creates a larger result set but reduces the number of queries.
- **When to Use select_related**: Use when accessing ForeignKey or OneToOne related fields frequently in a query, such as displaying a post’s author details. It’s ideal for one-to-one or one-to-many relationships where each object has exactly one related object.
- **Example Use Case**: Fetching a list of posts with their authors, where each post’s `author.name` is displayed.
- **Key Points**:
  - Only works with ForeignKey and OneToOneField.
  - Reduces queries but increases memory usage due to JOINs.
  - Use sparingly for large datasets to avoid performance issues.

- **What is prefetch_related?**: `prefetch_related()` optimizes queries by fetching related objects (ManyToManyField or reverse ForeignKey) in separate queries and joining them in Python. It avoids the Cartesian product issue of JOINs for many-to-many relationships.
- **When to Use prefetch_related**: Use for ManyToManyFields or reverse ForeignKey relationships, such as fetching all tags for a list of posts or comments for multiple posts. It’s ideal when related objects are numerous or variable per object.
- **Example Use Case**: Displaying a list of posts with their associated tags, where each post may have multiple tags.
- **Key Points**:
  - Executes additional queries but joins results efficiently in Python.
  - Better for complex relationships where JOINs would be inefficient.
  - Can be combined with `select_related` for mixed relationships.

- **Performance Tip**: Always profile queries with tools like `django-debug-toolbar` to determine whether `select_related` or `prefetch_related` is more efficient for your use case.

---

## ✅ 16. Exists, Count, Distinct

```python
# Check if records exist
Post.objects.filter(published=True).exists()

# Count records
Post.objects.count()

# Remove duplicates
Post.objects.values("author").distinct()

# Database-specific distinct
Post.objects.distinct("title")  # PostgreSQL only
```

**Explanation:**
- `exists()`: Efficiently checks for results without fetching data.
- `count()`: Returns the number of records, optimized for performance.
- `distinct()`: Removes duplicate rows; field-specific `distinct("field")` is PostgreSQL-only.

---

## 📈 17. Subqueries and F Expressions

```python
from django.db.models import F, Subquery, OuterRef

# Compare fields within the same model
Post.objects.filter(updated_at__gt=F("created_at"))

# Update with F expression
Post.objects.update(views=F("views") + 1)

# Subquery: Annotate with latest comment
latest_comment = Comment.objects.filter(post=OuterRef("pk")).order_by("-created_at").values("content")[:1]
Post.objects.annotate(latest_comment=Subquery(latest_comment))
```

**Explanation:**
- **What are F Expressions?**: `F` expressions allow referencing a model’s field values directly in queries, enabling database-level operations like comparisons or updates without fetching data into Python.
- **When to Use F Expressions**: Use `F` for operations involving a field’s current value, such as incrementing counters (e.g., `views += 1`), comparing fields (e.g., `updated_at > created_at`), or performing calculations. They ensure atomicity and reduce race conditions in concurrent environments.
- **Example Use Case**: Incrementing a post’s view count or filtering posts where the update timestamp is later than the creation timestamp.
- **Key Points**:
  - Executes operations in the database, improving performance.
  - Prevents race conditions in updates (e.g., multiple users incrementing the same counter).
  - Can be combined with arithmetic or other expressions.

- **Subquery and OuterRef**: Covered in the original explanation, these allow embedding queries within others, linking via `OuterRef` to the outer query’s context.

---

## 🛠 18. Custom Managers and QuerySets

```python
class PublishedQuerySet(models.QuerySet):
    def published(self):
        return self.filter(published=True)

class PublishedManager(models.Manager):
    def get_queryset(self):
        return PublishedQuerySet(self.model, using=self._db)

class Post(models.Model):
    title = models.CharField(max_length=200)
    published = models.BooleanField(default=False)
    objects = models.Manager()        # Default manager
    published = PublishedManager()    # Custom manager

# Usage
Post.published.all()  # Only published posts
Post.objects.all()    # All posts
```

**Explanation:**
- Custom managers encapsulate reusable query logic.
- Custom QuerySets allow chaining (e.g., `Post.published.filter(...)`).
- Preserve the default `objects` manager for unfiltered queries.

---

## 🐍 19. Raw SQL Queries

```python
# Raw SQL query
posts = Post.objects.raw("SELECT * FROM blog_post WHERE published = %s", [True])

# Execute raw SQL with cursor
from django.db import connection
with connection.cursor() as cursor:
    cursor.execute("SELECT COUNT(*) FROM blog_post WHERE published = %s", [True])
    count = cursor.fetchone()[0]
```

**Explanation:**
- `raw()`: Executes raw SQL queries, mapping results to model instances.
- Direct cursor access: Useful for non-ORM queries or database-specific operations.
- Use parameterized queries to prevent SQL injection.

---

## 🔐 20. Transactions

```python
from django.db import transaction

# Atomic transaction
with transaction.atomic():
    post = Post.objects.create(title="Transactional Post", author=author)
    post.views += 1
    post.save()

# Decorator for atomic functions
@transaction.atomic
def update_post(post_id, new_title):
    post = Post.objects.get(id=post_id)
    post.title = new_title
    post.save()
```

**Explanation:**
- `transaction.atomic()`: Ensures all operations succeed or roll back on failure.
- Use as a context manager or decorator for database consistency.

---

## 📡 21. Database Functions

```python
from django.db.models.functions import Lower, Concat, Length

# Case-insensitive filtering
Post.objects.annotate(title_lower=Lower("title")).filter(title_lower="django")

# Concatenate fields
Post.objects.annotate(full_title=Concat("title", Value(" - "), "author__name"))

# Calculate field length
Post.objects.annotate(title_length=Length("title")).filter(title_length__gt=10)
```

**Explanation:**
- Django provides database functions like `Lower`, `Concat`, and `Length` for SQL-level operations.
- Use with `annotate()` or `filter()` for dynamic calculations.

---

## 🛡️ 22. Handling Case Sensitivity

```python
# Case-sensitive exact match
Post.objects.filter(title__exact="Django")

# Case-insensitive exact match
Post.objects.filter(title__iexact="django")

# Case-insensitive filtering with Lower
from django.db.models.functions import Lower
Post.objects.annotate(title_lower=Lower("title")).filter(title_lower__contains="django")
```

**Explanation:**
- Use `iexact`, `icontains`, or `Lower()` for case-insensitive queries.
- Database-specific behavior may vary (e.g., PostgreSQL is case-sensitive by default).

---

## 📝 23. ORM Summary Map (Fully Expanded)

| Operation         | Methods / Lookups                                                                                                                                           |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| **CREATE**        | `.create()`, `.save()`, `.bulk_create()`                                                                                                                  |
| **READ**          | `.get()`, `.all()`, `.filter()`, `.first()`, `.last()`, `.exists()`                                                                                       |
| **UPDATE**        | `.save()`, `.update()`, `F()`                                                                                                                             |
| **DELETE**        | `.delete()`                                                                                                                                               |
| **FILTER**        | `__exact`, `__iexact`, `__contains`, `__icontains`, `__in`, `__gt`, `__gte`, `__lt`, `__lte`, `__range`, `__isnull`, `__startswith`, `__istartswith`, `__endswith`, `__iendswith`, `__year`, `__month`, `__day`, `__regex`, `__iregex` |
| **RELATIONS**     | ForeignKey, OneToOne, ManyToMany filtering using `__`                                                                                                     |
| **AGGREGATION**   | `.aggregate()`, `Count()`, `Avg()`, `Sum()`, `Min()`, `Max()`                                                                                             |
| **ANNOTATION**    | `.annotate()`, `Case()`, `When()`, `Value()`                                                                                                              |
| **OPTIMIZATION**  | `.select_related()`, `.prefetch_related()`, `.only()`, `.defer()`, `.values()`, `.values_list()`                                                           |
| **ADVANCED**      | `Q()`, `F()`, `Subquery()`, `OuterRef()`, `.raw()`, `.distinct()`, database functions (`Lower`, `Concat`, `Length`)                                        |
| **MANAGERS**      | Custom `Manager`, custom `QuerySet`                                                                                                                       |
| **TRANSACTIONS**  | `transaction.atomic()`                                                                                                                                    |

---

## 💡 24. Best Practices and Tips

- **Use `filter().first()` over `get()`**: Avoids exceptions for missing records.
- **Leverage `select_related` and `prefetch_related`**: Reduces query counts for related data.
- **Avoid excessive queries in loops**: Use bulk operations (`bulk_create`, `update`) or prefetching.
- **Use `exists()` for checks**: More efficient than counting or fetching records.
- **Profile queries**: Use tools like `django-debug-toolbar` to identify slow queries.
- **Secure raw SQL**: Always use parameterized queries to prevent SQL injection.
- **Keep managers reusable**: Encapsulate common filters in custom managers or QuerySets.
- **Use transactions for consistency**: Wrap related operations in `atomic()` blocks.
- **Test with realistic data**: Ensure queries perform well with large datasets.

---

## 📚 25. Additional Resources

- [Django ORM Documentation](https://docs.djangoproject.com/en/stable/topics/db/queries/)
- [Django QuerySet API](https://docs.djangoproject.com/en/stable/ref/models/querysets/)
- [Database Functions](https://docs.djangoproject.com/en/stable/ref/models/database-functions/)
- [Django Debug Toolbar](https://django-debug-toolbar.readthedocs.io/)

---

This cheat sheet provides a complete reference for Django ORM, enhanced with detailed explanations for annotations, F expressions, Q objects, `select_related`, and `prefetch_related`, along with best practices and resources for further learning.
