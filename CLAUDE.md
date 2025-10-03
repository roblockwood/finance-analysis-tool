# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a single-page financial analysis tool built with vanilla JavaScript and D3.js. The application allows users to track assets and debts, visualize financial flows using Sankey diagrams, and categorize items using a fixed set of predefined categories.

## Architecture

### Technology Stack
- **Frontend**: Vanilla JavaScript (ES6+), HTML5, CSS3
- **Visualization**: D3.js v7 with d3-sankey extension
- **Data Storage**: Browser LocalStorage
- **Development Server**: live-server

### Application Structure
The entire application is contained in a single `index.html` file with three main sections:

1. **HTML Structure** (lines 1-670): DOM elements for forms, summaries, visualizations, and item lists
2. **CSS Styling** (lines 9-546): Complete styling including responsive layouts and Sankey chart styling
3. **JavaScript Logic** (lines 673-1520): Application state management, data persistence, and visualization rendering

### Key Components

#### Data Model
Financial items are stored as objects with:
- `id`: Unique timestamp-based identifier
- `name`: Item name
- `assetValue`: Asset value (number)
- `debtValue`: Debt value (number)
- `netValue`: Calculated as assetValue - debtValue
- `category`: Single category string (required)

#### Fixed Categories
The application uses predefined categories defined in the `CATEGORIES` object (lines 927-947):
- **Asset Categories**: Cash & Savings, Investments, Stocks, Bonds, Retirement Accounts, Real Estate, Cryptocurrency, Vehicles, Personal Property
- **Debt Categories**: Credit Card, Mortgage, Auto Loan, Student Loan, Personal Loan, Other Debt
- **General**: Other

Each category has an associated color and type for visualization purposes.

#### Core Features
1. **LocalStorage Persistence** (lines 683-774): Automatic save/load with data migration support from tags to categories
2. **Category-Based Filtering** (lines 779-800): Dynamic filtering system using Set data structure
3. **Sankey Visualization** (lines 957-1370): Complex D3.js flow diagram with smart grouping algorithm
4. **Category Breakdown** (lines 1385-1485): Aggregated statistics by category
5. **CRUD Operations**: Add, edit, delete items with inline editing UI

#### Smart Grouping Algorithm (lines 1017-1097)
The Sankey chart uses an intelligent grouping algorithm:
- Groups items smaller than 3% of total value (configurable threshold)
- Requires minimum 3 items to form a group
- Groups by category or creates "misc" groups for uncategorized items
- Maintains category colors for visual consistency

## Development Commands

### Run Development Server
```bash
npm run dev
# or
npm start
```
Starts live-server on port 3000 and opens index.html

### Install Dependencies
```bash
npm install
```

## Important Implementation Details

### Category System
The application uses a fixed category dropdown (lines 594-617) instead of free-form tag input. Each item must have exactly one category. The `CATEGORIES` object defines available categories with their colors and types.

### Data Migration
The app includes automatic data migration logic (lines 690-768) that:
- Converts old tag-based system to category-based system
- Maps common tag names to appropriate categories using `tagToCategoryMap`
- Removes deprecated 'type' and 'tags' fields
- Recalculates netValue if incorrect
- Saves migrated data automatically

### Collapsible Sections
Two collapsible sections use a shared toggle function (lines 1487-1500):
- Category Breakdown
- Financial Items List

### LocalStorage Schema
Data is stored under the key `'financialItems'` as JSON array. Each item contains: id, name, assetValue, debtValue, netValue, and category. Always maintain backward compatibility when modifying the data structure.

## Common Modification Patterns

### Adding New Categories
1. Add new category to the `CATEGORIES` object (lines 927-947) with color and type
2. Add category option to both the form dropdown (lines 594-617) and edit form (lines 843-862)
3. Update the `tagToCategoryMap` in migration logic (lines 691-725) if migrating from tags
4. No other code changes required - visualization and breakdown will automatically use the new category

### Modifying Category Colors
Update the color value in the `CATEGORIES` object (lines 927-947). The entire app will use the new color automatically.

### Adding New Financial Calculations
1. Update the data model in form submission handler (lines 779-800)
2. Update display logic in `updateSummary()` (lines 912-936)
3. Update visualization in `updateChart()` if visual representation needed

### Modifying Sankey Visualization
- Node positioning: Adjust sankey configuration (lines 1195-1199)
- Grouping threshold: Modify line 1019 (currently 3% of total)
- Label positioning: Update label logic (lines 1305-1368)
