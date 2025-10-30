# 🤝 Contributing to Tekup Portfolio

Thank you for your interest in contributing to Tekup Portfolio! This document provides comprehensive guidelines for contributing to any of our projects.

---

## 📋 Table of Contents

1. [Getting Started](#-getting-started)
2. [Development Setup](#-development-setup)
3. [Project Structure](#-project-structure)
4. [Coding Standards](#-coding-standards)
5. [Git Workflow](#-git-workflow)
6. [Testing](#-testing)
7. [Documentation](#-documentation)
8. [Pull Request Process](#-pull-request-process)
9. [Code Review](#-code-review)

---

## 🚀 Getting Started

### Prerequisites

Before you begin, ensure you have the following installed:

- **Node.js 18+** (LTS version recommended)
- **pnpm 8+** (enforced via `packageManager` field)
- **Git** with conventional commit support
- **Docker** (for local services and databases)
- **PostgreSQL 14+** (or use Docker)

### Quick Setup

```bash
# Clone the repository
git clone https://github.com/TekupDK/<repository-name>.git
cd <repository-name>

# Install dependencies (also sets up git hooks)
pnpm install

# Copy environment variables
cp .env.example .env
# Edit .env with your credentials

# Start development
pnpm dev
```

---

## 🛠️ Development Setup

### Environment Configuration

Each project requires specific environment variables. Always start by copying the example file:

```bash
cp .env.example .env
```

**Required Variables (common across projects):**

```env
# Database
DATABASE_URL=postgresql://postgres:postgres@localhost:5432/dbname
SUPABASE_URL=https://[project].supabase.co
SUPABASE_ANON_KEY=your_anon_key
SUPABASE_SERVICE_KEY=your_service_key

# API Keys
OPENAI_API_KEY=sk-proj-...
API_KEY=your_internal_api_key

# Server
PORT=3000
NODE_ENV=development
LOG_LEVEL=info
```

### Database Setup

**Option 1: Docker (Recommended for local development)**

```bash
# Start PostgreSQL with docker-compose
docker-compose up -d

# Run migrations
pnpm prisma migrate dev
```

**Option 2: Supabase (Recommended for staging/production)**

```bash
# Link to Supabase project
npx supabase link --project-ref your-project-ref

# Push migrations
npx supabase db push
```

### Project-Specific Setup

Each project has detailed setup instructions in its README:

- **TekupVault**: `apps/production/tekup-vault/README.md`
- **Rendetalje**: `apps/rendetalje/README.md`
- **Services**: Check individual service README files

---

## 📁 Project Structure

Tekup projects follow a consistent monorepo structure:

```
repository/
├── apps/              # Applications (runtime-based organization)
│   ├── web/          # Web applications (Next.js, React)
│   ├── mobile/       # Mobile apps (React Native, Expo)
│   └── production/   # Production services (APIs, workers)
├── packages/          # Shared libraries and utilities
│   ├── ui/           # Shared UI components
│   ├── config/       # Shared configuration
│   └── types/        # Shared TypeScript types
├── services/          # Backend services and APIs
├── docs/              # Documentation
├── scripts/           # Automation and utility scripts
└── tests/             # Integration and E2E tests
```

**Organization Principle:** Structure by runtime and purpose, not by technology.

---

## 🎨 Coding Standards

### TypeScript Guidelines

**✅ Always use TypeScript strict mode:**

```typescript
// tsconfig.json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true
  }
}
```

**✅ Define interfaces for all data structures:**

```typescript
interface Customer {
  id: string;
  name: string;
  email: string;
  createdAt: Date;
}

interface ApiResponse<T> {
  success: boolean;
  data?: T;
  error?: string;
}
```

**✅ Use meaningful names and JSDoc comments:**

```typescript
/**
 * Fetch customer by ID from database
 * @param id - Customer UUID
 * @returns Promise resolving to Customer object or null if not found
 * @throws {DatabaseError} If database connection fails
 */
async function getCustomer(id: string): Promise<Customer | null> {
  // Implementation
}
```

### Code Style

We use automated tools to enforce consistent code style:

- **Formatter:** Prettier
- **Linter:** ESLint with strict rules
- **Line length:** 100 characters maximum
- **Indentation:** 2 spaces
- **Quotes:** Single quotes for JS/TS, double for JSON
- **Semicolons:** Required

**Run formatting and linting:**

```bash
# Format all code
pnpm format

# Lint code
pnpm lint

# Auto-fix issues
pnpm lint:fix
```

### Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| Files | `kebab-case.ts` | `user-service.ts` |
| Components | `PascalCase.tsx` | `UserProfile.tsx` |
| Functions | `camelCase()` | `getUserById()` |
| Constants | `UPPER_SNAKE_CASE` | `MAX_RETRIES` |
| Types/Interfaces | `PascalCase` | `UserData`, `ApiResponse` |
| Enums | `PascalCase` | `UserRole`, `OrderStatus` |

---

## 🔄 Git Workflow

### Branch Naming

Use descriptive branch names that follow this pattern:

```
<type>/<short-description>

Examples:
feature/add-customer-search
bugfix/fix-login-redirect
hotfix/critical-security-patch
refactor/improve-database-query
docs/update-api-documentation
chore/update-dependencies
```

### Conventional Commits

We strictly follow [Conventional Commits](https://www.conventionalcommits.org/) specification:

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, no logic change)
- `refactor`: Code refactoring
- `test`: Adding or updating tests
- `chore`: Maintenance tasks (dependencies, config)
- `perf`: Performance improvements
- `ci`: CI/CD changes

**Examples:**

```bash
# Feature with scope
git commit -m "feat(vault): add semantic search endpoint with OpenAI embeddings"

# Bug fix
git commit -m "fix(billy): resolve invoice duplication when retrying failed requests"

# Documentation
git commit -m "docs(readme): update quick start guide with Docker setup"

# Breaking change
git commit -m "feat(api)!: change authentication to use JWT tokens

BREAKING CHANGE: API now requires JWT token in Authorization header instead of API key"
```

**Git Hooks:**

Husky automatically validates commit messages before creation. Invalid commits will be rejected.

---

## 🧪 Testing

### Test Structure

```
project/
├── src/
│   └── user/
│       ├── user.service.ts
│       └── user.controller.ts
└── tests/
    ├── unit/
    │   ├── user.service.test.ts
    │   └── user.controller.test.ts
    ├── integration/
    │   └── user.api.test.ts
    └── e2e/
        └── user-flow.test.ts
```

### Running Tests

```bash
# Run all tests
pnpm test

# Run specific test suite
pnpm test:unit
pnpm test:integration
pnpm test:e2e

# Run tests in watch mode
pnpm test:watch

# Generate coverage report
pnpm test:coverage
```

### Test Coverage Requirements

- **Unit tests:** 80%+ coverage for all business logic
- **Integration tests:** All critical API endpoints covered
- **E2E tests:** Main user flows covered

### Writing Good Tests

```typescript
import { describe, it, expect, beforeEach } from 'vitest';
import { CustomerService } from './customer.service';

describe('CustomerService', () => {
  let service: CustomerService;

  beforeEach(() => {
    service = new CustomerService();
  });

  describe('getCustomer', () => {
    it('should return customer when ID exists', async () => {
      const customer = await service.getCustomer('123');
      
      expect(customer).toBeDefined();
      expect(customer.id).toBe('123');
      expect(customer.email).toMatch(/^[\w-.]+@([\w-]+\.)+[\w-]{2,4}$/);
    });

    it('should return null when customer not found', async () => {
      const customer = await service.getCustomer('invalid');
      expect(customer).toBeNull();
    });

    it('should throw error when database is unavailable', async () => {
      // Mock database failure
      await expect(service.getCustomer('123')).rejects.toThrow('Database unavailable');
    });
  });
});
```

---

## 📚 Documentation

### Code Documentation

**✅ Document all public APIs:**

```typescript
/**
 * Customer service for managing customer data
 * 
 * @example
 * ```typescript
 * const service = new CustomerService();
 * const customer = await service.getCustomer('123');
 * ```
 */
export class CustomerService {
  /**
   * Retrieve customer by ID
   * @param id - Customer UUID
   * @returns Customer object or null if not found
   */
  async getCustomer(id: string): Promise<Customer | null> {
    // Implementation
  }
}
```

**✅ Document complex logic:**

```typescript
// Calculate discounted price based on customer tier and order volume
// Tier 1 (>10 orders): 10% discount
// Tier 2 (>50 orders): 20% discount
// Tier 3 (>100 orders): 30% discount
const discount = calculateTierDiscount(customer.orderCount);
```

### Project Documentation

**Required files for each project:**

- `README.md` - Overview, setup, usage
- `CHANGELOG.md` - Version history and changes
- `API.md` - API documentation (if applicable)
- `ARCHITECTURE.md` - High-level architecture

### API Documentation

Use OpenAPI/Swagger for REST APIs:

```typescript
/**
 * @swagger
 * /api/customers/{id}:
 *   get:
 *     summary: Get customer by ID
 *     parameters:
 *       - in: path
 *         name: id
 *         required: true
 *         schema:
 *           type: string
 *     responses:
 *       200:
 *         description: Customer found
 *         content:
 *           application/json:
 *             schema:
 *               $ref: '#/components/schemas/Customer'
 *       404:
 *         description: Customer not found
 */
```

---

## 🔀 Pull Request Process

### 1. Create Branch

```bash
git checkout -b feature/my-awesome-feature
```

### 2. Make Changes

- Write code following our standards
- Add tests for new functionality
- Update documentation as needed
- Ensure all tests pass locally

### 3. Commit Changes

```bash
git add .
git commit -m "feat(scope): add awesome feature"
```

### 4. Push and Create PR

```bash
git push origin feature/my-awesome-feature
```

Then create a Pull Request on GitHub with:

- **Clear title** following conventional commit format
- **Detailed description** of changes and motivation
- **Screenshots** for UI changes
- **Breaking changes** clearly documented
- **Related issues** linked (e.g., "Closes #123")

### 5. PR Description Template

```markdown
## Description
Brief description of what this PR does.

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Changes Made
- Added X feature
- Fixed Y bug
- Updated Z documentation

## Testing
- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] Manual testing completed

## Screenshots (if applicable)
[Add screenshots here]

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Comments added for complex code
- [ ] Documentation updated
- [ ] No new warnings generated
- [ ] Tests added/updated
- [ ] All tests passing
```

---

## 👀 Code Review

### Review Checklist

**For Reviewers:**

- [ ] Code follows project standards and conventions
- [ ] Tests are comprehensive and passing
- [ ] Documentation is updated
- [ ] No security vulnerabilities introduced
- [ ] Performance implications considered
- [ ] Error handling is appropriate
- [ ] Code is maintainable and readable
- [ ] Commit messages follow conventions

**For Authors:**

- [ ] Self-review completed before requesting review
- [ ] All CI checks passing
- [ ] Addressed all review comments
- [ ] Tests cover edge cases
- [ ] Documentation is clear and complete

### Review Guidelines

**Be constructive and respectful:**

✅ Good:
```
Consider using a more descriptive variable name here.
This could be simplified using array.map().
Have you considered the case where user is null?
```

❌ Bad:
```
This code is terrible.
Why didn't you just...?
This is wrong.
```

### Merge Requirements

Before merging, ensure:

1. ✅ All CI/CD checks pass
2. ✅ At least one approving review from a maintainer
3. ✅ All review comments resolved
4. ✅ No merge conflicts
5. ✅ Branch is up to date with base branch

---

## ❓ Getting Help

### Resources

- 📖 **Documentation**: Check project-specific docs in `/docs`
- 💬 **Discussions**: Use GitHub Discussions for questions
- 🐛 **Issues**: Search existing issues before creating new ones
- 📧 **Contact**: Reach out to maintainers for urgent matters

### Issue Templates

Use appropriate issue templates:

- 🐛 **Bug Report**: For reporting bugs
- ✨ **Feature Request**: For suggesting new features
- 📚 **Documentation**: For documentation improvements
- ❓ **Question**: For asking questions

---

## 📝 Additional Guidelines

### Security

- Never commit secrets or credentials
- Use environment variables for sensitive data
- Report security vulnerabilities privately
- Follow our [Security Policy](SECURITY.md)

### Performance

- Consider performance implications of changes
- Profile code for performance bottlenecks
- Optimize database queries
- Use caching where appropriate

### Accessibility

- Follow WCAG 2.1 Level AA guidelines
- Test with screen readers
- Ensure keyboard navigation works
- Use semantic HTML

---

## 🙏 Thank You!

Thank you for contributing to Tekup Portfolio! Your efforts help us build better tools for businesses worldwide.

**Questions?** Don't hesitate to ask! We're here to help.

---

<div align="center">

**Built with ❤️ by the Tekup Team**

[GitHub](https://github.com/TekupDK) • [Issues](https://github.com/TekupDK/.github/issues) • [Discussions](https://github.com/TekupDK/.github/discussions)

</div>
