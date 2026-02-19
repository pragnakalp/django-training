# Part 4: Function-Based Views - Templates & Forms

## Django Template System (HTML-Based)

**Important Note:** Django uses its own template language (DTL - Django Template Language), NOT Jinja2. While they look similar, Django templates are HTML-based with special template tags.

Django's template system allows you to create dynamic HTML pages by combining static HTML with dynamic data from views.

### Template Syntax (Django Template Language)

**Variables** - Display dynamic data
```django
{{ variable }}
{{ user.username }}
{{ task.title }}
```

**Tags** - Control logic and flow
```django
{% tag %}
{% for item in items %}
{% if condition %}
{% url 'view_name' %}
```

**Filters** - Transform data
```django
{{ value|filter }}
{{ text|lower }}
{{ date|date:"Y-m-d" }}
{{ number|add:5 }}
```

**Comments**
```django
{# Single line comment #}
{% comment %}
Multi-line comment
{% endcomment %}
```

**Key Difference from Jinja2:**
- Django: `{% url 'view_name' %}` 
- Jinja2: `{{ url_for('view_name') }}`
- Django templates are HTML files with Django template tags embedded

---

## Creating Templates

### Directory Structure

Create the following structure:

```
tasks/
└── templates/
    └── tasks/
        ├── base.html
        ├── task_list.html
        ├── task_detail.html
        ├── task_form.html
        ├── task_confirm_delete.html
        ├── login.html
        └── register.html
```

### Base Template (Template Inheritance)

**File: `tasks/templates/tasks/base.html`**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}Task Manager{% endblock %}</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.0/font/bootstrap-icons.css">
    <style>
        :root 
            --primary-color: #4f46e5;
            --secondary-color: #7c3aed;
        body 
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        .navbar 
            background: rgba(255, 255, 255, 0.95) !important;
            backdrop-filter: blur(10px);
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        .main-content 
            margin-top: 80px;
            padding-bottom: 50px;
        .card 
            border: none;
            border-radius: 15px;
            box-shadow: 0 5px 20px rgba(0,0,0,0.1);
            transition: transform 0.3s ease;
        .card:hover 
            transform: translateY(-5px);
        .badge-priority-high 
            background-color: #dc3545;
        .badge-priority-medium 
            background-color: #ffc107;
            color: #000;
        .badge-priority-low 
            background-color: #28a745;
        .task-completed 
            opacity: 0.7;
    </style>
    {% block extra_css %}{% endblock %}
</head>
<body>
    <nav class="navbar navbar-expand-lg navbar-light fixed-top">
        <div class="container">
            <a class="navbar-brand fw-bold" href="{% url 'task_list' %}">
                <i class="bi bi-check2-square"></i> Task Manager
            </a>
            <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarNav">
                <ul class="navbar-nav ms-auto">
                    {% if user.is_authenticated %}
                        <li class="nav-item">
                            <a class="nav-link" href="{% url 'task_list' %}">
                                <i class="bi bi-list-task"></i> My Tasks
                            </a>
                        </li>
                        <li class="nav-item">
                            <a class="nav-link" href="{% url 'task_create' %}">
                                <i class="bi bi-plus-circle"></i> New Task
                            </a>
                        </li>
                        <li class="nav-item">
                            <span class="nav-link">
                                <i class="bi bi-person-circle"></i> {{ user.username }}
                            </span>
                        </li>
                        <li class="nav-item">
                            <a class="nav-link" href="{% url 'logout' %}">
                                <i class="bi bi-box-arrow-right"></i> Logout
                            </a>
                        </li>
                    {% else %}
                        <li class="nav-item">
                            <a class="nav-link" href="{% url 'login' %}">Login</a>
                        </li>
                        <li class="nav-item">
                            <a class="nav-link" href="{% url 'register' %}">Register</a>
                        </li>
                    {% endif %}
                </ul>
            </div>
        </div>
    </nav>

    <div class="container main-content">
        {% if messages %}
            {% for message in messages %}
                <div class="alert alert-{{ message.tags }} alert-dismissible fade show" role="alert">
                    {{ message }}
                    <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
                </div>
            {% endfor %}
        {% endif %}

        {% block content %}
        {% endblock %}
    </div>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
    {% block extra_js %}{% endblock %}
</body>
</html>
```

**Key Concepts:**

1. **Block Tags**: `{% block title %}` - Define sections that child templates can override
2. **URL Tag**: `{% url 'task_list' %}` - Generate URLs by name
3. **If Statement**: `{% if user.is_authenticated %}` - Conditional rendering
4. **For Loop**: `{% for message in messages %}` - Iterate over items
5. **Variable**: `{{ user.username }}` - Display variable value

---

### Task List Template

**File: `tasks/templates/tasks/task_list.html`**

```html
{% extends 'tasks/base.html' %}

{% block title %}My Tasks - Task Manager{% endblock %}

{% block content %}
<div class="row mb-4">
    <div class="col-md-12">
        <div class="card">
            <div class="card-body">
                <h2 class="card-title mb-4">
                    <i class="bi bi-list-check"></i> My Tasks
                </h2>
                
                <!-- Search and Filter Form -->
                <form method="GET" class="row g-3 mb-4">
                    <div class="col-md-4">
                        
                               placeholder="Search tasks..." value="{{ search_query|default:'' }}">
                    </div>
                    <div class="col-md-3">
                        <select name="status" class="form-control">
                            <option value="">All Status</option>
                            <option value="pending" {% if status_filter == 'pending' %}selected{% endif %}>
                                Pending
                            </option>
                            <option value="in_progress" {% if status_filter == 'in_progress' %}selected{% endif %}>
                                In Progress
                            </option>
                            <option value="completed" {% if status_filter == 'completed' %}selected{% endif %}>
                                Completed
                            </option>
                        </select>
                    </div>
                    <div class="col-md-3">
                        <select name="priority" class="form-control">
                            <option value="">All Priorities</option>
                            <option value="high" {% if priority_filter == 'high' %}selected{% endif %}>High</option>
                            <option value="medium" {% if priority_filter == 'medium' %}selected{% endif %}>Medium</option>
                            <option value="low" {% if priority_filter == 'low' %}selected{% endif %}>Low</option>
                        </select>
                    </div>
                    <div class="col-md-2">
                        <button type="submit" class="btn btn-primary w-100">
                            <i class="bi bi-search"></i> Filter
                        </button>
                    </div>
                </form>

                <!-- Task Statistics -->
                <div class="row mb-4">
                    <div class="col-md-4">
                        <div class="card bg-primary text-white">
                            <div class="card-body text-center">
                                <h5>Total Tasks</h5>
                                <h2>{{ tasks.count }}</h2>
                            </div>
                        </div>
                    </div>
                    <div class="col-md-4">
                        <div class="card bg-success text-white">
                            <div class="card-body text-center">
                                <h5>Completed</h5>
                                <h2>{{ tasks|length }}</h2>
                            </div>
                        </div>
                    </div>
                    <div class="col-md-4">
                        <div class="card bg-warning text-white">
                            <div class="card-body text-center">
                                <h5>Pending</h5>
                                <h2>{{ tasks|length }}</h2>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Task List -->
                {% if tasks %}
                    <div class="row">
                        {% for task in tasks %}
                            <div class="col-md-6 mb-3">
                                <div class="card {% if task.is_completed %}task-completed border-success{% endif %}">
                                    <div class="card-body">
                                        <div class="d-flex justify-content-between align-items-start mb-2">
                                            <h5 class="card-title {% if task.is_completed %}text-decoration-line-through text-muted{% endif %}">
                                                {{ task.title }}
                                            </h5>
                                            <span class="badge badge-priority-{{ task.priority }}">
                                                {{ task.get_priority_display }}
                                            </span>
                                        </div>
                                        
                                        <p class="card-text text-muted">
                                            {{ task.description|truncatewords:20 }}
                                        </p>
                                        
                                        <div class="mb-2">
                                            <span class="badge bg-info">{{ task.get_status_display }}</span>
                                            {% if task.due_date %}
                                                <span class="badge bg-secondary">
                                                    <i class="bi bi-calendar"></i> {{ task.due_date|date:"M d, Y" }}
                                                </span>
                                            {% endif %}
                                        </div>
                                        
                                        <div class="btn-group btn-group-sm" role="group">
                                            <a href="{% url 'task_detail' task.pk %}" class="btn btn-info">
                                                <i class="bi bi-eye"></i> View
                                            </a>
                                            <a href="{% url 'task_update' task.pk %}" class="btn btn-warning">
                                                <i class="bi bi-pencil"></i> Edit
                                            </a>
                                            <form method="post" action="{% url 'task_toggle_complete' task.pk %}" style="display:inline;">
                                                {{ "{% csrf_token %}" }}
                                                <button type="submit" class="btn btn-success btn-sm">
                                                    <i class="bi bi-check-circle"></i> 
                                                    {% if task.is_completed %}Undo{% else %}Done{% endif %}
                                                </button>
                                            </form>
                                            <a href="{% url 'task_delete' task.pk %}" class="btn btn-danger">
                                                <i class="bi bi-trash"></i>
                                            </a>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        {% endfor %}
                    </div>
                {% else %}
                    <div class="alert alert-info text-center">
                        <i class="bi bi-info-circle"></i> No tasks found. 
                        <a href="{% url 'task_create' %}" class="alert-link">Create your first task!</a>
                    </div>
                {% endif %}
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

**Template Features Explained:**

1. **Template Inheritance**: `{% extends 'tasks/base.html' %}`
2. **Filters**: `{{ task.description|truncatewords:20 }}` - Limit words
3. **Date Formatting**: `{{ task.due_date|date:"M d, Y" }}`
4. **Default Values**: `{{ search_query|default:'' }}`
5. **Method Calls**: `{{ task.get_priority_display }}`
6. **Conditional Classes**: `{% if task.is_completed %}task-completed{% endif %}`

---

### Task Detail Template

**File: `tasks/templates/tasks/task_detail.html`**

```html
{% extends 'tasks/base.html' %}

{% block title %}{{ task.title }} - Task Manager{% endblock %}

{% block content %}
<div class="row justify-content-center">
    <div class="col-md-8">
        <div class="card">
            <div class="card-body">
                <div class="d-flex justify-content-between align-items-start mb-3">
                    <h2 class="card-title">{{ task.title }}</h2>
                    <span class="badge badge-priority-{{ task.priority }} fs-6">
                        {{ task.get_priority_display }}
                    </span>
                </div>
                
                <div class="mb-3">
                    <span class="badge bg-info fs-6">{{ task.get_status_display }}</span>
                    {% if task.is_completed %}
                        <span class="badge bg-success fs-6">
                            <i class="bi bi-check-circle"></i> Completed
                        </span>
                    {% endif %}
                </div>
                
                <div class="mb-4">
                    <h5>Description:</h5>
                    <p class="text-muted">
                        {% if task.description %}
                            {{ task.description|linebreaks }}
                        {% else %}
                            <em>No description provided</em>
                        {% endif %}
                    </p>
                </div>
                
                <div class="row mb-4">
                    <div class="col-md-6">
                        <p><strong>Created By:</strong> {{ task.created_by.username }}</p>
                        <p><strong>Created At:</strong> {{ task.created_at|date:"F d, Y H:i" }}</p>
                    </div>
                    <div class="col-md-6">
                        <p><strong>Updated At:</strong> {{ task.updated_at|date:"F d, Y H:i" }}</p>
                        {% if task.due_date %}
                            <p><strong>Due Date:</strong> {{ task.due_date|date:"F d, Y" }}</p>
                        {% endif %}
                    </div>
                </div>
                
                <div class="btn-group" role="group">
                    <a href="{% url 'task_update' task.pk %}" class="btn btn-warning">
                        <i class="bi bi-pencil"></i> Edit
                    </a>
                    <form method="post" action="{% url 'task_toggle_complete' task.pk %}" style="display:inline;">
                        {{ "{% csrf_token %}" }}
                        <button type="submit" class="btn btn-success">
                            <i class="bi bi-check-circle"></i> 
                            {% if task.is_completed %}Mark Incomplete{% else %}Mark Complete{% endif %}
                        </button>
                    </form>
                    <a href="{% url 'task_delete' task.pk %}" class="btn btn-danger">
                        <i class="bi bi-trash"></i> Delete
                    </a>
                    <a href="{% url 'task_list' %}" class="btn btn-secondary">
                        <i class="bi bi-arrow-left"></i> Back
                    </a>
                </div>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

**New Filter:**
- `linebreaks`: Converts newlines to `<br>` and `<p>` tags

---

## Django Forms

Forms handle user input, validation, and data cleaning.

**Note:** We already created the `TaskForm` in the previous section ([FBV_Views_and_URLs.md](FBV_Views_and_URLs.md)). In this section, we'll expand on forms and create additional forms for user authentication.

### Expanding the forms.py File

Open the existing `tasks/forms.py` file and add the user registration form:

```python
from django import forms
from django.contrib.auth.models import User
from django.contrib.auth.forms import UserCreationForm
from .models import Task

# TaskForm already created in previous section
class TaskForm(forms.ModelForm):
    """Form for creating and updating tasks"""
    
    class Meta:
        model = Task
        fields = ['title', 'description', 'priority', 'status', 'due_date']
        widgets = 
            'title': forms.TextInput(attrs=
                'class': 'form-control',
                'placeholder': 'Enter task title',
                'required': True
            }),
            'description': forms.Textarea(attrs=
                'class': 'form-control',
                'rows': 4,
                'placeholder': 'Enter task description'
            }),
            'priority': forms.Select(attrs=
                'class': 'form-control'
            }),
            'status': forms.Select(attrs=
                'class': 'form-control'
            }),
            'due_date': forms.DateInput(attrs=
                'class': 'form-control',
                'type': 'date'
            }),
        labels = 
            'title': 'Task Title',
            'description': 'Description',
            'priority': 'Priority Level',
            'status': 'Current Status',
            'due_date': 'Due Date',
        help_texts = 
            'title': 'Enter a clear, concise title for your task',
            'due_date': 'Optional: Set a deadline for this task',
    
    def clean_title(self):
        title = self.cleaned_data.get('title')
        if len(title) < 3:
            raise forms.ValidationError("Title must be at least 3 characters long")
        return title
    
    def clean_due_date(self):
        due_date = self.cleaned_data.get('due_date')
        if due_date:
            from django.utils import timezone
            if due_date < timezone.now().date():
                raise forms.ValidationError("Due date cannot be in the past")
        return due_date

# Add User Registration Form below

class UserRegisterForm(UserCreationForm):
    email = forms.EmailField(
        required=True,
        widget=forms.EmailInput(attrs=
            'class': 'form-control',
            'placeholder': 'Email address'
        })
    )
    
    class Meta:
        model = User
        fields = ['username', 'email', 'password1', 'password2']
    
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.fields['username'].widget.attrs.update(
            'class': 'form-control',
            'placeholder': 'Username'
        })
        self.fields['password1'].widget.attrs.update(
            'class': 'form-control',
            'placeholder': 'Password'
        })
        self.fields['password2'].widget.attrs.update(
            'class': 'form-control',
            'placeholder': 'Confirm Password'
        })
    
    def clean_email(self):
        email = self.cleaned_data.get('email')
        if User.objects.filter(email=email).exists():
            raise forms.ValidationError("This email is already registered")
        return email
```

### Form Types

**ModelForm**
- Automatically creates form from model
- Handles model saving
- Best for CRUD operations

**Form**
- Manual field definition
- More control
- Use for non-model forms (search, contact, etc.)

### Form Validation

**Field-Level Validation**
```python
def clean_fieldname(self):
    data = self.cleaned_data.get('fieldname')
    # Validate data
    if not valid:
        raise forms.ValidationError("Error message")
    return data
```

**Form-Level Validation**
```python
def clean(self):
    cleaned_data = super().clean()
    field1 = cleaned_data.get('field1')
    field2 = cleaned_data.get('field2')
    
    if field1 and field2:
        # Cross-field validation
        if field1 > field2:
            raise forms.ValidationError("Field1 must be less than Field2")
    
    return cleaned_data
```

---

### Task Form Template

**File: `tasks/templates/tasks/task_form.html`**

```html
{% extends 'tasks/base.html' %}

{% block title %}{{ action }} Task - Task Manager{% endblock %}

{% block content %}
<div class="row justify-content-center">
    <div class="col-md-8">
        <div class="card">
            <div class="card-body">
                <h2 class="card-title mb-4">
                    <i class="bi bi-{% if action == 'Create' %}plus-circle{% else %}pencil{% endif %}"></i> 
                    {{ action }} Task
                </h2>
                
                <form method="POST" novalidate>
                    {{ "{% csrf_token %}" }}
                    
                    {% if form.non_field_errors %}
                        <div class="alert alert-danger">
                            {{ form.non_field_errors }}
                        </div>
                    {% endif %}
                    
                    {% for field in form %}
                        <div class="mb-3">
                            <label for="{{ field.id_for_label }}" class="form-label">
                                {{ field.label }}
                                {% if field.field.required %}
                                    <span class="text-danger">*</span>
                                {% endif %}
                            </label>
                            {{ field }}
                            {% if field.errors %}
                                <div class="text-danger small mt-1">
                                    {% for error in field.errors %}
                                        <div>{{ error }}</div>
                                    {% endfor %}
                                </div>
                            {% endif %}
                            {% if field.help_text %}
                                <small class="form-text text-muted d-block">{{ field.help_text }}</small>
                            {% endif %}
                        </div>
                    {% endfor %}
                    
                    <div class="d-flex gap-2">
                        <button type="submit" class="btn btn-primary">
                            <i class="bi bi-save"></i> {{ action }} Task
                        </button>
                        <a href="{% url 'task_list' %}" class="btn btn-secondary">
                            <i class="bi bi-x-circle"></i> Cancel
                        </a>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

**Form Template Features:**

1. **CSRF Token**: `{% csrf_token %}` - Security against CSRF attacks (required!)
2. **Form Iteration**: `{% for field in form %}` - Loop through all fields
3. **Field Properties**: `{{ field.label }}`, `{{ field.errors }}`, `{{ field.help_text }}`
4. **Error Display**: Show validation errors
5. **Required Fields**: Mark with asterisk

---

### Delete Confirmation Template

**File: `tasks/templates/tasks/task_confirm_delete.html`**

```html
{% extends 'tasks/base.html' %}

{% block title %}Delete Task - Task Manager{% endblock %}

{% block content %}
<div class="row justify-content-center">
    <div class="col-md-6">
        <div class="card border-danger">
            <div class="card-body">
                <h2 class="card-title text-danger mb-4">
                    <i class="bi bi-exclamation-triangle"></i> Confirm Delete
                </h2>
                
                <p class="lead">Are you sure you want to delete this task?</p>
                
                <div class="alert alert-warning">
                    <strong>Task:</strong> {{ task.title }}<br>
                    <strong>Priority:</strong> {{ task.get_priority_display }}<br>
                    <strong>Status:</strong> {{ task.get_status_display }}
                </div>
                
                <p class="text-danger">
                    <i class="bi bi-info-circle"></i> This action cannot be undone.
                </p>
                
                <form method="POST">
                    {{ "{% csrf_token %}" }}
                    <div class="d-flex gap-2">
                        <button type="submit" class="btn btn-danger">
                            <i class="bi bi-trash"></i> Yes, Delete
                        </button>
                        <a href="{% url 'task_list' %}" class="btn btn-secondary">
                            <i class="bi bi-x-circle"></i> Cancel
                        </a>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

---

### Authentication Templates

**File: `tasks/templates/tasks/login.html`**

```html
{% extends 'tasks/base.html' %}

{% block title %}Login - Task Manager{% endblock %}

{% block content %}
<div class="row justify-content-center">
    <div class="col-md-5">
        <div class="card">
            <div class="card-body">
                <h2 class="card-title text-center mb-4">
                    <i class="bi bi-box-arrow-in-right"></i> Login
                </h2>
                
                <form method="POST">
                    {{ "{% csrf_token %}" }}
                    <div class="mb-3">
                        <label for="username" class="form-label">Username</label>
                        <input type="text" name="username" id="username" class="form-control" required>
                    </div>
                    <div class="mb-3">
                        <label for="password" class="form-label">Password</label>
                        <input type="password" name="password" id="password" class="form-control" required>
                    </div>
                    <button type="submit" class="btn btn-primary w-100">
                        <i class="bi bi-box-arrow-in-right"></i> Login
                    </button>
                </form>
                
                <hr>
                <p class="text-center mb-0">
                    Don't have an account? 
                    <a href="{% url 'register' %}">Register here</a>
                </p>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

**File: `tasks/templates/tasks/register.html`**

```html
{% extends 'tasks/base.html' %}

{% block title %}Register - Task Manager{% endblock %}

{% block content %}
<div class="row justify-content-center">
    <div class="col-md-6">
        <div class="card">
            <div class="card-body">
                <h2 class="card-title text-center mb-4">
                    <i class="bi bi-person-plus"></i> Create Account
                </h2>
                
                <form method="POST" novalidate>
                    {{ "{% csrf_token %}" }}
                    
                    {% if form.non_field_errors %}
                        <div class="alert alert-danger">
                            {{ form.non_field_errors }}
                        </div>
                    {% endif %}
                    
                    {% for field in form %}
                        <div class="mb-3">
                            <label for="{{ field.id_for_label }}" class="form-label">
                                {{ field.label }}
                            </label>
                            {{ field }}
                            {% if field.errors %}
                                <div class="text-danger small mt-1">
                                    {% for error in field.errors %}
                                        <div>{{ error }}</div>
                                    {% endfor %}
                                </div>
                            {% endif %}
                            {% if field.help_text %}
                                <small class="form-text text-muted d-block">{{ field.help_text }}</small>
                            {% endif %}
                        </div>
                    {% endfor %}
                    
                    <button type="submit" class="btn btn-primary w-100">
                        <i class="bi bi-person-plus"></i> Register
                    </button>
                </form>
                
                <hr>
                <p class="text-center mb-0">
                    Already have an account? 
                    <a href="{% url 'login' %}">Login here</a>
                </p>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

---

## Common Template Filters

### String Filters

```django
{{ value|lower }}              {# Convert to lowercase #}
{{ value|upper }}              {# Convert to uppercase #}
{{ value|title }}              {# Title Case #}
{{ value|capfirst }}           {# Capitalize first letter #}
{{ value|truncatewords:10 }}   {# Limit to 10 words #}
{{ value|truncatechars:50 }}   {# Limit to 50 characters #}
{{ value|linebreaks }}         {# Convert newlines to <p> and <br> #}
{{ value|striptags }}          {# Remove HTML tags #}
{{ value|slugify }}            {# Convert to slug #}
```

### Number Filters

```django
{{ value|add:5 }}              {# Add 5 #}
{{ value|floatformat:2 }}      {# Format to 2 decimal places #}
```

### Date Filters

```django
{{ value|date:"Y-m-d" }}       {# 2024-01-23 #}
{{ value|date:"F d, Y" }}      {# January 23, 2024 #}
{{ value|time:"H:i" }}         {# 14:30 #}
{{ value|timesince }}          {# "2 hours ago" #}
{{ value|timeuntil }}          {# "in 3 days" #}
```

### List Filters

```django
{{ value|length }}             {# Number of items #}
{{ value|first }}              {# First item #}
{{ value|last }}               {# Last item #}
{{ value|join:", " }}          {# Join with comma #}
{{ value|slice:":5" }}         {# First 5 items #}
```

### Default Filters

```django
{{ value|default:"N/A" }}      {# Show "N/A" if empty #}
{{ value|default_if_none:"N/A" }} {# Show "N/A" if None #}
```

---

## Template Tags

### Control Flow

```django
{% if condition %}
    ...
{% elif other_condition %}
    ...
{% else %}
    ...
{% endif %}

{% for item in items %}
    {{ item }}
{% empty %}
    No items found
{% endfor %}
```

### Loop Variables

```django
{% for task in tasks %}
    {{ forloop.counter }}      {# 1, 2, 3, ... #}
    {{ forloop.counter0 }}     {# 0, 1, 2, ... #}
    {{ forloop.first }}        {# True on first iteration #}
    {{ forloop.last }}         {# True on last iteration #}
    {{ forloop.parentloop }}   {# Access parent loop #}
{% endfor %}
```

### Include

```django
{% include 'tasks/task_card.html' with task=task %}
```

### Static Files

```django
{{ "{% load static %}" }}
<link rel="stylesheet" href="{{ "{% static 'css/style.css' %}" }}">
<img src="{% static 'images/logo.png' %}">
```

---

## Additional Templates

### Task Restore Confirmation Template

**File: `tasks/templates/tasks/task_restore_confirm.html`**

```html
{% extends 'tasks/base.html' %}

{% block title %}Restore Task - Task Manager{% endblock %}

{% block content %}
<div class="row justify-content-center">
    <div class="col-md-6">
        <div class="card border-success">
            <div class="card-body">
                <h2 class="card-title text-success mb-4">
                    <i class="bi bi-arrow-counterclockwise"></i> Restore Task
                </h2>
                
                <p class="lead">Are you sure you want to restore this task?</p>
                
                <div class="alert alert-info">
                    <strong>Task:</strong> {{ task.title }}<br>
                    <strong>Priority:</strong> {{ task.get_priority_display }}<br>
                    <strong>Status:</strong> {{ task.get_status_display }}<br>
                    <strong>Deleted:</strong> {{ task.deleted_at|date:"F d, Y H:i" }}
                </div>
                
                <form method="POST">
                    {{ "{% csrf_token %}" }}
                    <div class="d-flex gap-2">
                        <button type="submit" class="btn btn-success">
                            <i class="bi bi-arrow-counterclockwise"></i> Yes, Restore
                        </button>
                        <a href="{% url 'task_trash' %}" class="btn btn-secondary">
                            <i class="bi bi-x-circle"></i> Cancel
                        </a>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

### Task Trash Template

**File: `tasks/templates/tasks/task_trash.html`**

```html
{% extends 'tasks/base.html' %}

{% block title %}Trash - Task Manager{% endblock %}

{% block content %}
<div class="row mb-4">
    <div class="col-md-12">
        <div class="card">
            <div class="card-body">
                <h2 class="card-title mb-4">
                    <i class="bi bi-trash"></i> Trash
                </h2>
                
                {% if deleted_tasks %}
                    <p class="text-muted">
                        <i class="bi bi-info-circle"></i> 
                        Deleted tasks are kept here. You can restore or permanently delete them.
                    </p>
                    
                    <div class="table-responsive">
                        <table class="table table-hover">
                            <thead>
                                <tr>
                                    <th>Title</th>
                                    <th>Priority</th>
                                    <th>Deleted At</th>
                                    <th>Actions</th>
                                </tr>
                            </thead>
                            <tbody>
                                {% for task in deleted_tasks %}
                                    <tr>
                                        <td>{{ task.title }}</td>
                                        <td>
                                            <span class="badge badge-priority-{{ task.priority }}">
                                                {{ task.get_priority_display }}
                                            </span>
                                        </td>
                                        <td>{{ task.deleted_at|date:"M d, Y H:i" }}</td>
                                        <td>
                                            
                                               class="btn btn-sm btn-success">
                                                <i class="bi bi-arrow-counterclockwise"></i> Restore
                                            </a>
                                        </td>
                                    </tr>
                                {% endfor %}
                            </tbody>
                        </table>
                    </div>
                {% else %}
                    <div class="alert alert-info text-center">
                        <i class="bi bi-info-circle"></i> Trash is empty.
                    </div>
                {% endif %}
                
                <a href="{% url 'task_list' %}" class="btn btn-secondary mt-3">
                    <i class="bi bi-arrow-left"></i> Back to Tasks
                </a>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

---

## Working with Forms Using DTL

### Why Use Standard Django Forms?

Standard Django forms with DTL (Django Template Language) provide you with:
- **Simplicity**: No JavaScript required for basic CRUD operations
- **Security**: Built-in CSRF protection
- **Validation**: Server-side validation with clear error messages
- **Accessibility**: Works without JavaScript enabled
- **SEO-friendly**: Full page loads are better for search engines
- **Maintainability**: Easier to debug and maintain

### Form Handling Pattern

Django follows a simple pattern for form handling:

**1. GET Request** - Display the form
**2. POST Request** - Process the form
**3. Validation** - Check if data is valid
**4. Success** - Save and redirect
**5. Error** - Show form with errors

**Example View:**
```python
@login_required(login_url='login')
def task_create(request):
    if request.method == 'POST':
        form = TaskForm(request.POST)
        if form.is_valid():
            task = form.save(commit=False)
            task.created_by = request.user
            task.save()
            messages.success(request, 'Task created successfully!')
            return redirect('task_list')
    else:
        form = TaskForm()
    return render(request, 'tasks/task_form.html', {'form': form})
```

**Example Template:**
```django
<form method="POST">
    {{ "{% csrf_token %}" }}
    {{ "{{ form.as_p }}" }}
    <button type="submit" class="btn btn-primary">Submit</button>
</form>
```

### Example 1: Search and Filter with GET Parameters

**HTML Template:**
```django
{# Search and Filter Form #}
<form method="GET" class="row g-3 mb-4">
    <div class="col-md-4">
        
               placeholder="Search tasks..." value="{{ search_value|default:'' }}">
    </div>
    <div class="col-md-3">
        <select name="status" class="form-control">
            <option value="">All Status</option>
            <option value="pending" {% if status_filter == 'pending' %}selected{% endif %}>Pending</option>
            <option value="in_progress" {% if status_filter == 'in_progress' %}selected{% endif %}>In Progress</option>
            <option value="completed" {% if status_filter == 'completed' %}selected{% endif %}>Completed</option>
        </select>
    </div>
    <div class="col-md-3">
        <select name="priority" class="form-control">
            <option value="">All Priorities</option>
            <option value="high" {% if priority_filter == 'high' %}selected{% endif %}>High</option>
            <option value="medium" {% if priority_filter == 'medium' %}selected{% endif %}>Medium</option>
            <option value="low" {% if priority_filter == 'low' %}selected{% endif %}>Low</option>
        </select>
    </div>
    <div class="col-md-2">
        <button type="submit" class="btn btn-primary w-100">
            <i class="bi bi-search"></i> Filter
        </button>
    </div>
</form>

{# Display filtered results #}
{% for task in tasks %}
    <div class="card mb-3">
        <div class="card-body">
            <h5>{{ task.title }}</h5>
            <p>{{ task.description|truncatewords:20 }}</p>
        </div>
    </div>
{% empty %}
    <p>No tasks found.</p>
{% endfor %}
```

### Example 2: Toggle Task Completion (POST-Only for Security)

**HTML Template:**
```django
{# IMPORTANT: Always use POST for state-changing actions #}
<form method="post" action="{% url 'task_toggle_complete' task.pk %}" style="display: inline;">
    {{ "{% csrf_token %}" }}
    <button type="submit" class="btn btn-success">
        <i class="bi bi-check-circle"></i> 
        {% if task.is_completed %}Mark Incomplete{% else %}Mark Complete{% endif %}
    </button>
</form>
```

**Why POST-only?**
- ✅ Prevents accidental toggles from browser prefetch
- ✅ Prevents crawler-triggered state changes
- ✅ Ensures CSRF protection is applied
- ✅ Follows Django security best practices

### Example 3: Delete Confirmation Pattern

**HTML Template:**
```django
{# Link to delete confirmation page #}
<a href="{% url 'task_delete' task.pk %}" class="btn btn-danger">
    <i class="bi bi-trash"></i> Delete
</a>

{# On the confirmation page (task_confirm_delete.html) #}
<form method="POST">
    {{ "{% csrf_token %}" }}
    <p>Are you sure you want to delete "{{ task.title }}"?</p>
    <button type="submit" class="btn btn-danger">Yes, Delete</button>
    <a href="{% url 'task_list' %}" class="btn btn-secondary">Cancel</a>
</form>
```

### Example 4: Pagination with DTL

**HTML Template:**
```django
{# Pagination controls #}
{% if is_paginated %}
    <nav aria-label="Page navigation">
        <ul class="pagination">
            {% if page_obj.has_previous %}
                <li class="page-item">
                    <a class="page-link" href="?page=1">First</a>
                </li>
                <li class="page-item">
                    <a class="page-link" href="?page={{ page_obj.previous_page_number }}">Previous</a>
                </li>
            {% endif %}
            
            <li class="page-item active">
                <span class="page-link">
                    Page {{ page_obj.number }} of {{ page_obj.paginator.num_pages }}
                </span>
            </li>
            
            {% if page_obj.has_next %}
                <li class="page-item">
                    <a class="page-link" href="?page={{ page_obj.next_page_number }}">Next</a>
                </li>
                <li class="page-item">
                    <a class="page-link" href="?page={{ page_obj.paginator.num_pages }}">Last</a>
                </li>
            {% endif %}
        </ul>
    </nav>
{% endif %}
```

---

## Summary

You've learned:

✅ Django template syntax (DTL - Django Template Language, NOT Jinja2)
✅ Template inheritance with `{% extends %}` and `{% block %}`
✅ Creating reusable base templates
✅ Working with template variables `{{ variable }}`
✅ Using template tags `{% tag %}`
✅ Applying template filters `{{ value|filter }}`
✅ Creating and validating forms with ModelForm
✅ Rendering forms in templates
✅ CSRF protection with `{% csrf_token %}`
✅ Building complete UI without JavaScript
✅ Search and filter patterns with GET parameters
✅ Form submission patterns with POST
✅ Pagination in templates

### Next Steps

👉 **Continue to:** [FBV_Complete_Project.md](FBV_Complete_Project.md)

In the next section, we'll:
- Put everything together
- Complete the Task Management System
- Add authentication views
- Test the application
- Review the complete FBV implementation

---

## Common Mistakes to Avoid

### Mistake 1: Using `{{ "{{ variable|safe }}" }}` Without Sanitization
**Error**: XSS (Cross-Site Scripting) vulnerability
**Solution**: Only use `|safe` filter on trusted content
```django
{# Good - Auto-escaped #}
{{ "{{ user_input }}" }}

{{ "{{ user_input|safe }}" }}
```

### Mistake 2: Forgetting `{{ "{% csrf_token %}" }}` in Forms
**Error**: CSRF verification failed
**Solution**: Always include CSRF token in POST forms
```django
{# Good #}
<form method="POST">
    {{ "{% csrf_token %}" }}
    {{ "{{ form.as_p }}" }}
</form>

{# Bad - Will fail #}
<form method="POST">
    {{ "{{ form.as_p }}" }}
</form>
```

### Mistake 3: Using Jinja2 Syntax Instead of DTL
**Error**: Template syntax errors
**Solution**: Use Django Template Language, not Jinja2
```django
{# Good - Django Template Language #}
{% url 'task_list' %}
{{ task.title }}
{% for task in tasks %}

{# Bad - Jinja2 syntax (doesn't work in Django) #}
{{ url_for('task_list') }}
{{ task['title'] }}
{% for task in tasks %}
```

### Mistake 4: Not Preserving Filter Values in Forms
**Error**: Filters reset after form submission
**Solution**: Use `value="{{ filter_value|default:'' }}"` to preserve values
```django
{# Good - Preserves filter value #}
<input type="text" name="search" value="{{ search_query|default:'' }}">

{# Bad - Loses value after submit #}
<input type="text" name="search">
```

### Mistake 5: Hardcoding URLs Instead of Using `{{ "{% url %}" }}`
**Error**: URLs break when URL patterns change
**Solution**: Always use `{% url %}` tag
```django
{# Good #}
<a href="{% url 'task_detail' task.pk %}">View</a>

{# Bad #}
<a href="/task/{{ task.pk }}/">View</a>
```

---

**Ready to build the complete project!** Continue to [FBV_Complete_Project.md](FBV_Complete_Project.md) to see everything working together.

### Mistake 1: Using `{{ "{{ variable|safe }}" }}` Without Sanitization
**Error**: XSS (Cross-Site Scripting) vulnerability
**Solution**: Only use `|safe` filter on trusted content
```django
{# Good - Auto-escaped #}
{{ "{{ user_input }}" }}

{# Bad - Dangerous if user_input contains scripts #}
{{ "{{ user_input|safe }}" }}
```

### Mistake 2: Not Using `{{ "{% load static %}" }}` for Static Files
**Error**: Static files don't load
**Solution**: Always load static tag at the top of templates
```django
{{ "{% load static %}" }}
<link rel="stylesheet" href="{{ "{% static 'css/style.css' %}" }}">
```

### Mistake 3: Forgetting form.is_valid() Check
**Error**: Invalid data saved to database
**Solution**: Always validate forms before saving
```python
# Good
if form.is_valid():
    form.save()

# Bad
form.save()  # Skips validation!
```

### Mistake 4: Not Displaying Form Errors
**Error**: Users don't know why form submission failed
**Solution**: Always display form errors in templates
```django
{% if form.errors %}
    <div class="alert alert-danger">
        {{ form.errors }}
    </div>
{% endif %}
```

### Mistake 5: Using Wrong Template Tag Syntax
**Error**: `TemplateSyntaxError`
**Solution**: Remember the difference between `{{ }}` and `{% %}`
```django
{# Variables - use {{ }} #}
{{ task.title }}

{# Tags - use {% %} #}
{% for task in tasks %}
{% endfor %}
```

---

**Templates and Forms are ready! Let's complete the project! 🎉**
