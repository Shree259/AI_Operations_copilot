# Contributing to Copilot AI

Thank you for your interest in contributing to Copilot AI! This document provides guidelines and instructions for contributing.

## 🚀 Getting Started

1. **Fork** the repository on GitHub
2. **Clone** your fork locally:
   ```bash
   git clone https://github.com/your-username/AI_Operations_copilot.git
   ```
3. **Create** a branch for your feature or fix:
   ```bash
   git checkout -b feature/your-feature-name
   ```

## 💻 Development Setup

### Backend

```bash
cd backend
python -m venv venv
.\venv\Scripts\activate    # Windows
source venv/bin/activate   # macOS/Linux
pip install -r requirements.txt
```

### Frontend

```bash
cd frontend
npm install
npm run dev
```

## 📋 Contribution Guidelines

### Code Style

- **Python**: Follow [PEP 8](https://peps.python.org/pep-0008/) style guidelines
- **JavaScript/React**: Follow the ESLint configuration provided in the project
- **Commits**: Write clear, descriptive commit messages following [Conventional Commits](https://www.conventionalcommits.org/)

### Pull Request Process

1. Update the README.md if your changes affect the public API or features
2. Ensure all existing functionality works correctly
3. Add sample data files if introducing new format support
4. Submit a Pull Request with a clear description of changes

### Reporting Bugs

Open an issue on GitHub with:
- A clear, descriptive title
- Steps to reproduce the issue
- Expected vs. actual behavior
- Screenshots if applicable
- Your environment details (OS, Python version, Node version)

### Suggesting Features

Open an issue with the `enhancement` label describing:
- The problem your feature solves
- Your proposed solution
- Any alternative approaches considered

## 🙏 Thank You

Every contribution, no matter how small, makes a difference. Thank you for helping make Copilot AI better!
