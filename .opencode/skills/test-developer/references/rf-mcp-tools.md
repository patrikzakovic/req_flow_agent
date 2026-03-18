# rf-mcp Tool Usage Reference

## Available Tools

The following rf-mcp tools MUST be used during script generation to ensure correctness.

### robot-get_environment_details

**When to use:** At the start of every generation task to check the RF environment.

**Returns:** Python version, Robot Framework version, installed libraries and their versions.

**Why it matters:** Keyword availability and syntax may differ between library versions. Always check before generating.

### robot-get_library_documentation

**When to use:** To look up all available keywords for a library.

**Input:** Library name (e.g., `Browser`)

**Returns:** Full list of keywords with brief descriptions.

**Why it matters:** Ensures you use real, existing keywords — not hallucinated ones.

### robot-get_keyword_documentation

**When to use:** When you need the exact signature of a specific keyword.

**Input:** Keyword name and library

**Returns:** Full keyword documentation including parameters, types, defaults, and examples.

**Why it matters:** Keyword parameters vary between libraries and versions. Always verify before using a keyword you're unsure about.

### robot-get_file_imports

**When to use:** After generating a `.robot` file to validate imports.

**Input:** Path to the `.robot` file

**Returns:** List of resolved imports (libraries, resources, variables files).

**Why it matters:** Catches missing or misconfigured imports before the user tries to run the suite.

## Recommended Workflow

```
1. robot-get_environment_details
   → Know what RF version and libraries are installed

2. robot-get_library_documentation  (for target library)
   → Get full keyword list

3. robot-get_keyword_documentation  (for uncertain keywords)
   → Verify exact signatures and parameters

4. Generate .robot files
   → Using verified keywords only

5. robot-get_file_imports  (on generated files)
   → Validate all imports resolve correctly
```
