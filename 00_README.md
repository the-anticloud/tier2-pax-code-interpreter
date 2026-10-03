# PAX Code Interpreter — Safe Sandbox Execution

**Status:** Production | **Version:** 1.0.0 | **Author:** PAX Execution Team  
**Domain:** 0-1.gg/pax/code-interpreter

Restricted Python/shell sandbox for executing LLM-generated code. Powers PAX_MATH_SOLVER, ANTICODE_AGENT, and other code-generating systems. No file I/O, networking, or subprocess access without explicit approval.

---

## Key Features

| Feature | Details |
|---------|---------|
| **Timeout** | 10 seconds per execution |
| **Memory limit** | 512MB per process |
| **Allowed imports** | sympy, numpy, math, json (configurable) |
| **Denied operations** | File I/O, networking, subprocess, exec/eval |
| **Audit trail** | AIOSS ledger integration |
| **Output capture** | stdout, stderr, return value |

---

## Architecture

```
LLM-Generated Code (from PAX_INFERENCE_CORE)
    ↓
AST validation (parse, check for forbidden ops)
    ↓
Sandbox environment setup (restricted globals)
    ↓
Execute with timeout + memory limit
    ↓
Capture output + errors
    ↓
AIOSS ledger: (code_hash, result, timestamp)
    ↓
Return result to LLM
```

---

## Quick Start

```bash
pip install pax-code-interpreter

from pax_code import CodeInterpreter

interp = CodeInterpreter(
    timeout=10,
    memory_limit_mb=512,
    allowed_imports=['sympy', 'numpy', 'math']
)

# Execute code
result = interp.execute("""
from sympy import symbols, solve
x = symbols('x')
solution = solve(x**2 - 5*x + 6, x)
print(solution)
""")

print(f"Output: {result.stdout}")
print(f"Execution time: {result.duration:.2f}s")
```

---

## Security Model

- **Parse-time:** AST validation forbids dangerous operations
- **Runtime:** Restricted globals (no `open()`, `requests`, `subprocess`)
- **Resource:** Timeout + memory limit prevent DoS
- **Audit:** Every execution logged to AIOSS ledger

```python
# This will be blocked (forbidden AST node)
interp.execute("import os; os.system('rm -rf /')")  # ✗ blocked

# This works (safe operation)
interp.execute("print(2 + 2)")  # ✓ allowed
```

---

## Integration with PAX Systems

- **PAX_MATH_SOLVER:** Executes SymPy-generated solutions
- **PAX_INFERENCE_CORE:** User-requested code execution
- **ANTICODE_AGENT:** Runs generated code snippets
- **PAX_CODE_GENERATOR:** Code validation before deployment

---

## Roadmap

- **Q4 2026:** GPU access (for ML code)
- **Q1 2027:** C++ execution (via Cython)
- **Q2 2027:** Multi-language support (JavaScript, Go)

---

**References:** 0-1.gg/pax/code-interpreter
