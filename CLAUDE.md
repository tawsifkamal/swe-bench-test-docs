# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

SWE-bench is a benchmark for evaluating large language models on real-world software engineering tasks. It tests LLMs' ability to resolve GitHub issues by generating patches for actual codebases.

## Development Commands

### Installation
```bash
pip install -e .  # Install in development mode
```

### Running Tests
```bash
pytest  # Run test suite
pytest tests/test_specific.py::test_name  # Run single test
```

### Documentation
```bash
mkdocs serve  # Serve docs locally at http://127.0.0.1:8000
mkdocs build  # Build static documentation
```

### Main Evaluation
```bash
# Run evaluation on SWE-bench Lite dataset
python -m swebench.harness.run_evaluation \
    --dataset_name princeton-nlp/SWE-bench_Lite \
    --predictions_path <path_to_predictions> \
    --max_workers 8 \
    --run_id <unique_run_id>

# Test with single instance
python -m swebench.harness.run_evaluation \
    --predictions_path gold \
    --max_workers 1 \
    --instance_ids sympy__sympy-20590 \
    --run_id validate-gold
```

## High-Level Architecture

### Core Modules
- **`swebench/harness/`**: Evaluation engine that runs patches in Docker containers
  - Multi-language support (Python, JS, Java, C, Go, PHP, Ruby, Rust)
  - Docker-based isolation for reproducibility
  - Parallel evaluation with configurable workers
  
- **`swebench/collect/`**: Dataset building from GitHub repositories
  - PR and issue extraction
  - Test case identification
  - Environment setup automation

- **`swebench/inference/`**: Model inference utilities
  - API model support (OpenAI, Anthropic)
  - Local model support (Llama variants)
  - Extensible inference pipeline

- **`swebench/resources/`**: Repository-specific configurations
  - Environment specs for each supported repository
  - Version management configurations

### Key Design Patterns
1. **Docker Isolation**: Each evaluation runs in its own container with the exact repository version and environment
2. **Language Abstraction**: Constants module defines language-specific behavior (test commands, installation, parsing)
3. **Parallel Processing**: Worker pool pattern for concurrent evaluation
4. **Extensibility**: New languages/repos can be added by extending constants and adding environment specs

### Dataset Variants
- **SWE-bench**: Full dataset (~2,300 problems)
- **SWE-bench Lite**: Smaller subset for testing
- **SWE-bench Verified**: Human-verified problems
- **SWE-bench Multimodal**: Visual software domains
- **SWE-bench Multilingual**: Multiple programming languages

### Resource Requirements
- Docker required for evaluation
- ~120GB free disk space
- 16GB+ RAM recommended
- Platform: x86_64 (arm64 experimental)