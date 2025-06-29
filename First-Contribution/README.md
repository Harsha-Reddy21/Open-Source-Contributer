# Contributing to Full Stack FastAPI Template

## Project Overview

The Full Stack FastAPI Template is a comprehensive starter template for building web applications with FastAPI backend and React frontend. It provides a solid foundation with many features already implemented, making it an excellent choice for rapid application development.

### Why I Chose This Project

I chose this project because:

1. It combines modern technologies (FastAPI, React, TypeScript)
2. It follows best practices for full-stack development
3. It has a clean architecture that can be extended
4. It addresses common needs like authentication, database integration, and API documentation

## Issue Addressed: Adding Item Categories Feature

I identified that the project lacked a way to categorize items, which is a common requirement in many applications. Users need to organize their items by category and filter them accordingly.

### Implementation Details

I implemented a complete Item Categories feature that includes:

1. **Backend Changes**:
   - Added a `category` field to the `ItemBase` model
   - Created a migration to add the category column to the database
   - Updated the API to support filtering items by category
   - Added an endpoint to retrieve unique categories

2. **Frontend Changes**:
   - Added category field to item creation and editing forms
   - Created a CategoryFilter component for filtering items
   - Updated the items table to display categories
   - Extended the client to support the new field and endpoint

## Code Changes

### Backend Model Changes
```python
# Shared properties
class ItemBase(SQLModel):
    title: str = Field(min_length=1, max_length=255)
    description: str | None = Field(default=None, max_length=255)
    category: str | None = Field(default=None, max_length=100)
```

### API Endpoint for Category Filtering
```python
@router.get("/", response_model=ItemsPublic)
def read_items(
    session: SessionDep, 
    current_user: CurrentUser, 
    skip: int = 0, 
    limit: int = 100,
    category: str | None = None
) -> Any:
    # Base query
    if current_user.is_superuser:
        query = select(Item)
        count_query = select(func.count()).select_from(Item)
    else:
        query = select(Item).where(Item.owner_id == current_user.id)
        count_query = select(func.count()).select_from(Item).where(Item.owner_id == current_user.id)
    
    # Apply category filter if provided
    if category:
        query = query.where(Item.category == category)
        count_query = count_query.where(Item.category == category)
```

### Frontend Category Filter Component
```tsx
const CategoryFilter = ({ categories, selectedCategory, route }: CategoryFilterProps) => {
  const navigate = useNavigate({ from: route })

  const handleCategoryChange = (category: string | null) => {
    navigate({
      search: (prev: { [key: string]: string }) => ({
        ...prev,
        category: category || undefined,
        page: 1,
      }),
    })
  }

  return (
    <HStack spacing={2} mb={4} flexWrap="wrap">
      <Text>Filter by category:</Text>
      <Button 
        leftIcon={<FiFilter />} 
        variant="outline" 
        size="sm"
        onClick={() => handleCategoryChange(null)}
        colorPalette={!selectedCategory ? "blue" : "gray"}
      >
        All
      </Button>
      {categories.map((category) => (
        <Button
          key={category}
          variant="outline"
          size="sm"
          onClick={() => handleCategoryChange(category)}
          colorPalette={selectedCategory === category ? "blue" : "gray"}
          ml={2}
          mb={2}
        >
          {category}
        </Button>
      ))}
    </HStack>
  )
}
```

## Benefits of the Feature

1. **Better Organization**: Users can now organize their items by category
2. **Improved Searchability**: Filtering makes it easier to find specific items
3. **Enhanced User Experience**: More structured data presentation
4. **Business Intelligence**: Categories enable reporting and analytics

This enhancement improves the organization and searchability of items while maintaining the project's clean architecture and design patterns.

## Pull Request

I've submitted a pull request with these changes:
[PR #123: Add Item Categories Feature](https://github.com/yourusername/full-stack-fastapi-template/pull/123)

The PR includes:
- Database model changes
- Migration script
- API endpoint updates
- Frontend components for category management
- Documentation updates
