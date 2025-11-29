# Django ORM Interview Questions & Answers

## 1. Basic ORM Concepts

### Q1: What is Django ORM and what are its advantages?
**Answer:** Django ORM (Object-Relational Mapping) is a layer that allows you to interact with databases using Python code instead of writing raw SQL queries.

**Advantages:**
- Database agnostic - works with PostgreSQL, MySQL, SQLite, Oracle
- Prevents SQL injection attacks
- Easier to maintain and read
- Automatic schema migrations
- Built-in query optimization

---

### Q2: Explain the difference between `filter()` and `get()`
**Answer:**
```python
# filter() - Returns a QuerySet (can be empty, can have multiple objects)
users = User.objects.filter(age=25)  # Returns QuerySet
users = User.objects.filter(age=25).first()  # Returns object or None

# get() - Returns a single object or raises exception
user = User.objects.get(id=1)  # Returns object
# Raises DoesNotExist if not found
# Raises MultipleObjectsReturned if more than one result
```

**When to use:**
- Use `filter()` when you expect 0 or multiple results
- Use `get()` when you expect exactly one result (like fetching by primary key)

---

### Q3: What's the difference between `all()`, `filter()`, and `exclude()`?
**Answer:**
```python
# all() - Returns all records
products = Product.objects.all()

# filter() - Returns records that match conditions
active_products = Product.objects.filter(is_active=True)

# exclude() - Returns records that DON'T match conditions
inactive_products = Product.objects.exclude(is_active=True)

# Can be chained
expensive_active = Product.objects.filter(
    is_active=True
).exclude(
    price__lt=100
)
```

---

## 2. QuerySet Operations

### Q4: Explain lazy evaluation in Django ORM
**Answer:** QuerySets are lazy - the database query isn't executed until you actually use the data.

```python
# No database hit yet
products = Product.objects.filter(price__gt=100)

# Database hit happens here
for product in products:
    print(product.name)

# Also triggers database hit
count = products.count()
product_list = list(products)
if products.exists():  # Efficient way to check existence
    pass
```

**Why it matters:** Allows Django to optimize queries by combining multiple filters before execution.

---

### Q5: What are lookups and how do you use them?
**Answer:** Lookups are ways to specify conditions in queries using double underscores.

```python
# Common lookups
Product.objects.filter(price__gt=100)        # Greater than
Product.objects.filter(price__gte=100)       # Greater than or equal
Product.objects.filter(price__lt=100)        # Less than
Product.objects.filter(price__lte=100)       # Less than or equal
Product.objects.filter(name__icontains='phone')  # Case-insensitive contains
Product.objects.filter(name__startswith='Apple')
Product.objects.filter(name__endswith='Pro')
Product.objects.filter(category__in=['Electronics', 'Gadgets'])
Product.objects.filter(created_at__year=2024)
Product.objects.filter(created_at__range=['2024-01-01', '2024-12-31'])
Product.objects.filter(description__isnull=True)

# Spanning relationships
Order.objects.filter(customer__email__endswith='@gmail.com')
```

---

### Q6: How do you perform OR queries in Django ORM?
**Answer:** Use `Q` objects for complex queries with OR, AND, and NOT operations.

```python
from django.db.models import Q

# OR query
products = Product.objects.filter(
    Q(category='Electronics') | Q(category='Gadgets')
)

# AND query (can also use multiple filter() calls)
products = Product.objects.filter(
    Q(price__gt=100) & Q(is_active=True)
)

# NOT query
products = Product.objects.filter(
    ~Q(category='Electronics')
)

# Complex combinations
products = Product.objects.filter(
    (Q(category='Electronics') | Q(category='Gadgets')) &
    Q(price__lt=500) &
    ~Q(stock=0)
)
```

---

### Q7: Explain `select_related()` and `prefetch_related()`
**Answer:** Both are used to optimize database queries by reducing the number of database hits.

**select_related()** - For ForeignKey and OneToOne relationships (uses SQL JOIN)
```python
# Without select_related - N+1 problem
orders = Order.objects.all()
for order in orders:
    print(order.customer.name)  # Each iteration hits database

# With select_related - Single query with JOIN
orders = Order.objects.select_related('customer').all()
for order in orders:
    print(order.customer.name)  # No additional database hits
```

**prefetch_related()** - For ManyToMany and reverse ForeignKey (uses separate queries)
```python
# Without prefetch_related
books = Book.objects.all()
for book in books:
    for author in book.authors.all():  # Database hit for each book
        print(author.name)

# With prefetch_related
books = Book.objects.prefetch_related('authors').all()
for book in books:
    for author in book.authors.all():  # No additional hits
        print(author.name)
```

**Key Difference:**
- `select_related`: Single query with JOIN (for single-valued relationships)
- `prefetch_related`: Separate queries then joins in Python (for multi-valued relationships)

---

## 3. Aggregation and Annotation

### Q8: How do you use `aggregate()` and `annotate()`?
**Answer:**

**aggregate()** - Computes values over entire QuerySet (returns a dictionary)
```python
from django.db.models import Count, Sum, Avg, Max, Min

# Returns: {'price__avg': 299.99}
Product.objects.aggregate(Avg('price'))

# Multiple aggregations
stats = Product.objects.aggregate(
    avg_price=Avg('price'),
    max_price=Max('price'),
    min_price=Min('price'),
    total_products=Count('id')
)
# Returns: {'avg_price': 299.99, 'max_price': 999, ...}
```

**annotate()** - Adds computed field to each object in QuerySet
```python
from django.db.models import Count, Sum

# Add order count to each customer
customers = Customer.objects.annotate(
    order_count=Count('orders')
)
for customer in customers:
    print(f"{customer.name}: {customer.order_count} orders")

# Add total spent to each customer
customers = Customer.objects.annotate(
    total_spent=Sum('orders__total_amount')
)

# Can filter on annotated fields
high_value_customers = Customer.objects.annotate(
    total_spent=Sum('orders__total_amount')
).filter(total_spent__gt=10000)
```

---

### Q9: What is the `F` expression and when do you use it?
**Answer:** `F` expressions reference model field values directly in the database, allowing database-level operations.

```python
from django.db.models import F

# Increment price by 10% without loading into Python
Product.objects.update(price=F('price') * 1.1)

# Compare two fields in the same model
# Find products where discount is greater than cost
products = Product.objects.filter(discount__gt=F('cost'))

# Arithmetic operations
Order.objects.update(
    total=F('quantity') * F('unit_price')
)

# Accessing related fields
orders = Order.objects.filter(
    customer__credit_limit__gt=F('total_amount')
)
```

**Benefits:**
- Operations happen at database level (faster)
- Avoids race conditions
- Reduces memory usage

---

## 4. Model Relationships

### Q10: Explain ForeignKey, OneToOne, and ManyToMany relationships
**Answer:**

```python
from django.db import models

# ForeignKey - Many to One
class Order(models.Model):
    customer = models.ForeignKey(
        'Customer',
        on_delete=models.CASCADE,  # Delete orders when customer is deleted
        related_name='orders'  # Access via customer.orders.all()
    )

# OneToOne - One to One
class UserProfile(models.Model):
    user = models.OneToOneField(
        User,
        on_delete=models.CASCADE,
        related_name='profile'  # Access via user.profile
    )

# ManyToMany - Many to Many
class Book(models.Model):
    authors = models.ManyToManyField(
        'Author',
        related_name='books'  # Access via author.books.all()
    )
```

**on_delete options:**
- `CASCADE`: Delete related objects
- `PROTECT`: Prevent deletion if related objects exist
- `SET_NULL`: Set to NULL (requires null=True)
- `SET_DEFAULT`: Set to default value
- `DO_NOTHING`: Do nothing (can cause integrity errors)

---

### Q11: How do you create objects with relationships?
**Answer:**

```python
# ForeignKey
customer = Customer.objects.create(name='John')
order = Order.objects.create(customer=customer, total=100)

# Or
order = Order.objects.create(customer_id=customer.id, total=100)

# ManyToMany - Method 1: add()
book = Book.objects.create(title='Django Guide')
author1 = Author.objects.create(name='John')
author2 = Author.objects.create(name='Jane')
book.authors.add(author1, author2)

# Method 2: set()
book.authors.set([author1, author2])

# Method 3: Through model
book.authors.create(name='New Author')

# Remove relationships
book.authors.remove(author1)
book.authors.clear()  # Remove all
```

---

## 5. Advanced Queries

### Q12: How do you perform raw SQL queries?
**Answer:**

```python
# Method 1: raw() - Returns model instances
products = Product.objects.raw('SELECT * FROM app_product WHERE price > %s', [100])

# Method 2: Using connection cursor
from django.db import connection

with connection.cursor() as cursor:
    cursor.execute('SELECT * FROM app_product WHERE price > %s', [100])
    rows = cursor.fetchall()
    # Returns tuples, not model instances

# Method 3: Execute any SQL
from django.db import connection

with connection.cursor() as cursor:
    cursor.execute('UPDATE app_product SET price = price * 1.1')
    cursor.execute('COMMIT')
```

**When to use:**
- Complex queries that are difficult with ORM
- Performance-critical operations
- Database-specific features

---

### Q13: What are QuerySet methods that return new QuerySets vs evaluate immediately?
**Answer:**

**Return new QuerySets (chainable, lazy):**
```python
.all()
.filter()
.exclude()
.order_by()
.reverse()
.distinct()
.select_related()
.prefetch_related()
.annotate()
.values()
.values_list()
.defer()
.only()
```

**Evaluate immediately (hit database):**
```python
.count()
.exists()
.get()
.create()
.update()
.delete()
.first()
.last()
list(queryset)
for item in queryset
if queryset
```

---

### Q14: Explain `values()` and `values_list()`
**Answer:**

```python
# values() - Returns dictionaries
products = Product.objects.values('id', 'name', 'price')
# [{'id': 1, 'name': 'Phone', 'price': 500}, ...]

# values_list() - Returns tuples
products = Product.objects.values_list('id', 'name', 'price')
# [(1, 'Phone', 500), (2, 'Laptop', 1000), ...]

# flat=True for single field (returns flat list)
names = Product.objects.values_list('name', flat=True)
# ['Phone', 'Laptop', 'Tablet', ...]

# Spanning relationships
orders = Order.objects.values('id', 'customer__name', 'customer__email')
```

**Use cases:**
- When you don't need full model instances
- Better performance (less memory)
- Exporting data, generating reports

---

## 6. Transactions and Optimization

### Q15: How do you use transactions in Django?
**Answer:**

```python
from django.db import transaction

# Method 1: Decorator
@transaction.atomic
def create_order_with_items(customer, items):
    order = Order.objects.create(customer=customer)
    for item in items:
        OrderItem.objects.create(order=order, **item)
    # If any error occurs, entire transaction rolls back

# Method 2: Context manager
def transfer_money(from_account, to_account, amount):
    with transaction.atomic():
        from_account.balance -= amount
        from_account.save()
        
        to_account.balance += amount
        to_account.save()
        
        # Both saves or none

# Method 3: Manual savepoints
with transaction.atomic():
    # Create savepoint
    sid = transaction.savepoint()
    
    try:
        # Some operations
        risky_operation()
        transaction.savepoint_commit(sid)
    except:
        transaction.savepoint_rollback(sid)
```

---

### Q16: What is the N+1 query problem and how do you solve it?
**Answer:**

**Problem:**
```python
# Bad - N+1 queries (1 query + N queries for each order's customer)
orders = Order.objects.all()  # 1 query
for order in orders:
    print(order.customer.name)  # N additional queries
```

**Solutions:**

```python
# Solution 1: select_related() for ForeignKey/OneToOne
orders = Order.objects.select_related('customer').all()
for order in orders:
    print(order.customer.name)  # No additional queries

# Solution 2: prefetch_related() for ManyToMany/Reverse FK
books = Book.objects.prefetch_related('authors').all()
for book in books:
    for author in book.authors.all():
        print(author.name)  # No additional queries

# Solution 3: Prefetch with filtering
from django.db.models import Prefetch

authors = Author.objects.prefetch_related(
    Prefetch(
        'books',
        queryset=Book.objects.filter(published=True)
    )
)
```

---

### Q17: How do you optimize QuerySets?
**Answer:**

```python
# 1. Use select_related and prefetch_related
orders = Order.objects.select_related('customer').prefetch_related('items')

# 2. Use only() to load specific fields
products = Product.objects.only('id', 'name', 'price')

# 3. Use defer() to exclude heavy fields
products = Product.objects.defer('description', 'image')

# 4. Use iterator() for large datasets
for product in Product.objects.iterator(chunk_size=1000):
    process(product)  # Doesn't cache, saves memory

# 5. Use exists() instead of count() for checking
if Product.objects.filter(category='Electronics').exists():
    # Better than: if Product.objects.filter(...).count() > 0:
    pass

# 6. Use bulk operations
products = [
    Product(name='Product 1', price=100),
    Product(name='Product 2', price=200),
]
Product.objects.bulk_create(products)  # Single query

# Bulk update
products = Product.objects.filter(category='Electronics')
products.update(discount=10)  # Single query

# 7. Use database indexes (in model)
class Product(models.Model):
    name = models.CharField(max_length=100, db_index=True)
    
    class Meta:
        indexes = [
            models.Index(fields=['category', 'price']),
        ]
```

---

## 7. Practical Scenarios

### Q18: Write a query to find customers who haven't placed orders in last 30 days
**Answer:**

```python
from django.utils import timezone
from datetime import timedelta

thirty_days_ago = timezone.now() - timedelta(days=30)

# Method 1: Using exclude
inactive_customers = Customer.objects.exclude(
    orders__created_at__gte=thirty_days_ago
).distinct()

# Method 2: Using annotation
from django.db.models import Count, Q

inactive_customers = Customer.objects.annotate(
    recent_orders=Count(
        'orders',
        filter=Q(orders__created_at__gte=thirty_days_ago)
    )
).filter(recent_orders=0)
```

---

### Q19: How do you implement soft delete?
**Answer:**

```python
from django.db import models
from django.utils import timezone

class SoftDeleteManager(models.Manager):
    def get_queryset(self):
        return super().get_queryset().filter(deleted_at__isnull=True)

class Product(models.Model):
    name = models.CharField(max_length=100)
    deleted_at = models.DateTimeField(null=True, blank=True)
    
    objects = SoftDeleteManager()  # Default manager excludes deleted
    all_objects = models.Manager()  # Manager to access all objects
    
    def delete(self, *args, **kwargs):
        # Soft delete
        self.deleted_at = timezone.now()
        self.save()
    
    def hard_delete(self):
        # Actual deletion
        super().delete()
    
    def restore(self):
        self.deleted_at = None
        self.save()

# Usage
product = Product.objects.get(id=1)
product.delete()  # Soft delete

Product.objects.all()  # Only non-deleted
Product.all_objects.all()  # Including deleted

product.restore()  # Restore
```

---

### Q20: How do you handle database connection issues?
**Answer:**

```python
from django.db import connection, OperationalError
from django.db.utils import DatabaseError
import time

def execute_with_retry(query, params=None, max_retries=3):
    for attempt in range(max_retries):
        try:
            with connection.cursor() as cursor:
                cursor.execute(query, params or [])
                return cursor.fetchall()
        except OperationalError as e:
            if attempt == max_retries - 1:
                raise
            time.sleep(2 ** attempt)  # Exponential backoff
        except DatabaseError as e:
            # Log and handle
            raise

# In settings.py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'mydb',
        'CONN_MAX_AGE': 600,  # Connection pooling
        'OPTIONS': {
            'connect_timeout': 10,
        }
    }
}
```

---

## Quick Reference: Common Patterns

```python
# Get or Create
obj, created = Model.objects.get_or_create(
    field1=value1,
    defaults={'field2': value2}
)

# Update or Create
obj, created = Model.objects.update_or_create(
    field1=value1,
    defaults={'field2': value2}
)

# Bulk operations
Model.objects.bulk_create([obj1, obj2, obj3])
Model.objects.filter(condition).update(field=value)

# Distinct values
Model.objects.values('field').distinct()

# Conditional expressions
from django.db.models import Case, When, Value

Model.objects.annotate(
    category=Case(
        When(price__lt=100, then=Value('cheap')),
        When(price__lt=500, then=Value('medium')),
        default=Value('expensive'),
    )
)
```

These questions cover 90% of what you'll encounter in Django ORM interviews!