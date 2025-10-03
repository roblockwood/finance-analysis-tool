# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a single-page financial analysis tool built with vanilla JavaScript and D3.js. The application allows users to track assets and debts, visualize financial flows using Sankey diagrams, project future values with customizable scenarios, and categorize items using a fixed set of predefined categories with configurable growth rates.

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
- `assetCategory`: Category for asset portion (if assetValue > 0)
- `debtCategory`: Category for debt portion (if debtValue > 0)
- `debtPayoffYears`: Years until debt is paid off (optional, used for amortization)

#### Fixed Categories
The application uses predefined categories defined in the `CATEGORIES` object:
- **Asset Categories**: Cash & Savings, Investments, Stocks, Bonds, Retirement Accounts, Real Estate, Short Term Rental, Cryptocurrency, Vehicles, Personal Property
- **Debt Categories**: Credit Card, Mortgage, Auto Loan, Student Loan, Personal Loan, Other Debt
- **General**: Other

Each category has:
- `color`: Hex color for visualization
- `type`: 'asset', 'debt', or 'general'
- `pessimistic`: Lower growth/interest rate scenario
- `neutral`: Expected growth/interest rate
- `optimistic`: Higher growth/interest rate scenario

Custom category rates can be configured via the Settings UI and are persisted to localStorage.

#### Core Features
1. **LocalStorage Persistence**: Automatic save/load with data migration support
2. **Dual-Category System**: Separate asset and debt categories for items with both
3. **Projection Modeling**: Calculate future values at intervals (6mo, 1yr, 2yr, 5yr, 10yr, 15yr, 20yr, 30yr)
4. **Scenario Analysis**: Pessimistic, neutral, and optimistic rate scenarios
5. **Debt Amortization**: Proper loan amortization calculations with payoff timelines
6. **Category-Based Filtering**: Dynamic filtering by asset categories
7. **Sankey Visualization**: D3.js flow diagram with smart grouping algorithm
8. **Category Breakdown**: Aggregated statistics with annual projections per category
9. **Settings UI**: Customizable category growth/interest rates
10. **CRUD Operations**: Add, edit, delete items with inline editing UI

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
Data is stored under two keys:
- `'financialItems'`: JSON array of financial items with id, name, assetValue, debtValue, netValue, assetCategory, debtCategory, and debtPayoffYears
- `'customCategoryRates'`: JSON object of custom category rates (optional)

Always maintain backward compatibility when modifying the data structure.

## Common Modification Patterns

### Adding New Categories
1. Add new category to the `CATEGORIES` object with color, type, and three rate scenarios (pessimistic, neutral, optimistic)
2. Add category option to both the asset/debt form dropdowns and edit forms
3. Update the `tagToCategoryMap` in migration logic if migrating from old data
4. No other code changes required - visualization, breakdown, and projections will automatically use the new category

### Modifying Category Rates
Users can customize rates via the Settings UI, or developers can update the default rates in the `CATEGORIES` object. Rates are stored as decimals (0.10 = 10%).

### Projection Calculations
- **Asset growth**: Uses compound interest formula with category growth rates
- **Debt projection**: Uses proper amortization formulas accounting for monthly payments and payoff timelines
- **Annual projections**: Shows estimated yearly earnings (assets) and costs (debts) based on current values

## Deployment

**GitHub Repository**: https://github.com/roblockwood/finance-analysis-tool
**Live Site**: https://roblockwood.github.io/finance-analysis-tool/

Deployed via GitHub Pages from the `gh-pages` branch.
