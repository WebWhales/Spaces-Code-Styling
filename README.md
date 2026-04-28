# Spaces Code Styling

A comprehensive code styling and best practices documentation package designed for AI agents. This package provides detailed guidelines for PHP and JavaScript development to ensure consistent, high-quality code generation.

## 📦 Installation

### Composer (PHP)

Add the repository to your project's `composer.json`:

```json
{
  "repositories": {
    "webwhales/spaces-code-styling": {
      "type": "github",
      "url": "https://github.com/WebWhales/Spaces-Code-Styling"
    }
  },
  "require-dev": {
    "webwhales/spaces-code-styling": "dev-master"
  }
}
```

Then run:
```bash
composer update webwhales/spaces-code-styling
```

### NPM (JavaScript)

```bash
npm install @webwhales/spaces-code-styling
```

## 📚 Documentation Structure

This package contains two main documentation directories:

### PHP Documentation (`/php/`)

- **action-classes.md** - Guidelines for creating and structuring action classes
- **attributes.md** - Best practices for using PHP attributes
- **classes.md** - Class structure, naming conventions, and organization
- **directory-structure-naming.md** - Directory organization and file naming standards
- **docblocks.md** - Documentation block formatting and requirements
- **enums.md** - Enum usage and implementation guidelines
- **functions.md** - Function design, naming, and best practices
- **general.md** - General PHP coding standards and conventions
- **interfaces.md** - Interface design and implementation patterns
- **tests.md** - Testing standards and best practices
- **traits.md** - Trait usage and composition guidelines

### JavaScript Documentation (`/javascript/`)

- **classes.md** - JavaScript class structure and ES6+ patterns
- **comments.md** - Code commenting standards and practices
- **docblocks.md** - JSDoc formatting and documentation requirements
- **functions.md** - Function design, arrow functions, and best practices
- **general.md** - General JavaScript coding standards and conventions
- **vue-js.md** - Vue.js specific guidelines and component patterns

## 🚀 Usage

This package is designed to be referenced by AI agents. Simply include it in your project and reference the documentation files in your AI prompts.

## 🤖 AI Agent Integration

This package is specifically designed to be referenced in AI agent prompts. You can instruct AI agents to follow the guidelines in this package:

### Example AI Prompts

**For PHP Development:**
```
When generating PHP code, please follow the coding standards defined in the
webwhales/spaces-code-styling package. Specifically, refer to:
- php/classes.md for class structure
- php/docblocks.md for documentation requirements
- php/functions.md for function design
- php/general.md for general conventions
```

**For JavaScript Development:**
```
Please follow the JavaScript coding standards from webwhales/spaces-code-styling:
- javascript/classes.md for class patterns
- javascript/functions.md for function design
- javascript/general.md for general conventions
```

**For Vue.js Development:**
```
Please review the Vue.js component guidelines in
webwhales/spaces-code-styling/javascript/vue-js.md before creating components.
```

### Referencing Specific Files

All documentation files are markdown files located in either `/php/` or `/javascript/` directories. Simply reference them by their path in your AI prompts.

## 🎯 Use Cases

- **AI Code Generation** - Provide consistent coding standards for AI-generated code
- **Team Onboarding** - Quick reference for coding conventions
- **Code Reviews** - Standardized guidelines for reviewing code
- **Documentation** - Centralized source of truth for coding practices
- **Automation** - Programmatic access to coding standards

## 📄 License

MIT

## 👤 Author

Bas van Dijk

## 🔗 Repository

[https://github.com/webwhales/spaces-code-styling](https://github.com/webwhales/spaces-code-styling)

