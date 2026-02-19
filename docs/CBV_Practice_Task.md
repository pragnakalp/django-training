# Practice Task: Book Library System (CBV)

## Overview
Build a simple Book Library System using Class-Based Views to practice the concepts learned in CBV modules.

**Focus**: ListView, DetailView, CreateView, UpdateView, DeleteView, and Custom Mixins

---

## What You'll Build

A book library management system with the following pages:

### 1. **Book List Page** (`/books/`)
- Display all books in a paginated list (10 books per page)
- Show: Book title, author, genre, publication year, availability status
- Include search functionality (search by title or author)
- Filter by genre (Fiction, Non-Fiction, Science, History, Biography)
- Filter by availability (Available, Borrowed)
- Display statistics: Total books, Available books, Borrowed books

### 2. **Book Detail Page** (`/books/<id>/`)
- Show complete book information
- Display related books (same genre, exclude current book, limit to 5)
- Show "Edit" and "Delete" buttons
- Display borrowing history count (if book has been borrowed before)

### 3. **Add Book Page** (`/books/create/`)
- Form to add a new book
- Fields: Title, Author, Genre, Publication Year, ISBN, Description, Availability Status
- Show success message after creation
- Redirect to book list after successful creation

### 4. **Edit Book Page** (`/books/<id>/update/`)
- Pre-filled form with existing book data
- Same fields as Add Book page
- Show success message after update
- Redirect to book detail page after successful update

### 5. **Delete Book Page** (`/books/<id>/delete/`)
- Confirmation page before deletion
- Show book title and warning message
- "Confirm Delete" and "Cancel" buttons
- Redirect to book list after deletion

### 6. **Borrowed Books Page** (`/books/borrowed/`)
- List only borrowed books
- Same layout as Book List page
- Show when each book was borrowed (use updated_at field)

---

## Technical Requirements

### Models
You need to create a `Book` model with these fields (figure out the field types yourself):
- title
- author
- genre (choices: Fiction, Non-Fiction, Science, History, Biography)
- publication_year
- isbn
- description
- is_available (boolean)
- created_at (auto timestamp)
- updated_at (auto timestamp)

### Views to Implement

1. **BookListView** (ListView)
   - Pagination: 10 items per page
   - Search functionality
   - Filter by genre and availability
   - Add statistics to context

2. **BookDetailView** (DetailView)
   - Display single book
   - Add related books to context

3. **BookCreateView** (CreateView)
   - Form for creating books
   - Success message

4. **BookUpdateView** (UpdateView)
   - Form for editing books
   - Success message

5. **BookDeleteView** (DeleteView)
   - Confirmation page
   - Success message

6. **BorrowedBooksView** (ListView)
   - Filter to show only borrowed books
   - Reuse BookListView logic

### Custom Mixins to Create

1. **SuccessMessageMixin**
   - Automatically add success messages on form submission
   - Reusable across Create and Update views

2. **BookStatsMixin**
   - Add book statistics to context
   - Reusable across list views

---

## What You Need to Figure Out

1. **Model Definition**: Define the Book model with appropriate field types
2. **Form Creation**: Create a ModelForm for Book
3. **URL Patterns**: Set up URL routing for all views
4. **Templates**: Create templates for each page (basic HTML structure)
5. **View Logic**: Implement filtering, searching, and pagination
6. **Mixins**: Create reusable mixins for common functionality

---

## Success Criteria

✅ All 6 pages work correctly  
✅ Pagination works on list pages  
✅ Search finds books by title or author  
✅ Filters work for genre and availability  
✅ Statistics display correctly  
✅ Related books show on detail page  
✅ Forms validate and save data  
✅ Success messages appear after actions  
✅ Delete confirmation works  
✅ Custom mixins are reused across views  

---

## Bonus Challenges (Optional)

1. **Add sorting**: Allow users to sort by title, author, or publication year
2. **Genre statistics**: Show count of books per genre
3. **Recent additions**: Show 5 most recently added books on list page
4. **Export functionality**: Add a button to download book list as CSV
5. **Bulk actions**: Select multiple books and mark as available/borrowed

---

## Getting Started

1. Create a new Django project: `django-admin startproject library`
2. Create a new app: `python manage.py startapp books`
3. Define your Book model
4. Create and run migrations
5. Create your forms
6. Implement views one by one
7. Create templates
8. Set up URLs
9. Test each feature

---

## Expected Project Structure

```
library/
├── manage.py
├── library/
│   ├── settings.py
│   └── urls.py
└── books/
    ├── models.py          # Book model
    ├── forms.py           # BookForm
    ├── views.py           # All CBV views
    ├── mixins.py          # Custom mixins
    ├── urls.py            # URL patterns
    └── templates/
        └── books/
            ├── book_list.html
            ├── book_detail.html
            ├── book_form.html
            ├── book_confirm_delete.html
            └── borrowed_books.html
```

---

## Testing Checklist

- [ ] Create 15+ books with different genres
- [ ] Test pagination (should show 10 per page)
- [ ] Search for books by title
- [ ] Search for books by author
- [ ] Filter by each genre
- [ ] Filter by availability status
- [ ] View book details
- [ ] Check related books appear
- [ ] Create a new book
- [ ] Edit an existing book
- [ ] Delete a book
- [ ] View borrowed books page
- [ ] Verify statistics are correct

---

**Good luck! Focus on understanding how CBV components work together rather than making it perfect. The goal is to practice ListView, DetailView, CreateView, UpdateView, DeleteView, and custom mixins.**
